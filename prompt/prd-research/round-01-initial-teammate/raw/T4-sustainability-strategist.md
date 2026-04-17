---
round: 1
round_label: initial-teammate
file_kind: raw
teammate_id: T4
teammate_slug: sustainability-strategist
axes:
  - harness-token-economy
  - harness-maintenance-burden
  - product-llm-cost-branch-a
  - product-llm-cost-branch-b
  - pricing-model-comparison
  - competitor-pricing-benchmark
  - regulatory-risk-matrix-korea
  - regulatory-risk-matrix-global
  - data-governance
  - expansion-limits-b2c-to-b2b-and-languages
  - long-term-maintenance-1-to-3-years
  - scenario-6-12-24-months
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
related_files:
  - ../summary/T4-sustainability-strategist.conclusions.md
  - ../../cross-cutting/decisions-log.md
notes:
  - "본 파일은 T4 teammate의 원문 조사 보고서. 100% 보존 원칙."
  - "하네스 지속성 + 제품 지속성 2층위 분석."
  - "Claude Code 구독 한도·유지보수 + 상용 LLM 비용·과금·규제·확장·장기 유지 통합."
---

# T4 — Sustainability Strategist Raw Report

## 원본 질문 (요지)

> 장기 지속 가능성을 두 레벨에서 분석.
> (1) 하네스 지속성: Claude Code 구독 한도·유지보수·디버깅 부담.
> (2) 제품 지속성: 상용 론칭 후 LLM 비용·과금 모델·규제 리스크·팀 확장.

## 조사 방법론

- **하네스 경제**: Claude Code Max Plan·API per-token 단가·prompt caching·5시간 rolling limit 공식 문서. AgenticWorkflow 실측 token consumption 추정.
- **제품 LLM 비용**: Anthropic·OpenAI·Google API 공시 가격 + 로컬 LLM 오픈소스 비용 모델 비교.
- **과금 모델**: 경쟁사(Lasting·Paired·Replika·Relish·Wysa·트로스트) 공개 가격 + 산업 리포트(App Annie·Sensor Tower).
- **규제 리스크**: 한국 개인정보보호법 시행령·의료법·정신건강증진법·심리서비스법(안) + 글로벌 GDPR·HIPAA·App Store Guideline.
- **장기 시나리오**: 6·12·24개월 지속 가능성 모델링.

---

## §1. 하네스 토큰 경제 (Harness Token Economy)

### 1.1 Claude Code 구독·과금 구조 (2026-04 기준)

- **Claude Pro**: 월 $20, Sonnet·Haiku 주력, Opus 제한적 접근. 5시간 rolling limit.
- **Claude Max**: 월 $100·$200 2 tier. Opus·Sonnet 대량 접근. 5시간 rolling limit 관대.
- **API**: per-token 과금. Opus 4.7 약 $15/$75 per 1M tokens (input/output, 변동 가능). Sonnet 4.6 ~$3/$15. Haiku 4.5 ~$0.80/$4.
- **Prompt caching**: 시스템 프롬프트 캐시 히트 시 input 토큰 90% 할인.
- **Batch API**: 50% 할인, 24시간 처리.

### 1.2 AgenticWorkflow 실측 토큰 소비 패턴 (추정)

관찰된 워크플로우 한 번 실행의 토큰 분포:

| 작업 유형 | input 토큰 | output 토큰 | 비용 (Sonnet 기준) |
|---|---|---|---|
| 단순 파일 편집 | 5K-15K | 1K-3K | $0.03-$0.09 |
| 중간 규모 워크플로우 (10-20 단계) | 50K-150K | 10K-30K | $0.30-$0.90 |
| 대형 조사 워크플로우 (본 PRD 사전 리서치 차수) | 200K-500K | 50K-120K | $1.50-$3.30 |
| 앱 feature 1개 구현 (end-to-end) | 300K-1M | 100K-300K | $3.00-$7.50 |

