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

## §4. Branch A 상세 결론 (Privacy-First Local-Capable)

**가정**: 앱이 유저 기기 로컬 중심으로 동작하고, 동기화는 E2E 암호화를 거친 선택적 경로만 사용. 프라이버시를 상업 해자로 전면 배치.

### 4.1 하네스 자체 구조 (Branch A 전제에 맞춘 [L0] 설계)

Branch A 선택이 하네스에 미치는 영향은 "앱 런타임 제약을 하네스가 이해해야 한다"는 점이다. 하네스 자체의 실행 환경은 여전히 사용자 로컬 PC(ABSOLUTE ANCHOR ②).

- **`.claude/agents/` 구성 (Branch A 특화)**:
  - `@local-llm-capability-analyst` (신규) — 온디바이스 LLM(EXAONE 3.5 7.8B, Solar Pro, Qwen 2.5 7B, Gemma 2 등) 한국어 성능·메모리·배터리 프로필 분석 전담.
  - `@on-device-ml-optimizer` (신규) — MLX(Apple Silicon)·Core ML·TFLite·ONNX 양자화(4bit·8bit)·토큰 속도 최적화 조언.
  - `@e2e-encryption-designer` (신규) — libsignal·Noise Protocol·WebCrypto 기반 파트너 간 공유 채널 암호화 스펙 설계.
  - `@regulatory-compliance-auditor`, `@conflict-psychology-researcher`, `@persona-empathy-simulator`는 공통.

- **`workflows/` 카탈로그 (Branch A 전용 feature)**:
  - `feature-local-llm-bundling.md` — 앱 번들에 로컬 모델 포함(초기 설치 대용량) vs 첫 실행 시 다운로드 선택 로직.
  - `feature-on-device-inference-harness.md` — 모바일 RN 브리지·iOS Metal·Android NNAPI 연결.
  - `feature-partner-e2e-sync.md` — 파트너 간 세션 공유 프로토콜 + 키 교환 UX.
  - `feature-offline-first-storage.md` — SQLite(암호화)·WatermelonDB·RxDB 선택, 충돌 해결 전략.
  - `feature-optional-cloud-telemetry.md` — 동의 기반 익명 관측성(Sentry 해싱·차분 프라이버시).

- **Hook 구성 (Branch A 특화 강화)**:
  - 기존 `output_secret_filter.py` 외에 `validate_no_external_data_egress.py` 신설 — 생성된 앱 코드에 대화 본문이 외부 엔드포인트로 전송되는 패턴(fetch/axios/HttpClient.post + conversation payload) 탐지 시 exit 2.
  - `validate_local_first_marketing_claim.py` — 마케팅 카피·스토어 설명·프라이버시 정책 파일이 "완전 로컬" 표현을 쓰는 경우, 실제 코드에 남은 외부 호출 목록을 diff로 자동 첨부. NO-5 방지.

- **테스트 전략 (하네스가 자동 생성)**:
  - RN Detox·iOS UITest·Android Espresso의 **airplane mode 테스트**: 네트워크 off 상태에서도 핵심 코칭 기능이 동작하는지 검증. 하네스가 CI 스크립트에 자동 주입.
  - 모델 응답 품질 측정은 **오프라인 골든셋**(합성 대화) 기반. 점수 하락 시 PR 차단.

### 4.2 하네스가 생성하는 앱 아키텍처 (Branch A, [L1] 설계)

관계 코칭 앱의 **최종 사용자 런타임**은 모바일 우선(iOS·Android), 웹 부차. Branch A에서는 대부분 기능이 기기 내부에서 완결된다.

