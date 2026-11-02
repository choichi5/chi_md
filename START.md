# 시작 10분 루틴 (과제 화면이 열리면 바로)

1. 루트 README와 과제 설명을 끝까지 읽는다(5분). 데이터 파일 목록, 제공 코드, 제출 방식, 시간을 확인한다.
2. 터미널에서 현재 상태부터 파악한다:
   ```bash
   ls -la && git status && git log --oneline | head
   head -5 data/*   # 데이터 형식 확인
   ```
3. 하네스를 설치한다 (README의 "설치" 명령 복사 → 붙여넣기). 그다음 `$kickoff`(Codex) 또는 `/kickoff`(Claude Code)를 호출한다.
   설치가 안 되면 AI에게 "아래 내용으로 AGENTS.md를 만들어라"라고 지시해 직접 만든다:
   `AGENTS.md`, `GIT.md`, `PROBLEM.md`, `DECISIONS.md`, `VERIFY.md`
   → `chore: 작업 지침·문서 틀 세팅` 커밋 (이후 커밋은 AI가 `git-commit` 절차로 단계마다 알아서 한다)

## 스킬 사용 시점
| 언제 | 스킬 |
|---|---|
| 시작 직후 | `kickoff` (안에서 `data-check`까지 실행) |
| 인터뷰 중간, 끝 | `problem-brief` → 남은 질문을 실무자에게 |
| 기능 하나 끝날 때마다 | `step-verify` |
| 큰 구현 직후, 90분 무렵 | `scope-check` |
| 문제 발견·수정·AI 결과 거절 때 (AI가 자동으로) | `challenge-log` |
| 약 120분, 마감 30분 전 | `explain-prep` (wrap-handoff 안에서도 실행) |
| 단계가 끝날 때마다 (AI가 자동으로) | `git-commit` |
| 마감 30분 전 | `wrap-handoff` |
4. 실무자(현업 담당자) 인터뷰를 시작한다: 사소한 맥락 질문부터 → 최근 에피소드 → 현재 우회책 → 숫자로 된 성공 기준 → 예외·제약.
5. 합의가 끝나면 PROBLEM.md를 채우고 커밋한 뒤 첫 Codex 지시를 내린다:
   ```
   AGENTS.md와 PROBLEM.md를 읽고, 성공 기준을 만족하기 위한 구현 계획을 단계별로 제안하라.
   각 단계마다 완료 조건(실행할 테스트/명령)을 붙여라. 아직 코드는 쓰지 마라.
   ```
   → 계획을 검토한 뒤 고칠 점을 지적하고 나서 1단계 실행.

## Codex 세션 관리
- 대화가 길어져 맥락이 흐려지면 HANDOFF.md를 갱신하고 새 세션에서 "AGENTS.md, PROBLEM.md, HANDOFF.md를 읽고 이어서 하라"고 지시한다.
- 시점을 미리 정해 둔다: **약 90분, 160분**.
