# Codex Harness Orchestrator

이 문서는 기획 겸 디자이너가 사람 손으로 하던 작업을 Codex 하네스인 작업 에이전트, 게이트, 판정 스크립트로 실행하기 위한 오케스트레이터 초안이다.

`CLAUDE.md`가 있으면 참고할 수 있지만, 이 하네스의 최종 오케스트레이터 기준은 `AGENTS.md`로 둔다.

## 1. 목적

| 항목 | 내용 |
| --- | --- |
| 사용자 | 기획 겸 디자이너인 사용자 |
| 매번 달라지는 입력값 | `input/PRD.md` |
| PRD 기반 생성 산출물 | `output/story-service.md` |
| 고정 참조 문서 | `context/story-work.md`, `context/domain.md`, `context/design.md` |
| 완료 기준 | `AGENTS.md` 초안 1개가 생성되고, R0~R8 게이트가 모두 통과하면 완료 |

## 2. 입력 파일

| 파일 | 용도 | 필수 |
| --- | --- | --- |
| `input/PRD.md` | 프로젝트마다 갈아끼우는 서비스 요구사항 원문 | 예 |
| `output/story-service.md` | `input/PRD.md`에서 생성한 서비스 유저스토리와 금지 조건 | 생성 산출물 |
| `context/story-work.md` | 디자인 워크플로우 유저스토리 | 예 |
| `context/domain.md` | 장기요양 도메인 지식과 용어·오해 방지 기준 | 예 |
| `context/design.md` | 디자인 기본값과 게이트 기준 SSOT | 예 |
| `rounds/purpose.md` | 목적, 사용자, 완료 기준 | 예 |
| `rounds/pipeline.md` | 파이프라인과 복귀 지점 | 예 |
| `rounds/artifacts.md` | 산출물과 재개 기준 | 예 |
| `rounds/gates.md` | 게이트 판정 조건 | 예 |
| `rounds/roles.md` | 역할, 편집 폴더, 자연어 트리거 | 예 |
| `rules.yaml` | 스크립트가 읽는 게이트 판정 기준 SSOT | 예 |

## 3. 파이프라인

| 순서 | 단계 | 입력 | 출력 | 실패 시 복귀 |
| --- | --- | --- | --- | --- |
| 1 | 입력 확인 | `input/PRD.md`, `context/story-work.md`, `context/domain.md`, `context/design.md` | 입력 파일 확인 결과 | R0 |
| 2 | 스토리 생성·확인 | `input/PRD.md`, `context/domain.md` | `output/story-service.md` | R1 |
| 3 | 디자인 기준 확인 | `context/design.md`, `assets/palette/*.tokens.json` | 디자인 기준 확인 결과 | `context/design.md` |
| 4 | `AGENTS.md` 작성 | R2~R6 산출물 | `AGENTS.md` 초안 | 직전 단계 |
| 5 | 검증 | 모든 산출물 | 게이트 `pass`/`fail` 결과 | 실패한 게이트의 복귀 지점 |

단계 수는 최소 3개, 최대 5개로 제한한다.

## 4. 산출물

| 라운드 | 파일 | 역할 |
| --- | --- | --- |
| R2 | `rounds/purpose.md` | 목적, 사용자, 완료 기준 |
| R3 | `rounds/pipeline.md` | 단계 분할, 입출력, 실패 시 복귀 |
| R4 | `rounds/artifacts.md` | 산출물 파일명, 규칙 SSOT, 재개 가능 여부 |
| R5 | `rounds/gates.md` | 기계가 셀 수 있는 통과 조건과 사람 승인 지점 |
| R6 | `rounds/roles.md` | 에이전트별 역할, 편집 폴더, 자연어 트리거 |
| R7~R8 | `AGENTS.md` | 오케스트레이터 초안, 검증과 리뷰 기준 |
| 매 프로젝트 | `output/story-service.md` | `input/PRD.md`에서 재생성되는 서비스 유저스토리와 금지 조건 |

파일명은 `^[a-z0-9-]+\\.md$` 형식을 따른다. 단, 최종 오케스트레이터 파일 `AGENTS.md`는 사용자 지정 예외로 허용한다. 이 규칙의 기계 판정 기준은 `rules.yaml`에 둔다.

마지막으로 승인된 라운드 파일이 있으면 다음 라운드부터 재개할 수 있다.

## 5. 역할

| 역할 | 에이전트 | 편집 폴더 | 권한 |
| --- | --- | --- | --- |
| 인터뷰어 | `interviewer` | `agents/interviewer` | 질문, 답변 정리, 사용자 확인 요청 |
| 문서 작성자 | `writer` | `agents/writer` | 승인된 내용을 문서로 작성 |
| 게이트 판정자 | `judge` | `agents/judge` | 읽기전용 판정, 파일 수정 금지 |

역할 경계는 아래를 따른다.

| 규칙 | 통과 조건 |
| --- | --- |
| 인터뷰어는 바로 파일을 만들지 않는다 | 사용자 확인 후 작성자에게 넘긴다 |
| 문서 작성자는 승인된 내용만 쓴다 | 미승인 내용이 파일에 들어가지 않는다 |
| 게이트 판정자는 파일을 수정하지 않는다 | 판정 결과만 `pass` 또는 `fail`로 낸다 |

## 6. 자연어 트리거

