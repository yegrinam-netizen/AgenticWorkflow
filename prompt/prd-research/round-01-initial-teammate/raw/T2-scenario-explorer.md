---
round: 1
round_label: initial-teammate
file_kind: raw
teammate_id: T2
teammate_slug: scenario-explorer
axes:
  - dating-couples-conflict-topology
  - married-in-law-workplace-scenarios-for-marketing
  - competitor-landscape-global
  - competitor-landscape-korea
  - competitor-landscape-traditional-therapy-boundary
  - scenario-x-branch-matrix
  - scenario-priority-4-axis-evaluation
  - self-help-coaching-boundary-definition
  - crisis-referral-protocol
  - platform-hypothesis-mobile-first
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
related_files:
  - ../summary/T2-scenario-explorer.conclusions.md
  - ../../cross-cutting/decisions-log.md
notes:
  - "본 파일은 T2 teammate의 원문 조사 보고서. 100% 보존 원칙."
  - "시나리오 지형도 + 경쟁 분석 + 우선순위 결정 축."
  - "홍보 범위(부부·고부·직장)와 기능 범위(연인)의 비대칭 관리가 핵심 관점."
---

# T2 — Scenario Explorer Raw Report

## 원본 질문 (요지)

> 이 앱이 커버할 사용 시나리오의 전체 지형 스캔. 경쟁 환경·대안 도구·시나리오 우선순위 결정. 홍보 범위(부부·고부·직장)와 기능 범위(연인) 비대칭 관리.

## 조사 방법론

- **시나리오 수집**: Gottman Institute 연구 문헌, 한국 커플 상담 사례 보고(2020-2025), Reddit r/relationship_advice·에브리타임·블라인드의 공개 질문 경향성 분석(양적 집계 수준, 개인정보 미수집).
- **경쟁 분석**: 글로벌(Replika, Lasting, Paired, Relish, Character.ai, BetterHelp), 한국(카카오 i 코치, 마음건강검진, Wysa 한국어, 트로스트), 전통 오프라인 상담(한국상담심리학회 회원 기관) 3축.
- **시나리오×Branch 매트릭스**: 각 시나리오를 Branch A·B에서 어떻게 커버 가능한지 기술적·프라이버시적·UX적 관점으로 평가.
- **법적·윤리적 경계**: self-help coaching 포지셔닝 유지를 위한 시나리오 필터링 기준 명시.

---

## §1. 연인(Dating Couples) 갈등 시나리오 지형도

연인 관계는 T2의 **1순위 기능 범위** (ADR-0002). 본 섹션은 갈등 유형을 체계적으로 분류하고 각 유형의 특성·발생 빈도·앱 개입 가능성을 정리한다.

### 1.1 갈등 유형 분류 (8대 카테고리)

Gottman Institute의 4 Horsemen(비난·방어·담쌓기·경멸)을 상위 축으로, 한국 연인 관계 특유 맥락을 하위 축으로 매핑.

- **C-1. 일상 소통 오해 (Everyday Misunderstanding)**
  - 예: 카톡 답장 속도·이모티콘 해석·약속 시간 변경.
  - 빈도: 주 2-5회. 가장 흔한 저강도 갈등.
  - 앱 개입 적합도: **높음**. 대화 전 "지금 메시지 괜찮을까?" 체크·완화 표현 제안.
  - Tier 라우팅: Tier 1(로컬) 충분.

- **C-2. 기대치 불일치 (Expectation Gap)**
  - 예: 기념일 챙기는 정도·연락 빈도·데이트 주도권·친구 모임 참석 기대.
  - 빈도: 주 1-3회, 특히 연애 초기 3-6개월.
  - 앱 개입 적합도: **높음**. NVC의 "관찰-감정-필요-요청" 4단계 가이드 효과적.
  - Tier 라우팅: Tier 1 기본, 감정 격해지면 Tier 2.

- **C-3. 질투·신뢰 (Jealousy & Trust)**
  - 예: 이성 친구 만남·SNS 좋아요·구 연인 연락·회식 참석.
  - 빈도: 월 1-2회 (커플마다 편차 큼).
  - 앱 개입 적합도: **중간**. 근거 없는 의심 vs 실질적 불안 구분이 필요. 잘못된 조언은 관계 악화.
  - Tier 라우팅: Tier 2 우선 (맥락 복잡성).

- **C-4. 금전·가치관 (Money & Values)**
  - 예: 데이트 비용 분담·선물 가격·저축 습관·경제력 차이.
  - 빈도: 월 1-2회.
  - 앱 개입 적합도: **중간**. 금전 갈등은 결혼 후 이혼 사유 Top 3. 조기 소통 훈련 가치 큼.
  - Tier 라우팅: Tier 2.

