# 근무 스케줄 관리 (서점/마트) – Static Web App

팀장(엄마) 1명이 **웹에서 스케줄을 수정**하고, 팀원들은 **URL로 확인(보기 전용)** 또는 **PNG 이미지로 공유(카카오톡)** 할 수 있는 **단일 HTML 파일 기반** 스케줄러입니다. GitHub Pages에 올리면 바로 실행됩니다.

> 비밀번호(관리자): **1234**
> 
> 기본 시작 주차: **2026-01-19(월)**
> 
> 2026년 공휴일(대한민국) 표시: **빨간색**

---

## ✅ 현재 완료된 기능

### Google Sheets 스키마 싱크(중요)
- `weeks` 시트의 2번째 컬럼 헤더가 **startDate** 이어야 정상 동작합니다.
- 현재 운영 중인 시트가 `week1` 같은 이름을 쓰는 경우도 있어, 프론트(index.html)에서 **startDate 또는 week1 둘 다 허용**하도록 보완했습니다(호환 모드).
- 권장: 시트 헤더를 `id,startDate`로 통일(가장 안전).

### Google Sheets 동기화(공용 데이터)
- Google Apps Script Web App을 백엔드처럼 사용
- 팀원(보기 전용)은 주기적으로 서버에서 데이터를 다시 불러와 **항상 최신 스케줄 확인**
- 팀장(관리자, 비번 1234)만 저장(POST import) 가능

> Apps Script Web App URL은 `index.html` 상단 상수 `SHEETS_API_BASE`에 설정되어 있습니다.

### UI/가독성(라이트 테마)
- 어두운 테마 → **밝고 깔끔한 라이트 테마**로 변경
- 버튼/텍스트 대비 강화(40~50대 가독성)
- PNG 내보내기 배경도 흰색으로 통일

### 역할/권한
- 관리자/일반 사용자 구분
  - 관리자: 비밀번호 **1234** 입력 시 편집 가능
  - 팀원: 기본 **보기 전용**

### 주간 스케줄(전체)
- 주차(월요일 시작) 기준 스케줄 테이블
- 셀 클릭 → 팀원 다중 선택 후 저장 (관리자)
- 팀원 태그 **드래그 앤 드롭**으로 셀에 빠르게 배치 (관리자)
- 필터(장소명/팀원명)로 빠르게 찾기

### 개인별 / 장소별 / 월간 달력 뷰
- 개인별: 선택한 팀원의 주간 배정 목록 + 미니 주간표
- 장소별: 선택한 장소의 주간 배정 목록
- 월간 달력: 월 단위로 배정 요약 표시, 날짜 클릭 시 해당 주차로 이동

### 자동 경고 (연속 근무)
- 같은 팀원이 **같은 장소에 2일 연속**: 노란색(warn2)
- **3일 이상 연속**: 주황색(warn3)

### PNG 이미지 내보내기
- 현재 보고 있는 화면(전체/개인/장소/달력)을 **PNG로 다운로드**
- html2canvas 사용

### URL 공유
- 상단 **URL 복사** 버튼: 현재 뷰/선택 주차/선택 필터 상태를 쿼리스트링으로 반영해 공유

### 데이터 관리
- LocalStorage 자동 저장
- 백업: JSON 다운로드
- 복원: JSON 업로드
- 초기화: 전체 데이터 리셋

### 팀원/장소 관리 (인원 변동 반영)
- 팀원 추가(이름, 색상)
- 팀원 삭제
- 팀원 활성/비활성 전환
- 팀원 이름 변경, 색상 변경
- 장소 추가(분류: 서점/마트/기타)
- 장소 삭제/이름 변경/분류 변경

### 공휴일 표시(2026)
- 2026년 대한민국 공휴일을 내장
- 주간/개인별 주간표/월간 달력에서 **공휴일은 빨간색**으로 표시

### PWA(기본)
- `manifest.webmanifest` + `sw.js` 포함
- 모바일에서 “홈 화면에 추가”로 앱처럼 사용 가능

---

## 📱 모바일 최적화
- 기본 주간 화면은 모바일에서 **카드(주간) 뷰**가 기본입니다.
- 데스크톱에서는 표(주간) 뷰도 사용 가능합니다.

## 🔗 기능 진입 URI (정적 사이트)

### Google Sheets API(백엔드)
- `GET {SHEETS_API_BASE}?op=export`
  - returns: `{members, places, weeks, assignments}`
- `POST {SHEETS_API_BASE}?op=import`
  - body: `{members, places, weeks, assignments}`
  - notes: 관리자 모드에서만 호출(공용DB 저장)

