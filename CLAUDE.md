# CLAUDE.md — 웹소설 남주 유형 테스트 + Study with Me

## 프로젝트 개요

단일 HTML 파일로 구성된 웹 앱. 백엔드 없음, 빌드 도구 없음.
웹소설 남주 유형 테스트 → 캐릭터 매칭 → 포모도로 타이머 → AI 남주 채팅의 4단계 플로우.

- **배포**: GitHub Pages — https://jjjuni-0818.github.io/novel-hero-study/
- **진입점**: `index.html` (전체 앱이 이 파일 하나에 있음)

## 파일 구조

```
novel-hero-study/
├── index.html      # 앱 전체 (HTML + CSS + JS)
├── README.md       # 프로젝트 소개 및 기술 스택
├── CLAUDE.md       # 이 파일 — AI 작업 컨텍스트
└── PRD.md          # 제품 요구사항 문서
```

## 핵심 구조 (index.html)

### 페이지 전환
4개 페이지를 `display: none/block`으로 전환. 라우터 없음.
```
#page-landing → #page-quiz → #page-result → #page-timer
```

### 주요 전역 변수
```javascript
let scores = { A: 0, B: 0, C: 0, D: 0 }  // 퀴즈 점수
let resultChar = null                        // 매칭된 캐릭터 키 ('A'|'B'|'C'|'D')
let timerMode = 'focus'                      // 'focus' | 'break'
let chatHistory = []                         // Groq API에 넘기는 대화 기록 (최대 20턴)
let groqApiKey = ''                          // localStorage에서 로드
```

### 캐릭터 데이터
`CHARACTERS` 객체에 4개 캐릭터(A/B/C/D) 정의.
각 캐릭터: `emoji`, `name`, `desc`, `tags`, `focusQuotes`, `breakQuotes`, `chatReplies`

### AI 채팅
- API: Groq `/openai/v1/chat/completions`
- 모델: `llama-3.3-70b-versatile`
- 시스템 프롬프트: `SYSTEM_PROMPTS[resultChar]`
- API 키: `localStorage('groq_api_key')`에 저장, 앱 로드 시 자동 복원
- 폴백: API 키 없으면 `CHARACTERS[char].chatReplies` 키워드 매칭으로 프리셋 응답

### 타이머
- 포모도로: 집중 25분 / 휴식 5분 자동 전환
- SVG 링 애니메이션: `stroke-dashoffset` 조작
- 5분마다 캐릭터 대사 교체 (`focusQuotes` / `breakQuotes`)

### 채팅 UI
- FAB(Floating Action Button): 타이머 페이지에서만 표시
- 팝업: `width: 420px`, `max-height: 640px`
- 한국어 IME 이중 전송 방지: `onkeydown="if(event.key==='Enter' && !event.isComposing)sendChat()"`

## 배포

```bash
# 변경 후 push하면 GitHub Pages 자동 반영 (1~2분 소요)
git add index.html
git commit -m "변경 내용"
git push origin main
```

## 주의사항

- CSS/JS 분리 파일 없음. 모두 `index.html` 안에 있음.
- `localStorage` 사용: `groq_api_key`
- Groq API 키는 클라이언트에서 직접 호출 — 프로덕션 환경에서는 백엔드 프록시 필요