**추정 근거**: Anthropic 공식 pricing + 각 도구·에이전트·Sub-agent 결과 누적 관찰 + `update_work_log.py` 이벤트 카운트.

### 1.3 Max Plan 일일 capacity

- 월 $200 Max Plan 기준 일일 워크플로우 실행 capacity: **4-8회** (feature 구현 규모 기준).
- 프롬프트 캐시·Haiku routing으로 **20-40% 확장 가능**.
- 대형 조사성 워크플로우(본 PRD 사전 리서치): 1회 실행에 상당 capacity 소모.

### 1.4 토큰 절감 전략

- **Prompt caching 최대 활용**: CLAUDE.md·agent 시스템 프롬프트·도메인 지식 파일을 캐시. 변동 적은 맥락 우선.
- **Haiku로 routing**: 단순 작업은 Haiku(훨씬 저렴). 라우터 로직은 workflow 단에서.
- **Sub-agent 결과 요약**: 긴 결과는 Orchestrator가 요약 후 메인 컨텍스트로. 풀 텍스트는 SOT 파일로.
- **Batch API**: 지연 허용 가능 작업은 50% 할인 활용.
- **Tool result 크기 제한**: Read·Grep·Bash 결과가 길면 의도적으로 잘라 넘김.

### 1.5 예산 붕괴 시나리오

- Workflow 내 retry 무한 루프: `validate_retry_budget.py`로 RB1-RB3 상한 강제.
- Sub-agent 과다 호출: NO-4 규칙 + `meta/discarded-options.md#D-5`.
- 대규모 조사에서 Read·Grep 결과가 불필요하게 전체 로드되는 경우: 도구 필터링·범위 제한 강제.
- Prompt caching 실패 누적: 변경 빈도 높은 시스템 프롬프트 수정 시 캐시 무효화 반복 → 모니터링 필요.

### 1.6 하네스 지속 가능성 판정

- **현 구독 선택**: Max Plan $200/월이 본 프로젝트 규모의 최소선.
- **확장 시점**: 팀 2인+·feature 동시 병렬 작업 시 API 전환 검토.
- **장기 경제성**: 1인 개발자는 $200/월로 연 $2,400 → 단일 feature 비용 $25-50 수준.

---

## §2. 하네스 유지보수 부담 (Harness Maintenance Burden)

### 2.1 유지보수 대상 자산 분류

AgenticWorkflow 하네스의 유지보수 범위를 **정적 자산**과 **동적 자산**으로 분리한다.

**정적 자산** (변동 빈도 낮음):
- `CLAUDE.md`, `AGENTS.md`, `soul.md`, `DECISION-LOG.md`: 프로젝트 헌법. 분기 단위 업데이트.
- `docs/protocols/*.md`: autopilot·quality-gates·ulw·context-preservation·code-change. 월 1-2회 수정.
- `.claude/settings.json`: Hook 등록. 신규 Hook 추가 시만.

**동적 자산** (변동 빈도 높음):
- `.claude/hooks/scripts/*.py` (21개+ 스크립트): Claude Code 업데이트·신규 규칙 추가 시 수정.
- `.claude/agents/*.md` (translator·reviewer·fact-checker): 도메인 확장·프롬프트 튜닝.
- `.claude/commands/*.md` (/install·/maintenance): 워크플로우 진화에 따라.
- `.claude/skills/workflow-generator/`, `doctoral-writing/`: 스킬 자체 진화.
- `translations/glossary.yaml`: 번역 용어 누적.
- `prompt/`, `coding-resource/`: 프로젝트 자료.

### 2.2 유지보수 이벤트 유형별 예상 빈도

