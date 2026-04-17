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