> `SHEETS_API_BASE`는 `index.html` 내 상수로 설정되어 있습니다.

## 🔗 기능 진입 URI (정적 사이트)

- `/index.html`
  - Query params(공유용):
    - `view=week|weekCards|person|place|month`
    - `week=<weekId>`
    - `person=<memberId>`
    - `place=<placeId>`

> 주의: `weekId/personId/placeId`는 내부 UUID라 사람이 직접 입력하기보다는 **앱에서 URL 복사**로 공유하는 것을 권장합니다.

---

## ❗ 아직 구현하지 않은 것(제한/향후)

- **실시간 동기화(여러 명이 동시에 수정)**: 현재는 LocalStorage 기반이라 기기/브라우저가 다르면 데이터가 공유되지 않습니다.
- **서버 기반 로그인/권한/감사 로그**: 정적 사이트 한계로 미구현.
- **자동 알림(카카오/메일)**: 외부 서비스 연동(인증/키)이 필요하여 정적만으로는 제한.

---

## 🧭 추천 다음 단계

1. **팀장 1명만 수정**하는 운영이라면
   - 현재 버전 그대로 사용 + 주기적 JSON 백업을 권장
2. 팀원들도 같은 데이터를 같은 링크에서 보게 하려면(동기화 필요)
   - Google Sheets(공개 JSON) 또는 CORS 가능한 인증 없는 REST API를 “읽기 전용 데이터 소스”로 연결
   - 혹은 별도 백엔드/DB 필요(정적 범위 밖)
3. 공유 링크를 사람이 읽기 쉬운 형태로
   - `week=2025-08-25` 같은 파라미터를 추가로 지원하도록 개선 가능

---

## 📦 프로젝트 구조

```
index.html              # 단일 앱(모든 UI/JS/CSS 포함)
manifest.webmanifest    # PWA 매니페스트
sw.js                   # 오프라인 캐시용 서비스워커
README.md
```

---

## 🧩 데이터 모델(로컬 저장)

LocalStorage 키: `schedule_app_v1`

```json
{
  "version": 1,
  "members": [{"id":"...","name":"...","color":"#rrggbb","active":true}],
  "places": [{"id":"...","name":"...","category":"bookstore|mart|other"}],
  "weeks": [
    {
      "id":"...",
      "startDate":"YYYY-MM-DD",  // 월요일
      "assignments": {
        "placeId": {
          "YYYY-MM-DD": ["memberId", "memberId"]
        }
      },
      "warnings": {
        "placeId": {
          "YYYY-MM-DD": 2|3
        }
      }
    }
  ]
}
```

---

## 🧰 Google Sheets / Apps Script 설정(필수)

1) Google Sheets에 탭 4개 생성:
- `members` / `places` / `weeks` / `assignments`

2) Apps Script → Web App 배포
- 접근 권한: **모든 사용자(익명 포함)**
- URL(예): `https://script.google.com/macros/s/.../exec`

3) `index.html`에서 아래 상수를 본인 URL로 설정
- `SHEETS_API_BASE`

> 현재 프로젝트는 이미 아래 URL로 설정됨:
> `https://script.google.com/macros/s/AKfycbzeRHvFNESvR5JBZ6hdCZyhTOsfYjtki-9jFuGufDZr0P-o6dTxSecGoV_yCw1sqBTk/exec`

4) 팀장 운영
- 관리자(1234) 로그인 → 편집 → 자동 저장(구글시트 반영)

5) 팀원 운영
- 링크 접속 → 보기 전용 → 자동으로 최신 데이터 갱신

## 🚀 배포(중요)

이 프로젝트는 정적 사이트입니다. **Publish(또는 GitHub Pages)**로 올리면 바로 동작합니다.

- GitHub Pages 사용 시
  1. 이 레포를 GitHub에 푸시
  2. Settings → Pages → Branch 선택
  3. 발급된 URL로 접속

---

## 📌 초기 데이터

### 팀원(10명)
- 진영희, 임승주, 김두니, 지정하, 이미자
- 백수연, 임경원, 최형심, 안정희, 김민지

### 장소
- 영풍문고 인천터미널점
- 반디앤루니스 목동점
- 더북 안양엔터식스점
- 영풍문고 왕십리점
- 인천 스퀘어원 (10:30-19:00)
- 북앤북스 간석홈플러스점
- 영풍문고 부천포도몰
- 영풍문고 김포공항점
- 교보문고 영등포점
- 용산 영풍문고
- 이마트 (산본, 오산, 은평, 다산)
- 롯데마트 서울역점

