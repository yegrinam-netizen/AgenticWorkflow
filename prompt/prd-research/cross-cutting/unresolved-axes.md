---
round: 0
round_label: cross-cutting
file_kind: cross-cutting
axis: unresolved-axes
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "1차 조사의 역방향 점검(3층위 자기점검)에서 도출된 미해결 축 대장."
  - "차수 간 누적. 해결되면 status를 resolved로 갱신 후 해결 근거 기록."
---

# Unresolved Axes — 미해결 축 대장

> 1차 조사에서 확인된 "조사하지 않은 축" 또는 "조사했으나 결론 내리지 못한 축". PRD 작성 시 빈칸이 되거나 위험 가정으로 남는 지점.
>
> 각 항목은 `cross-cutting/decisions-log.md`에 결정이 기록되는 순간 `status: resolved`로 옮긴다.

---

## R1. 홍보(부부·고부·직장) vs 기능(연인 only)의 기대-현실 gap 관리

- **status**: open
- **왜 중요한가**: 첫 설치 유저 실망 → 리뷰 폭탄 → ASO 붕괴. 홍보 광고로 유입된 부부·고부·직장 유저가 연인 기능만 발견했을 때의 체류·전환율 미확인.
- **현재 근거 결여**: Operator Analyst 페르소나에 해당 세그먼트 없음. Scenario Explorer의 "공유 코어 엔진 + 도메인 페르소나 레이어" 제안이 있으나 검증 데이터 없음.
- **해결 경로 후보**: (a) 홍보 메시지를 연인 중심으로 좁힘, (b) 다른 관계 유형 베타 뱃지 공개, (c) 유입 소스별 분기 온보딩.
- **흔들리는 PRD 섹션**: §4 사용자 정의, §10 UX 원칙, §18 로드맵.

---

## R2. 커플 양측 동시 사용 시 게임이론적 위험

- **status**: open
- **왜 중요한가**: A가 앱 조언 적용 → B가 "조종당한다" 느끼면 관계 악화 → **앱이 갈등의 원인**. 기존 경쟁 앱(Paired, Lasting)도 정면으로 다루지 않는 사각지대.
- **현재 근거 결여**: Scenario/Operator/Sustainability 전원 언급 부족. 파트너 초대 기능 법적 리스크만 부분 다룸.
- **해결 경로 후보**: 양측 동의 기반 공유 모드, 비대칭 정보 설계(양쪽이 같은 AI 조언 공유), 투명성 UI.
- **흔들리는 PRD 섹션**: §6 기능, §8 데이터 거버넌스, §9 감정 안전.

---

## R3. 한국어 관계 코칭 데이터셋 확보 경로

- **status**: open
- **왜 중요한가**: 콜드 스타트 문제. 로컬 LLM fine-tuning·클라우드 프롬프트 튜닝 모두 데이터 필요.
- **현재 근거 결여**: Workflow Architect가 "합성 대화 데이터" 언급(raw/T1 §3 NO-2)하나 출처·대표성 미검증. Reddit/디시 수집은 저작권·윤리 문제 파킹.
- **해결 경로 후보**: (a) Gottman Institute 라이선스, (b) 임상심리사 자문 + 자체 제작, (c) 공개 설문 + 비식별화, (d) RL from human feedback.
- **흔들리는 PRD 섹션**: §7 AI 아키텍처 Tier 1·2, §11 하네스 sub-agent(@conflict-psychology-researcher).

---

## R4. "관계 개선" 성공 지표의 합의 부재

- **status**: open
- **왜 중요한가**: NPS·DAU는 실 효능 없이도 수치 상승 가능. 제품이 "재미있지만 관계는 나빠지는" 상태 측정 불가.
- **현재 근거 결여**: Operator Analyst §11.4에 "7일 내 2회 이상 세션 유저 비율" 북극성 제안이 있으나 실 효능 지표 아님. Sustainability §9에서 "장기 종단 연구" 필요성만 언급.
- **해결 경로 후보**: PHQ-9·관계 만족도 척도(Relationship Assessment Scale)·갈등 해결 자기보고, 6-12개월 quasi-experimental.
- **흔들리는 PRD 섹션**: §16 성공 지표.

---

## R5. 음성 입력 UX + STT 프라이버시

- **status**: open
- **왜 중요한가**: 갈등 중 타이핑 불편 → 음성 니즈. 모바일 우선 가정과 충돌 없음이나 프라이버시 재설계 필요.
- **현재 근거 결여**: Operator Analyst §5.2 페르소나 B "음성 입력" 언급 수준. Sustainability §12 파킹 로트에 STT/TTS 비용 구조 미조사.
- **해결 경로 후보**: 온디바이스 STT(Apple Speech / Google Speech on-device), 녹음 즉시 파기 정책.
- **흔들리는 PRD 섹션**: §6 기능(Yellow Zone 음성), §8 데이터 거버넌스.

---

## R6. 한국 인앱결제 생태계 특수성

