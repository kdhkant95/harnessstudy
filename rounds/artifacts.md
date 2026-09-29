# 하네스 산출물

이 문서는 Codex 하네스의 단계별 산출물 파일명, 규칙 SSOT, 재개 가능 조건, 파일명 규칙을 정의한다.

## R4 산출물 요약

| 항목 | 확정 내용 | 출처 |
| --- | --- | --- |
| 단계별 산출물 파일명 | `output/story-service.md`, `rounds/purpose.md`, `rounds/pipeline.md`, `rounds/artifacts.md`, `rounds/gates.md`, `rounds/roles.md`, `AGENTS.md` | PRD 교체 구조 개선 |
| 규칙 SSOT 파일 | `rules.yaml` | 체크리스트 보완 |
| 재개 기준 | 마지막으로 승인된 라운드 파일이 있으면 다음 라운드부터 재개 | 기본값 |
| 파일명 규칙 | `^[a-z0-9-]+\\.md$` 형식만 허용 | 기본값 |

## 단계별 산출물

| 라운드 | 파일 | 역할 |
| --- | --- | --- |
| R1-A | `output/story-service.md` | `input/PRD.md`에서 생성되는 서비스 유저스토리와 금지 조건 |
| R2 | `rounds/purpose.md` | 목적, 사용자, 완료 기준 |
| R3 | `rounds/pipeline.md` | 단계 분할, 입출력, 실패 시 복귀 |
| R4 | `rounds/artifacts.md` | 산출물 파일명, 규칙 SSOT, 재개 가능 여부 |
| R5 | `rounds/gates.md`, `rules.yaml` | 기계가 셀 수 있는 통과 조건과 사람 승인 지점 |
| R6 | `rounds/roles.md` | 에이전트별 역할, 편집 폴더, 자연어 트리거 |
| R7~R8 | `AGENTS.md` | 오케스트레이터 초안, 검증과 리뷰 기준 |

## 재개 가능 조건

| 조건 | 판정 |
| --- | --- |
| 마지막 승인 라운드의 파일이 존재함 | 다음 라운드부터 재개 가능 |
| 마지막 승인 라운드의 파일이 없음 | 해당 라운드부터 다시 확인 필요 |

## 파일명 규칙

| 규칙 | 통과 조건 |
| --- | --- |
| Markdown 파일 | `.md` 확장자를 사용한다 |
| 소문자 파일명 | 대문자를 사용하지 않는다 |
| 구분자 | 단어 구분은 하이픈만 사용한다 |
| 정규식 | `^[a-z0-9-]+\\.md$` |

`AGENTS.md`는 사용자가 지정한 최종 오케스트레이터 파일명이므로 파일명 규칙의 예외로 허용한다.
