---
round: 1
round_label: initial-teammate
file_kind: meta
axis: discarded-options
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "1차 조사에서 검토되었으나 기각된 선택지 기록."
  - "종합 단계에서 '왜 이 선택지를 안 했는가' 추적을 위해 보존."
---

# Discarded Options — 1차 조사 기각 선택지

> 기각된 선택지는 버리지 않고 **기각 근거와 함께** 보존한다. 2차 이상 조사에서 조건이 바뀌면 부활 가능.

---

## D-1. Pure Branch A — Privacy-First Local-Only (완전 로컬, 클라우드 없음)

- **기각 시점**: round-01 수렴 단계 (ADR-0005)
- **기각 근거**:
  - **한국어 AI 품질 상한**: Llama 3.1 8B / EXAONE 3.5 7.8B / Qwen 2.5 7B 등 7-8B 로컬 모델은 KMMLU 36-64% 수준. Claude Sonnet 78% 대비 격차 큼. 관계 갈등 상담의 감정 뉘앙스·한국어 어조·안전 거버넌스에서 60-70% 수준 (T4 §3.1).
  - **iOS 빌드 경로 병목**: Linux 하네스에서 iOS 빌드 불가. Expo EAS Cloud Build 또는 macOS 러너 별도 필요 (T1 §7.6).
  - **B2B 확장성 낮음**: 기업 SSO·감사 로그·요금제가 로컬 앱에 부적합 (T1 §6 비교표, T4 §8.1).
  - **모델 업데이트 부담**: 앱 크기 3-8GB, OTA 배포 시 유저 경험 손상 (T4 §3.1, §10 Branch A 시나리오).
  - **파트너 간 공유 기능 복잡**: E2E P2P 동기화 프로토콜 필요 (T2 §4 매트릭스).
  - **결제·관측성도 결국 네트워크**: "편의상 클라우드 API 1개만" 허용의 순간 "관계 대화 절대 외부로 안 나감" 마케팅 클레임이 거짓말화 → 표시·광고 공정화 법상 기만적 표시 (T1 §3 NO-5).
- **부활 조건**:
  - 로컬 LLM(EXAONE 3.5 7.8B 또는 후속) 한국어 관계 상담 품질이 Sonnet 대비 ≥ 90% 달성 + macOS 빌드 경로 확보 + B2C 전용 포커스 유지.

---

## D-2. Pure Branch B — Cloud-Native Only (완전 클라우드, 로컬 데이터 없음)

- **기각 시점**: round-01 수렴 단계 (ADR-0005)
- **기각 근거**:
  - **프라이버시 해자 약화**: Consumer Reports 2023 조사 관계 앱 사용자 58%가 "파트너가 볼까 걱정" — 일반 앱의 4배 (T3 §1.5). 클라우드 전용은 이 불안에 직접 대응 불가.
  - **PIPA 제23조 민감정보 국외이전 부담**: Anthropic API 사용 시 명시적 별도 동의 + DPIA 필수. 유저 거부 시 서비스 불가 (T4 §6.1, §7).
  - **BetterHelp형 프라이버시 사고 리스크**: 2023 FTC $7.8M 합의 선례. 건강 데이터가 Facebook 광고주로 공유된 사건 (T2 §3.4, T4 §6.2).
  - **Claude API 비용 선형 증가**: 세션당 약 400원(Sonnet) × 월 유저당 8-15 세션 = 3,200-6,200원/유저/월. 저가 freemium 모델과 충돌 (T4 §3.2).
  - **실시간 갈등 de-escalation 네트워크 의존**: 오프라인 불가, 응답 지연 체감 시 위기 순간 가치 상실 (T2 §4 매트릭스).
  - **Apple/Google 심사 특히 심리 카테고리 엄격화**: Character.ai 2024 소송 이후 Apple이 AI 동반자 앱 심사 강화 (T2 §3.4, T4 §6.2).
- **부활 조건**:
  - 로컬 LLM 품질이 Sonnet 대비 50% 이하로 실측 + 프라이버시 신뢰 신호 다른 방법으로 확보 + 대형 자본 확보 시.

---

## D-3. 단일 거대 workflow.md로 "앱 전체"를 한 번에 생성

- **기각 시점**: round-01 (T1 §3 NO-1)
- **기각 근거**:
  - `state.yaml`의 `current_step`이 200+로 폭증.
  - 재시도 예산(`validate_retry_budget.py` RB1-RB3)이 의미 상실.
  - 컨텍스트 윈도우 소진 확실 (T1 §7.1).
- **대안 채택**: `workflows/feature-conflict-translator.md`, `workflows/infra-auth.md` 등 **기능 단위 workflow.md 카탈로그** + 루트에 `roadmap.yaml`.
- **부활 조건**: 없음 (구조적 실패 확실).

