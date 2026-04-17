---
file_kind: readme
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "PRD 사전 리서치 폴더 사용법·확장 규칙."
  - "N차 조사 확장 시 반드시 이 README 규칙 준수."
---

# PRD 사전 리서치 — README

> **목적**: 관계 갈등 self-help coaching 앱의 PRD.md 작성 전 **심층 사전 리서치 기록소**.
> **구조**: N차 반복 가능. 각 차수는 독립된 시점·방법론·teammate 구성.
> **원칙**: ABSOLUTE ANCHOR ①·② 엄수 + 100% 보존 + 단일 파일 SOT.

---

## 1. 왜 이 폴더가 존재하는가

PRD.md는 **합의된 결정을 기록**하지만, 그 결정에 도달하기까지의 **조사 과정·기각 이력·미결 쟁점**은 PRD에 담기 어렵다.
본 폴더는:

- **투명성**: 왜 A가 아닌 B를 선택했는지 추적 가능 (discarded-options).
- **재사용성**: 2차·3차 조사에서 1차 결과를 참조·검증·확장.
- **조직 학습**: teammate·Orchestrator가 산출한 raw·summary 재활용.
- **규제 대응**: PIPA·GDPR 감사 시 설계 의사결정 문서화 증명.

---

## 2. ABSOLUTE ANCHOR (깨면 안 되는 원칙)

### ① 품질 절대우선
> 조사의 깊이·폭·정확도가 절대 기준. 속도·토큰·편의는 이유가 될 수 없다.

- 분량 제한 없음.
- raw 파일은 **100% 보존** (요약으로 대체 불가).
- 의심스러우면 파킹 로트·후속 질문으로 기록.

### ② 로컬 실행 불변 (Dual-Layer)
> AgenticWorkflow **하네스는 로컬 PC·Claude Code 고정**이다. Branch A/B·Hybrid는 [L1] 앱 런타임의 선택지일 뿐.

- 모든 파일 frontmatter `local_execution_compliance.harness: compliant` 명시.
- `product: deployment-flexible` 는 [L1] 앱 레이어가 Branch A / B / Hybrid 어느 것이든 될 수 있음을 의미.
- 이 원칙 위반 내용은 **자동 기각**.

---

## 3. 폴더 구조 (표준)

```
prompt/prd-research/
├── INDEX.md                                ← 전체 색인
├── README.md                               ← 이 파일
├── schema/metadata-schema.yaml             ← frontmatter 필수 필드
├── cross-cutting/                          ← 차수 무관
│   ├── glossary.md
│   ├── decisions-log.md                    ← ADR
│   └── unresolved-axes.md
└── round-NN-label/                         ← 각 차수
    ├── ROUND.md                            ← 차수 맥락
    ├── meta/
    │   ├── assumptions.yaml
    │   ├── axis.yaml
    │   └── discarded-options.md
    ├── raw/Tn-slug.md                      ← teammate별 원문
    ├── summary/Tn-slug.conclusions.md      ← 결론 발췌
    ├── parking-lot/round-NN.parking-lot.md
    └── follow-up-questions/round-NN.follow-up.md
```

**필수 파일**: INDEX.md, README.md, schema/, cross-cutting/, round-NN-label/{ROUND.md, meta/, raw/, summary/, parking-lot/, follow-up-questions/}.

---

## 4. Frontmatter 스키마 (필수)

모든 `.md` 파일 맨 위에 다음 필드를 포함해야 한다:

```yaml
---
round: <숫자 or "N/A">
round_label: <차수 라벨>
file_kind: raw | summary | meta | parking-lot | follow-up | index | readme | cross-cutting
# (teammate 파일인 경우)
teammate_id: T1~Tn
teammate_slug: <slug>
axes:
  - <axis-key>
# 공통
generated_at: YYYY-MM-DD
local_execution_compliance:
  harness: compliant        # 반드시 compliant
  product: deployment-flexible | branch-a-only | branch-b-only | hybrid
related_files:
  - <상대 경로>
notes:
  - "<설명>"
---
```

**schema 상세**: [schema/metadata-schema.yaml](schema/metadata-schema.yaml).

---

## 5. 새 차수 개시 방법

### 5.1 트리거