- **레이어 구성**:
  - **UI 레이어**: React Native(단일 코드베이스 우선) 또는 네이티브(iOS Swift / Android Kotlin). v1은 RN 권장 — 개발 속도·하네스 자동 생성 난이도.
  - **상태 관리**: Zustand/Redux + 로컬 SQLite/SQLCipher (암호화 저장).
  - **AI 추론 레이어** (핵심):
    - `LocalLLMClient` — MLX(Apple)·MediaPipe Llama(Android)·llama.cpp 기반. 모델 로딩·프롬프트 템플릿·토큰 스트리밍·배터리 프로파일.
    - 모델 후보: EXAONE 3.5 7.8B(한국어 최상, KMMLU 63.7%), Solar Pro 10.7B(LG), Qwen 2.5 7B, Gemma 2 9B.
    - 양자화: 4bit 필수(모바일 메모리 제약). 8GB RAM 이상 기기에서도 빠듯함 — 7B가 현실적 상한.
  - **대화 오케스트레이터**: 상황 분류기(갈등 단계·감정 상태) → 프레임워크 선택(Gottman/NVC/IFS) → 프롬프트 조립 → 로컬 LLM 호출 → 안전 필터 후처리.
  - **안전 필터 (앱 내부)**: 생성 응답에 임상 언어·진단·처방이 포함되면 재생성 또는 대체 문구 삽입. 크라이시스 키워드 발견 시 1393/1366 안내 카드 강제 삽입.
  - **파트너 공유 레이어**: 사용자가 명시적으로 초대한 파트너와의 세션 공유. libsignal 기반 E2E + Perfect Forward Secrecy. 서버는 ciphertext relay 역할만.

- **데이터 저장 모델**:
  - 대화 세션: `sessions` 테이블 (SQLCipher 암호화). `created_at`·`persona_tag`·`conflict_stage`·`summary_local`(LLM 로컬 생성).
  - 원문 메시지: `messages` 테이블. **서버로 절대 전송 금지** 플래그.
  - 사용자 프로파일: `profile` (로컬). 개인화 파라미터(선호 프레임워크·갈등 패턴 요약).
  - 익명 관측성: `telemetry_events` (동의 시만 전송). 이벤트는 사전 해싱·차분 프라이버시 적용.

- **배포·업데이트**:
  - App Store·Play Store. 앱 크기 3-8GB(모델 번들 포함) — Apple 200MB 초기 다운로드 제한 고려하여 **온디맨드 리소스**(iOS) / **동적 feature**(Android) 활용.
  - 모델 업데이트는 OTA(in-app) 다운로드 + 체크섬 검증. 하네스가 업데이트 스크립트 자동 생성.

- **비용·수익 모델 (Branch A)**:
  - LLM 호출 비용 **0원** (기기 내부 계산). 전력·메모리는 사용자 부담.
  - 매출 구조는 구독 기반(월 9,900 / 연 79,000) + 프리미엄(월 24,900, 인간 코치 제한 접근). 상세는 T4 담당.
  - 서버 비용: 인증·결제·E2E relay·opt-in 텔레메트리에 한정 → 매우 낮음 (유저 1인당 월 50-200원 추정).

- **리스크**:
  - 한국어 로컬 LLM 품질이 관계 상담 난이도에서 Sonnet 대비 60-70% 수준(T4 §3.1). 페르소나 B(위기 first-time)는 품질 민감도 가장 높음 → 이탈 위험.
  - iOS 빌드 경로가 Linux 하네스에서 불가 — Expo EAS Cloud Build 또는 macOS 러너 필요(§7.6).
  - 배터리·발열 이슈 — 세션당 3-5분 연속 추론 시 모바일 발열 체감. UX 설계에서 로딩·분할 응답으로 완화 필요.
  - B2B 확장성 낮음. 기업 SSO·감사 로그·요금제가 로컬 앱에 부적합(T4 §8.1).

- **Pure Branch A 기각 사유**: `meta/discarded-options.md#D-1` 참조. 본 §4.2는 **Hybrid 아키텍처의 Tier 1(로컬 경량)** 컴포넌트로 재활용된다는 전제 아래 기술.

---

## §5. Branch B 상세 결론 (Cloud-Native Feature-Rich)

**가정**: 서버 중심 아키텍처, 대화 본문은 서버 측 암호화 저장·추론. E2E 암호화는 파트너 공유 채널 등 제한적으로 적용. AI 품질·크로스디바이스 연속성·코칭 연속성을 우선.

### 5.1 하네스 자체 구조 (Branch B 전제에 맞춘 [L0] 설계)