| 트리거 | 실행 역할 | 의미 |
| --- | --- | --- |
| 하네스 시작 | `interviewer` | 라운드 질문을 시작한다 |
| 게이트 검사 | `judge` | 게이트 통과 여부를 읽기전용으로 판정한다 |
| AGENTS 초안 만들어 | `writer` | 승인된 산출물을 바탕으로 `AGENTS.md` 초안을 작성한다 |

## 7. 게이트

| 게이트 | 이름 | 통과 조건 | 실패 시 복귀 |
| --- | --- | --- | --- |
| G1 | 입력 파일 확인 | `rules.yaml`의 `G1.targets`가 모두 존재한다 | R0 |
| G2 | 스토리 최신성 확인 | `output/story-service.md`가 존재하고 `input/PRD.md`보다 같거나 최신이다 | R1 |
| G3 | 서비스 금지 조건 확인 | `AGENTS.md`에 `권한 없는 데이터 노출 금지`, `최고관리자 외 경영·재무 노출 금지`가 모두 있다 | R1 |
| G4 | 디자인 기준 확인 | `AGENTS.md`에 MUI, 팔레트 2개, 1440x1080, 2~3개가 모두 있다 | `context/design.md` |
| G5 | 산출물 파일명 확인 | 산출물 파일명이 `^[a-z0-9-]+\\.md$` 규칙을 만족하거나 예외 목록에 있다 | R4 |
| G6 | 역할 경계 확인 | `agents/interviewer/status.md`, `agents/writer/status.md`, `agents/judge/status.md`가 모두 존재한다 | R6 |
| G7 | 최종 승인 확인 | `AGENTS.md`에 최종 승인 문구 2개가 모두 있다 | R7 |

게이트 판정값은 `pass` 또는 `fail`만 사용한다. 하나라도 `fail`이면 완료로 보지 않는다. 스크립트가 읽는 판정 수치는 `rules.yaml` 한 파일에 둔다.

## 8. 서비스 금지 조건

아래 조건은 `output/story-service.md`에서 온 필수 게이트 조건이다.

| 금지 조건 | 실패 조건 |
| --- | --- |
| 조회 권한이 없는 정보는 위젯, 행, 건수, AI 요약, API 응답을 통해 노출하면 안 된다. | 최종 산출물에 권한 없는 데이터 노출 금지 조건이 없으면 실패 |
| 경영·재무 데이터와 이를 포함한 AI 요약·추천은 최고관리자 Role 외 사용자에게 노출하면 안 된다. | 최종 산출물에 최고관리자 외 경영·재무 노출 금지 조건이 없으면 실패 |

## 9. 디자인 기준

`context/design.md`를 디자인 기준 SSOT로 둔다.

| 항목 | 기준 |
| --- | --- |
| 디자인 시스템 | MUI 디자인 시스템 |
| 시안 | 대시보드 1차 시안 |
| 컬러 팔레트 | `assets/palette/light.tokens.json`, `assets/palette/dark.tokens.json` |
| 키 스크린 | 1440x1080 사이즈 2~3개 |
| 기본 테마 | Light theme 우선 |
| 상태 표현 | 색상만 쓰지 않고 텍스트 상태명을 함께 표시 |

## 10. PRD 교체 규칙

스프린트나 프로젝트가 바뀌면 `input/PRD.md`만 교체한다. 교체 후에는 반드시 `output/story-service.md`를 다시 생성한다.

| 상황 | 처리 |
| --- | --- |
| `input/PRD.md`가 바뀜 | `output/story-service.md`를 새 PRD 기준으로 재생성 |
| `input/PRD.md`가 `output/story-service.md`보다 최신 | G2 실패, R1로 복귀 |
| `output/story-service.md`가 새로 승인됨 | 이후 디자인 기준, 게이트, 최종 오케스트레이터 검증으로 진행 |

## 11. R8 검증과 리뷰

검증은 읽기전용으로 수행한다.

| 검증 항목 | 통과 조건 |
| --- | --- |
| 파일 존재 | `input/PRD.md`, `output/story-service.md`, `rounds/purpose.md`, `rounds/pipeline.md`, `rounds/artifacts.md`, `rounds/gates.md`, `rounds/roles.md`, `AGENTS.md`, `rules.yaml`이 존재한다 |
| 입력 기준 존재 | `input/PRD.md`, `context/story-work.md`, `context/domain.md`, `context/design.md`가 존재한다 |
| 스토리 최신성 | `output/story-service.md`가 `input/PRD.md`보다 같거나 최신이다 |
| 서비스 금지 조건 반영 | 권한 없는 데이터 노출 금지와 최고관리자 외 경영·재무 노출 금지가 포함되어 있다 |
| 디자인 기준 반영 | MUI, 팔레트, 1440x1080, 키 스크린 2~3개 기준이 포함되어 있다 |
| 도메인 기준 존재 | `context/domain.md`가 존재하고 장기요양 도메인 지식은 요구사항이 아니라 배경 기준으로 정의되어 있다 |
| 역할 경계 반영 | `judge`가 읽기전용이고 파일 수정 금지로 정의되어 있다 |
| 최종 승인 상태 | 사용자 승인 완료 문구가 있다 |

## 12. 최종 승인

사용자가 승인했으므로 `AGENTS.md`를 최종 오케스트레이터로 확정한다.
