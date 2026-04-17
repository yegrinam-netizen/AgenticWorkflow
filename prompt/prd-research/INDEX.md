---
file_kind: index
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "전체 PRD 사전 리서치 색인. N차 확장 구조."
  - "각 차수는 round-NN-label/ 하위 폴더."
---

# PRD 사전 리서치 — INDEX

> **프로젝트**: 관계 갈등 self-help coaching 앱 (AgenticWorkflow 하네스 기반).
> **목적**: PRD.md 작성 전 사전 심층조사 기록 + N차 확장 가능한 구조.
> **최상위 원칙**: ABSOLUTE ANCHOR ① (품질 절대우선), ② (로컬 실행 불변: 하네스는 로컬 PC·Claude Code 고정).

---

## 폴더 구조

```
prompt/prd-research/
├── INDEX.md                                    ← 이 파일
├── README.md                                   ← 사용법·확장 규칙
├── schema/
│   └── metadata-schema.yaml                    ← frontmatter 스키마
├── cross-cutting/                              ← 차수 무관 공통 자산
│   ├── glossary.md                             ← 용어 사전
│   ├── decisions-log.md                        ← ADR-0001~0005
│   └── unresolved-axes.md                      ← R1~R12 미결 축
│
├── round-01-initial-teammate/                  ← 1차 조사
│   ├── ROUND.md                                ← 차수 맥락·방법론·기준
│   ├── meta/
│   │   ├── assumptions.yaml                    ← 가정·전제
│   │   ├── axis.yaml                           ← 조사 축
│   │   └── discarded-options.md                ← D-1~D-9 기각 선택지
│   ├── raw/                                    ← 원문 100% 보존
│   │   ├── T1-workflow-architect.md
│   │   ├── T2-scenario-explorer.md
│   │   ├── T3-operator-analyst.md
│   │   └── T4-sustainability-strategist.md
│   ├── summary/                                ← 결론 발췌
│   │   ├── T1-workflow-architect.conclusions.md
│   │   ├── T2-scenario-explorer.conclusions.md
│   │   ├── T3-operator-analyst.conclusions.md
│   │   └── T4-sustainability-strategist.conclusions.md
│   ├── parking-lot/
│   │   └── round-01.parking-lot.md             ← 통합 파킹 (T1~T4)
│   └── follow-up-questions/
│       └── round-01.follow-up.md               ← 통합 후속 질문 (T1~T4)
│
└── round-02-*/                                 ← (미생성) 2차 이상 확장
```

---

## Round 01 — Initial Teammate (완료)

**차수 라벨**: initial-teammate
**생성일**: 2026-04-17
**teammate 수**: 4 (T1-T4)
**조사 축**: 23개 (axis.yaml 참조)
**산출물**: raw 4 + summary 4 + parking-lot 1 + follow-up 1 + meta 3 = 13 파일

### 팀원별 역할

| ID | Slug | 조사 초점 | Raw | Summary |
|---|---|---|---|---|
| T1 | workflow-architect | Claude Code 하네스 구조·Dual-Layer·워크플로우 카탈로그·Branch A/B 기술 | [raw](round-01-initial-teammate/raw/T1-workflow-architect.md) | [summary](round-01-initial-teammate/summary/T1-workflow-architect.conclusions.md) |
| T2 | scenario-explorer | 관계 갈등 시나리오·페르소나·경쟁 지형·Crisis Referral | [raw](round-01-initial-teammate/raw/T2-scenario-explorer.md) | [summary](round-01-initial-teammate/summary/T2-scenario-explorer.conclusions.md) |
| T3 | operator-analyst | 유저 심리·여정·프라이버시 신호·UX·감정 안전 | [raw](round-01-initial-teammate/raw/T3-operator-analyst.md) | [summary](round-01-initial-teammate/summary/T3-operator-analyst.conclusions.md) |
| T4 | sustainability-strategist | 하네스 경제·제품 LLM 비용·과금·규제·확장·장기 유지 | [raw](round-01-initial-teammate/raw/T4-sustainability-strategist.md) | [summary](round-01-initial-teammate/summary/T4-sustainability-strategist.conclusions.md) |

### 차수 통합 산출물