- **`.claude/agents/` 구성 (Branch B 특화)**:
  - `@cloud-cost-optimizer` (신규) — Anthropic·OpenAI·Google Gemini API 비용 분석·프롬프트 캐시·토큰 압축·동적 모델 라우팅.
  - `@backend-architect` (신규) — Supabase·Neon·Cloudflare Workers·Fly.io·AWS 등 런타임 선택·데이터 레지던시·스케일링 설계.
  - `@data-governance-designer` (신규) — PIPA 제23조 민감정보 국외이전 동의 UX·DPA 템플릿·감사 로그·데이터 보존/파기 정책.
  - `@observability-engineer` (신규) — 관측성·에러 추적·레이트 리밋 대응·SLO/SLA 설계.
  - 공통 재사용: `@conflict-psychology-researcher`, `@regulatory-compliance-auditor`, `@persona-empathy-simulator`, `@translator`, `@reviewer`, `@fact-checker`.

- **`workflows/` 카탈로그 (Branch B 전용 feature)**:
  - `feature-backend-api-gateway.md` — 인증된 앱 호출을 수신하여 LLM API로 라우팅하는 게이트웨이. Rate limit·비용 쿼터·모델 선택 정책 내장.
  - `feature-prompt-cache-optimization.md` — Anthropic prompt caching(2024 GA)로 시스템 프롬프트 캐시 → 토큰 90% 절감 패턴.
  - `feature-cross-device-sync.md` — 사용자 인증 기반 기기 간 세션 동기화. E2E 암호화 옵션 토글.
  - `feature-session-summary-retention.md` — 세션 장기 요약(개인화) 저장 + 사용자 요청 시 전량 삭제(GDPR Article 17, PIPA 제36조).
  - `feature-admin-dashboard.md` — 운영자용 대시보드(집계 통계만 노출, 개별 대화 열람 금지가 기본 원칙).
  - `feature-stripe-toss-billing.md` — Stripe + 토스페이먼츠 이중 결제. Apple/Google IAP 병행 (2022 인앱결제 강제 대응법 이후 외부결제 조건부 허용).

- **Hook 구성 (Branch B 특화 강화)**:
  - `validate_data_residency.py` (신규, Stop) — 백엔드 코드에 서울 리전(ap-northeast-2) 외 데이터 저장 경로가 있는지 AST 레벨 탐지. PIPA 제28조의8 국외이전 요건 회피.
  - `validate_dpa_coverage.py` (신규, PreToolUse on DB migration) — 신규 테이블이 민감정보 컬럼을 포함할 때 DPA(Data Processing Agreement)·RoPA(Record of Processing Activities) 업데이트 필요 알림.
  - `block_plaintext_conversation_storage.py` (신규, PostToolUse(Edit|Write)) — DB 스키마·ORM 모델에서 대화 본문 컬럼이 암호화(pgcrypto·AES·KMS) 없이 정의되면 exit 2.
  - 기존 `output_secret_filter.py`로 API 키·JWT·DATABASE_URL 유출 방지(이미 구현).

- **환경 변수·비밀 관리 (하네스 레벨)**:
  - Anthropic API 키는 로컬 `.env.local`(gitignored) + 프로덕션은 서버 시크릿 매니저(AWS Secrets Manager·Doppler·Cloudflare Secrets).
  - 하네스 자체 개발 시 Anthropic API 호출은 ADR 허용 범위 내. 그러나 실사용자 대화 데이터는 절대 투입 금지(NO-2).

- **CI/CD 통합**:
  - GitHub Actions에서 백엔드 → Fly.io/Cloudflare Workers/Supabase로 자동 배포. 하네스가 워크플로우 YAML 자동 생성.
  - DB 마이그레이션은 수동 승인 게이트 권장 — `validate_dpa_coverage.py`가 경고만 내고 인간이 최종 승인.

### 5.2 하네스가 생성하는 앱 아키텍처 (Branch B, [L1] 설계)

- **레이어 구성**:
  - **클라이언트**: React Native (모바일 우선) + Next.js (웹). 클라이언트는 경량 — 대화는 서버로 전송되어 처리.
  - **API 게이트웨이**: Cloudflare Workers 또는 Fly.io의 Hono/Elysia. JWT 인증·레이트 리밋·관측성·비용 쿼터.
  - **AI 오케스트레이션 서비스**:
    - `AnthropicAPIClient` (Claude Sonnet/Opus) — 주력. 한국어 관계 상담 품질 최상.
    - 대체/비용 최적화: Gemini 1.5 Pro·GPT-4 Turbo·로컬 호스팅 오픈소스(Solar/EXAONE on self-host).
    - 동적 라우팅: 대화 복잡도에 따라 Haiku(단순 응답)·Sonnet(기본)·Opus(위기·복잡 갈등) 자동 선택.
    - 프롬프트 캐시: 시스템 프롬프트·도메인 지식·사용자 프로파일 요약을 캐시 히트 형태로 전달.
  - **백엔드 저장소**:
    - PostgreSQL(Supabase/Neon) + pgcrypto. 대화 본문은 AES-256-GCM 컬럼 암호화. 키는 KMS 관리.
    - 벡터 DB(pgvector·Pinecone)는 v1 범위 밖(개인화 고도화 단계에서 도입).
  - **관측성·관리**:
    - Sentry(에러)·PostHog(제품 분석, PII 제거 설정)·Better Stack(uptime·로그).
    - LLM 비용 대시보드: 유저당 월 토큰·모델별 분포·크라이시스 세션 식별.

