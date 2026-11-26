# arttoy-icon-studio

미니어처 아트토이 스타일 3D 아이콘의 **프롬프트 설계 → 이미지 생성 → 품질 검수**를 지원하는 스킬 모음입니다. 세 스킬은 같은 스타일 가이드(`shared/`)를 참조하며, 각각 독립적으로 호출할 수 있습니다.

## 폴더 구조

```text
arttoy-icon-studio/
├── CLAUDE.md                  # 공통 운영 원칙 (스킬 간 규칙, 정직한 완료 보고 등)
├── README.md                  # 이 문서
├── LICENSE.txt                # 저작권·라이선스 (CC BY-NC 4.0)
├── arttoy-ic-prompt/
│   └── SKILL.md               # 프롬프트 생성·최적화
├── arttoy-ic-maker/
│   └── SKILL.md               # 이미지 생성·수정
├── arttoy-ic-review/
│   └── SKILL.md               # 이미지 검수
└── shared/                    # 모든 스킬이 참조하는 단일 출처
    ├── brand-style-guide.md   # 스타일 정의·제작 기준
    ├── negative-rules.md      # 금지·회피 요소
    ├── review-checklist.md    # 검수 기준
    └── reference-images/
        └── README.md          # 기준 이미지 등록 방법
```

## 사용 방법

| 하고 싶은 일 | 호출 |
|---|---|
| 프롬프트 만들기 | `arttoy-ic-prompt` |
| 이미지 만들기/고치기 | `arttoy-ic-maker` |
| 이미지 검수받기 | `arttoy-ic-review` |

## 연계 워크플로우

```text
사용자 제작 요청
      ↓
arttoy-ic-prompt  (프롬프트 설계)
      ↓
arttoy-ic-maker   (이미지 생성/수정)
      ↓
arttoy-ic-review  (검수)
      ↓
 ┌────┴─────┐
통과       수정 필요
 ↓           ↓
최종 에셋   arttoy-ic-prompt (프롬프트 보완) → arttoy-ic-maker (재생성)
```

## 스타일 변경 시

`shared/`의 파일만 수정합니다. SKILL.md에는 스타일 내용이 없습니다.

## 사용 전 준비

- 포인트 컬러가 필요하면 `shared/brand-style-guide.md`의 `[프로젝트별 지정]`, `[ACCENT HEX]`를 프로젝트에 맞게 지정하세요. 지정하지 않으면 포인트 컬러를 임의로 정하지 않습니다.
- 기준 이미지를 `shared/reference-images/`에 추가하고, 같은 폴더의 `README.md` 표를 함께 갱신하세요. 기준 이미지가 없으면 텍스트 가이드만으로 진행하고, 이미지 비교가 필요한 검수 항목은 판단 불가로 처리합니다.

## 설치 (Claude Code)

Claude Code는 `.claude/skills/<스킬명>/SKILL.md` **한 단계 깊이**에서 스킬을 인식하며, frontmatter(`name`, `description`)가 필요합니다. 이 폴더 아래에 중첩된 스킬은 그대로는 자동 인식되지 않으므로 아래 중 하나로 등록합니다.

- **심볼릭 링크(권장)**: 폴더 구조는 유지한 채 각 스킬만 연결합니다. 아래 경로는 이 저장소를 프로젝트 루트 옆에 `arttoy-icon-studio/`라는 이름으로 둔 경우의 예시이며, 실제 폴더 이름에 맞게 바꿔 사용하세요.
  ```bash
  mkdir -p .claude/skills
  for s in arttoy-ic-prompt arttoy-ic-maker arttoy-ic-review; do
    ln -s ../../arttoy-icon-studio/$s .claude/skills/$s
  done
  ```
  링크해도 `../shared/` 상대경로가 실제 위치 기준으로 해석되는지 첫 실행 때 확인하세요. 안 되면 복사 배치를 사용하세요.
- **복사 배치**: `.claude/skills/`에 `arttoy-ic-*` 3개와 `shared/`를 같은 레벨로 복사합니다. 상대경로(`../shared/`)가 그대로 유효합니다.
- **플러그인 패키징**: 팀 배포가 필요하면 `skills/` 아래에 같은 구성으로 넣어 플러그인으로 묶습니다.

## 설계 메모

### 공통 참조 파일 접근
스킬은 SKILL.md 지시에 따라 `../shared/*.md`를 읽습니다. 자동 주입이 아니므로 각 SKILL.md의 "참조 파일" 항목에 "시작 전에 읽는다"를 명시했습니다. 이 방식은 모델이 지시를 따르는 데 의존하므로, 결과물이 기준에서 벗어나면 참조 단계가 누락됐는지 먼저 확인하세요.

### 이미지 생성 도구 연동
스킬 자체는 이미지를 생성하지 못하고, 연결된 이미지 생성 도구(MCP 등)를 호출합니다. 도구가 없으면 `arttoy-ic-maker`는 완료를 주장하지 않고 필요한 조건을 안내합니다. 사용하는 도구가 투명 배경, 1024×1024 출력을 지원하는지는 첫 실행 때 확인하세요.

### CLAUDE.md를 둔 이유
세 스킬에 공통인 운영 원칙(임의 생성 금지, 정직한 완료 보고, 연계 시 전달 정보)을 SKILL.md에 중복하지 않고 한곳에 두기 위해서입니다.

### reference-images/README.md를 둔 이유
이미지만으로는 "무엇의 기준인지" 알 수 없어, 이미지별 설명표를 두었습니다.

## 라이선스

© 2026 이수용

이 저작물은 [크리에이티브 커먼즈 저작자표시-비영리 4.0 국제 라이선스(CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/)에 따라 이용할 수 있습니다.

- 출처(저작자 이수용, 라이선스, 원본 링크)를 표시하면 자유롭게 공유·수정할 수 있습니다. 수정한 경우 수정했다는 사실을 밝혀 주세요.
- 상업적 목적으로는 이용할 수 없습니다.

전문은 `LICENSE.txt`를 참고하세요.
