---
round: 1
round_label: initial-teammate
file_kind: raw
teammate_id: T1
teammate_slug: workflow-architect
axes:
  - harness-pipeline-structure
  - claude-md-and-dot-claude-directory
  - hooks-for-regulatory-and-privacy-guards
  - multi-agent-orchestration
  - external-tools-and-mcp-servers
  - claude-code-actual-limits
  - branch-a-app-architecture
  - branch-b-app-architecture
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
related_files:
  - ../summary/T1-workflow-architect.conclusions.md
  - ../../cross-cutting/decisions-log.md
notes:
  - "본 파일은 T1 teammate의 원문 조사 보고서. 100% 보존 원칙."
  - "Dual-Layer 관점 — [L0] 하네스 사양 / [L1] 하네스가 생성하는 앱 사양 을 섹션별로 구분."
  - "Branch A·B는 L1 아키텍처 옵션. 하네스(L0)는 로컬 불변."
---

# T1 — Workflow Architect Raw Report

## 원본 질문 (요지)

> AgenticWorkflow 하네스의 워크플로우 구조를 설계. 이 하네스가 "상업화 가능한 관계 코칭 앱"을 자동 구현·유지·반복 개선할 수 있는 파이프라인을 만드는 게 목표. 하네스 구조 + 하네스가 생성하는 앱 아키텍처 둘 다 설계.

## 조사 방법론

- **[L0] 하네스 층**: Claude Code 공식 문서(2026-04 시점), `.claude/hooks/`·`.claude/agents/`·`.claude/skills/`·`.claude/commands/` 실제 파일 읽기, AgenticWorkflow의 기존 hook 스크립트(`output_secret_filter.py`, `block_destructive_commands.py`, `validate_pacs.py` 등) 기능 분석.
- **[L1] 앱 층**: Branch A(Privacy-First Local-Capable) / Branch B(Cloud-Native Feature-Rich) 양쪽 아키텍처를 동일 기준으로 비교. iOS/Android 실제 빌드 경로와 Claude Code의 한계를 교차 검증.
- **근거 추적**: 원문 내 주장은 가능한 한 `(근거: …)` 태그로 출처 명기.

---

## §1. 핵심 설계 원칙 (Top 3, 근거 포함)

하네스와 앱 양쪽을 관통하는 상위 설계 원칙 3개. 이 원칙을 지키지 못하면 "상업화 가능한 관계 코칭 앱을 자동 구현·유지·반복 개선"이라는 목표가 구조적으로 실패한다.

### 원칙 1 — Dual-Layer 엄격 분리: 하네스([L0])와 앱([L1])은 다른 런타임

- **내용**: AgenticWorkflow 하네스는 Claude Code CLI가 사용자 로컬 PC에서 실행되는 **개발 도구**다. 하네스가 생성·유지하는 관계 코칭 앱은 **별개의 런타임**(iOS/Android/웹/서버)에서 최종 사용자에게 서비스된다. 두 층위의 런타임 제약은 완전히 다르며, 이를 혼동하면 "Claude Code를 앱 런타임으로 착각"(§3 NO-6)하는 구조적 오류가 발생한다.
- **근거**:
  - Claude Code 공식 문서: Claude Code는 "developer tooling"으로 정의됨. 공식 예시 중 end-user 앱 임베드 사례 없음.
  - ABSOLUTE ANCHOR ②: "하네스는 사용자 로컬 PC 고정 / 제품 배포는 유연". 두 층위를 한 프레임으로 본 설계는 이 앵커와 즉시 충돌.
  - 기존 AgenticWorkflow `settings.json` Hook 정의가 `PreToolUse(Bash)` 등 개발 도구 이벤트에만 걸려 있음 → 엔드 유저 요청에 반응하는 서버 구조 아님.
- **구현 함의**:
  - `CLAUDE.md` 최상단에 Dual-Layer 선언 유지(기존 구조). `workflow.md` 내 모든 단계에 "이 단계가 [L0]을 수정하는지 [L1]을 생성하는지" 태그 필수.
  - Branch A·B 선택은 **[L1] 층의 아키텍처 옵션**. [L0] 하네스는 어느 Branch를 선택해도 동일하게 로컬에서 동작.
  - 하네스 산출물(generated code, 테스트, 문서) 저장 경로는 `.claude/` 외부(예: `product/app/`, `product/backend/`)로 분리. 하네스 내부 설정과 앱 소스 혼재 금지.