- **데이터 저장 모델**:
  - 서버 측 `users`·`sessions`·`messages`·`summaries`·`subscriptions` 테이블.
  - `messages.body` 컬럼: `bytea`(암호화). 애플리케이션 레이어에서 복호화 후 LLM 호출.
  - `audit_log`: 관리자 접근·삭제 요청 처리 기록. PIPA 제32조 + GDPR Article 30.
  - 파트너 공유: 별도 `shared_sessions` 테이블, 양측 사용자 합의 플래그 + 접근 제어.

- **보안·프라이버시 기본 설계**:
  - **Zero-Knowledge 옵션**(유료 tier) — 대화 본문을 클라이언트에서 암호화해 서버에 저장, 서버 측 복호화 없이 LLM 호출은 제한(별도 엔클레이브 경로 필요). v1은 기본 제공 아님, 로드맵.
  - **최소 수집 원칙**: 이메일·닉네임만 필수. 성별·나이·관계 유형은 선택. 결혼 상태·성 지향·종교 등 민감정보 **금지**(PIPA 제23조 회피).
  - **감사·삭제**: 사용자 대시보드에 "데이터 다운로드(JSON)"·"계정 삭제(즉시 비식별화)" 버튼 의무화.
  - **지역 제한**: 초기 서비스 지역 = 한국. 데이터 레지던시 = ap-northeast-2.

- **배포·업데이트**:
  - 앱 크기 경량(~50-100MB). OTA 업데이트(Expo Updates) 활용.
  - 서버 배포: 주 1-2회 릴리스 권장. 대규모 DB 마이그레이션은 blue-green 또는 rolling.

- **비용·수익 모델 (Branch B)**:
  - LLM 호출 비용: 세션당 400원(Sonnet) × 월 8-15 세션 = 3,200-6,200원/유저/월(T4 §3.2). 구독가의 30-60%를 원가가 차지 — freemium 전환율 민감.
  - 서버 인프라: 유저당 월 100-500원 예상(DB·관측성·게이트웨이).
  - 공헌이익: 유료 구독 9,900원 기준 월 유저당 3,000-5,000원 수준 — 스케일·캐시 최적화로 개선 필수.

- **리스크**:
  - PIPA 제23조 민감정보 + 국외이전 이중 동의 리스크(T4 §6.1). Anthropic API 서버 위치(미국)가 기본 전제. 별도 동의 플로우 필수.
  - BetterHelp 류 프라이버시 사고 선례(T2 §3.4, T4 §6.2). 광고 픽셀·SDK 도입 시 매우 조심. 기본 원칙: 광고 트래커 0개.
  - Apple/Google 심사 강화(Character.ai 2024 소송 이후). 심리·AI 동반자 카테고리 추가 검토. 심사 거부 대응 시간 2-6주 예비.
  - 네트워크 의존 → 오프라인 갈등 상황에서 응답 불가. UX에서 "안정적 Wi-Fi 권장"·간소 오프라인 모드(최근 세션 캐시 열람·프레임워크 가이드 읽기) 설계.

- **Pure Branch B 기각 사유**: `meta/discarded-options.md#D-2` 참조. 본 §5.2는 **Hybrid 아키텍처의 Tier 2·3(클라우드 Sonnet·Opus+인간)** 컴포넌트로 재활용된다는 전제 아래 기술.

---

## §6. Branch A vs B 비교 — Claude Code 실제 기능 기반 판단