| 이벤트 유형 | 예상 빈도 | 작업량 (1인 기준) |
|---|---|---|
| Claude Code 버전 업데이트 대응 | 월 1-2회 | 2-6시간 |
| 새 Hook 추가 | 분기 1-2회 | 4-10시간 |
| 기존 검증 스크립트 버그 수정 | 월 2-4회 | 1-3시간 |
| Agent 프롬프트 튜닝 | 월 1-3회 | 1-4시간 |
| 문서 동기화 (`/maintenance`) | 월 1회 | 30분-2시간 |
| Workflow 템플릿 신규 추가 | 분기 1-2회 | 6-16시간 |
| Glossary 용어 추가 | 주 1-2회 | 5-15분 |

**월간 총계 (1인 유지보수)**: **10-30시간/월**. 1일 평균 30분-1시간 수준.

### 2.3 디버깅 부담 패턴

Hook 시스템·Sub-agent·병렬 실행이 얽히면 디버깅 난이도가 급상승한다. 실제 관찰된 패턴:

**패턴 1 — Hook silent failure**:
- `generate_context_summary.py` 등 Stop/SessionEnd hook은 stdout/stderr가 유저에게 노출되지 않음.
- 증상: 스냅샷 파일이 생성되지 않거나 오래된 상태.
- 디버깅: `.claude/hooks/logs/` (있다면) 확인 → `stderr | tee`로 수동 로그 주입 → 직접 실행.
- 평균 해결 시간: 1-3시간.

**패턴 2 — validate_*.py 규칙 충돌**:
- `validate_pacs.py` PA3가 `validate_review.py` R2와 상호 의존할 때, 한쪽 스키마 변경 시 체인 실패.
- 증상: 특정 작업 단계에서 모호한 exit 2.
- 디버깅: 스크립트별 단독 실행 + 입력 재현.
- 평균 해결 시간: 2-5시간.

**패턴 3 — 컨텍스트 복원 실패**:
- `restore_context.py`가 RLM 인덱스 손상 시 복원 무효화.
- 증상: `[CONTEXT RECOVERY]` 표시는 있으나 내용이 비어 있음.
- 디버깅: `.claude/context-snapshots/knowledge-index.jsonl` 수동 검증.
- 평균 해결 시간: 30분-2시간.

**패턴 4 — 병렬 Task 도구 결과 병합 오류**:
- 5-10 Sub-agent 결과를 Orchestrator가 병합할 때 일부 누락.
- 증상: summary/index 파일에 일부 teammate 결과가 빠짐.
- 디버깅: 개별 Task 결과를 원천에서 재확인.
- 평균 해결 시간: 1-2시간.

**패턴 5 — `update_work_log.py` 9-도구 필터 drift**:
- Claude Code 신규 도구 추가 시 work log 누락.
- 증상: PostToolUse에서 특정 도구 이벤트가 기록되지 않음.
- 평균 해결 시간: 30분-1시간.

### 2.4 버전 드리프트 리스크

- **Claude Code 업데이트 주기**: 약 2-4주. minor 업데이트마다 Hook signature·도구 목록·권한 모델 미세 변경 가능.
- **드리프트 발생 지점**:
  - `settings.json` hook 이벤트명 변경 → 모든 hook 무효화 리스크.
  - 새 built-in 도구 추가 → `update_work_log.py` 필터 누락.
  - Task tool 병렬 상한 정책 변경 → 워크플로우 병렬도 재조정 필요.
  - MCP 서버 spec 변경 → `mcp__github__*` 도구 이름 변경.
- **대응 전략**:
  - `setup_maintenance.py`를 주 1회 자동 실행하여 드리프트 조기 감지.
  - Claude Code CHANGELOG 모니터링 (공식 docs + `/help`).
  - `/install` 명령으로 setup-init 검증 주기화.

### 2.5 1인 vs 2인+ 팀 유지보수 비교

| 항목 | 1인 개발 | 2인+ 팀 |
|---|---|---|
| 월 유지보수 부담 | 10-30시간 | 20-60시간 (분담 가능) |
| SOT 동시 쓰기 충돌 리스크 | 없음 | 있음 — 절대 기준 2 엄격 적용 필요 |
| CLAUDE.md 해석 불일치 | 낮음 | 중간 — 정기 리뷰 필요 |
| Hook 실험 cost | 낮음 | 높음 (팀원 작업 블로킹 가능) |
| 워크플로우 카탈로그 확장 속도 | 느림 | 빠름 |
| 디버깅 burden 분담 | 불가 | 가능 (전문 분야 분리) |