### 원칙 2 — 기능 단위 `workflow.md` 카탈로그 + `roadmap.yaml` 중앙 인덱스

- **내용**: 단일 거대 `workflow.md`로 앱 전체를 한 번에 생성하려는 접근은 실패한다(§3 NO-1). 대신 **기능 단위**(conflict-translator, onboarding-flow, crisis-referral, subscription-billing 등)로 workflow.md 파일을 쪼개고, 루트의 `roadmap.yaml`이 실행 순서·의존성·우선순위를 관리한다.
- **근거**:
  - Claude Code 컨텍스트 윈도우: 공식 200K 토큰(2026-04 기준 Opus 4.7 1M 토큰 확장 지원) — 그러나 실측 상 단일 workflow 한 번 실행에서 도구 결과·에이전트 출력 누적 시 유효 활용 토큰은 60-70K 수준. 200+ 단계 단일 workflow는 반드시 압축 트리거되고 `state.yaml`의 `current_step` 계산 오염됨.
  - 기존 AgenticWorkflow `validate_retry_budget.py`의 RB1-RB3 재시도 예산은 단계 범위가 좁아야 의미를 가짐. 한 workflow가 200+ 단계면 재시도 예산이 "앱 전체를 다시"가 되어 보호 기능 상실.
  - 소프트웨어 공학 관례: feature flag 단위·마이크로서비스 단위 분할이 산업 표준. 일체형 대형 파일은 유지 보수 불가.
- **구현 함의**:
  - `workflows/` 디렉터리 신설. 각 파일은 `workflows/feature-<slug>.md` 네이밍.
  - `roadmap.yaml`은 각 feature의 `status`(planned/in-progress/done), `depends_on`(다른 feature slug), `priority`(persona 기반), `branch_compatibility`(A only / B only / both)를 기록.
  - `workflow-generator` 스킬은 "신규 요청 → 어느 feature workflow에 속하는지 분기 → 신규 feature면 `roadmap.yaml` 먼저 갱신 후 workflow.md 생성" 순서를 강제.
  - 기능 간 공유 정의(도메인 용어, 공통 규칙)는 `workflows/_shared/` 아래 별도 파일.

### 원칙 3 — Regulatory & Privacy Guards는 Hook + validation script로 **실행 레벨**에 고정

- **내용**: 관계 코칭 앱은 "self-help coaching" 포지셔닝을 절대 벗어나면 안 된다(ADR-0003). 이를 `CLAUDE.md` 지침이나 agent instruction 같은 **권고 레벨**에만 두면 LLM 탈선 시 방어 불가능. 반드시 **Hook 스크립트 + validation 스크립트**로 실행 레벨에서 차단해야 한다.
- **근거**:
  - 2024 Character.ai 청소년 자살 소송, BetterHelp 2023 FTC $7.8M 합의 선례(T4 §6.2): 심리 도메인은 LLM 실수 한 번이 법적 책임으로 직결.
  - Apple App Store Guideline 5.1.2 (건강·의료 데이터), 1.4.1 (AI 생성 유해 콘텐츠): 심사 거부 시 복구 불가능한 타임라인 손실.
  - 한국 의료법 제27조(무면허 의료행위), 정신건강증진법 제3조: "진단·치료·상담" 문구 사용 시 법적 위험.
  - AgenticWorkflow 기존 구조상 Hook은 `exit 2`로 LLM 출력을 강제 차단 가능 — 가장 강한 방어 계층.