다음 중 하나일 때 새 차수 개시:
- 기존 차수의 follow-up 질문 중 **3+ 항목**이 동시에 조사 필요.
- 외부 이벤트 (규제·경쟁사·기술 변화) 발생.
- 특정 MAU·매출 마일스톤 도달.
- 설계 결정 (ADR) 번복 필요 상황.

### 5.2 폴더 생성

```bash
mkdir -p prompt/prd-research/round-NN-label/{meta,raw,summary,parking-lot,follow-up-questions}
```

**라벨 규칙**: `initial-teammate` → `deep-dive` → `legal-review` → `market-validation` → … (내용 식별 가능).

### 5.3 ROUND.md 작성

각 차수 루트에 `ROUND.md` 생성. 필수 섹션:
- 차수 목적·맥락.
- 이전 차수로부터 이어받은 follow-up·parking-lot 항목.
- 본 차수 teammate 구성·역할.
- 조사 방법론.
- 완료 기준.

### 5.4 meta/ 파일

- **assumptions.yaml**: 이번 차수의 가정·전제.
- **axis.yaml**: 조사 축 목록 (T1~Tn의 axes 필드 집합).
- **discarded-options.md**: 이번 차수에서 기각된 선택지 (D-NN).

### 5.5 teammate 산출물

- **raw/Tn-slug.md**: 각 teammate 원문 보고서 (100% 보존).
- **summary/Tn-slug.conclusions.md**: 결론·권고만 발췌.

### 5.6 집계 파일

- **parking-lot/round-NN.parking-lot.md**: 전체 teammate 파킹 통합.
- **follow-up-questions/round-NN.follow-up.md**: 전체 teammate 후속 질문 통합.

### 5.7 cross-cutting 업데이트

- 새 결정이 있으면 `decisions-log.md`에 ADR 추가.
- 새 용어는 `glossary.md`에 반영.
- 미결 축 변경 시 `unresolved-axes.md` 갱신.

### 5.8 INDEX.md 갱신

새 차수 추가 시 `INDEX.md`에 해당 섹션 추가.

---

## 6. 100% 보존 원칙

- **raw/**는 절대 편집 금지 (오탈자·사실 오류도 별도 정오표로 대응).
- **summary/**는 raw에서 발췌. raw를 **대체**하지 않고 **요약**.
- **discarded-options.md**는 기각 근거와 함께 보존. 부활 조건 명시.
- **parking-lot**은 미결 사항 **버리지 않고** 기록.

---

## 7. 조사 기록 위반 예시 (해선 안 되는 것)

- ❌ raw를 summary로 덮어쓰기.
- ❌ 기각 선택지 삭제 (근거 함께 보존해야 함).
- ❌ 특정 결론에 불리한 데이터 제거.
- ❌ frontmatter 없는 `.md` 파일 추가.
- ❌ `local_execution_compliance.harness: compliant` 이외 값 사용 (하네스 로컬 불변).
- ❌ N차 조사 진행 시 N-1차 파일 수정 (별도 정오표만 허용).

---

## 8. PRD.md 작성 시 본 폴더 활용

PRD.md는 본 폴더를 **참조 기반**으로 작성한다:

- 각 결정에 해당 ADR 번호 인용 (`decisions-log.md`).
- 기각 선택지 언급 시 D-NN 링크.
- 상세 데이터·근거는 raw/summary 링크.
- 미결 사항은 follow-up 링크로 "차수 NN에서 해결 예정" 명시.

**PRD.md 자체는 본 폴더와 분리**. PRD는 합의된 결정 기록, 본 폴더는 조사 기록.

---

## 9. 관련 문서

- **프로젝트 헌법**: [/CLAUDE.md](../../CLAUDE.md).
- **Agents 공통 지시**: [/AGENTS.md](../../AGENTS.md).
- **워크플로우 설계 스킬**: `.claude/skills/workflow-generator/`.
- **학술 글쓰기 스킬**: `.claude/skills/doctoral-writing/`.

---

## 10. 변경 이력

| 날짜 | 변경 | 담당 |
|---|---|---|
| 2026-04-17 | Round 01 완료. 4 teammate × 13 파일 + cross-cutting 3 + schema 1 + INDEX + README. | AgenticWorkflow Orchestrator |