**1인의 핵심 리스크**: 번아웃·단일 장애점. **2인+의 핵심 리스크**: 컨벤션 표류.

### 2.6 자동화 가능 유지보수 작업

- ✅ Glossary 용어 추가: `translator` 서브에이전트가 자동 반영.
- ✅ 드리프트 감지: `setup_maintenance.py` 주기 실행.
- ✅ Hook 건강 검사: `/install` 명령.
- ✅ SOT 스키마 검증: `validate_*.py` 14종.
- ❌ Agent 프롬프트 품질 평가: 인간 판단 필요.
- ❌ 새 Workflow 설계: `workflow-generator` 스킬 보조는 가능하나 최종 의사결정 인간.
- ❌ Claude Code 신규 기능 통합: 사람이 문서 읽고 설계.

### 2.7 유지보수 부담 종합 판정

- **1인 1년 유지보수**: 연 120-360시간 = 월 10-30시간 = 전업 개발자 시간의 약 5-15%.
- **핵심 부담**: Claude Code 버전 업데이트 대응이 절반 이상 차지.
- **권장**: `setup_maintenance.py` 주 1회 자동 실행 + 월 1회 `/maintenance` 수동 수행 + 분기 1회 문서-코드 동기화 감사.

---

## §3. 제품 LLM 비용 (Product LLM Cost)

> **주의**: 이 섹션은 **[L1] 앱 런타임**의 LLM 비용. 하네스 비용과 완전 분리.

### 3.1 Branch A — 로컬 LLM 비용 구조

**디바이스 on-device 추론**이 전제. 서버 비용 없음, 사용자 단말의 연산·배터리·스토리지 자원 소비.

**주력 후보 모델**:

| 모델 | 파라미터 | 양자화 후 크기 | KMMLU (한국어) | 관계 상담 적합성 |
|---|---|---|---|---|
| EXAONE 3.5 7.8B | 7.8B | 약 4-5GB (Q4) | 63.7% | 한국어 최적화·한국 뉘앙스 우수 |
| Solar Pro (Upstage) | ~10B | 5-6GB (Q4) | 60-65% | 한국어 특화·상용화 이력 |
| Qwen 2.5 7B | 7B | 4GB (Q4) | 55-60% | 다국어·중국계 편향 주의 |
| Gemma 2 9B | 9B | 5GB (Q4) | 50-55% | Google 튜닝·안전성 필터 강함 |
| Llama 3.1 8B | 8B | 4.5GB (Q4) | 36-45% | 한국어 약함·영어 중심 |

**로컬 실행 추론 비용**:
- **하드웨어 요구**: iPhone 15 Pro (Neural Engine) / Android Snapdragon 8 Gen 3 / M1+ Mac.
- **추론 속도**: 7-10B Q4 기준 iPhone 15 Pro에서 10-20 tok/s 수준.
- **배터리 소비**: 연속 10분 대화 약 5-10% 배터리 (기종 의존).
- **서버 비용**: **0원** (모델 배포·OTA 업데이트는 CDN 비용 별도).

**앱 크기 부담**:
- 기본 앱 50-100MB + 모델 4-5GB → 사용자 다운로드 결정 임계점.
- 대안: 첫 실행 시 모델 다운로드 (lazy load) + Wi-Fi 강제.
- iOS App Store 200MB OTA 제한 → lazy download 필수.

**업데이트 비용**:
- 모델 교체 시 4-5GB 재배포. OTA 부담.
- 주기: 반기 1회 정도가 현실적.

### 3.2 Branch B — 클라우드 LLM 비용 구조

**서버에서 Anthropic/OpenAI/Google API 호출**. 세션당 토큰 소비 → 월 운영비 선형 증가.

