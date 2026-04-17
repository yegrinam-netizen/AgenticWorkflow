---
round: 1
round_label: initial-teammate
file_kind: summary
teammate_id: T4
teammate_slug: sustainability-strategist
source_raw: ../raw/T4-sustainability-strategist.md
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "T4 raw 파일의 결론 섹션 발췌본. 판단 근거·데이터는 raw 참조."
  - "Orchestrator·종합 단계에서 빠른 참조용."
---

# T4 — Sustainability Strategist 결론 요약

> **원문**: [../raw/T4-sustainability-strategist.md](../raw/T4-sustainability-strategist.md)
> 아래는 결론·권고·의사결정 근거 핵심만 추린 요약. **증거·데이터·대안 평가는 raw 참조**.

---

## C1. 하네스 토큰 경제

- **현 구독 선택**: **Claude Max Plan $200/월**이 본 프로젝트 규모 최소선.
- **일일 capacity**: feature 구현 규모 기준 **4-8회 워크플로우**.
- **절감 전략**: Prompt caching (90% 할인) · Haiku routing · Sub-agent 요약 · Batch API (50%) · Tool 결과 범위 제한.
- **예산 붕괴 방어**: `validate_retry_budget.py` RB1-RB3 + NO-4 병렬 상한 + 캐시 모니터링.
- **장기 경제성**: 1인 연 $2,400 → feature당 $25-50.

## C2. 하네스 유지보수 부담

- **월 유지보수**: 1인 **10-30시간** (전업의 5-15%).
- **핵심 부담**: Claude Code 버전 업데이트 대응 (월 1-2회, 2-6시간/회).
- **디버깅 패턴 5종**: Hook silent failure · validate 규칙 충돌 · 컨텍스트 복원 실패 · 병렬 Task 병합 오류 · work_log drift.
- **권장**: `setup_maintenance.py` 주 1회 + `/maintenance` 월 1회 + 분기 1회 감사.

## C3. 제품 LLM 비용 (ADR-0005 3-tier Hybrid 기준)

- **Tier 1 로컬 (60%)**: EXAONE 3.5 7.8B · 비용 0원.
- **Tier 2 클라우드 (35%)**: Sonnet 4.6 · 세션당 30-80원.
- **Tier 3 Opus+인간 (5%)**: 세션당 400원 + 전문가 인건비.
- **MAU 10K 월 비용**: 3-tier **30-50만원** vs Pure Branch B **300-800만원** (**90%+ 절감**).
- **Freemium 지속 가능성**: Hybrid 필수.

## C4. 과금 모델

- **Phase 1 (0-6개월)**: **Pure Freemium Subscription** (월 9,900 / 연 79,000 / 프리미엄 24,900원).
- **Phase 2 (6-12개월)**: 토큰팩 옵션 확장 (전문가 매칭 30,000원, 커플 워크숍 150,000원).
- **Phase 3 (12-24개월)**: B2B EAP 병행.
- **Usage-based B2C 영구 금지**: Replika 2023 선례 재확인.

## C5. 경쟁사 가격 벤치마크

- **한국 중앙값 정렬**: 월 9,900원은 트로스트·Lasting·Paired와 정렬.
- **프리미엄 24,900원 정당화**: 전문가 1:1 채팅 월 2회 + 4 Horsemen 진단 + 커플 동기화 (AI 단독 정당화 어려움).
- **해외 확장 시 가격**: $9.99-11.99 (Lasting·Paired 수준).

## C6. 한국 규제 핵심 리스크

- **PIPA 제28조의9 국외이전**: Branch B·Hybrid Tier 2+3의 핵심 법적 장벽 → **명시적 별도 동의 + DPIA 필수**.
- **PIPA 제23조 민감정보**: 관계 대화의 민감정보 해당 여부 회색 영역 → **유권해석 신청** 필요.
- **의료법 제27조**: 진단 언어 절대 금지, self-help 경계 엄수.
- **심리서비스법(안)**: 2026 시행 예정 → 용어 (상담 → 코칭/wellness) 정비.

## C7. 글로벌 규제 핵심 리스크

- **GDPR**: Branch B·EU 확장 시 SCCs·DPIA·72시간 침해 통지.
- **EU AI Act 2026**: "제한적 위험 AI" 투명성 의무 (AI임을 명시).
- **App Store 2024 강화**: Character.ai 소송 이후 AI 심리 앱 심사 강화. 자해·자살 handling 프로토콜 필수.
- **COPPA**: 14세 제한 설계로 회피.

## C8. 데이터 거버넌스

- **4분류**: Class 1 (로컬 전용) · Class 2 (동의 기반 클라우드) · Class 3 (공개) · Class 4 (운영).
- **7원칙 P-D1~7**: 최소화·목적 제한·저장 기간·투명성·유저 통제·보안·책임.
- **차별화 자산**: "데이터 흐름 시각화" (P-D4) — BetterHelp 대비 신뢰 해자.
- **학습 데이터**: default opt-out · 합성 데이터 우선.

## C9. 확장 한계

- **B2C→B2B 전환**: SSO·감사 로그·멀티 테넌트·SOC 2 인증 필요. **Phase 3 (24+개월)** 권장.
- **다언어 우선순위**: 일본어 ★★★★★ > 영어(US)·중문 번체 ★★★★☆ > 영어(EU) ★★★☆☆.
- **Branch A pure는 B2B 약함** → Hybrid 구조가 양립 가능.

## C10. 장기 유지보수 1-3년

- **1년차**: 제품 안정화. 예산 1.5-3억.
- **2년차**: 스케일 + 차별화. 예산 7-12억.
- **3년차**: 성숙 + 플랫폼화. 예산 25-40억.
- **손익분기**: 3년차, MAU 30-50만 + 유료 전환 5-6% 필요.
- **장기 리스크 5종**: LLM 공급자 lock-in · 로컬 LLM 품질 정체 · 앱스토어 정책 · 결제 수수료 · 경쟁 모델.

## C11. 시나리오 6·12·24개월

- **6개월 Base Case**: MAU 1-2K · 유료 2% · 월 매출 20-40만원.
- **12개월 Base Case**: MAU 10-20K · 유료 3-4% · 월 매출 3-7천만원 (자력 지속).
- **24개월 Base Case**: MAU 50-100K · 유료 5% · 월 매출 2-4억 · 손익분기 근접.
- **하네스는 모든 시나리오에서 안전 자산**: 월 $200 Max Plan 고정.

## C12. 지속 가능성 종합 판정

- **하네스**: Max Plan 안정, 월 10-30시간 유지보수로 2-3년 지속 가능.
- **제품**: 3-tier Hybrid + Freemium + 한국 B2C 집중이 가장 현실적.
- **규제**: PIPA 제28조의9 + 의료법 + 심리서비스법이 핵심 변수.
- **확장**: 일본 Phase 2 · 영어권·B2B Phase 3.
- **재무**: 3년차 손익분기 가능, 2년차 Series A 고려.

---

## 상호 참조

- **파킹 로트**: [raw §12 P-T4-1~12](../raw/T4-sustainability-strategist.md#§12) 또는 [../parking-lot/round-01.parking-lot.md](../parking-lot/round-01.parking-lot.md).
- **후속 질문**: [raw §13 Q1-Q6](../raw/T4-sustainability-strategist.md#§13) 또는 [../follow-up-questions/round-01.follow-up.md](../follow-up-questions/round-01.follow-up.md).
- **관련 결정**: ADR-0005 (3-tier Hybrid).
- **기각 선택지**: D-1 (Pure Branch A), D-2 (Pure Branch B), D-4 (Usage-based).
