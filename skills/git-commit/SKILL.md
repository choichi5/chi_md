---
name: git-commit
description: 작업 단계(세팅, 문제 정의, 기능 구현, 버그 수정, 범위 축소, 문서 정리)가 하나 끝날 때마다 사용자 지시 없이 실행한다. 변경 범위와 비밀값을 점검하고, staged diff를 읽어 GIT.md 형식(type, 무엇, 왜, 검증 결과)으로 커밋 메시지를 요약해 커밋한다.
---

# git-commit

목적: 단계마다 "무엇을, 왜, 어떻게 검증했는지"가 git log에 남게 한다. 규칙 전체는 저장소 루트의 `GIT.md`에 있다.

## 절차
1. 저장소가 아니면 `git init`. `.gitignore`가 없으면 GIT.md의 기본 목록으로 만든다.
2. `git status --short`로 변경 파일을 본다. 이번 단계와 무관한 파일은 이번 커밋에서 뺀다.
3. 스테이징: 관련 파일만 `git add <파일>`. 그다음 `git diff --staged --stat`와 `git diff --staged`를 읽는다.
4. **비밀값 점검**: staged diff에 `api_key`, `token`, `password`, `secret`, `sk-`, `.env`가 있으면 커밋을 멈추고 사용자에게 알린다.
5. 이번 단계에서 실행한 테스트/명령과 결과를 확인한다. 실행하지 않았으면 지금 실행한다(`pytest -q` 등). 실패하면 커밋하지 않고 보고한다.
6. 메시지를 쓴다:
   ```
   <type>: <무엇, 50자 이내> — <왜 / 성공기준 #n>

   - 변경: ...
   - 검증: <명령> → <결과>
   - 결정: <DECISIONS.md 항목, 있으면>
   ```
   - type: `chore | docs | feat | fix | test | refactor | wip`
   - diff에 없는 내용은 쓰지 않는다. 실행하지 않은 검증은 `검증: 미실행 (이유)`.
7. 메시지를 `.git/COMMIT_MSG_DRAFT`에 저장하고 파일로 커밋한다(셸 따옴표·줄바꿈 문제 방지):
   ```bash
   git commit -F .git/COMMIT_MSG_DRAFT
   ```
8. `git log --oneline -3`을 보여 준다.

## 금지
- `--amend`, `rebase`, `push --force`, `reset --hard`, `--no-verify`
- 사용자 승인 없는 `git push`

## 보고 (3줄)
- 커밋 해시와 제목
- 포함한 파일 수, 뺀 파일(있으면)
- 검증 결과 한 줄
