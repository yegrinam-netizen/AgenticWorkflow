---
round: 1
round_label: initial-teammate
file_kind: follow-up
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "T1~T4 각 teammate raw 후속 심층조사 항목 통합."
  - "2차 이상 차수에서 반드시 파고들어야 할 질문들."
---

# Round 01 — Follow-up Questions (통합)

> 1차 조사에서 발견한 **다음 차수 심층조사가 필요한 질문**.
> 본 1차 범위를 초과하는 것들이며, 2차 `round-02-*` 에서 teammate·방법론을 재설계하여 탐구.

---

## T1 — Workflow Architect 후속 (Q1-Q6)

1. **Q1-T1. Claude Code 병렬 Task tool 상한의 실측 재검증**: 5-10 한도의 환경 의존성 (네트워크·rate limit·모델 로드).
2. **Q2-T1. workflow.md DSL 표준화**: 기능 단위 workflow.md 간 재사용 가능한 패턴 추출 후 DSL 또는 템플릿 언어화.
3. **Q3-T1. Agent Team 프로덕션 마이그레이션 전략**: 실험 플래그 해제 시 점진적 이전 경로.
4. **Q4-T1. Branch A iOS 빌드 파이프라인 상세 설계**: Expo EAS vs GitHub Actions macOS runner 비교 + 비용·시간 실측.
5. **Q5-T1. MCP 서버 통합 최적 조합**: 본 프로젝트에 필요한 MCP 서버 우선순위.
6. **Q6-T1. Hook 스크립트 TypeScript 전환 검토**: 현재 Python 21종의 TS 전환 ROI.

---

## T2 — Scenario Explorer 후속 (Q1-Q6)

1. **Q1-T2. 페르소나 B 세분화**: "위기 first-time"을 사건 유형별 (외도 의심·별거·이별 직전·장기 냉전) 하위 분류 후 기능 차별화.
2. **Q2-T2. 한국 갈등 시나리오 실측 데이터**: 포커스그룹 20-30명 심층 인터뷰 + 관계 앱 후기 분석 (App Store·구글 플레이).
3. **Q3-T2. 가정폭력·아동학대 스크리닝 알고리즘**: 초기 대화 자동 감지 + 법정 신고 의무 대응.
4. **Q4-T2. AI 동반자 의존성 건강 지표**: Character.ai 소송 선례 기반 의존성 자동 평가·개입.
5. **Q5-T2. 전통 상담·심리 상담사 통합 경로**: 치료사 partner 네트워크 구축 전략.
6. **Q6-T2. 고부·가족 갈등 하위 시나리오**: 명절·제사·호칭·경제 지원 등 세부 시나리오 50+ 구축.

---

## T3 — Operator Analyst 후속 (Q1-Q6)

1. **Q1-T3. 첫 대화 품질 A/B 테스트 설계**: 공감 첫 메시지의 다양한 버전 (짧은 vs 긴 / 질문형 vs 선언형) 전환률 비교.
2. **Q2-T3. 파트너 초대 전환 시나리오**: 거절 시 자기 비난 방지 UX·메시지 디자인.
3. **Q3-T3. Flooding 완화 UX 연구**: 심박수·호흡 연동 (Apple Watch·Galaxy Health) 실제 효과 측정.
4. **Q4-T3. 오프라인 프로그램 (4주 챌린지) 구조 설계**: Phase 2 프리미엄 핵심 기능의 콘텐츠·완주율.
5. **Q5-T3. 페르소나별 onboarding 분기**: 초기 설문 3개로 페르소나 자동 분류 + 맞춤 경로.
6. **Q6-T3. 접근성·저사양 전략**: WCAG 2.1 AA + Android Go 디바이스 지원 범위.

---

## T4 — Sustainability Strategist 후속 (Q1-Q6)

1. **Q1-T4. PIPA 제23조 "관계 갈등 대화" 민감정보 해당 여부 법적 유권해석**: 개인정보보호위원회 유권해석 신청 시나리오 + 이중 동의 UX + 법무 자문.
2. **Q2-T4. Anthropic Enterprise DPA 협상**: 규모 기준·KR 데이터 localization 옵션·협상 창구·leverage.
3. **Q3-T4. 로컬 LLM 관계 상담 특화 벤치마크**: 200-500 시나리오 × 5 모델 × 3 심사자 + 한국 심리 전문가 10인 평가단 구성.
4. **Q4-T4. B2B EAP 시장 진입 경제성**: 한국 상위 20개 기업 EAP 예산 실측 + Sales 사이클 + 경쟁사 조사.
5. **Q5-T4. SOC 2 Type II 인증 로드맵 상세**: 벤더·비용·타임라인 + ISMS-P 대체 가능성.
6. **Q6-T4. 장기 재무 민감도 한국 실측**: 경쟁사 유료 전환률·Churn·LTV/CAC 벤치마크.

---

## Cross-cutting 후속 (teammate 간 교차)

1. **QC-1. 페르소나 B × Branch 선택 UX 실측**: B 유저에게 Branch A vs B 선택 UI를 어떻게 제시할지 A/B 테스트.
2. **QC-2. 3-tier Hybrid 세션당 Tier 선택 로직**: 유저 대화 내용 자동 분류 → Tier 라우팅 알고리즘.
3. **QC-3. 위기 리퍼럴 false-positive·false-negative**: Level 분류 자동화 정확도 목표 + 측정 프로토콜.
4. **QC-4. 법적 단위 compliance 체크리스트**: PIPA·GDPR·App Store·정신건강법 통합 체크리스트 (PRD 첨부).
5. **QC-5. 데이터 흐름 시각화 UX 프로토타입**: P-D4 원칙을 UI로 구체화.
6. **QC-6. Branch 결정 매트릭스 수치화**: T1·T4가 각각 제시한 판단 기준 통합 후 가중치 기반 스코어링.

---

## 차수 2 권장 teammate 재설계

본 follow-up 질문들을 효율적으로 다루기 위해 **round-02-deep-dive** 차수에서:

- **T5 — Legal Counsel (신규)**: Q1-T4 (PIPA 유권해석), QC-4 (법적 체크리스트) 담당.
- **T6 — Data Scientist (신규)**: Q3-T4 (로컬 LLM 벤치마크), QC-2 (Tier 라우팅), QC-3 (위기 분류) 담당.
- **T7 — Growth PM (T3 진화)**: Q1-T3·Q2-T3·Q5-T3·QC-1 담당.
- **T8 — Clinical Advisor (T2 진화)**: Q3-T2·Q4-T2·Q6-T2 담당.
- **T1·T4는 유지·심화**: Q1-T1~Q6-T1, Q4-T4·Q5-T4·Q6-T4 담당.

**규모**: 차수 2는 **4-8 teammate 설정 권장**. 1차 4명 구조를 확장.