Pure A·Pure B 어느 쪽도 단독으로는 "상업화 가능한 관계 코칭 앱"을 성립시키지 못한다는 결론(ADR-0005)에 도달하기까지의 비교 분석. 판단 기준은 관념적 선호가 아니라 **Claude Code로 실제 무엇을 생성·유지할 수 있는가**이다.

### 6.1 10개 축 비교표

| 축 | Branch A (Privacy-First Local) | Branch B (Cloud-Native) | 승자 | 비고 |
|---|---|---|---|---|
| 한국어 AI 품질 (관계 상담) | 60-70% (EXAONE 3.5 7.8B 기준, Sonnet=100) | 100% (Claude Sonnet 기준선) | B | T4 §3.1 근거 |
| 오프라인 가용성 | 완전 가용 | 불가 (캐시된 자료 열람만) | A | 페르소나 B 위기 순간 가치 |
| 프라이버시 신뢰 신호 | 강함 (기기 내 완결) | 약함 (E2E 옵션·DPA로 보완) | A | T3 §1.5 Consumer Reports 58% |
| 크로스디바이스 연속성 | 제한적 (파트너 공유 E2E 필요) | 강함 (사용자 계정 기반 자동) | B | |
| 파트너 공유 기능 구현 난이도 | 높음 (libsignal·키 교환 UX) | 낮음 (서버 중재) | B | T2 §4 매트릭스 |
| 모델 업데이트 난이도 | 높음 (OTA 3-8GB) | 낮음 (서버 배포) | B | T4 §3.1 |
| B2C→B2B 확장성 | 낮음 (SSO·감사·요금제 부적합) | 높음 (SaaS 표준) | B | T4 §8.1 |
| 규제 컴플라이언스 부담 | 낮음 (데이터 이동 최소) | 높음 (PIPA 제23조·국외이전·DPIA) | A | T4 §6.1 |
| 단기 매출 마진 | 높음 (LLM 원가 0) | 중간 (원가 30-60%) | A | T4 §3 |
| 심사·앱스토어 통과 | 낮은 마찰 (로컬 처리 어필) | 높은 마찰 (건강·AI 카테고리 엄격화) | A | T2 §3.4 |

**요약**: A는 "가치·마진·규제", B는 "품질·기능·확장성". 어느 축도 0점이 없어서 한 쪽을 선택하면 다른 축의 손실이 크다.

### 6.2 Claude Code 자동 생성 가능성 측면의 비교

하네스(Claude Code)가 각 Branch의 구성 요소를 얼마나 안정적으로 자동 생성·유지할 수 있는가를 기능별로 평가.

- **React Native UI/화면 로직**: 양 Branch 동일하게 **강함**. 공식 문서·오픈소스·템플릿 풍부. 하네스 워크플로우가 컴포넌트·훅·네비게이션을 높은 정확도로 생성.
- **백엔드 API (Branch B)**: **강함**. Hono/Express/Fastify 패턴 보편, TypeScript 타입 + ORM(Drizzle/Prisma) 정합성 검증까지 하네스가 수행 가능.
- **로컬 LLM 통합 (Branch A)**: **중간**. MLX·llama.cpp의 RN 바인딩은 비교적 최신 영역이라 공개 예제가 부족. 하네스가 초안은 생성해도 플랫폼별 크래시·메모리 이슈 디버깅은 인간 개입 필요성 높음.
- **libsignal·Noise Protocol 통합 (Branch A)**: **약함**. 암호 프로토콜은 오류 시 치명적이라 하네스 자동 생성보다 **전문가 감수** 위주. 하네스는 래퍼·테스트 스캐폴드 역할.
- **iOS/Android 빌드 (양 Branch)**: Claude Code 단독 불가 (§7.6). Expo EAS 경로 또는 macOS 러너 필수.
- **결제 통합 (양 Branch)**: **중간**. Stripe는 잘 알려져 있어 안정적. 토스페이먼츠·Apple IAP·Google Play Billing은 한국 특수 조건으로 인간 QA 필수.
- **관측성 + 프라이버시 필터 (Branch B)**: **중간**. Sentry·PostHog 설정 보편적이지만 PII 마스킹 규칙을 도메인 맞춤으로 설계해야 하며 하네스 단독 정확도 낮음.
- **DB 암호화 스키마 (Branch B)**: **중간**. pgcrypto·KMS 패턴 보편. 하네스가 생성하되 `validate_dpa_coverage.py` 류 Hook으로 검증 병행.

