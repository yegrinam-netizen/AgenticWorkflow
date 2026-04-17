---
round: 1
round_label: initial-teammate
file_kind: meta
axis: round-metadata
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "이 파일은 1차 조사의 진입점. 질문 원문·축·teammate 식별·결과 링크를 포함."
---

# Round 01 — Initial Teammate Research

## 개요

- **차수**: 1
- **라벨**: initial-teammate
- **수행 시점**: 2026-04-17
- **수행 프레임워크**: `/prompt/prd_teammate_executable.md` (Fork-based 4 teammate 광범위 리서치)
- **수행 범위**: PHASE 1 (조사 에이전트 독립 실행). PHASE 2 토론·PHASE 3 수렴·PHASE 4 통합은 **미수행** (후속 차수에서 진행).

## 고정 전제 (ADR에서 확정)

- **개발 환경**: 사용자 로컬 PC, Claude Code CLI 기반 (Anthropic API 호출은 개발 도구로 허용)
- **제품 배포**: 미정, 상업화 가능 형태 필수, 배포처 변경 가능한 유연 아키텍처 요구
- **타겟**: B2C 개인 소비자 우선 → B2B 확장 (ADR-0001)
- **1순위 관계 유형**: 연인(dating couples). 홍보는 부부·고부·직장 동시 (ADR-0002)
- **법적 포지셔닝**: self-help coaching (임상 상담·의료 행위 회피) (ADR-0003)

## 가정 축 (ADR-0004)

**"Privacy-First Local-Capable (A) vs Cloud-Native Feature-Rich (B)"**

- Branch A: 앱이 유저 기기 로컬 중심 동작, 선택적 E2E 암호화 동기화. 프라이버시를 상업 해자로.
- Branch B: 서버 중심, E2E 암호화로 프라이버시 보완. AI 품질·크로스디바이스 우선.

각 teammate는 Branch A·B 양쪽을 **자신의 관점 안에서** 동시 탐색 (8 branch를 4 teammate에 내재화).

## teammate 식별 및 원본 질문

### T1 — Workflow Architect

- **slug**: workflow-architect
- **역할**: AgenticWorkflow 하네스 구조 + 하네스가 생성하는 앱 아키텍처 설계
- **원본 질문 (요지)**:
  > AgenticWorkflow 하네스의 워크플로우 구조를 설계. 이 하네스가 "상업화 가능한 관계 코칭 앱"을 자동 구현·유지·반복 개선할 수 있는 파이프라인을 만드는 게 목표. 하네스 구조 + 하네스가 생성하는 앱 아키텍처 둘 다 설계.
- **조사 항목**: 하네스 파이프라인 / CLAUDE.md·.claude/ 구조 / Hooks / 멀티에이전트 오케스트레이션 / 외부 도구·MCP / Claude Code 실제 한계 / Branch A vs B 아키텍처 차이
- **산출**:
  - raw: `raw/T1-workflow-architect.md`
  - summary: `summary/T1-workflow-architect.conclusions.md`

### T2 — Scenario Explorer

- **slug**: scenario-explorer
- **역할**: 사용 시나리오 지형도 + 경쟁 분석 + 시나리오 우선순위
- **원본 질문 (요지)**:
  > 이 앱이 커버할 사용 시나리오의 전체 지형 스캔. 경쟁 환경·대안 도구·시나리오 우선순위 결정. 홍보 범위(부부·고부·직장)와 기능 범위(연인) 비대칭 관리.
- **조사 항목**: 연인 갈등 시나리오 지형도 / 홍보용 다른 관계 유형 / 경쟁 지형(글로벌·한국·전통 상담) / 시나리오 × Branch 매트릭스 / 시나리오 우선순위 / self-help 경계선 / 플랫폼 가설
- **산출**:
  - raw: `raw/T2-scenario-explorer.md`
  - summary: `summary/T2-scenario-explorer.conclusions.md`

### T3 — Operator Analyst