- **status**: open
- **왜 중요한가**: 인앱결제 강제 대응법(2022) 이후 외부결제 허용. 토스·PG·Apple/Google 심사 충돌 복잡도 높음.
- **현재 근거 결여**: Sustainability §13 파킹 로트 "한국 PG 구독 서비스 수수료·정산 조건 비교" 미조사.
- **해결 경로 후보**: Stripe 직접 결제 + Apple IAP 이중화, 외부결제 허용 조항 활용 설계.
- **흔들리는 PRD 섹션**: §13 상업화·과금 모델.

---

## R7. AI 잘못된 조언의 법적 책임·보험

- **status**: open
- **왜 중요한가**: "헤어져야 해요" 오조언 → 실제 이별 → 소송 시나리오. Character.ai 2024 청소년 자살 소송 선례(`raw/T2 §3.2`).
- **현재 근거 결여**: Sustainability §6.2 GDPR·HIPAA 일반 규제만 다룸. 민사 책임·AI 면책 조항·책임보험 미검토.
- **해결 경로 후보**: 전자상거래법 약관 면책, 사이버보험(Cyber Liability Insurance), 미국 Section 230 유사 해석 한국 적용성.
- **흔들리는 PRD 섹션**: §9 감정 안전, §14 규제·법무 매트릭스.

---

## R8. 심리 전문가 자문단 운영 모델

- **status**: open
- **왜 중요한가**: self-help 포지셔닝 안전 테두리 유지 + 콘텐츠 품질 보증. 자문 없이 AI만으로는 법적 방어벽 취약.
- **현재 근거 결여**: Sustainability §13 파킹 로트 "DTx 허가 경로" 연관되나 본 조사 범위 밖.
- **해결 경로 후보**: 내부 고용(비용 高), 프리랜서 자문(인건비 중간·지속성 低), 학계 협업(저비용·인증 강함·속도 低).
- **흔들리는 PRD 섹션**: §11 하네스 agents(@conflict-psychology-researcher), §14 규제.

---

## R9. 대형사(카카오·네이버·SKT·KT) 직접 진출 시나리오

- **status**: open
- **왜 중요한가**: 2025-26 사이 관계·심리 AI 진출 가능성. 경쟁 환경 급변 시 본 제품 포지션 붕괴.
- **현재 근거 결여**: Scenario Explorer §3.3 "네이버 클로바 헬스케어 상담 — 공식 AI 관계 상담 제품 없음(2026 기준)" 정도. 로드맵 추적 없음.
- **해결 경로 후보**: IR 자료·특허 출원·개발자 컨퍼런스 발표 모니터링.
- **흔들리는 PRD 섹션**: §15 위험 가정, §18 로드맵.

---

## R10. 유입 경로(CAC) 조사

- **status**: open
- **왜 중요한가**: 페르소나 B의 진입 경로 불확실 → 마케팅 예산 배분 불가.
- **현재 근거 결여**: Operator Analyst §2 "친구에게 '너 상담 좀 받아봐' 듣고 구글에 '여친이랑 싸웠을 때' 검색" 한 줄. 세부 검색어·유튜버·커뮤니티 미조사.
- **해결 경로 후보**: Google Keyword Planner, 네이버 검색 트렌드, 유튜브 연애 채널 시청자 분석, 디시·에브리타임 스크래핑.
- **흔들리는 PRD 섹션**: §13 상업화, §18 로드맵 초기 마케팅.

---

## R11. 정체성: "갈등 해결" vs "관계 성장"

- **status**: open
- **왜 중요한가**: 같은 기능도 포지셔닝 전환 시 타겟·가격·콘텐츠 전부 달라짐. "위기 대응 도구"(페르소나 B 중심)와 "평상시 관계 강화"(페르소나 C 중심)가 다른 상품.
- **현재 근거 결여**: Operator Analyst가 페르소나 B 우선을 권장했으나 정체성 레벨 결정 아님.
- **해결 경로 후보**: 사용자 인터뷰, 경쟁사 포지셔닝 표, 브랜드 워크숍.
- **흔들리는 PRD 섹션**: §3 제품 정의, §4 사용자, §13 상업화.

---

## R12. Gottman·NVC 등 프레임워크 상표·라이선스

- **status**: open
- **왜 중요한가**: "Gottman 기반" 문구 무단 사용 시 상표권 침해. 공식 라이선스 비용·조건 미확인.
- **현재 근거 결여**: 모든 teammate가 Gottman·NVC 프레임워크 언급하나 라이선스 검토 없음.
- **해결 경로 후보**: Gottman Institute API·라이선스 프로그램 확인, NVCC(Nonviolent Communication Copyright) 조항 검토.
- **흔들리는 PRD 섹션**: §11 하네스 skills(`relationship-coaching-rubric`), §3 브랜드 톤, §14 규제·법무.

---

## 향후 추가 축 기록 규칙

- 새 미해결 축 발견 시 `R13, R14, ...` 연번.
- 각 항목에 `status: open|investigating|resolved|discarded` 유지.
- `resolved` 전환 시 해결 근거(파일 경로) 명시.
- `discarded` 전환 시 기각 근거.
