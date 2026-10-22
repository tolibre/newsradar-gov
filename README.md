# 뉴스레이다 · 행정판

CBS노컷뉴스 김민재 기자(사회정책팀장)용 보고 준비 페이지.
원본 [tolibre/news-monitor](https://github.com/tolibre/news-monitor)(김광일 기자 출입처판)에서 설정만 바꿔 만든 판이다.
같은 방식(사회정책팀): 기후·문체판 [newsradar-climate-culture](https://github.com/tolibre/newsradar-climate-culture) · 복지·보건판 [newsradar-welfare](https://github.com/tolibre/newsradar-welfare) · 노동판 [newsradar-labor](https://github.com/tolibre/newsradar-labor) · 교육판 [newsradar-education](https://github.com/tolibre/newsradar-education).

- 페이지: https://tolibre.github.io/newsradar-gov/ (→ `/live/`)
- 섹션: 총리실 · 행안부 · 권익위 · 소방청 · 인사처 · 중수청 (+ 맨 끝 김민재 기자 본인 기사, 보고 양식에서 `<기처리>`)
- 보고: 업무일 09:00 · 14:00 (원본과 같은 구간·화면 전환 시각)

## 코드를 직접 고치지 말 것

`news_monitor.py`·`render_page.py`·`merge_db.py`·`retitle.py`는 **`tools/make_variant.py`가 원본에서 생성**한다.

```
git clone --depth 1 https://github.com/tolibre/news-monitor /tmp/nm
python tools/make_variant.py /tmp/nm .
```

생성기는 치환마다 원본 등장 횟수를 검사하고, 생성본을 import해 검색어·그룹·보관 기간 값을 확인한 뒤에만 파일을 쓴다. 함수는 바꾸지 않는다.
검색어·제목 판정어는 **`tools/social_team.py`의 김민재 줄 한 곳**에서 관리한다(이 판의 섹션과 다른 사회정책팀 판의 당직 묶음이 같은 목록을 쓴다).

## 원본과 다른 점

| | 원본 | 이 판 |
|---|---|---|
| 검색어 | 공정위·방미통위·과기정통부·우주항공청·CBS 등 | 국무조정실·국조실·총리실·'한성숙 총리' · 행정안전부·행안부·'윤호중 장관' · 국민권익위원회·권익위·'정일연 위원장' · 소방청 · 인사혁신처·인사처 · 중대범죄수사청·중수청 |
| 제목 판정 전용(검색 안 함) | — | 한성숙 · 윤호중 · 행안장관 · 정일연 · 최동석 — 사람 이름만 쓴 제목을 제 섹션으로. '최동석'은 동명 방송인(전 KBS 아나운서) 기사가 쏟아질 수 있어 검색하지 않는다(인사혁신처장 기사는 본문의 '인사혁신처'로 들어온다) |
| 오탐 예방 | — | '인사처리·인사처분·인사처우·인사처벌', '최동석 (전) 아나운서'·'박지윤…최동석' — `tools/social_team.py` |
| 클러스터링 | — | 네 사람 이름은 인명 키워드(원본 '나이영'과 같은 자리)로 출입처명 토큰에서 뺀다 |
| DB 보관 | 90일 | 45일(첫 실행 실측 뒤 조정) |
| 기관장 이름 씨앗 | — | 한성숙(총리실)·윤호중(행안부)·정일연(권익위)·최동석(인사처) |
| 당직(휴일 특근) | 경제정책팀 8곳 | **사회정책팀 묶음**(사회정책팀 다섯 명 — 양형욱·정다운·김동빈·박종환·김민재 — 출입처 합침, `tools/social_team.py`) 중 이 판 섹션이 아닌 것 — '휴일 특근' 스위치로 봄(0-67·0-70) |
| 본인 기사 섹션 | 김광일 기자 | 김민재 기자 |
| 브라우저 저장키 | `nm-live-*` | `nm-gov-*` |
| 기기 연동·텔레그램·digest 페이지·수확 | 있음 | 없음(다른 변형판과 같음) |

## 운영 (cron-job.org → workflow_dispatch)

URL `https://api.github.com/repos/tolibre/newsradar-gov/actions/workflows/newsradar-gov.yml/dispatches`, POST,
헤더는 원본 잡과 같음(PAT `news-monitor-cron`에 이 저장소 추가 필요), 시간대 Asia/Seoul.

| 잡 | 스케줄 | body |
|---|---|---|
| gov-check | `0 * * * *` | `{"ref":"main","inputs":{"mode":"check"}}` |
| gov-precut | `25 8,13 * * 1-5` | 위와 같음 |
| gov-digest | `0 6 * * *` | `{"ref":"main","inputs":{"mode":"digest"}}` |
| gov-retitle | `15 */2 * * *` | `{"ref":"main","inputs":{"mode":"retitle","apply":"1","limit":"100"}}` |

Secrets: `NAVER_CLIENT_ID`, `NAVER_CLIENT_SECRET`(원본과 같은 값). Pages: main / docs.
