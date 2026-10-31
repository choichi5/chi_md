# TOOLS.md — 자주 쓰는 bash + 사내 툴 만드는 방식

## 1. 자주 쓰는 bash

**위치·파일 보기**
```bash
pwd                 # 현재 폴더
ls -la              # 파일 목록 (숨김 포함)
cd 폴더 / cd ..      # 이동 / 상위로
cat 파일            # 전체 출력
head -5 파일        # 앞 5줄
tail -5 파일        # 뒤 5줄
```

**데이터 빠르게 훑기 (CSV)**
```bash
wc -l data.csv                             # 행 수
head -3 data/*.csv                         # 모든 CSV 앞 3줄
cut -d, -f3 data.csv | sort | uniq -c      # 3번째 열 값별 개수
grep -c "INTERLOCK" data.csv               # 특정 단어가 든 행 수
grep -n -- "-999" data.csv | head          # 특수값 위치
```

**파일·폴더**
```bash
mkdir -p src tests     # 폴더 만들기
cp a b / mv a b        # 복사 / 이동·이름 변경
rm 파일                 # 삭제 (원본 데이터 금지)
```

**실행·테스트**
```bash
python main.py data/x.csv     # 스크립트 실행
pip install -r requirements.txt
pytest -q                     # 전체 테스트
pytest -k 이름 -q             # 특정 테스트만
```

**git**
```bash
git status
git diff                      # 커밋 전 변경 확인
git add 파일 && git commit -m "feat: 무엇 — 왜"
git log --oneline | head
```

**서버·프로세스**
```bash
python -m http.server 8000    # 현재 폴더를 웹으로 띄우기
Ctrl+C                        # 실행 중지
명령 &                         # 백그라운드 실행
```

## 2. 사내 툴 만드는 방식

### 먼저 정할 것 (현업 담당자에게 물어본다)
1. **누가 쓰나**: 본인 혼자 / 현장 여러 명 / 보고받는 사람
2. **어떻게 쓰나**: 하루 1회 파일 넣기 / 화면을 계속 봄
3. **제약**: 데이터 외부 반출·외부 LLM 금지 여부, 설치 가능 여부, 실행 환경

### 방식 비교
| 방식 | 언제 | 장점 | 단점 |
|---|---|---|---|
| ① Python 스크립트 (CLI) | 파일 넣으면 결과 파일이 나오면 됨 | 가장 단순, 테스트 쉬움 | 화면 없음 |
| ② **Python → HTML 보고서** ⭐ | 결과를 보고·공유해야 함 (주간 보고, 요약 화면) | 계산은 Python(검증 쉬움), 결과는 브라우저. 서버 불필요 | 상호작용(필터) 제한 |
| ③ HTML 단일 파일 (JS) | 사용자가 CSV를 올리고 바로 화면에서 거르거나 정렬 | 설치 없이 더블클릭, 데이터가 밖으로 안 나감 | JS 로직은 테스트·오류 추적이 어려움 |
| ④ 로컬 서버 (Flask / FastAPI / Streamlit) | 여러 명이 같은 화면, 입력 폼, 저장, 실시간 갱신 | 상호작용 자유 | 무거움. 요구 없으면 오버엔지니어링 |

### 기본 추천: ② 결정론적 Python 코어 + HTML 출력
```
data/raw/*.csv → core.py (정제·계산·판정, pytest) → report.py → output/report.html
```
- 계산은 Python 함수 + 테스트로 검증한다. 화면은 결과만 보여 준다(화면에서 계산하지 않음).
- 상호작용이 필요해지면 같은 core.py를 ③이나 ④에 붙여 확장한다.

### AI 지시 예
- ① "`main.py 입력.csv` → `output/result.csv`. pandas만 쓰고 핵심 로직은 `core.py` 함수로 분리, 테스트 포함."
- ② "core.py 결과를 pandas `to_html` 또는 Jinja2로 `output/report.html` 한 파일에. 외부 CDN 없이 CSS 인라인."
- ③ "외부 라이브러리 없는 단일 HTML. 파일 업로드 → 표 출력. 로직 함수는 분리해서 node로 테스트 가능하게."
- ④ "Streamlit 한 파일. 계산은 core.py를 import, 앱 파일엔 화면만." → `streamlit run app.py`

### 공통 규칙
- 원본은 `data/raw` 그대로, 결과는 `output/`.
- 숫자는 코드가 계산한다. LLM은 요약 문장에만, 외부 LLM에는 집계값만 넘긴다.
- 실행 명령 한 줄을 README와 HANDOFF.md에 적는다.
- 서버·DB·로그인은 요구가 있을 때만. 뺀 이유("하루 N행, 사용자 N명이라 불필요")를 DECISIONS.md에 남긴다.

### 설계를 설명하는 문장
> "사용자가 1명이고 하루 1회 파일 단위로 처리하는 일이라, 서버 대신 Python 코어 + 정적 HTML 보고서로 했습니다. 계산을 Python에 둬서 테스트로 검증할 수 있고, 화면은 결과만 표시합니다. 한계는 화면에서 필터링을 못 한다는 점이고, 필요해지면 같은 코어를 Streamlit에 붙이면 됩니다."