- **slug**: operator-analyst
- **역할**: 최종 사용자(개인 소비자) 심리·행동·마찰 분석
- **원본 질문 (요지)**:
  > 이 앱을 실제로 사용하는 사람의 심리·행동 패턴·마찰 지점 분석. 관계 갈등 상황의 유저는 일반 SaaS 유저와 근본적으로 다름.
- **조사 항목**: 갈등 중 유저 심리 / 3개 페르소나 / 9단계 유저 여정 / 프라이버시 신뢰 신호 / 페르소나별 기능 요구 / 수용 가능 복잡도 / 감정 안전 설계 / 커뮤니티 리스크 / 상업화 수용성
- **산출**:
  - raw: `raw/T3-operator-analyst.md`
  - summary: `summary/T3-operator-analyst.conclusions.md`

### T4 — Sustainability Strategist

- **slug**: sustainability-strategist
- **역할**: 하네스 + 제품 장기 지속 가능성
- **원본 질문 (요지)**:
  > 장기 지속 가능성을 두 레벨에서 분석. (1) 하네스 지속성: Claude Code 구독 한도·유지보수·디버깅 부담. (2) 제품 지속성: 상용 론칭 후 LLM 비용·과금 모델·규제 리스크·팀 확장.
- **조사 항목**: 하네스 토큰 경제 / 하네스 유지보수 / 제품 LLM 비용(Branch A·B) / 과금 모델 / 경쟁사 과금 벤치마크 / 규제 리스크(한국·글로벌) / 데이터 거버넌스 / 확장 한계 / 장기 유지보수(1-3년) / 6·12·24개월 시나리오
- **산출**:
  - raw: `raw/T4-sustainability-strategist.md`
  - summary: `summary/T4-sustainability-strategist.conclusions.md`

## 공통 산출

- **파킹 로트 통합**: `parking-lot/round-01.parking-lot.md` (4 teammate 파킹 로트 집합)
- **후속 심층조사 항목 통합**: `follow-up-questions/round-01.follow-up.md`
- **고정 전제 정리**: `meta/assumptions.yaml`
- **커버 축 정리**: `meta/axis.yaml`
- **기각된 선택지**: `meta/discarded-options.md` (Pure A / Pure B)

## 4 teammate 독립 수렴 결론 (ADR-0005)

4 teammate 모두 Pure A·Pure B 단독 기각. **3-tier Hybrid** 아키텍처(Tier 1 로컬 경량 / Tier 2 클라우드 Sonnet / Tier 3 클라우드 Opus+인간)로 독립 수렴.

상세 근거 및 번복 조건: `cross-cutting/decisions-log.md#ADR-0005`.

## 수행되지 않은 단계 (다음 차수 후보)

- PHASE 2: Fork-based 4 토론 브랜치 (Workflow/Scenario/Operator/Sustainability 우선 토론)
- PHASE 3: 3 시나리오 수렴 (Deep/Selective/Minimal Automation)
- PHASE 4: 통합 — 1차 PRD 초안 + 후속 심층조사 실행 계획
- 그 외: `follow-up-questions/round-01.follow-up.md`의 항목들 (기술/외부연동/사용자행동/구조적리스크 4 카테고리)

## 로컬 실행 전제 준수 체크 (ABSOLUTE ANCHOR ②)

- **하네스**: 로컬 불변. Claude Code CLI가 사용자 PC에서 실행되며 Anthropic API 호출은 개발 도구로 허용 (ADR 범위 내).
- **제품**: 배포 유연. Branch B(Cloud-Native) 선택지는 **제품 배포 단계의 아키텍처 옵션**이지 하네스 실행 환경이 아님. 각 raw 파일 frontmatter에 `local_execution_compliance.harness: compliant` / `product: deployment-flexible`로 이원 표기.
- **경계선 검토 결과**: 4 teammate 산출물 중 하네스 실행 환경을 클라우드로 옮기자는 제안 **없음**. 로컬 실행 전제 흔들림 **없음**.
