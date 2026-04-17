---
round: 1
round_label: initial-teammate
file_kind: summary
teammate_id: T1
teammate_slug: workflow-architect
source_raw: ../raw/T1-workflow-architect.md
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "T1 raw 파일의 결론 섹션 발췌본. 판단 근거·데이터는 raw 참조."
  - "Orchestrator·종합 단계에서 빠른 참조용."
---

# T1 — Workflow Architect 결론 요약

> **원문**: [../raw/T1-workflow-architect.md](../raw/T1-workflow-architect.md)
> 아래는 결론·권고·의사결정 근거 핵심만 추린 요약. **증거·데이터·대안 평가는 raw 참조**.

---

## C1. 핵심 설계 원칙 Top 3

1. **Dual-Layer 불변**: [L0] 하네스(로컬 PC·Claude Code 고정) / [L1] 앱(Branch A/B 선택). 이 경계를 깨는 어떤 선택지도 자동 기각.
2. **workflows 카탈로그 (기능 단위)**: 단일 거대 workflow.md 금지. `workflows/feature-*.md` + 루트 `roadmap.yaml`.
3. **Hook 실행레벨 불변성**: `.claude/settings.json` + 21+ Python 스크립트가 SOT·품질·안전 보장. 우회 금지.

## C2. 필수 구성 요소 C1-C5

- **C1**: 루트 CLAUDE.md / AGENTS.md / soul.md / DECISION-LOG.md (헌법).
- **C2**: `.claude/hooks/scripts/` (SOT·검증·안전 Python 레이어).
- **C3**: `.claude/agents/` (translator·reviewer·fact-checker 3종).
- **C4**: `.claude/skills/workflow-generator`, `doctoral-writing` (스킬 2종).
- **C5**: `docs/protocols/` (autopilot·quality-gates·ulw·context-preservation·code-change 5종).

**의존성**: C1 → C2 → C3·C4·C5 순차 구축. C2 없이 C3·C4·C5는 무효.

## C3. 피해야 할 구조 (NO-1~NO-6)

- **NO-1**: 단일 거대 workflow.md.
- **NO-2**: 사용자 대화를 Claude Code 컨텍스트 직접 투입 (PIPA·Anthropic Usage Policy 위반).
- **NO-3**: Hook 스크립트 bypass (exit 0 강제).
- **NO-4**: Sub-agent 50+ 동시 병렬 (실측 상한 5-10).
- **NO-5**: "완전 로컬" 마케팅 + 실제 API 호출 (표시·광고 공정화법 기만).
- **NO-6**: Claude Code를 앱 런타임으로 오인 (구조적 불가).

## C4. Branch A 결론 (Privacy-First Local-Capable)

- **하네스**: Claude Code 유지 (변화 없음).
- **앱**: React Native + on-device LLM (EXAONE 3.5 7.8B 등).
- **핵심 이점**: 프라이버시 해자·오프라인 동작·PIPA 제28조의9 회피.
- **핵심 제약**: 한국어 AI 품질 상한 (Sonnet 대비 60-70%)·iOS 빌드 Linux 불가·B2B 확장 약함·앱 크기 3-5GB.

## C5. Branch B 결론 (Cloud-Native Feature-Rich)

- **하네스**: Claude Code 유지.
- **앱**: React Native + Anthropic API (Sonnet 주력).
- **핵심 이점**: 최고 품질 대화·B2B 친화·앱 가벼움.
- **핵심 제약**: MAU 10K에서 월 LLM 비용 300-800만원·PIPA 국외이전 장벽·프라이버시 해자 약함·오프라인 불가.

## C6. Branch A vs B 10축 매트릭스 → Hybrid 수렴

10축 비교에서 A·B 모두 **pure 형태는 실패**. Tier 1 로컬 + Tier 2 Sonnet + Tier 3 Opus+인간의 **3-tier Hybrid** (ADR-0005)로 수렴.

## C7. Claude Code 실제 한계 7종

- **C-1**: 컨텍스트 윈도우 200K 공칭 / 60-70K 실질.
- **C-2**: 5시간 rolling limit (Max $200 기준 일 4-8 워크플로우).
- **C-3**: Hooks 15종 이벤트, 21+ 스크립트.
- **C-4**: Task tool 병렬 5-10.
- **C-5**: 파일 크기 권장 ≤ 2000 lines (Read tool).
- **C-6**: iOS 빌드 Linux 하네스 불가 (Expo EAS / macOS 별도).
- **C-7**: 런타임 모킹 시스템 취약 → 실제 실행 우선.

## C8. 우선순위 결론 (Architect 관점)

- **1순위**: 하네스 SOT·Hook 안정화 (없으면 모든 것 실패).
- **2순위**: workflows 카탈로그 설계 (feature 단위).
- **3순위**: Branch A/B 분기는 [L1] 앱 레이어 결정 — 하네스와 분리.

---

## 상호 참조

- **파킹 로트**: [raw §8 P-T1-1~19](../raw/T1-workflow-architect.md#§8) 또는 [../parking-lot/round-01.parking-lot.md](../parking-lot/round-01.parking-lot.md).
- **후속 질문**: [raw §9 Q1-Q6](../raw/T1-workflow-architect.md#§9) 또는 [../follow-up-questions/round-01.follow-up.md](../follow-up-questions/round-01.follow-up.md).
- **관련 결정**: ADR-0001 (Dual-Layer), ADR-0002 (workflows 카탈로그), ADR-0005 (3-tier Hybrid).
- **기각 선택지**: D-1 (Pure Branch A), D-3 (단일 거대 workflow), D-6 (Claude Code 런타임 오인).
