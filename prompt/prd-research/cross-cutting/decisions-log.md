---
round: 0
round_label: cross-cutting
file_kind: cross-cutting
axis: decisions-log
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "ADR 스타일 결정 로그. 차수에 종속되지 않고 누적."
  - "이번 턴 시드 엔트리 5건. 새 차수마다 추가."
---

# Decisions Log — 차수 간 누적 ADR

> 형식: ADR-NNNN. 결정의 **근거**가 어느 차수·어느 파일에서 나왔는지 역추적 가능해야 한다.

---

## ADR-0001 — 타겟 세그먼트 고정

- **상태**: Accepted
- **결정 시점**: 2026-04-17 (사용자 확정, round-01 진입 전)
- **결정 내용**: B2C 개인 소비자 우선, B2B(기업 HR·상담실) 후 확장 가능한 구조로 설계.
- **근거**: 사용자 직접 지시 (본 세션 대화 로그).
- **영향**:
  - Operator Analyst의 페르소나 설계 (B2C 3인) → `round-01-initial-teammate/raw/T3-operator-analyst.md §2`
  - Sustainability Strategist의 B2B EAP 확장 분석 → `round-01-initial-teammate/raw/T4-sustainability-strategist.md §8.1`
- **번복 조건**: B2B 선진출이 현저히 유리해지는 시장 신호 (직접 파트너십 제안 등).

---

## ADR-0002 — 1순위 관계 유형 고정

- **상태**: Accepted
- **결정 시점**: 2026-04-17 (사용자 확정)
- **결정 내용**: 연인(dating couples) 갈등을 **기능 1순위**로 하되, 홍보 메시지는 부부·고부·직장을 **동시에 노출**.
- **근거**: 사용자 직접 지시.
- **영향**:
  - Scenario Explorer의 우선순위 Top 3이 연인 시나리오로 수렴 → `round-01-initial-teammate/raw/T2-scenario-explorer.md §5`
  - 홍보-기능 비대칭으로 인한 유저 기대 gap 리스크 **R1**으로 추출 → `cross-cutting/unresolved-axes.md`
- **번복 조건**: 부부 세그먼트에서 압도적 니즈 데이터 확보 시.

---

## ADR-0003 — 법적 포지셔닝 고정

- **상태**: Accepted
- **결정 시점**: 2026-04-17 (사용자 확정)
- **결정 내용**: **self-help coaching** 범주. 심리상담·임상 치료 영역 침범 금지. 한국 의료법 제27조·정신건강증진법·심리상담사법(안) 회피 경계 유지.
- **근거**: 사용자 직접 지시.
- **영향**:
  - Workflow Architect의 **원칙 2 (임상 용어 PreToolUse Hook 차단)** → `round-01-initial-teammate/raw/T1-workflow-architect.md §1 원칙 2`
  - Scenario Explorer의 **절대 금지 시나리오 7종** → `raw/T2-scenario-explorer.md §5 절대 피해야 할 시나리오`
  - Operator Analyst의 **감정 안전 설계** → `raw/T3-operator-analyst.md §7`
  - Sustainability Strategist의 **규제 리스크 매트릭스** → `raw/T4-sustainability-strategist.md §6`
- **번복 조건**: 조직적 임상 자격자 확보 + DTx 인증 경로 진입 시에만.

---

## ADR-0004 — 가정 축 (1차 조사 한정)

- **상태**: Accepted
- **결정 시점**: 2026-04-17 (Claude 제안 → 사용자 묵시 승인)
- **결정 내용**: 1차 teammate 조사의 대립 가정 축을 **"Privacy-First Local-Capable (A) vs Cloud-Native Feature-Rich (B)"**로 설정.
- **근거**: "개발 로컬·배포 유연·상업화" 전제 하에 PRD 결정을 가장 크게 흔드는 축으로 식별됨. 로컬 LLM 여부는 상위 결정이 내려져야 하위 섹션 결정 가능.
- **기각된 대체 축**:
  - "Claude Code 단독 vs 외부 도구 연동" — Claude Code가 이미 Anthropic API 경유하므로 이분법 부정확.
  - "단일 사용자 vs 팀 공유" — B2C 우선 ADR-0001에 의해 자동 결정.
  - "최소 자동화 vs 최대 자동화" — Phase 3 수렴 단계에서 다룰 축이지 Phase 1 탐색 축 아님.
- **영향**: 4 teammate 전원 이 축 양쪽을 탐색.
- **번복 조건**: 2차 이상 조사에서 이 축이 더 이상 결정 드라이버가 아니라고 판명 시.

---

## ADR-0005 — 1차 조사의 수렴 결론

- **상태**: Accepted (**잠정**. 2차 조사에서 로컬 LLM 실측으로 재검증 필요)
- **결정 시점**: 2026-04-17 (4 teammate 독립 수렴)
- **결정 내용**: Pure Branch A(로컬 전용)·Pure Branch B(클라우드 전용) 둘 다 기각. **3-tier Hybrid**(Tier 1 로컬 / Tier 2 클라우드 Sonnet / Tier 3 클라우드 Opus + 인간)로 수렴.
- **근거**: 4 teammate가 독립 조사에서 동일 결론 도달.
  - T1: `raw/T1-workflow-architect.md §6 Branch A vs B 비교` — "권장 판단: 하이브리드 시퀀싱".
  - T2: `raw/T2-scenario-explorer.md §8.2 추천` — "하이브리드, 기본값 Branch A, 기능별 Branch B opt-in".
  - T3: `raw/T3-operator-analyst.md §9.3 하이브리드 가능성` — "페르소나 A·B·C 모두 수용 가능한 유일한 경로".
  - T4: `raw/T4-sustainability-strategist.md §11 필수 구성 요소 1번` — "Hybrid AI 아키텍처".
- **영향**: PRD 아키텍처 섹션 전체가 이 수렴 위에 구축됨.
- **번복 조건**:
  - 2차 조사에서 로컬 LLM(EXAONE 3.5 7.8B 등) 한국어 관계 상담 품질이 Sonnet 대비 70% 미만으로 실측될 경우 → Tier 1 로컬 삭제, Branch B 단독으로 전환.
  - 반대로 90% 이상 달성 + iOS 빌드 경로 확보 시 → Tier 1 비중 확대 검토.
- **기각 사유**:
  - Pure A: 7-10B 로컬 모델 한국어 품질 불확실, iOS 빌드 경로 병목, B2B 확장성 낮음, 모델 업데이트 배포(3-8GB) 부담.
  - Pure B: 프라이버시 해자 약화, PIPA 제23조 민감정보 국외이전 부담, Claude API 비용 선형 증가, BetterHelp형 프라이버시 사고 리스크.
