# SH Running Coach — Phase 3 (로그·체중·날씨)

비행 로스터 기반 개인 러닝·영양 코치 PWA. **로스터 JSON → 규칙 엔진으로 훈련·영양 플랜 자동 생성.**

## 동작 (P0 + P2)
- **오늘 / 주간 / 계산기 / 설정** 4개 탭 (하단 탭바)
- **규칙 엔진** — 로스터의 근무 유형(off·flight·layover·reserve)과 플래그(sectors·nightDep·redeye·early·coast·humid·heat·hint)로 세션·탄수·시간대·야외/실내·저녁/걷기 옵션을 **자동 도출**
- **프로필 캘리브레이션** — AT/AnT·페이스 바꾸면 러닝머신 속도·시간·존이 전 화면 자동 재계산
- **로스터 편집** — 설정 탭에서 JSON 붙여넣고 "적용 & 재생성" → 즉시 플랜 반영
- **계산기** — 페이스↔km/h, 거리(100/300/400/600m·1km)→시간
- 로컬 저장(localStorage) · 오프라인(service worker `shrc-v3`) · 홈 설치(manifest)


## Phase 3 · 기록 / 자동 보정 (신규)
- **기록 탭** — 체중 입력 → 추세 그래프·로드맵·주간 감량률·조정 제안(정체 시 −kcal / 과속 시 +kcal)
- **러닝 로그** — 날짜·종류·거리·시간·평균HR·최대HR·케이던스·kcal 입력·저장(localStorage `shrc_logs`)
- **자동 보정** — 최근 이지런 평균 HR을 AT와 비교 → "너무 세게" 경고 / "존2 잘 지킴", 관측 최대심박 > 설정이면 상향 제안
- **날씨 연동** — 설정 탭 "날씨 불러오기(수원)" → Open-Meteo(무키·CORS) 최고기온으로 로스터 `heat` 자동 채움 → 플랜 재생성 (오프라인이면 조용히 실패, 수동 heat 유지)
- **아이콘** — `icon-192.png`·`icon-512.png` 포함 (설치 아이콘 적용)

## 로스터 스키마
```json
{ "month":"2026-08", "days":[
  { "d":7, "kind":"off", "label":"OFF", "hint":"interval", "reps":4, "heat":"낮 37°C 폭염peak" },
  { "d":8, "kind":"flight", "label":"FLT ×3", "sectors":3, "layover":"부산" },
  { "d":10,"kind":"layover","label":"L/O 홍콩","humid":1,"heat":"홍콩 32°C" },
  { "d":18,"kind":"reserve","label":"RESV","hint":"easyrec" }
]}
```
- `kind`: off · flight · layover · reserve
- `hint`(off/reserve): interval · threshold · long · easy · easyrec · rest  (없으면 easy)
- 플래그: `sectors` · `nightDep`(야간 출발지) · `redeye` · `early`(다음날 이른 show-up) · `coast`(해안 레이오버) · `humid` · `heat` · `opt`(선택 세션) · `reps`(인터벌 반복수) · `reason`(휴식 사유)

## 규칙 요약 (engine)
- **세션**: off→hint / layover(야간출발)→해안이면 이지·아니면 트레드밀 / layover(종일)→트레드밀 / flight(야간출발)→오전 이지 / flight(레드아이·5섹터·3섹터)→휴식
- **탄수**: 퀄리티·롱런·3섹터·야간=고탄수 / 그 외=저탄수 / 레드아이=회복
- **저녁 옵션**: 다음날 early·redeye·5섹터면 퀄리티 저녁 금지
- **걷기 옵션**: 단순 왕복·레이오버行(레드아이·이른근무·5섹터·야간출발 제외)

## 배포 (github.io)
`index.html`·`manifest.json`·`sw.js` push → Settings→Pages→브랜치 → 폰에서 "홈 화면에 추가".
> 업데이트 시 `sw.js`의 `CACHE`(현재 `shrc-v2`)를 올려야 새 버전이 강제 적용됨.

## 다음 (Phase 3~)
- 운동 로그 입력 → 존·페이스 자동 보정, 체중 추세·조정 제안
- 날씨 API(Cloudflare Worker 프록시) → heat 자동 채움
- Google Drive 백업(기존 로직) · SH Pilot Logbook 로스터 공유

> 코칭 로직은 실측 기반 참고용이며 의료 자문이 아님.