- **ROUND.md**: [round-01-initial-teammate/ROUND.md](round-01-initial-teammate/ROUND.md) — 차수 맥락·조사 방법론·완료 기준.
- **가정·전제**: [meta/assumptions.yaml](round-01-initial-teammate/meta/assumptions.yaml).
- **조사 축**: [meta/axis.yaml](round-01-initial-teammate/meta/axis.yaml).
- **기각 선택지**: [meta/discarded-options.md](round-01-initial-teammate/meta/discarded-options.md) — D-1~D-9.
- **파킹 로트**: [parking-lot/round-01.parking-lot.md](round-01-initial-teammate/parking-lot/round-01.parking-lot.md) — P-T1~P-T4 51 항목.
- **후속 질문**: [follow-up-questions/round-01.follow-up.md](round-01-initial-teammate/follow-up-questions/round-01.follow-up.md) — Q1-T1~Q6-T4 + QC-1~6.

---

## Cross-cutting (차수 무관)

- **용어 사전**: [cross-cutting/glossary.md](cross-cutting/glossary.md) — Branch A/B, 3-tier Hybrid, 페르소나, Flooding, PIPA 조항 등.
- **결정 로그**: [cross-cutting/decisions-log.md](cross-cutting/decisions-log.md) — ADR-0001~0005.
  - ADR-0001: Dual-Layer [L0]/[L1] 분리.
  - ADR-0002: workflows 카탈로그 (기능 단위).
  - ADR-0003: Crisis Referral Protocol.
  - ADR-0004: 페르소나 B (준호) 1순위.
  - ADR-0005: 3-tier Hybrid 수렴.
- **미결 축**: [cross-cutting/unresolved-axes.md](cross-cutting/unresolved-axes.md) — R1~R12.
- **스키마**: [schema/metadata-schema.yaml](schema/metadata-schema.yaml) — frontmatter 필수·선택 필드.

---

## 향후 차수 (N차 확장 계획)

### Round 02 — Deep Dive (예정)

**트리거**: Round 01 follow-up 질문 중 우선순위 Q1-T4 (PIPA 유권해석), Q3-T4 (로컬 LLM 벤치마크), QC-1 (페르소나 B × Branch UX) 등 활성화 시 개시.

**권장 teammate 재설계** (follow-up §"차수 2 권장 teammate 재설계"):
- T5 Legal Counsel (신규).
- T6 Data Scientist (신규).
- T7 Growth PM (T3 진화).
- T8 Clinical Advisor (T2 진화).
- T1·T4는 유지·심화.

### Round 03+ — 상황 기반

특정 결정 이벤트 발생 시 새 차수 개시:
- PIPA 하위법령 개정.
- Claude Code major version update.
- 경쟁사 전략 변경.
- MAU 10K 도달.
- B2B 첫 계약.
- Series A 준비.

---

## 빠른 참조 인덱스

### 결정 찾기
- **Branch A vs B 결정 근거** → T1 §4·§5·§6 + T4 §3·§4 + ADR-0005.
- **페르소나 우선순위 근거** → T3 §2 + ADR-0004.
- **Crisis Referral 프로토콜** → T2 §7 + ADR-0003.
- **규제 리스크 전체** → T4 §6·§7.
- **하네스 운영 비용** → T4 §1·§2.

### 기각 선택지 찾기
- **Pure Branch A 기각** → D-1.
- **Pure Branch B 기각** → D-2.
- **단일 거대 workflow 기각** → D-3.
- **Usage-based 과금 기각** → D-4.
- **Sub-agent 50+ 병렬 기각** → D-5.
- **Claude Code 앱 런타임 오인 기각** → D-6.
- **사용자 대화 Claude 투입 기각** → D-7.
- **파워 유저 우선 기각** → D-8.
- **자유 게시판 기각** → D-9.

### 주요 수치·데이터
- **Claude Max Plan 비용** → T4 §1.
- **3-tier Hybrid 세션 비용** → T4 §3.3.
- **경쟁사 가격** → T4 §5.
- **페르소나별 지불 의향** → T3 §10 + T4 §4.4.
- **12개월 재무 시나리오** → T4 §11.

---

## 완결성 체크리스트

- [x] 4 teammate raw 보고서 (T1-T4).
- [x] 4 teammate summary 결론 발췌.
- [x] 파킹 로트 통합 (51 항목).
- [x] 후속 질문 통합 (24 항목 + QC 6).
- [x] 기각 선택지 기록 (D-1~D-9).
- [x] ADR 결정 로그 (ADR-0001~0005).
- [x] Glossary·스키마·미결 축.
- [x] INDEX.md·README.md.
