# chi_md — AI 코딩 에이전트 작업 하네스 (Codex · Claude Code 공용)

AI 에이전트와 일할 때 **문제 정의 → 단계별 구현·검증 → 단계마다 자동 커밋 → 인수인계** 흐름을 강제하는 마크다운 지침 + Agent Skills 모음.
코드는 없고 `.md` 파일만 있다.

## 구성
| 파일 | 용도 |
|---|---|
| `AGENTS.md` | 에이전트 공통 작업 지침 (Codex가 자동으로 읽음) |
| `CLAUDE.md` | Claude Code용 추가 규칙 (AGENTS.md를 따르게 함) |
| `GIT.md` | **git 관리 하네스**: 언제 커밋하나, 메시지 형식, 커밋 금지 대상, 이력 보호 |
| `PROBLEM.md` | 문제 정의서: 배경, 한 문장 문제, 숫자 성공 기준, 범위, 제약, 가정 |
| `DECISIONS.md` | 결정 로그: 결정 / 근거 / 버린 대안, AI 오류 수정 기록 |
| `VERIFY.md` | 검증 기록 + 알려진 한계·위험 |
| `HANDOFF.md` | 인수인계 문서 틀 |
| `COMPONENTS.md` | **구성과 책임**: 구성별 책임 한 문장, 입력→출력, 따로 둔 이유, 깨지면, 구조 결정자 |
| `CHALLENGES.md` | **문제 인식 → AI 지시(원문) → 검증 → 극복** 기록 |
| `START.md` | 시작 10분 루틴과 스킬 사용 시점 |
| `TOOLS.md` | 자주 쓰는 bash 명령 + 사내 툴 만드는 방식(CLI / HTML 보고서 / 단일 HTML / 로컬 서버) 비교 |
| `skills/*/SKILL.md` | Agent Skills 9개 (아래) |

## 스킬
| 스킬 | 언제 | 하는 일 |
|---|---|---|
| `kickoff` | 시작 직후 1회 | 저장소·과제 파악, 문서 틀과 .gitignore 생성, 데이터 점검. 코드 작성 안 함 |
| `data-check` | 데이터를 받았을 때 | 기간 이탈, 그룹 편중, 특수값, 단위 혼재, 중복 → 담당자에게 물을 질문으로 정리 |
| `problem-brief` | 인터뷰 중간·끝 | 인터뷰 내용으로 PROBLEM.md 작성, 빈칸을 질문 목록으로 |
| `step-verify` | 기능 하나 끝날 때마다 | 직접 실행, 경계·실패 케이스 테스트, 성공 기준 대조, 기록 |
| `scope-check` | 큰 구현 직후 | 요구에 연결 안 되는 과한 설계를 찾아 축소 제안 (승인 후 제거) |
| `challenge-log` | **문제 발견·수정·AI 결과 거절 때 자동** | 문제 인식 근거, AI 지시 원문, 사람의 판단, 검증 수치를 CHALLENGES.md에 기록 |
| `explain-prep` | 약 120분, 마감 30분 전 | 구성·책임, 챌린지, AI 역할 분담을 AI 없이 말할 1인칭 답변 스크립트로 정리 |
| `git-commit` | **단계마다 자동** | 변경 범위·비밀값 점검 → staged diff를 요약해 GIT.md 형식으로 커밋 |
| `wrap-handoff` | 마감 30분 전 | 문서-코드 대조, HANDOFF 작성, 깨질 지점 5개, 설계 설명 정리 |

호출: Codex는 `$kickoff`, Claude Code는 `/kickoff`. `git-commit`은 AGENTS.md 규칙에 따라 에이전트가 단계마다 알아서 실행한다.

## 설치 (작업할 저장소 루트에서, bash)
```bash
git clone --depth 1 https://github.com/choichi5/chi_md.git /tmp/chi_md
mkdir -p .agents/skills .claude/skills
cp -r /tmp/chi_md/skills/* .agents/skills/
cp -r /tmp/chi_md/skills/* .claude/skills/
for f in AGENTS.md CLAUDE.md GIT.md PROBLEM.md DECISIONS.md VERIFY.md HANDOFF.md COMPONENTS.md CHALLENGES.md; do [ -e "$f" ] || cp "/tmp/chi_md/$f" .; done
ls .agents/skills
```
- Codex: 저장소의 `.agents/skills/`(또는 사용자 전역 `~/.agents/skills/`)를 읽는다.
- Claude Code: `.claude/skills/`를 읽는다.
- 이미 `AGENTS.md`가 있으면 덮어쓰지 않는다. 그때는 `/tmp/chi_md/AGENTS.md`의 "git 관리" 절만 기존 파일 끝에 붙인다.

## 커밋 메시지 예 (GIT.md 형식)
```
feat: 5분 간격 반복 알람 묶기 — 성공기준 #1

- 변경: group_alarms() 추가, 같은 장비·코드가 5분 안에 반복되면 1건으로
- 검증: pytest -q → 12 passed / 실제 데이터 1,750행 → 245묶음
- 결정: DECISIONS 11:40 (고정 윈도우 대신 gap 5분 세션 윈도우)
```

## 원칙
- 성공 기준(숫자)에 없는 기능은 만들지 않는다. 과한 설계는 깎는다.
- 계산·판정은 결정론적 코드로, LLM은 꼭 필요한 곳에만.
- "완료"는 실행한 명령과 출력으로 증명한다.
- git 이력은 고쳐 쓰지 않는다. 과정 자체가 기록이다.
