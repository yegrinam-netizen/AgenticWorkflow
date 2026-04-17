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