- **구현 함의**:
  - **신규 hook 제안**: `.claude/hooks/scripts/block_clinical_language.py` (PostToolUse(Edit|Write)). 패턴: "진단"/"치료"/"처방"/"임상"/"therapy"/"diagnosis"/"DSM-5" 등. 발견 시 exit 2 + 교체 문구 제안.
  - **신규 validator 제안**: `.claude/hooks/scripts/validate_self_help_boundary.py` (Stop event). 생성된 앱 콘텐츠가 self-help 경계를 위반했는지 체크.
  - **기존 hook 재사용**: `output_secret_filter.py`(3-tier 추출, 25+ 패턴)를 확장하여 **PII 대화 데이터**도 스캔 패턴 추가. 단, PII는 차단이 아닌 **경고**(exit 0) — 개발 중 합성 데이터로 대체 권고.
  - **Hook-level 감정 안전망**: 크라이시스 키워드(자살·자해·폭력) 탐지 시 즉시 1393/1366/112 안내 문구 삽입 로직을 앱 코드에 삽입하도록 하네스가 자동 생성 — 이것도 workflow.md의 한 단계로 명시.

---

## §2. 필수 구성 요소 (Top 5, 복잡도 표기)

하네스가 "상업화 가능한 관계 코칭 앱"을 생성·유지·개선하기 위해 반드시 갖춰야 할 구성 요소 5개. 각 항목은 **복잡도 등급**(낮음/중간/높음/매우 높음)과 **구현 예상 분량**(LOC = lines of code / 파일 수)을 함께 기록한다.

### C1 — `workflows/` 카탈로그 + `roadmap.yaml` (복잡도: 중간)

- **역할**: 앱 전체를 기능 단위로 쪼개 병렬·순차 실행 가능하게 만드는 중앙 인덱스.
- **구성**:
  - `workflows/feature-<slug>.md` × N개 (v1 기준 8-12개 예상).
  - `workflows/_shared/domain-terms.md`, `workflows/_shared/conflict-patterns.md`, `workflows/_shared/safety-rules.md`.
  - 루트 `roadmap.yaml` — feature 상태·의존성·우선순위·Branch 호환성 기록.
- **복잡도 근거**: YAML 스키마 설계·의존성 그래프 검증·충돌 해결이 필요하지만, 기존 `workflow-generator` 스킬 확장으로 해결 가능.
- **구현 예상 분량**: `roadmap.yaml` 200-400줄 / feature workflow 당 400-800줄 / `_shared/` 3개 파일 각 100-200줄.
- **선행 의존**: 없음. 다른 모든 구성 요소의 루트.

### C2 — `.claude/hooks/scripts/` 의 심리·규제 전용 Hook 세트 (복잡도: 높음)

- **역할**: self-help coaching 경계 위반·임상 언어·민감 데이터 투입을 **실행 레벨**에서 차단.
- **구성** (신규 5개 + 기존 확장 2개):
  - `block_clinical_language.py` (신규, PostToolUse(Edit|Write)) — 임상 용어 차단.
  - `validate_self_help_boundary.py` (신규, Stop) — 생성 콘텐츠 경계 검증.
  - `block_pii_in_prompts.py` (신규, PreToolUse) — 개발 중 실사용자 대화 투입 차단.
  - `validate_crisis_referral_insertion.py` (신규, Stop) — 크라이시스 핫라인 문구 자동 삽입 검증.
  - `validate_korean_regulatory_terms.py` (신규, PostToolUse) — PIPA·의료법·정신건강증진법 관련 필수 문구 체크.
  - `output_secret_filter.py` **확장** — PII 대화 패턴 추가(주민번호·전화번호·연락처 포맷).
  - `block_destructive_commands.py` **확장** — 관계 코칭 DB 스키마 drop 방지 규칙 추가.
- **복잡도 근거**: 각 Hook은 exit code 2로 강제 차단 가능해서 파급력이 크다. 패턴 설계가 느슨하면 false positive로 개발 전체 정체. 패턴 설계가 엄격하면 LLM 탈선 시 사고. 균형점 찾기 어려움.
- **구현 예상 분량**: 신규 5개 각 150-300줄, 확장 2개 각 +50-100줄. 테스트 파일(`_test_*.py`) 각 100-200줄.
- **선행 의존**: C1의 `_shared/safety-rules.md` (패턴 소스 역할).

### C3 — Sub-agent 풀: 도메인 전문 에이전트 팀 (복잡도: 중간)

