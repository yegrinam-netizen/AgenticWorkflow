---
round: 0
round_label: cross-cutting
file_kind: cross-cutting
axis: glossary
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "차수 무관 누적. 새 용어 등장 시 차수 번호와 함께 추가."
---

# Glossary — PRD 리서치 공통 용어집

> 이 파일은 차수에 종속되지 않는 공통 용어 사전이다. 새 차수 조사에서 용어가 추가될 때마다 이 파일에 누적한다. 항목 뒤의 `[RNN]`은 그 용어가 처음 등장한 차수.

## 프레임워크·메타

- **AgenticWorkflow 하네스** `[R01]` — Claude Code 기반 로컬 개발 자동화 시스템. 사용자 PC에서 실행되며 상용 제품(관계 코칭 앱)을 자동 구현한다.
- **Dual-Layer (L0 / L1)** `[R01]` — L0 = 하네스 사양, L1 = 하네스가 만드는 앱 사양. 동일 PRD에서 두 층을 혼동하지 않기 위한 라벨.
- **SOT (Single Source of Truth)** `[R01]` — `state.yaml` 단일 파일 상태. Orchestrator/Team Lead 단일 쓰기.
- **DNA 유전** `[R01]` — 자식 시스템이 부모 AgenticWorkflow의 헌법·구조·검증·안전·기억·비판·투명 게놈을 내장하는 원칙 (`soul.md §0`).
- **Green / Yellow / Red Zone** `[R01]` — 합의도 3단 분류. Green = 4 teammate 전원 동의, Yellow = 3 동의(조건부 트리거 명시), Red = 2 이하 동의(재검토 시점 명시).
- **파킹 로트** `[R01]` — 현재 브랜치/차수 범위 밖이나 후속 조사 필요한 발견 사항. 버리지 않고 기록.

## 가정 축 (1차)

- **Branch A — Privacy-First Local-Capable** `[R01]` — 앱이 유저 기기 로컬 중심 동작, 선택적 E2E 암호화 동기화. 프라이버시를 상업 해자로 삼는 가정.
- **Branch B — Cloud-Native Feature-Rich** `[R01]` — 서버 중심, E2E 암호화로 프라이버시 보완. AI 품질·크로스디바이스·코칭 연속성 우선.
- **Hybrid 3-tier** `[R01]` — 4 teammate 전원 수렴한 권장 아키텍처. Tier 1 로컬 경량 모델(일상) / Tier 2 클라우드 Sonnet(갈등 해결) / Tier 3 클라우드 Opus + 인간(위기).

## 도메인·법무

- **self-help coaching** `[R01]` — 임상 상담·의료 행위 회피 경계의 포지셔닝. 한국 의료법 제27조·정신건강증진법 회피.
- **DPA (Diffuse Physiological Arousal)** `[R01]` — Gottman 연구 용어. 갈등 중 심박 100bpm 초과 시 전두엽 기능 저하 상태.
- **TTFV (Time to First Value)** `[R01]` — 설치 후 첫 유의미한 가치 경험까지의 시간. 페르소나 B 기준 60초 이내.
- **Crisis referral 프로토콜** `[R01]` — 자살·자해·폭력 감지 시 공공 핫라인(1393·1366·1577-0199·112)으로 자동 안내.
- **PIPA (Personal Information Protection Act)** `[R01]` — 한국 개인정보보호법. 제23조 민감정보(건강·성생활) 특별 규정.
- **Motivational Interviewing** `[R01]` — Miller &amp; Rollnick 심리상담 기법. 지시적 언어보다 반영적 언어로 변화 동기 유도.

## 페르소나

- **페르소나 A — 민지** `[R01]` — 29세 IT 기획자, 파워 유저, 자기계발 독자, 월 15만원 subscription budget. 프레임워크·데이터 요구.
- **페르소나 B — 준호** `[R01]` — 34세 제조업, 위기 상황 first-time, 자기계발 앱 경험 0. 즉시 답·가입 전 체험 요구. **1순위 지원 페르소나**.
- **페르소나 C — 수빈** `[R01]` — 41세 두 자녀, 장기 사용자, 시댁 갈등 누적. 장기 이력·크로스디바이스 요구.

## 경쟁 앱·참고

- **Paired / Lasting / Relish / Replika / Wysa / Woebot / Character.ai / Earkick** `[R01]` — 글로벌 관계·정신건강 AI 앱들. 경쟁 지형 §3 참조.
- **마인드카페 / 트로스트 / 상담의마음 / 썸원 / 비트윈** `[R01]` — 국내 관련 서비스.
- **EXAONE 3.5 (LG) / Solar Pro (Upstage) / HyperCLOVA X (NAVER)** `[R01]` — 한국어 네이티브 LLM. 로컬·클라우드 후보.

## 규제 키워드

- **정신건강증진법 제3조** `[R01]` — 정신건강서비스 정의.
- **의료법 제27조** `[R01]` — 무면허 의료행위 금지.
- **개인정보보호법 제23조** `[R01]` — 민감정보.
- **정보통신망법 제70조** `[R01]` — 명예훼손.
- **통신비밀보호법 제3조** `[R01]` — 녹음 당사자 원칙.
- **청소년보호법 / 아동·청소년 성보호법** `[R01]`
- **GDPR Article 9 / Article 17 / Article 20** `[R01]` — EU 민감정보·삭제권·이동성.
- **FTC Health Breach Notification Rule** `[R01]` — 미국 건강 앱.
- **Apple App Store Guideline 1.4.1 / 5.1.1 / 5.1.2** `[R01]` — 의료·건강·프라이버시 심사.

## 미해결 축 참조

- **R1–R12** `[R01]` — 1차 조사가 다루지 않은 12개 축. 상세: `cross-cutting/unresolved-axes.md`.