### 6.3 Pure A 또는 Pure B 단독 결론의 구조적 불가

- **Pure A 단독 불가**: 한국어 품질 격차가 페르소나 B의 가치 제안 파괴적. "위기 상황에서 도움이 됐다"는 첫 경험을 만들어야 하는데 로컬 LLM만으로는 확률적 실패.
- **Pure B 단독 불가**: 관계 대화 데이터는 민감정보의 농도가 극단적으로 높음. "대화가 어디로 가는가"에 대한 유저 불안이 초기 전환율을 직접 파괴(T3 §1.5).

### 6.4 Hybrid 수렴 결론 (ADR-0005)

4 teammate 전원이 독립적으로 수렴한 **3-tier Hybrid**:

- **Tier 1 (로컬 경량)**: 온디바이스 LLM(EXAONE 3.5 7.8B 등)이 첫 응답·감정 감지·요약·오프라인 모드 담당. 대화 본문은 서버 전송 없음.
- **Tier 2 (클라우드 Sonnet)**: 사용자 동의 기반, 복잡한 갈등·긴 맥락·프레임워크 심화 코칭에 Claude Sonnet 호출. 전송 전 PII 마스킹·사용자 확인.
- **Tier 3 (클라우드 Opus + 인간)**: 위기 감지·심각 갈등·유료 프리미엄 세션에서 Claude Opus + 인간 코치 에스컬레이션. 인간 개입은 법적 안전 테두리와 self-help 경계선 최종 보장.

이 Hybrid는 Branch A의 "프라이버시·마진·오프라인" 장점과 Branch B의 "품질·연속성·확장성" 장점을 층으로 분리한다. 사용자는 설정에서 **어느 Tier까지 허용할지 명시적 토글**할 수 있어야 한다 — 이 토글 자체가 프라이버시 신뢰 신호가 된다.

### 6.5 Hybrid의 하네스 구현 함의

- `workflows/feature-ai-tier-router.md` (신규) — 런타임에 Tier를 선택하는 라우팅 로직.
- `workflows/feature-consent-ux-per-tier.md` (신규) — Tier별 동의 UX. UI 문구·안내 카드·감사 로그.
- `workflows/feature-pii-masking-pre-cloud.md` (신규) — Tier 2·3 호출 직전 PII 마스킹·동의 상태 재확인.
- `@tier-routing-policy-designer` (신규 agent) — 라우팅 규칙·폴백·비용 임계선 설계 전담.
- 기존 Hook 세트 재사용 + `validate_tier_consent_flow.py` 신설.

---

## §7. Claude Code 실제 한계 — 공식 기능 범위와 실무 병목

하네스 설계에서 가장 자주 "되는 줄 알았는데 안 되는" 영역을 7개 축으로 정리. 각 한계는 **공식 사양 근거 + 실무 관찰 + 하네스 차원 우회책**을 함께 기록한다.

### 7.1 컨텍스트 윈도우 — 공칭 200K·확장 1M vs 실질 60-70K

- **공식 사양 (2026-04)**:
  - Claude Opus 4.7·Sonnet 4.6 — 공칭 200K 토큰. Opus 4.7은 1M 토큰 확장 옵션.
  - Claude Code는 시스템 프롬프트·CLAUDE.md·도구 결과·에이전트 출력을 모두 한 창에 쌓음.
- **실무 관찰**:
  - 워크플로우 한 번 실행에 **도구 결과**(Read·Bash·Grep)가 가장 빠르게 누적. Large repo grep 한 번에 5-15K 토큰.
  - Sub-agent 호출의 결과 반환도 그대로 컨텍스트에 들어옴.
  - 실질 **유효 활용 토큰은 60-70K** 수준. 그 이상이면 압축 트리거되거나 응답 품질 하락.
- **하네스 차원 우회**:
  - 원칙 2 (§1) — 기능 단위 workflow.md 카탈로그. 단일 workflow 하나가 40-60단계 내로 수렴.
  - Sub-agent 결과를 SOT 파일에 먼저 저장하고 **요약만** 메인 컨텍스트로 가져오는 패턴.
  - `generate_context_summary.py` (기존) — 증분 스냅샷 + Knowledge Archive로 과거 세션 참조 대체.
  - `restore_context.py` (기존) — RLM 포인터로 knowledge-index.jsonl 쿼리 기반 접근.