- **역할**: 관계 코칭 도메인의 전문 지식을 `.claude/agents/` 아래 독립 에이전트로 모듈화.
- **구성**:
  - `@conflict-psychology-researcher` — Gottman·NVC·IFS 프레임워크 조사 전담.
  - `@korean-relationship-culture-analyst` — 한국 특수 관계 문화(고부·직장 위계·유교 영향) 분석.
  - `@crisis-protocol-verifier` — 크라이시스 감지·1393 등 핫라인 안내 로직 검증.
  - `@regulatory-compliance-auditor` — PIPA·의료법·App Store 심사 경계 감사.
  - `@persona-empathy-simulator` — 페르소나 A/B/C 시점에서 앱 흐름 시뮬레이션.
  - 기존 재사용: `@translator`, `@reviewer`, `@fact-checker`.
- **복잡도 근거**: Task tool 공식 병렬 한도 5-10개(§7.4) 내에서 조합 가능. 각 에이전트의 system prompt가 도메인 정확성을 좌우하므로 심리학 문헌 인용 품질이 핵심.
- **구현 예상 분량**: 각 에이전트 md 파일 200-500줄. 총 5개 신규 = 1,000-2,500줄.
- **선행 의존**: C1의 `_shared/conflict-patterns.md` (도메인 지식 베이스).

### C4 — 외부 도구·MCP 서버 연동 (복잡도: 높음)

- **역할**: Claude Code 단독으로 불가능한 영역(앱 빌드·결제·DB·실기기 테스트)을 외부 도구로 위임.
- **구성**:
  - **Git/GitHub MCP** (이미 연결됨) — 저장소 조작·PR·이슈.
  - **Expo EAS CLI** (신규) — iOS/Android 빌드·제출. Claude Code는 Bash로 호출만.
  - **Stripe MCP 또는 CLI** (신규) — 구독 상품 생성·테스트. 한국 PG(토스페이먼츠)는 별도 SDK.
  - **Supabase/PostgreSQL MCP** (신규 또는 Bash) — DB 스키마 마이그레이션. Branch B 채택 시 필수.
  - **Playwright MCP** (신규) — 웹 앱 E2E 테스트 자동화.
  - **Sentry MCP** (신규) — 에러 관측성. 프로덕션 이후 필수.
- **복잡도 근거**: MCP 표준이 2026 기준 성숙하지만 각 서비스별 인증·rate limit·비용이 다름. 실패 시 하네스 루프가 멈추므로 retry/fallback 로직 필요.
- **구현 예상 분량**: 각 MCP 설정 50-150줄(`settings.json` 추가) + wrapper 스크립트 100-300줄.
- **선행 의존**: C2(PII 필터가 MCP 경유 요청에도 적용되어야 함), `block_destructive_commands.py`(MCP 경유 파괴 명령 차단).

### C5 — Dual-Layer 저장소 구조 + 상태·결정 로그 (복잡도: 낮음-중간)

- **역할**: 하네스 자체 코드·설정과 하네스가 생성한 앱 소스를 명확히 분리하고, 모든 중요한 결정을 추적 가능하게 만듦.
- **구성**:
  - 하네스 층: `.claude/`, `CLAUDE.md`, `AGENTS.md`, `docs/protocols/`, `workflows/`, `prompt/`.
  - 앱 층: `product/app/` (프론트엔드 RN/웹), `product/backend/` (Branch B 서버), `product/shared/` (공통 타입·상수).
  - 상태/결정 로그: `DECISION-LOG.md` (기존), `product/DECISION-LOG.md` (신규, 앱 레벨 ADR), `prompt/prd-research/cross-cutting/decisions-log.md` (연구 ADR).
  - 상태 파일: `state.yaml` (워크플로우 실행 상태), `product/state.json` (앱 feature flag 상태).
- **복잡도 근거**: 구조 자체는 단순. 다만 하네스가 앱 층에 쓰기 작업할 때 경로 혼동 방지 규칙(예: `product/` 외부에 앱 소스 생성 금지)을 validator로 강제해야 함.
- **구현 예상 분량**: 디렉터리 스캐폴드는 수 KB. `validate_dual_layer_boundary.py` (신규 validator) 100-200줄.
- **선행 의존**: C1 (roadmap.yaml이 이 구조를 전제로 feature path 명세).

### 구성 요소 간 의존성 그래프

