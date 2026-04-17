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