**세션당 토큰 소비 추정** (관계 갈등 대화 평균 세션 기준):
- 시스템 프롬프트: 2K tokens (캐시 가능).
- 유저 대화 히스토리: 3-8K tokens.
- AI 응답: 500-2K tokens.
- 합계: input 5-10K / output 500-2K per 세션.

**모델별 세션 비용 (USD)**:

| 모델 | input $/1M | output $/1M | 세션 평균 비용 | 월 10 세션 |
|---|---|---|---|---|
| Claude Opus 4.7 | $15 | $75 | $0.12-0.30 | $1.20-3.00 |
| Claude Sonnet 4.6 | $3 | $15 | $0.024-0.060 | $0.24-0.60 |
| Claude Haiku 4.5 | $0.80 | $4 | $0.007-0.016 | $0.07-0.16 |
| GPT-4o | $2.50 | $10 | $0.018-0.045 | $0.18-0.45 |
| GPT-4o-mini | $0.15 | $0.60 | $0.001-0.003 | $0.01-0.03 |
| Gemini 2.0 Flash | $0.10 | $0.40 | $0.0008-0.002 | $0.008-0.02 |

**KRW 환산 (1 USD ≈ 1,350 KRW)**:
- Sonnet 세션당 약 30-80원 → 월 10 세션 시 300-800원/유저.
- Opus 세션당 약 160-400원 → 월 10 세션 시 1,600-4,000원/유저.
- Haiku 세션당 약 10-20원 → 월 10 세션 시 100-200원/유저.

### 3.3 Hybrid 수렴 비용 모델 (ADR-0005 3-tier)

ADR-0005에서 채택한 3-tier hybrid 구조의 세션당 비용:

| Tier | 사용 비율 (가정) | 모델 | 세션당 비용 | 가중 평균 |
|---|---|---|---|---|
| Tier 1 — 로컬 경량 | 60% | EXAONE 3.5 7.8B | 0원 | 0원 |
| Tier 2 — 클라우드 Sonnet | 35% | Sonnet 4.6 | 30-80원 | 10-28원 |
| Tier 3 — Opus + 인간 | 5% | Opus 4.7 + 인간 리뷰 | 400원 + 인건비 | 20원 + $ |

**월 유저당 예상 LLM 비용**:
- 활성 유저 월 10 세션 가정: **약 30-50원/유저/월** (Tier 1 로컬 주력 시).
- 순수 클라우드 (Branch B pure) 대비 10-20배 저렴.

### 3.4 비용 스케일링 시나리오

| MAU | Pure Branch B (Sonnet) | 3-tier Hybrid | 절감율 |
|---|---|---|---|
| 1,000 | 30-80만원/월 | 3-5만원/월 | 90%+ |
| 10,000 | 300-800만원/월 | 30-50만원/월 | 90%+ |
| 100,000 | 3,000-8,000만원/월 | 300-500만원/월 | 90%+ |
| 1,000,000 | 3-8억원/월 | 3-5천만원/월 | 90%+ |

**수익 필요 기준 (Pure Branch B)**:
- MAU 10K, LLM 비용 500만원/월 커버를 위해 ARPU 약 500원/월 필요.
- 실제 인프라·운영비 포함 시 ARPU 2,000-5,000원/월 마지노선.
- **Freemium 전환률 5% 시 유료 유저 ARPU 40,000-100,000원/월 필요** → 비현실.

**Hybrid로 가면**:
- MAU 10K, LLM 비용 50만원/월 → ARPU 50원/월 커버 가능.
- 여유가 생겨 **Freemium 지속 가능**.

### 3.5 추가 클라우드 비용 (Branch B / Hybrid Tier 2+3)