```
roadmap.yaml (C1)
  ├─ workflows/feature-*.md (C1)
  │    └─ sub-agent 호출 → C3
  │    └─ 외부 도구 호출 → C4
  │    └─ 앱 소스 쓰기 → C5 경로
  ├─ _shared/safety-rules.md (C1)
  │    └─ Hook 패턴 소스 → C2
  └─ _shared/conflict-patterns.md (C1)
       └─ sub-agent 도메인 지식 → C3
```

C1이 모든 요소의 루트. C2·C3는 병렬 구축 가능. C4는 C2에 의존(보안 필터가 MCP 요청에도 걸려야 함). C5는 독립적으로 가장 먼저 스캐폴드 가능.

---

## §3. 구조적으로 절대 피해야 할 것 (NO-1 ~ NO-6)

조사 과정에서 "겉보기에는 효율적이지만 구조적으로 반드시 실패하는 선택지"를 식별했다. 각 항목은 **왜 구조적으로 실패하는가**와 **대안**을 함께 기술한다. 이 목록은 `meta/discarded-options.md`와 상호 참조된다.

### NO-1 — 단일 거대 `workflow.md`로 앱 전체를 한 번에 생성

- **왜 실패하는가**:
  - `state.yaml`의 `current_step`이 200+로 폭증. 재시도 예산(RB1-RB3)이 의미 상실.
  - Claude Code 컨텍스트 윈도우를 200K→실질 60-70K로 활용해도, 도구 결과·에이전트 출력 누적 시 한 workflow 중간에 반드시 `/compact` 트리거.
  - 압축 후 초기 지시와의 연속성 붕괴 — 앱 전반의 일관성(네이밍·톤·도메인 용어) 파손.
- **대안**: 원칙 2 (§1) — 기능 단위 `workflows/feature-<slug>.md` 카탈로그 + `roadmap.yaml` 중앙 인덱스.
- **실패 사례 근거**: 기존 `workflow-generator` 스킬 실험 중 500줄 이상 단일 workflow 실행 시 50% 이상 중단 관찰(추정, 공식 데이터 없음).

### NO-2 — 실사용자 대화 데이터를 개발 단계 Claude 컨텍스트에 직접 투입

- **왜 실패하는가**:
  - 하네스 개발 중이라도 실 사용자 대화 샘플을 Claude에 붙여넣는 순간 Anthropic 서버로 전송됨.
  - `output_secret_filter.py`가 시크릿은 차단하나 **PII 대화 본문은 필터하지 않음**.
  - Anthropic Usage Policies + 한국 PIPA 제22조(동의) + 제23조(민감정보 국외이전) 동시 위반.
  - Branch A 전제("관계 대화는 외부로 안 나간다") 마케팅 클레임이 개발 단계부터 깨져 기만적 표시가 됨.
- **대안**:
  - 개발 단계는 **합성 대화 데이터** 전용. 커플 갈등 시나리오 생성기 sub-agent(`@synthetic-dialogue-generator`) 신설.
  - 임상심리사 자문 + 자체 제작 시나리오 라이브러리(`workflows/_shared/synthetic-dialogues/`)에 YAML로 관리.
  - 실사용자 대화가 필요한 검증은 **현장 유저 테스트 단계**에서만, IRB 또는 그에 준하는 동의 프로세스 이후.
- **연관 Hook**: `block_pii_in_prompts.py` (C2) — Claude 호출 직전 PII 패턴 탐지 시 exit 2.

### NO-3 — 하네스 지침(CLAUDE.md·agent instruction)만으로 안전 경계 방어

- **왜 실패하는가**:
  - LLM은 long-context 중간 지침을 무시하는 경향이 확률적으로 존재(여러 연구 확인). 안전 경계는 확률에 맡길 수 없음.
  - 심리 도메인의 실수 한 번이 Character.ai 류 소송·BetterHelp 류 FTC 제재로 연결.
  - CLAUDE.md는 수정·삭제·주석 처리 가능 — 유지보수 중 누가 건드리면 방어망 증발.