---

## D-4. Usage-based 과금 (세션·토큰 단위 결제)

- **기각 시점**: round-01 (T4 §4.3, §11)
- **기각 근거**:
  - **Replika 2023 선례**: usage-based → subscription 전환. 이유는 "유저가 위기 순간 과금 불안으로 사용 주저 → 제품 가치 훼손" (T4 §4.3).
  - 관계 위기에 "토큰 부족" 메시지는 제품 파괴 수준.
  - 페르소나 B(위기 first-time)의 불안 증폭.
- **대안 채택**: Freemium subscription (월 9,900 / 연 79,000) + 프리미엄 tier (월 24,900) + Hybrid 토큰팩 옵션.
- **부활 조건**: 대기업 B2B 전용 종량제 계약 (일반 B2C는 영구 금지).

---

## D-5. Sub-agent 50+ 동시 병렬

- **기각 시점**: round-01 (T1 §7.4)
- **기각 근거**:
  - Task 도구 공식 한 턴 병렬 실측 상한 **5-10개**.
  - 이 이상은 컨텍스트 주입 충돌 + rate limit.
  - Agent Team (`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`) 실험적 기능 — 프로덕션 신뢰성 미검증.
- **대안 채택**: 기능 단위 순차 실행 + 티켓 단위 Agent Team(5 teammate 이하).
- **부활 조건**: Agent Team GA 전환 + 실측 안정성 확인.

---

## D-6. Claude Code를 최종 사용자 앱의 런타임으로 가정

- **기각 시점**: round-01 (T1 §3 NO-6)
- **기각 근거**:
  - Claude Code는 **개발 도구**. 사용자 앱 안에 Claude Code가 동작하지 않음.
  - 앱 런타임은 Anthropic API(Branch B) 또는 로컬 LLM(Branch A).
  - 하네스는 그것을 호출하는 앱 코드를 **생성**할 뿐.
- **대안 채택**: Dual-Layer 개념([L0]=하네스, [L1]=앱)으로 경계 명시.
- **부활 조건**: 없음 (기술적으로 불가능).

---

## D-7. 사용자 대화 데이터를 Claude Code 컨텍스트에 직접 투입 (개발 단계)

- **기각 시점**: round-01 (T1 §3 NO-2)
- **기각 근거**:
  - 하네스 개발 중이라도 실 사용자 대화 샘플을 Claude에 붙여넣는 순간 Anthropic 서버로 전송.
  - `output_secret_filter.py`가 시크릿은 차단하나 PII 대화는 필터하지 않음.
  - Anthropic Usage Policies + 한국 PIPA 제22조 동의 위반.
  - Branch A 전제("관계 대화는 외부로 안 나간다")의 신뢰성이 개발 단계부터 깨짐.
- **대안 채택**: 개발 단계는 **합성 대화 데이터**(가상의 커플 대화 생성기) 전용.
- **부활 조건**: 없음 (법적·윤리적으로 영구 금지).

---

## D-8. 사전 확정 페르소나: 파워 유저(민지) 우선

- **기각 시점**: round-01 (T3 §11)
- **기각 근거**:
  - 시장 규모: 관계 문제로 "처음" 도구를 찾는 사람이 압도적 다수 (AppsFlyer 2024 기준 first-time 사용자 60%+).
  - 진입 장벽 낮춤이 Branch A/B 공통 설계 기준: B 기준 설계는 A·C 수용 가능, 역은 성립 안 함.
  - Time-sensitive 니즈 = 즉시 지불 의향 높음.
  - Viral/Referral 동력 페르소나 B가 최고 (NPS 결정적).
- **대안 채택**: 페르소나 B(준호, 위기 first-time) 1순위. A·C는 2·3순위로 진화 경로.
- **부활 조건**: B2B pivot 시 파워 유저 우선 전환 가능.

---

## D-9. 자유 게시판·실시간 채팅 커뮤니티

- **기각 시점**: round-01 (T3 §8.1, §8.4)
- **기각 근거**:
  - Reddit r/relationship_advice 분석 (Amaro et al., 2023): 조언의 60%가 "헤어져"로 수렴, "unhealthy externalization".
  - SNS 비교 지옥 (Vogel et al., 2014).
  - 익명 peer 전문성 0, 잘못된 조언 확산.
  - 정보통신망법 제70조 명예훼손 위험.
  - moderation 규모 불가능.
- **대안 채택**: Phase 1 커뮤니티 없음 → Phase 2 read-only 큐레이션 → Phase 3 고도 moderated 익명 Q&amp;A (전문가 응답만) → Phase 4 peer 상호작용 (강력한 moderation + 신고 시스템 + AI 독성 필터).
- **부활 조건**: Phase 4 도달 + 독성 제어 알고리즘 검증 후.
