---
round: 1
round_label: initial-teammate
file_kind: parking-lot
generated_at: 2026-04-17
local_execution_compliance:
  harness: compliant
  product: deployment-flexible
notes:
  - "T1~T4 각 teammate raw §파킹 섹션 통합. 보존 원칙."
  - "차수 확장 시 재논의 항목 풀. 결론 내리지 못한 것은 버리지 않고 여기에."
---

# Round 01 — Parking Lot (통합)

> 1차 조사에서 **발견했으나 본 차수에 결론 내리지 못한 항목**.
> 2차 이상 조사에서 조건이 바뀌면 재심 대상.

---

## T1 — Workflow Architect (P-T1-1~19)

### A. 하네스 기술 심화
- **P-T1-1. Agent Team GA 전환 시점**: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 실험 플래그가 프로덕션 정식 기능 전환 시기·안정성.
- **P-T1-2. MCP 서버 카탈로그 확장**: GitHub·Jira·Linear·Notion·Slack MCP 통합 시 워크플로우 자동화 범위 변화.
- **P-T1-3. Claude Code custom skill registry**: 커뮤니티 skill 공유 메커니즘 등장 시 `workflow-generator` 스킬 구조 재검토.
- **P-T1-4. Background mode 장기 실행**: Task tool `run_in_background` 30분 이상 작업 안정성.
- **P-T1-5. Multi-machine orchestration**: 여러 개발자 PC 간 Claude Code 세션 연결 가능성.

### B. Branch 기술 분기
- **P-T1-6. iOS on-device LLM API 공개**: iOS 19+ 예상. Branch A 구현 방식 재설계.
- **P-T1-7. Android Gemini Nano**: Pixel 계열 on-device 모델 활용 여부.
- **P-T1-8. WebAssembly LLM 브라우저 실행**: 웹 버전 Branch A 가능성.
- **P-T1-9. EXAONE 후속 모델**: LG AI EXAONE 4.0 출시 시 품질 재평가.
- **P-T1-10. Solar Pro 상용 API**: Upstage 엔터프라이즈 라이선스 조건.

### C. 하네스 운영
- **P-T1-11. SOT 파일 conflict 해결 정책**: 2인+ 팀에서 동시 쓰기 발생 시 resolver 설계.
- **P-T1-12. Hook 스크립트 버전 관리**: 스크립트 변경 시 backward compatibility 보장 전략.
- **P-T1-13. `.claude/context-snapshots/` 크기 관리**: 장기 사용 시 디스크 소비 증가.
- **P-T1-14. Knowledge Archive 검색 고도화**: 현재 grep 기반 → 벡터 검색 전환 가능성.

### D. 워크플로우 카탈로그
- **P-T1-15. workflows/feature-*.md 템플릿 표준화**: 기능별 workflow.md 일관성 보장.
- **P-T1-16. roadmap.yaml 스키마 정립**: 기능 간 의존성·우선순위 표현 구조.
- **P-T1-17. Gated workflow (인간 승인 구간)**: critical 기능 구현 시 중간 승인 기입 방식.
- **P-T1-18. Failed workflow rollback**: 실패 시 복구 프로토콜.
- **P-T1-19. Cross-project workflow 재사용**: 다른 프로젝트에서 workflow.md 재사용 메커니즘.

---

## T2 — Scenario Explorer (P-T2-1~10)

- **P-T2-1. 청소년(14-18) 관계 앱 법적 허용 경계**: 청소년보호법·정보통신망법 상 허용 범위 세밀.
- **P-T2-2. LGBTQ+ 커플 특화 시나리오**: 국내 법적 지위 변동 (동성혼·파트너십) 추이 반영.
- **P-T2-3. 다문화·국제 커플**: 한-일·한-중·한-동남아 커플 갈등 특수성 + 이중언어 대화.
- **P-T2-4. 장거리 연애 전용 시나리오**: 군 복무·유학·주재원 상황 특화.
- **P-T2-5. 비동거 혼인 (주말부부)**: 한국 특수 라이프스타일.
- **P-T2-6. 재혼·블렌디드 가족**: 자녀·전 배우자 얽힘.
- **P-T2-7. 가정폭력 스크리닝 알고리즘**: 초기 대화에서 DV 징후 자동 감지.
- **P-T2-8. 아동 학대·방임 의심 신호**: 법정 신고 의무 (아동학대처벌법) 대응.
- **P-T2-9. AI 동반자 의존성 평가**: Character.ai 소송 선례. 건강한 사용 모니터링.
- **P-T2-10. 커플 매칭·데이팅 앱 공유 구독 비즈니스**: 틴더·번팔·글램 등 제휴 가능성.