### 7.2 토큰 소비·구독 한도 — Max Plan·API 요금·5시간 rolling limit

- **공식 사양 (2026-04)**:
  - Claude Code 구독: Pro·Max 티어. Max Plan은 Opus/Sonnet 더 많은 할당. 단 **5시간 rolling limit** 존재.
  - API 직접 사용 시 per-token 과금 — Opus 4.7 ~$15/$75 per 1M tokens (input/output, 변동 가능), Sonnet 4.6 ~$3/$15.
  - Prompt caching: 시스템 프롬프트 캐시 적중 시 90% 할인.
- **실무 관찰**:
  - AgenticWorkflow 규모의 workflow 1회 실행 = 100K-500K 토큰(도구·에이전트 누적). Max Plan 5시간 한도에 2-5회 실행 수용.
  - 장시간 워크플로우는 중간에 한도 hit → 세션 대기 또는 API 전환 필요.
- **하네스 차원 우회**:
  - 토큰 예산 도입: `validate_retry_budget.py` (기존)에 전역 토큰 예산 항목 추가 검토.
  - 장시간 워크플로우는 체크포인트 설계 — 단계 완료 시 state 저장, 재시작 가능.
  - CLAUDE.md·agent md의 prompt caching 최대 활용 (변경 빈도 낮은 파일 우선).

### 7.3 Hooks — 조건 분기·우선순위·실패 전파

- **공식 사양 (2026-04)**:
  - Hook 이벤트: PreToolUse·PostToolUse·SessionStart·SessionEnd·PreCompact·Stop 등.
  - 각 hook은 도구·이벤트 매처(regex)와 커맨드(스크립트)로 구성.
  - exit code 0 = 정상 / 2 = 차단(LLM이 원인 메시지를 보게 됨) / 기타 = 에러로 보고.
- **실무 관찰**:
  - 여러 hook이 동시 매칭될 때 **순서는 settings.json 선언 순** — AgenticWorkflow는 `context_guard.py` 디스패처 통합으로 해결.
  - 복잡한 분기 로직(예: "Bash 도구 + 커맨드에 git + 브랜치가 main"인 경우만)은 hook 스크립트 내부에서 구현해야 함.
  - stderr 출력이 길면 LLM 컨텍스트 낭비 — 메시지 요약 중요.
- **하네스 차원 우회**:
  - `_context_lib.py` 공통 라이브러리로 hook 간 로직 공유.
  - Hook 테스트 파일(`_test_*.py`)로 회귀 방지 — 기존 output_secret_filter 131 테스트 패턴 확장.
  - 민감 도메인(관계 코칭)은 Hook을 **이중 방어**로 걸어 하나가 실패해도 보완되도록 설계.

### 7.4 Sub-agent 병렬 — Task tool 한 턴 5-10개 상한

