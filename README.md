# chi_md — AI 코딩 에이전트 작업 하네스 (Codex · Claude Code 공용)

AI 에이전트와 일할 때 **문제 정의 → 방법 비교 → 단계별 구현·검증 → 자동 기록·커밋 → 인수인계** 흐름을 따르게 하는 마크다운 지침 + Agent Skills 6개. 코드는 없고 `.md` 파일만 있다.

## ⚡ 설치 (작업할 저장소 루트에서, 두 줄)
```bash
git clone --depth 1 https://github.com/choichi5/chi_md.git /tmp/chi_md
C=/tmp/chi_md; mkdir -p .agents/skills .claude/skills && cp -r $C/3-skills/* .agents/skills/ && cp -r $C/3-skills/* .claude/skills/ && cp -n $C/1-rules/*.md $C/2-docs/*.md . && ls .agents/skills
```
그다음 `$kickoff`(Codex) 또는 `/kickoff`(Claude Code).

- 1-rules, 2-docs의 파일은 작업 저장소 **루트에 평평하게** 복사된다(문서끼리 루트 경로로 서로를 참조한다). `cp -n`이라 이미 있는 파일은 덮어쓰지 않는다.
- 이미 `AGENTS.md`가 있으면 `/tmp/chi_md/1-rules/AGENTS.md`의 "설명할 수 있게 기록하기"와 "git 관리" 절만 기존 파일 끝에 붙인다.
- clone이 안 되면: GitHub에서 **Code → Download ZIP** → 압축을 풀고 `C=<풀린 폴더 경로>`로 바꿔 두 번째 줄만 실행.
- Codex는 `.agents/skills/`(또는 전역 `~/.agents/skills/`), Claude Code는 `.claude/skills/`를 읽는다.

## 📁 폴더 구조 (번호 = 쓰는 순서)
```
chi_md/
├── 1-rules/    AI가 따르는 규칙          → 작업 저장소 루트에 복사
├── 2-docs/     작업하며 채우는 문서 틀    → 작업 저장소 루트에 복사
├── 3-skills/   Agent Skills 6개          → .agents/skills/, .claude/skills/에 복사
└── 4-guides/   사람이 읽는 참고 자료      (복사 안 함)
```

### 1-rules/ — AI 규칙
| 파일 | 용도 |
|---|---|
| `AGENTS.md` | 에이전트 공통 지침 (Codex가 자동으로 읽음): 범위, 방법 비교, 구조 결정, 기록, 커밋, 금지 |
| `CLAUDE.md` | Claude Code용 추가 규칙 (AGENTS.md를 따르게 함) |
| `GIT.md` | git 관리: 언제 커밋하나, 메시지 형식, 커밋 금지 대상, 이력 보호 |

### 2-docs/ — 작업 문서 틀 (채우는 순서)
| 파일 | 언제 | 용도 |
|---|---|---|
| `PROBLEM.md` | 인터뷰 후 | 배경, 한 문장 문제, 숫자 성공 기준, 범위, 제약, 가정 |
| `COMPONENTS.md` | 구조 정할 때·기능 추가 때 | 구성별 책임 한 문장, 입력→출력, 따로 둔 이유, 깨지면, 구조 결정자 |
| `DECISIONS.md` | 결정할 때마다 | 결정 / 근거 / 버린 대안, AI 오류 수정 |
| `VERIFY.md` | 검증할 때마다 | 검증 기록 + 알려진 한계·위험 |
| `CHALLENGES.md` | 문제 발견·수정 때 | 문제 인식 → AI 지시(원문) → 검증 → 극복 |
| `HANDOFF.md` | 마감 전 | 인수인계 |

### 3-skills/ — 스킬 6개 (쓰는 순서)
| 스킬 | 언제 | 하는 일 |
|---|---|---|
| `kickoff` | 시작 직후 1회 | 저장소 파악, 문서 틀·.gitignore 생성, **데이터 함정 점검** → 현업 질문 목록. 코드 작성 안 함 |
| `problem-brief` | 인터뷰 중간·끝 | 인터뷰 내용으로 PROBLEM.md 작성, 빈칸을 질문 목록으로 |
| `choice-compare` | 알고리즘·모델을 정할 때 | 대안 비교표 → 단순 기준선 먼저 → 같은 데이터로 수치 비교 → 규모 시험 → 기록. "왜 A 대신 B?"에 수치로 답하게 함 |
| `step-verify` | **기능 하나 끝날 때마다 (자동)** | 직접 실행·경계 테스트 → VERIFY/COMPONENTS 갱신 → **챌린지 기록** → **GIT.md 형식 커밋** |
| `checkpoint` | 큰 구현 직후, 90~120분 | 과한 설계 축소(승인 후) + 설명 준비 빈칸 점검 |
| `wrap-handoff` | 마감 30분 전 | 문서-코드 대조, HANDOFF, 깨질 지점 5개, **AI 없이 말할 1인칭 설명 스크립트** |

### 4-guides/ — 사람용 참고
| 파일 | 용도 |
|---|---|
| `START.md` | 시작 10분 루틴과 스킬 사용 시점 |
| `TOOLS.md` | 자주 쓰는 bash + 사내 툴 만드는 방식(CLI / HTML 보고서 / 단일 HTML / 로컬 서버) 비교 |

## 커밋 메시지 예 (GIT.md 형식)
```
feat: 5분 간격 반복 알람 묶기 — 성공기준 #1

- 변경: group_alarms() 추가, 같은 장비·코드가 5분 안에 반복되면 1건으로
- 검증: pytest -q → 12 passed / 실제 데이터 1,750행 → 245묶음
- 결정: DECISIONS 11:40 (고정 윈도우 대신 gap 5분 세션 윈도우)
```

## 원칙
- 성공 기준(숫자)에 없는 기능은 만들지 않는다. 과한 설계는 깎는다.
- 방법은 비교해서 고른다. 단순한 기준선이 충분하면 단순한 쪽을 쓴다.
- 계산·판정은 결정론적 코드로, LLM은 꼭 필요한 곳에만.
- "완료"는 실행한 명령과 출력으로 증명한다.
- git 이력은 고쳐 쓰지 않는다. 과정 자체가 기록이다.