- **인프라**: AWS/GCP 서버, DB, 스토리지, CDN. 월 50-300만원 (MAU 10K 기준).
- **관측성**: Sentry·Datadog·Mixpanel. 월 20-50만원.
- **CS 도구**: Intercom·Zendesk. 월 10-30만원.
- **결제**: Stripe·KG이니시스 수수료 3-5%.
- **SMS·이메일**: 월 5-20만원.

### 3.6 제품 LLM 비용 종합 판정

- **순수 Branch A**: LLM 비용 0원. 서버 인프라 최소. **대신 품질 상한 제약**.
- **순수 Branch B**: MAU 10K에서 월 수백만원 LLM 비용. **freemium 수익 모델과 구조적 충돌**.
- **3-tier Hybrid (ADR-0005)**: Tier 1 로컬이 60% 흡수 → 90%+ 비용 절감. **freemium + 프리미엄 구조 유지 가능**.
- **민감 상담·안전 케이스는 Tier 3 Opus + 인간**: 비용 높아도 안전·차별화 핵심.

---

## §4. 과금 모델 비교 (Pricing Model Comparison)

### 4.1 주요 과금 모델 유형

**모델 A — Pure Freemium Subscription**:
- Free tier(제한적) + Premium 월/연 구독.
- 예시: Lasting(월 $11.99), Paired(월 $9.99, 연 $29.99), Replika(월 $9.99).
- 장점: 유저 불안 최소 (정액제) · 예측 가능 매출.
- 단점: 무료 유저가 비용 유발 (Branch B일 때 특히 심각).

**모델 B — Usage-Based (토큰·세션)**:
- 세션당 결제 또는 월 토큰팩 충전.
- 예시: OpenAI API, Character.ai 일부 tier.
- 장점: 매출-비용 선형 일치.
- 단점: **관계 위기 순간의 과금 불안** → Replika 2023 이탈 선례 (D-4 참조).
- **본 프로젝트 적용 불가** (B2C 금지).

**모델 C — Hybrid Freemium + Token Pack**:
- Free + Premium 구독 + 옵션 토큰팩(장시간 세션·전문가 매칭).
- 예시: Wysa (구독 + therapist add-on).
- 장점: 가벼운 유저는 구독, 헤비유저는 토큰팩.
- 단점: 복잡도 증가 · UX 커뮤니케이션 난이도.

**모델 D — One-time Purchase + In-App**:
- 앱 구매 후 개별 콘텐츠 결제.
- 예시: Gottman Card Decks, Love Nudge 일부.
- 장점: 부담 없는 진입.
- 단점: 지속 매출 어려움.

**모델 E — B2B SaaS (조직 계약)**:
- 회사·EAP(Employee Assistance Program)·건강보험사가 구독.
- 예시: Ginger, Lyra Health.
- 장점: ARPU 매우 높음, 이탈률 낮음.
- 단점: Sales cycle 길고, B2C 플라이휠 약함.

### 4.2 본 프로젝트 권장 구조 (ADR-0005 수렴 기반)

**Phase 1 (출시 0-6개월)**: **Pure Freemium Subscription (모델 A)**
- Free tier:
  - Tier 1 로컬 LLM 무제한.
  - 공감·대화 번역·감정 temperature check 무제한.
  - Tier 2 클라우드 Sonnet 월 5회 제한.
- Premium (월 9,900원 / 연 79,000원):
  - Tier 2 Sonnet 무제한.
  - 4 Horsemen 진단·NVC 코치·Gottman 카드덱 Pro.
  - 커플 함께 쓰기 기능.
- Premium+ (월 24,900원):
  - Tier 3 Opus + 전문가 1:1 채팅 월 2회.
  - 위기 리퍼럴 fast-track.

**Phase 2 (6-12개월)**: 모델 C로 확장 — **Hybrid Freemium + Token Pack 검토**
- 전문가 매칭 토큰팩 (1회 30분, 30,000원) 옵션.
- 커플 워크숍 (4주 프로그램, 150,000원) 추가.

**Phase 3 (12-24개월)**: 모델 E B2B 병행
- 기업 EAP 파트너십 (연 1,000만원~, 직원당 연 12,000원 수준).
- 결혼 준비 업체 제휴 (웨딩홀·혼수).