---

## T3 — Operator Analyst (P-T3-1~10)

- **P-T3-1. Dark Pattern 윤리 가이드**: 구독 유도·FOMO·사회 증명 등 사용 경계.
- **P-T3-2. 음성 입력·TTS**: 손으로 타이핑 어려운 위기 순간 음성 활용. 프라이버시 트레이드오프.
- **P-T3-3. 위젯·워치·푸시**: iOS 홈 화면 위젯, Apple Watch 감정 temperature 체크.
- **P-T3-4. 오프라인 전용 모드 토글**: 유저가 명시적으로 "기기 내에서만" 선택 시 UX.
- **P-T3-5. 사용 시간 제한 (self-control)**: 건강한 사용 촉진 vs 매출 트레이드오프.
- **P-T3-6. 파트너 초대 기능의 심리**: "초대가 거절될 때" 자기 비난 증폭 리스크.
- **P-T3-7. 유저 피드백 메커니즘**: NPS·이유 수집 UX.
- **P-T3-8. 접근성 (시각·청각 장애)**: WCAG 2.1 AA 준수 범위.
- **P-T3-9. 저사양 Android 호환**: Branch A에서 저사양 기기 배제 리스크.
- **P-T3-10. Biometric 로그인**: 파트너가 모를 PIN·지문 권장.

---

## T4 — Sustainability Strategist (P-T4-1~12)

- **P-T4-1. Anthropic prompt caching 2026 정책 변경**: 90% 할인 축소 시 하네스 비용 30-50% 상승.
- **P-T4-2. EU AI Act 고위험 AI 재분류 리스크**: 감정 인식 AI 고위험 재분류 시 EU compliance 막대한 비용.
- **P-T4-3. 개인정보보호위원회 AI 가이드**: 2026 하반기 발표 예상.
- **P-T4-4. 로컬 LLM 양자화 기술 발전**: Q2·Q3 품질 충분 시 앱 크기 절반.
- **P-T4-5. On-device Speculative Decoding**: Apple Intelligence·Gemini Nano 발전 시 추론 속도 2-3배.
- **P-T4-6. 보험업법 개정 (2026)**: 건강관리서비스업 카테고리 영향.
- **P-T4-7. 공정거래위원회 구독 해지 간소화 가이드**: UX 강제 변경 가능성.
- **P-T4-8. Anthropic DPA 조항 변경**: 학습 사용 기본값 변화 가능성.
- **P-T4-9. 한국 토종 LLM 정부 지원**: K-LLM 프로젝트 전개.
- **P-T4-10. Apple on-device LLM API 공개**: iOS 19 예상. Branch A 재설계.
- **P-T4-11. 카카오·네이버 생성형 AI 경쟁자 진입**: 거대 유통망 빅테크.
- **P-T4-12. B2B EAP 시장 규모 실측 데이터**: 전문 조사기관 의뢰 가치.

---

## 재심 트리거 (Reactivation Triggers)

다음 이벤트가 발생하면 해당 파킹 항목을 즉시 재논의:

- **Claude Code major version update** → P-T1-1, P-T1-2, P-T1-4, P-T1-12, P-T1-14.
- **iOS/Android OS 업데이트 (연 1회)** → P-T1-6, P-T1-7, P-T4-10.
- **EXAONE·Solar 신버전** → P-T1-9, P-T1-10, P-T4-4.
- **EU AI Act·GDPR 가이드 변경** → P-T4-2.
- **PIPA 하위법령 개정** → P-T4-3, P-T4-6, P-T4-7.
- **경쟁사 (Lasting·Paired·Character.ai·카카오·네이버) 전략 변경** → P-T2-9, P-T2-10, P-T4-11.
- **법률·판례 변동 (심리서비스법·동성혼)** → P-T2-2.
- **내부 트리거 (MAU 10K·B2B 첫 계약·Series A 준비)** → P-T1-11, P-T3-1, P-T4-12.
