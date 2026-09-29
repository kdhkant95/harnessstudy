# Harness Study

기획 겸 디자이너가 매 프로젝트마다 반복하는 `PRD → 유저스토리 → 디자인 기준 → 게이트 검증 → 작업 에이전트 지시` 흐름을 Codex 하네스로 정리한 저장소입니다.

이 하네스의 핵심은 프로젝트마다 `input/PRD.md`만 갈아끼우면, 그 PRD에 맞는 `output/story-service.md`를 다시 만들고 이후 디자인·검증 기준을 이어서 적용하는 것입니다.

## 핵심 파일

| 파일 | 역할 |
| --- | --- |
| `AGENTS.md` | Codex가 따를 최종 오케스트레이터 |
| `rules.yaml` | 스크립트가 읽는 게이트 판정 기준 SSOT |
| `input/PRD.md` | 프로젝트마다 교체하는 요구사항 입력 |
| `output/story-service.md` | PRD에서 생성되는 서비스 유저스토리와 금지 조건 |
| `context/story-work.md` | 디자이너 작업 흐름 |
| `context/domain.md` | 장기요양 도메인 지식 |
| `context/design.md` | 디자인 기본값과 Figma 기준 |

## 폴더 구조

```text
.
├── AGENTS.md
├── rules.yaml
├── input/
│   └── PRD.md
├── output/
│   └── story-service.md
├── context/
│   ├── design.md
│   ├── domain.md
│   └── story-work.md
├── rounds/
│   ├── artifacts.md
│   ├── gates.md
│   ├── pipeline.md
│   ├── purpose.md
│   └── roles.md
├── assets/
│   └── palette/
└── agents/
    ├── interviewer/
    ├── writer/
    └── judge/
```

## 사용 방법

1. 새 프로젝트나 스프린트가 시작되면 `input/PRD.md`를 교체합니다.
2. Codex에게 PRD 기준으로 `output/story-service.md`를 다시 생성하게 합니다.
3. `context/domain.md`와 `context/design.md`를 참고해 화면 설계 기준을 확인합니다.
4. `rules.yaml`의 게이트를 기준으로 산출물이 통과 가능한지 검사합니다.
5. 필요하면 Figma에 1440x1080 키스크린 2~3개를 제작합니다.

## PRD 교체 규칙

`input/PRD.md`가 바뀌면 `output/story-service.md`는 반드시 다시 생성해야 합니다.

`rules.yaml`에는 `mtime_gte` 게이트가 있어, `input/PRD.md`가 `output/story-service.md`보다 최신이면 스토리 최신성 게이트가 실패하도록 되어 있습니다.

## 게이트 요약

| 게이트 | 조건 |
| --- | --- |
| G1 | 필수 입력 파일이 모두 존재한다 |
| G2 | `output/story-service.md`가 `input/PRD.md`보다 같거나 최신이다 |
| G3 | 서비스 금지 조건이 최종 오케스트레이터에 반영되어 있다 |
| G4 | 디자인 기준이 반영되어 있다 |
| G5 | 산출물 파일명이 규칙을 만족한다 |
| G6 | 에이전트 역할 경계가 존재한다 |
| G7 | 최종 승인 문구가 기록되어 있다 |

## 서비스 금지 조건

현재 하네스는 장기요양 대시보드 프로젝트를 기준으로 아래 조건을 필수 게이트에 반영합니다.

- 조회 권한이 없는 정보는 위젯, 행, 건수, AI 요약, API 응답에 노출하지 않는다.
- 경영·재무 데이터와 이를 포함한 AI 요약·추천은 최고관리자 Role 외 사용자에게 노출하지 않는다.

## Figma 디자인 기준

`context/design.md`는 다음 기준을 기본값으로 둡니다.

- MUI 디자인 시스템 참고
- Light theme 우선
- `assets/palette/*.tokens.json` 팔레트 사용
- 1440x1080 키스크린 2~3개
- 상태는 색상만으로 표현하지 않고 텍스트 상태명을 함께 표시

## 역할

| 역할 | 책임 |
| --- | --- |
| `interviewer` | 질문, 답변 정리, 사용자 확인 |
| `writer` | 승인된 내용만 문서화 |
| `judge` | 읽기전용 게이트 판정 |

## 참고

최종 운영 기준은 `AGENTS.md`입니다. `rules.yaml`은 기계 판정용 규칙이고, `context/*`는 판단 배경과 디자인/도메인 기준입니다.