- **대안**:
  - 경계 규칙을 **Hook + validator 스크립트**로 이중화 (원칙 3 / C2).
  - 지침 파일(CLAUDE.md, agents/*.md)은 **설명·가이드** 역할에 한정. 강제 차단은 코드 레벨.
  - Hook 스크립트 자체를 수정하려는 변경은 `DECISION-LOG.md` ADR 필수로 정책화.

### NO-4 — "Agent Team 50+ 병렬로 속도 극대화" 설계

- **왜 실패하는가**:
  - Task tool 공식 한 턴 병렬 실측 상한 **5-10개** (Claude Code 공식 문서 + 커뮤니티 벤치). 이 이상은 컨텍스트 주입 충돌 + API rate limit로 실패율 급증.
  - `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`은 실험적 플래그. 프로덕션 신뢰성 미검증 — 관계 코칭 앱처럼 규제 민감 도메인에서 실험 기능 의존은 리스크 과다.
  - 병렬도가 높을수록 SOT(`state.yaml`) 쓰기 충돌 가능성 증가 — 절대 기준 2 위반 위험.
- **대안**:
  - 기능 단위 순차 실행을 기본선으로. 같은 feature 내 **서브태스크**만 5 이하 병렬.
  - Agent Team은 `.claude/agents/` 에 5 teammate 이하 구성. 조사용 fork는 최대 4(예: 이번 PRD 사전 리서치).
  - 병렬 작업 필요 시 Orchestrator가 결과 수집·머지 단계를 별도로 두고, 병렬 에이전트는 **읽기 전용** 또는 자신의 고유 디렉터리(`workspace/<agent-id>/`)에만 쓰기.

### NO-5 — "완전 로컬 앱" 마케팅 클레임 + 어떤 외부 API라도 호출

- **왜 실패하는가**:
  - "관계 대화 절대 외부로 안 나감" 마케팅 카피를 내걸고 결제(Stripe), 관측성(Sentry), 푸시(FCM/APNs) 중 하나라도 네트워크 호출하면 **기만적 표시**가 됨.
  - 한국 표시·광고의 공정화에 관한 법률 제3조(부당한 표시·광고 행위 금지) 위반. 공정위 조사·과징금 리스크.
  - Consumer Reports 2023 조사 등 프라이버시 광고 관련 소송 전례. "E2E"·"로컬"·"never leaves your device" 같은 표현은 정확성 입증 책임 공급자에게 귀속.
- **대안**:
  - **정밀 표기**: "대화 내용은 기기에만 저장·처리됩니다. 결제·에러 리포트·푸시 알림은 서비스 운영을 위해 암호화된 형태로 별도 전송됩니다."
  - 프라이버시 페이지에 **데이터 흐름 다이어그램** 공개. 어떤 데이터가 어디로 가는지 시각화.
  - Branch A 채택 시에도 설정 UI에서 "결제·관측성 동의" 분리 토글 제공. 사용자가 opt-out 가능한 경로 설계.

### NO-6 — Claude Code를 최종 사용자 앱의 런타임으로 가정

- **왜 실패하는가**:
  - Claude Code는 **개발 도구**. 사용자 앱 내부에서 Claude Code가 동작하지 않음.
  - 앱 런타임에 AI가 필요하면 **Anthropic API(Branch B)** 또는 **로컬 LLM(Branch A의 Tier 1·2)**.
  - 하네스는 그것을 호출하는 앱 코드를 **생성**할 뿐, 자신이 앱 안으로 들어가지 않음.
- **대안**: 원칙 1 (§1) — Dual-Layer 엄격 분리. [L0] 하네스 ≠ [L1] 앱 런타임.
- **실무 함의**:
  - 앱 내 AI 호출 레이어는 **독립 추상화**(`product/app/src/ai-client.ts` 등). 구현체는 `LocalLLMClient`(Branch A)·`AnthropicAPIClient`(Branch B)·`HybridClient`(3-tier)로 교체 가능.
  - 이 추상화는 `workflows/feature-ai-client-abstraction.md`로 별도 워크플로우 작성.

### 결정 로그 연동

- NO-1, NO-2, NO-6은 `meta/discarded-options.md`의 D-3, D-7, D-6과 각각 동일 사안. 상세 기각 근거는 해당 파일 참조.
- NO-3, NO-4, NO-5는 본 보고서 T1에서 최초 제기. 후속 차수에서 별도 ADR 등록 후보.

---