### 4.3 과금 모델 비교 매트릭스

| 모델 | 본 프로젝트 적합성 | 수익 안정성 | 유저 경험 | 구현 복잡도 | 경쟁사 선례 |
|---|---|---|---|---|---|
| A (Pure Freemium) | ★★★★★ | ★★★★☆ | ★★★★★ | ★★☆☆☆ | Lasting·Paired |
| B (Usage-based) | ☆☆☆☆☆ (B2C 금지) | ★★★☆☆ | ★☆☆☆☆ | ★★☆☆☆ | Character.ai (이탈) |
| C (Hybrid) | ★★★★☆ | ★★★★★ | ★★★☆☆ | ★★★★☆ | Wysa |
| D (One-time) | ★★☆☆☆ | ★★☆☆☆ | ★★★★☆ | ★★☆☆☆ | Gottman Cards |
| E (B2B) | ★★★☆☆ (Phase 3) | ★★★★★ | N/A | ★★★★★ | Lyra·Ginger |

### 4.4 가격 포인트 검증

- **한국 구독 앱 평균 ARPU** (Sensor Tower 2024): 월 4,500-12,000원.
- **유사 카테고리 벤치마크**:
  - 삼성 헬스+: 월 4,900원.
  - 정신건강 앱 트로스트: 월 9,900원.
  - Wysa 한국: 연 99,000원.
- **페르소나별 지불 의향** (T3 §10):
  - 페르소나 B (위기): 월 9,900-14,900원 즉시 지불 의향.
  - 페르소나 A (파워 유저): 월 14,900-24,900원, 연 10-20% 할인 선호.
  - 페르소나 C (장기 복귀): 저가 freemium 유지 → 연 구독 가능.

**결론**: 월 9,900원 / 연 79,000원 / 프리미엄 24,900원 구조가 한국 시장 중앙값과 정렬.

### 4.5 과금 모델 리스크

- **Freemium 무료 유저 비용**: Branch B pure는 freemium 무한 대화 시 파산. 3-tier Hybrid가 해결.
- **구독 피로도**: Netflix·Disney+·YouTube 등 포화. **트라이얼 7일 → 전환률** 핵심.
- **연 구독 refund 정책**: Apple/Google 앱스토어 환불 규정 준수.
- **가격 상승 커뮤니케이션**: 서비스 성숙 시 가격 인상은 유저 이탈 리스크.

### 4.6 과금 모델 종합 판정

- **Phase 1 필수**: Pure Freemium Subscription (모델 A) — 단순·명확·경쟁사 검증.
- **토큰팩은 Phase 2 이후**: 가치 증명 후 추가.
- **Usage-based B2C 영구 금지**: D-4 재확인.
- **B2B는 Phase 3 선택지**: 본 B2C 플라이휠 확정 후.

---

## §5. 경쟁사 가격 벤치마크 (Competitor Pricing Benchmark)

### 5.1 글로벌 관계·커플 앱

| 앱 | 기본 구독 | 연 구독 | 특이사항 | 검증 출처 |
|---|---|---|---|---|
| Lasting (Talkspace) | $11.99/월 | $59.99/년 | Gottman 기반·커플 동기화 | Apple App Store 2025 |
| Paired | $9.99/월 | $29.99/년 | 일일 질문·대화 카드 | Apple App Store 2025 |
| Relish | $19.99/월 | $119.99/년 | 1:1 코치 포함 | 공식 사이트 2024 |
| Official by Honi | $7.99/월 | $59.99/년 | 게임화 커플 앱 | Apple App Store 2025 |
| Love Nudge | Free + IAP | — | 5 Love Languages 기반 | Apple App Store 2025 |

### 5.2 글로벌 AI 동반자·정신건강 앱

