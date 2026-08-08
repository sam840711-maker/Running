# SH Running Coach — Phase 1 MVP

비행 로스터 기반 개인 러닝·영양 코치 PWA. 종합본(마스터 플랜)의 로직·데이터를 앱으로 옮긴 **Phase 1** 버전.

## 지금 동작하는 것 (P0 · MVP)
- **오늘 뷰** — 오늘 날짜(8월) 세션 + 근무 상태 + 고/저탄수 + 심박 존
- **주간 뷰** — 6개 주차 네비 · 세션·영양·걷기 옵션 카드
- **계산기** — 페이스↔러닝머신 km/h 변환, 거리(100/300/400/600m·1km)→시간 환산, 내 존·페이스표
- **설정(캘리브레이션)** — 키·체중·목표, 안정/최대/AT/AnT 심박, 페이스 존, 칼로리
  - **핵심:** 프로필을 바꾸면 세션 상세의 페이스·러닝머신 속도·시간·심박존이 **전 화면 자동 재계산**됨
- **로컬 저장**(localStorage) + **오프라인**(service worker) + **홈화면 설치**(manifest)

## 배포 (github.io)
1. 이 폴더를 리포에 넣고 push (`index.html`, `manifest.json`, `sw.js`)
2. Settings → Pages → 브랜치 지정 → `https://<id>.github.io/<repo>/`
3. 폰에서 열고 "홈 화면에 추가" → 앱처럼 실행 (오프라인 동작)

### 아이콘
`icon-192.png`, `icon-512.png`를 폴더에 추가하면 설치 아이콘이 적용됨(없어도 실행은 됨).

## 데이터 구조
- `DEFAULT` — 프로필 스키마 (localStorage 키: `shrc_profile`)
- `PLAN` — 8월 세션 데이터(정적). Phase 2에서 로스터 JSON → 규칙 엔진으로 자동 생성 예정
- `build()` / `easyRun` `treadRun` `longRun` `threshold` `interval` — 프로필 기반 세션 빌더(순수 함수)
- `zones()` / `kmh()` / `distTime()` — 재사용 계산 모듈

## 다음 단계
- **Phase 2** — `roster.json` 임포트 → `classify()`·`assignSession()`·`carbDay()` 규칙 엔진으로 PLAN 자동 생성 (로그북 export 스키마와 통일)
- **Phase 3** — 운동 로그 입력 → 존·페이스 자동 보정, 체중 추세·조정 제안
- **Phase 4** — 날씨 API(Cloudflare Worker 프록시) 연동, 워치 스크린샷 분석
- **Phase 5** — Google Drive 자동 백업(기존 로직 재사용), SH Pilot Logbook 통합

> 코칭 로직은 실측 기반 참고용이며 의료 자문이 아님.