- **공식 사양 (2026-04)**:
  - Task tool(`Agent`)은 한 응답에 여러 subagent_type 호출을 병렬 실행 가능.
  - 공식 문서 상 "병렬 가능"이라 명시하되 상한은 명시되지 않음.
  - 실험적 플래그 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`로 Agent Team 구성 가능.
- **실무 관찰**:
  - 실측 5-10개 병렬에서 안정. 이를 초과하면 **컨텍스트 주입 충돌·API rate limit** 발생.
  - Agent Team은 실험 단계 — 프로덕션 신뢰성 미검증.
  - 병렬 수가 많을수록 SOT 쓰기 충돌 가능성 증가(절대 기준 2).
- **하네스 차원 우회**:
  - NO-4의 대안: 기능 단위 순차 + 같은 feature 내 서브태스크만 ≤5 병렬.
  - 병렬 결과는 Orchestrator가 수집 후 머지하는 Fan-out/Fan-in 패턴.
  - 병렬 에이전트는 **자기 전용 디렉터리**(`workspace/<agent-id>/`)에만 쓰기 허용 — SOT 직접 수정 금지.

### 7.5 파일 쓰기·네트워크·쉘 — 권한 모델과 샌드박스

- **공식 사양 (2026-04)**:
  - Write·Edit·NotebookEdit 도구로 파일 작성. 경로 제한은 없고 사용자 OS 권한에 의존.
  - Bash 도구로 쉘 명령 실행. 네트워크·시스템 호출 가능.
  - Permission mode: acceptEdits·plan·bypassPermissions 등.
- **실무 관찰**:
  - 파괴적 명령(`rm -rf`, `git reset --hard`, `git push --force`) 실수가 치명적.
  - 네트워크 호출(curl·wget)은 개발 도구로는 유용하지만 **실사용자 데이터 유출** 통로가 될 수 있음.
  - 쉘에서 생성된 임시 파일(`/tmp/...`)에 시크릿 남을 수 있음.
- **하네스 차원 우회**:
  - `block_destructive_commands.py` (기존, 43 테스트) — 위험 명령 + 네트워크 유출 차단.
  - `output_secret_filter.py` (기존, 131 테스트) — 도구 결과에서 시크릿 탐지.
  - `security_sensitive_file_guard.py` (기존, 44 테스트) — 민감 파일 수정 경고.
  - 신규 `validate_no_external_data_egress.py` (§4.1) — 대화 본문 외부 전송 코드 차단.

### 7.6 CI/CD·모바일 빌드 — Claude Code 단독 불가 영역

- **공식 사양 (2026-04)**:
  - Claude Code는 빌드 도구가 아님. Bash로 외부 빌드 툴 호출만 가능.
  - iOS 빌드는 macOS Xcode 필수. Android는 JDK + Android SDK + Gradle.
- **실무 관찰**:
  - Linux 하네스 환경에서 iOS 빌드 **불가**.
  - 로컬 macOS가 있어도 사용자 PC 사양(CPU·디스크)에 따라 빌드 시간 비용 체감.
  - Android도 Gradle 다운로드·빌드 시간이 노이즈.
- **하네스 차원 우회**:
  - **Expo EAS Cloud Build** (권장) — iOS/Android 모두 클라우드 빌드. 하네스가 `eas build` 명령어만 호출.
  - **GitHub Actions macOS 러너** — 자체 CI 파이프라인 구성. 시간 비용 있음.
  - **로컬 Android only + iOS는 EAS** 이중화도 가능.
  - 하네스 워크플로우 `workflows/feature-mobile-build-pipeline.md`로 분리.

### 7.7 DB·인증·결제 모킹 — 하네스 환경의 외부 의존

- **공식 사양**: Claude Code는 Postgres·Redis·Stripe·OAuth 공급자를 직접 띄울 수 없음 (Bash 명령으로 컨테이너 실행만 가능).
- **실무 관찰**:
  - Postgres·Redis는 Docker Compose로 로컬 구동 용이. Supabase는 `supabase start` CLI 제공.
  - Stripe는 테스트 모드 키로 대부분 검증 가능. 한국 PG(토스페이먼츠)는 실 계좌 없이는 end-to-end 테스트 제한.
  - Apple IAP·Google Play Billing은 **실기기·실 스토어 빌드 필수** — 로컬에서 통합 테스트 어려움.
  - OAuth(Apple·Google·Kakao)는 Redirect URI·도메인 등록 필요. 로컬 개발은 `localhost` 허용 범위 내에서만.
- **하네스 차원 우회**:
  - `workflows/feature-mock-environment.md` (신규) — 로컬 개발용 모의 서비스(WireMock·Mockoon) 구성. 하네스가 JSON 목·지연 시뮬레이션까지 생성.
  - 결제 통합은 **단위·통합·E2E 분리 테스트 전략**: 단위는 모크, 통합은 Stripe 테스트 모드, E2E는 QA 단계 실기기.
  - Secret은 `.env.local`(gitignored) + 프로덕션은 서버 시크릿 매니저로 이원화. `output_secret_filter.py`가 유출 방어.

### 7.8 한계 종합과 하네스 설계 반영

- 위 7개 한계는 "하네스가 모든 것을 자동화할 수 없다"는 현실 인정의 근거.
- 자동화 우선 영역: UI·백엔드 API·DB 스키마·Hook/validator·워크플로우 문서·테스트 스캐폴드.
- 인간 개입 영역: iOS/Android 최종 빌드 승인·결제 실 환경 테스트·암호 프로토콜 감수·심리 전문가 콘텐츠 감수·법무 리뷰.
- `workflows/` 카탈로그는 자동화 가능 영역을 **명시적으로 구분**하고, 인간 개입 필요 단계를 `(human-gate)` 태그로 표기해야 한다.

---