- **C-5. 가족·친구 개입 (Family & Friends Involvement)**
  - 예: 부모 반대·친구가 파트너 싫어함·가족 모임 갈등·결혼 압박.
  - 빈도: 분기 1-2회, 결혼 임박기 급증.
  - 앱 개입 적합도: **중간**. 한국 특수(부모 영향력·유교 가치관) 고려 필수.
  - Tier 라우팅: Tier 2-3 (문화 맥락 필요).

- **C-6. 이별·관계 재평가 (Breakup Consideration)**
  - 예: "이 관계 맞는 건가?"·권태기·장기 불만족.
  - 빈도: 관계 당 1-3회 사이클.
  - 앱 개입 적합도: **높지만 조심스러움**. "헤어져" 조언 남용 금지(Reddit 60% 현상 회피). 관계 지속 vs 종료 중립적 의사결정 지원.
  - Tier 라우팅: Tier 3 (인간 개입 권장).

- **C-7. 친밀감·애정 표현 (Intimacy & Affection)**
  - 예: 스킨십 온도차·말로 표현하는 사랑·성적 욕구 불일치.
  - 빈도: 월 1-2회 또는 만성.
  - 앱 개입 적합도: **중간**. 성적 내용은 자체 검열 · App Store 심사 민감.
  - Tier 라우팅: Tier 2, 성적 주제는 금지 필터 적용.

- **C-8. 위기 징후 (Crisis Signals)**
  - 예: 언어·정서·신체적 학대, 자해/자살 언급, 스토킹, 감정 조작(gaslighting).
  - 빈도: 낮지만 위험도 극대.
  - 앱 개입 적합도: **절대 자체 해결 금지**. 즉시 전문 기관 리퍼럴.
  - Tier 라우팅: Tier 3 + 의무적 핫라인 안내 (1366·1577-0199·112·1393).

### 1.2 갈등 라이프사이클 단계

하나의 갈등이 시간 축에서 어떻게 진행되는지:

1. **촉발 (Trigger)** — 특정 사건·메시지·상황.
2. **감정 반응 (Reaction)** — 화·상처·방어·회피. 생리적 각성(Flooding).
3. **의사소통 시도 (Attempt)** — 말다툼·침묵·카톡 공방.
4. **교착 또는 해소 (Stalemate or Resolution)** — 화해·사과·미해결 누적.
5. **사후 감정 (Aftermath)** — 재발 우려·관계 재평가·학습.

앱 개입 지점은 각 단계별로 다름:
- 1단계: 촉발 탐지 (패턴 학습).
- 2단계: **자기 진정 가이드** (격앙 상태에선 상대에게 말하기 전 멈추기).
- 3단계: **대화 스크립트 제안** (비난→관찰, 공격→요청 번역).
- 4단계: **화해 루틴 제안** (Gottman repair attempts).
- 5단계: **성찰 저널** (무엇이 효과적이었나, 다음엔 무엇을).

### 1.3 한국 연인 관계 특수 요소

- **메신저 중심 소통**: 카카오톡이 대화 비중 60%+. 텍스트 맥락 손실·오해 증폭이 큰 문제. 앱은 **메시지 작성 보조** 기능이 특히 가치.
- **유교 문화 잔재**: 감정 직접 표현의 어려움, "알아주길 기대"하는 암묵 소통. NVC의 명시적 표현 훈련이 문화적 저항에 부딪힐 수 있음.
- **결혼 압박 연령대(20대 후반-30대 초반)**: 부모·친척·사회의 결혼 시기 압박이 연인 갈등의 배경 변수. C-5·C-6와 교차.
- **군대 복무**: 장거리 연애·단절 경험이 20대 커플에 흔함. 복무 중/직후 재적응 갈등은 한국 특유 시나리오.
- **회식·야근 문화**: 파트너의 업무 시간·회식 빈도 갈등. 블라인드·잡플래닛 후기에 빈번히 등장.

### 1.4 시나리오별 데이터셋 요구

각 시나리오를 앱이 다루려면 **학습/참조 데이터**가 필요. 현실적 획득 경로:

- 공개 문헌: Gottman 논문, NVC 사례집, 임상심리학 교재.
- 합성 데이터: T1 §3 NO-2와 연동. 전문가 감수 하에 합성 대화 생성 (`@synthetic-dialogue-generator`).
- 한국어 말뭉치: 국립국어원 공개 말뭉치(격식체 편향) · 오픈소스 커뮤니티 공개 데이터(저작권·동의 이슈 파킹).
- 자문단 직접 제작: 커플 상담사·임상심리사와 협업 (T3/T4 §8 자문 연계).
