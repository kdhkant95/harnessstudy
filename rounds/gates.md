# 하네스 게이트

이 문서는 Codex 하네스가 다음 단계로 넘어가기 전에 확인해야 하는 통과 조건을 설명한다. 스크립트가 실제로 읽는 판정 기준의 SSOT는 `rules.yaml`이다.

## R5 게이트 요약

| 게이트 | 이름 | 통과 조건 | 실패 시 복귀 |
| --- | --- | --- | --- |
| G1 | 입력 파일 확인 | `rules.yaml`의 `G1.targets`가 모두 존재한다 | R0 |
| G2 | 스토리 최신성 확인 | `output/story-service.md`가 존재하고 `input/PRD.md`보다 같거나 최신이다 | R1 |
| G3 | 서비스 금지 조건 확인 | `AGENTS.md`에 `권한 없는 데이터 노출 금지`, `최고관리자 외 경영·재무 노출 금지`가 모두 있다 | R1 |
| G4 | 디자인 기준 확인 | `AGENTS.md`에 MUI, 팔레트 2개, 1440x1080, 2~3개가 모두 있다 | `context/design.md` |
| G5 | 산출물 파일명 확인 | 산출물 파일명이 `^[a-z0-9-]+\\.md$` 규칙을 만족하거나 예외 목록에 있다 | R4 |
| G6 | 역할 경계 확인 | `agents/interviewer/status.md`, `agents/writer/status.md`, `agents/judge/status.md`가 모두 존재한다 | R6 |
| G7 | 최종 승인 확인 | `AGENTS.md`에 최종 승인 문구 2개가 모두 있다 | R7 |

## 필수 입력 파일

| 파일 | 필수 여부 |
| --- | --- |
| `input/PRD.md` | 필수 입력 |
| `output/story-service.md` | PRD 기반 생성 산출물 |
| `context/story-work.md` | 필수 |
| `context/domain.md` | 필수 |
| `context/design.md` | 필수 |
| `rounds/purpose.md` | 필수 |
| `rounds/pipeline.md` | 필수 |
| `rounds/artifacts.md` | 필수 |
| `rounds/gates.md` | 필수 |
| `rounds/roles.md` | 필수 |
| `assets/palette/light.tokens.json` | 필수 |
| `assets/palette/dark.tokens.json` | 필수 |
| `rules.yaml` | 필수 |

## G3 서비스 금지 조건

최소 1개 게이트는 R1-A의 “이 서비스에서 어기면 안 되는 것”을 위반하면 실패해야 한다.

| 금지 조건 | 실패 조건 |
| --- | --- |
| 조회 권한이 없는 정보는 위젯, 행, 건수, AI 요약, API 응답을 통해 노출하면 안 된다. | `AGENTS.md`에 권한 없는 데이터 노출 금지 조건이 없으면 실패 |
| 경영·재무 데이터와 이를 포함한 AI 요약·추천은 최고관리자 Role 외 사용자에게 노출하면 안 된다. | `AGENTS.md`에 최고관리자 외 경영·재무 노출 금지 조건이 없으면 실패 |

## 사람 승인 지점

| 승인 지점 | 통과 조건 |
| --- | --- |
| 최종 승인 | `AGENTS.md` 초안 생성 후 사용자 승인 1회를 받는다 |

## 게이트 판정 형식

| 값 | 의미 |
| --- | --- |
| `pass` | 통과 |
| `fail` | 실패 |

게이트 하나라도 `fail`이면 완료로 보지 않는다. 구체적인 판정 타입은 `rules.yaml`의 `type` 값을 따른다.