| 앱 | 기본 구독 | 연 구독 | 특이사항 | 검증 출처 |
|---|---|---|---|---|
| Replika Pro | $9.99/월 | $69.99/년 | AI 동반자·LLM 기반 | 공식 사이트 2024 |
| Character.ai Plus | $9.99/월 | $119.88/년 | 멀티 캐릭터 AI | 공식 사이트 2024 |
| Wysa Pro | $99/년 | — | CBT AI + 치료사 add-on | 공식 사이트 2024 |
| Woebot | Free (B2B 중심) | — | 기업·보험사 계약 | 공식 사이트 2024 |
| Calm | $14.99/월 | $69.99/년 | 명상·수면·스트레스 | 공식 사이트 2024 |
| Headspace | $12.99/월 | $69.99/년 | 명상·마음챙김 | 공식 사이트 2024 |

### 5.3 한국 관계·심리·상담 앱

| 앱 | 기본 구독 | 연 구독 | 특이사항 | 검증 출처 |
|---|---|---|---|---|
| 트로스트 | 월 9,900원 | — | 텍스트 상담 구독 | 공식 사이트 2024 |
| 마인드카페 | 상담당 결제 | — | 전문 상담사 매칭 | 공식 사이트 2024 |
| 아토머스 심심이 | Free + 광고 | — | 대화형 AI·소셜 지향 | 공식 사이트 2024 |
| 마음검진 (카카오) | Free | — | 검진·리포트 | 공식 사이트 2024 |
| 베러미 | 월 19,900원 | — | 명상·심리 콘텐츠 | 공식 사이트 2024 |

### 5.4 전통 상담·치료 가격 (참조점)

- **한국 심리상담사 1:1 세션**: 8-15만원/회.
- **가족·부부 상담**: 15-25만원/회.
- **정신과 의사 진료**: 보험 적용 시 1-2만원, 비급여 10만원+.
- **BetterHelp (미국)**: 주 $65-90 (약 주 8.8-12만원).
- **Talkspace (미국)**: 월 $260-400.

### 5.5 본 프로젝트 가격 포지셔닝

**월 9,900원 / 연 79,000원 / 프리미엄 24,900원**의 경쟁 대비 포지션:

- **Lasting·Paired 수준** (관계 앱 벤치마크): 월 1만원 구간 정렬.
- **트로스트와 동일**: 한국 심리 앱 중앙값 정렬.
- **Replika 대비 약간 저렴**: 한국 AOV 고려.
- **전통 상담 1회 가격으로 연간 구독 가능**: Freemium 킬러 메시지.

**차별화 가치 (가격 근거)**:
- Branch A 로컬 LLM: 프라이버시 해자 → 가치 할증 가능.
- 3-tier Hybrid: Tier 3 Opus + 전문가 매칭은 Relish·Wysa 수준 기능을 월 24,900원에 제공.
- 한국어 관계 특수 (고부·명절·직장) 지원: 글로벌 앱 대비 차별화.

### 5.6 프리미엄 가격 스트레스 테스트

월 24,900원 프리미엄 tier가 정당화되려면:

- ✅ 전문가 1:1 채팅 월 2회 포함 = 약 16-30만원 가치를 25,000원에 제공 (단 짧은 세션).
- ✅ 4 Horsemen 진단·Gottman Pro 카드덱: 전통적으로 유료 워크숍 15-30만원.
- ✅ 커플 동기화·대화 번역 고도 기능.
- ❌ 단순 AI 대화 무제한만으로는 정당화 어려움 → 인간 요소가 핵심.

### 5.7 경쟁사 가격 벤치마크 종합 판정

- **월 9,900원 기본**: 한국 시장 중앙값 정렬. 유저 저항 최소.
- **연 79,000원 (월 6,580원 환산)**: 34% 할인 효과로 연 구독 유도.
- **프리미엄 24,900원**: 전문가 요소 필수. AI만으로는 가치 부족.
- **해외 확장 시**: Lasting·Paired 수준의 USD $9.99-11.99 정렬 권장.

---