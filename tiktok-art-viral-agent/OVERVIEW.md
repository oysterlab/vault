# TikTok Art Autoresearch System

Andrej Karpathy의 autoresearch 패턴을 TikTok 아트 콘텐츠 최적화에 적용한 멀티 에이전트 시스템.
4개의 AI 에이전트가 자율적으로 콘텐츠를 생성하고, 검수하고, 업로드하고, 성과를 측정하며 개선한다.

- 계정: @oysterlab7
- 포맷: 포토 슬라이드쇼 (7장, 1024x1536px)
- 스케줄: 매일 06:00, 19:00 자동 실행 (cron)

---

## 전체 플로우

```
cron (06:00 / 19:00)
    │
    ▼
run.sh ─────────────────────────────────────────────────────────
    │
    │  ┌─────────────────────────────────────────────────────┐
    │  │ Agent 1: Sam (Metrics)                              │
    │  │ 읽기: notes.md                                      │
    │  │ 실행: python run.py evaluate                        │
    │  │ 쓰기: notes.md (메트릭 결과, 인사이트)                │
    │  └─────────────────────────────────────────────────────┘
    │                          │
    │                  notes.md (메트릭 + 인사이트)
    │                          ▼
    │  ┌─────────────────────────────────────────────────────┐
    │  │ Agent 2: Alex (Planner)                             │
    │  │ 읽기: notes.md, strategy.md, content/posts.json     │
    │  │ 실행: WebSearch (명화 리서치)                         │
    │  │ 쓰기: plan.json (명화 + 3개 포스트 설계)              │
    │  │ 쓰기: notes.md (전략 근거)                           │
    │  └─────────────────────────────────────────────────────┘
    │                          │
    │                  plan.json (명화, 크롭, 훅, 슬라이드)
    │                          ▼
    │  ┌──────────────────────────────────────────────────────────────┐
    │  │                Generator ↔ Evaluator 루프 (최대 3회)           │
    │  │                                                              │
    │  │  ┌───────────────────────────────────────────────────────┐   │
    │  │  │ Agent 3: Maya (Generator)                               │   │
    │  │  │ 읽기: plan.json (1차) 또는 verdict.json (재시도)       │   │
    │  │  │ 실행: create_post() → prepare.py → Playwright 렌더링  │   │
    │  │  │ 쓰기: content/output/{post_id}/slide_1~7.png          │   │
    │  │  │ 쓰기: notes.md (생성 결과)                             │   │
    │  │  └───────────────────────────────────────────────────────┘   │
    │  │                         │                                    │
    │  │              slide_1~7.png (생성된 슬라이드)                  │
    │  │                         ▼                                    │
    │  │  ┌───────────────────────────────────────────────────────┐   │
    │  │  │ Agent 4: Jin (Evaluator)                              │   │
    │  │  │ 읽기: plan.json, slide_1~7.png (이미지 직접 검수)      │   │
    │  │  │ 검수: 슬라이드1 임팩트, 인접 차이, 이미지-텍스트 일치   │   │
    │  │  │ 쓰기: verdict.json (통과/탈락 + 수정 지시)             │   │
    │  │  │ 실행: python run.py upload (통과한 포스트만)            │   │
    │  │  └───────────────────────────────────────────────────────┘   │
    │  │                         │                                    │
    │  │              verdict.json                                    │
    │  │                │                                             │
    │  │        전부 통과? ──YES──→ 루프 종료                          │
    │  │                │                                             │
    │  │               NO (탈락 있음)                                  │
    │  │                │                                             │
    │  │                └──→ Maya가 verdict.json 읽고 수정 후 재생성   │
    │  │                                                              │
    │  └──────────────────────────────────────────────────────────────┘
    │
    │  임시 파일 정리 (tmp/)
    │
    │  ┌─────────────────────────────────────────────────────┐
    │  │ Slack 브리핑 (utils/notify.py)                      │
    │  │ 읽기: notes.md, verdict.json, plan.json             │
    │  │ 전송: 성과분석 / 이번사이클 / 인사이트 / 다음계획     │
    │  └─────────────────────────────────────────────────────┘
    │
    ▼
  완료. 다음 사이클까지 대기.
```

---

## 파일 역할

### 메인 코드

| 파일 | 역할 | 누가 수정 |
|------|------|----------|
| `prepare.py` | 고정 인프라: ADB, Playwright 렌더링, Gemini API, 메트릭 수집, 업로드 자동화, 스코어링 | 사람 |
| `create.py` | 콘텐츠 생성 로직: create_post() 함수. 슬라이드/크롭/텍스트 조합 | AI (자유 수정) |
| `create.py.best` | 마지막으로 스코어가 개선된 create.py 사본 | 자동 (run.py) |
| `run.py` | 평가 실행 + keep/revert 판정 + 실험 로그 기록 | 사람 |
| `run.sh` | cron 엔트리포인트: 4개 에이전트 순서 실행 + Slack 알림 + tmp 정리 | 사람 |

### 에이전트 프롬프트

| 파일 | 에이전트 | 페르소나 | 하는 일 |
|------|---------|---------|--------|
| `agents/metrics.md` | Sam | 냉철한 데이터 분석가 | run.py evaluate 실행, 스코어 분석, 인사이트 도출 |
| `agents/plan.md` | Alex | TikTok 크리에이티브 디렉터 | 명화 선정, 9:16 크롭 적합성 테스트, A/B/C 변형 설계, plan.json 작성 |
| `agents/generate.md` | Maya | 테크니컬 프로듀서 | plan.json 실행, create_post() 호출, 에러 디버깅, 재시도 시 verdict.json 반영 |
| `agents/evaluate.md` | Jin | 50만 팔로워 틱톡 크리틱 | 슬라이드 이미지 직접 검수, 통과/탈락 판정, 업로드 실행 |

### 에이전트 간 핸드오프 파일

| 파일 | 쓰는 에이전트 | 읽는 에이전트 | 내용 |
|------|-------------|-------------|------|
| `notes.md` | Sam, Alex, Maya, Jin (전원) | Sam, Alex (다음 사이클) | 메트릭, 전략, 생성 결과, QA 결과, 다음 계획 |
| `plan.json` | Alex (Planner) | Maya (Generator) | 명화 정보 + 3개 포스트 정의 (훅, 크롭, 슬라이드) |
| `verdict.json` | Jin (Evaluator) | Maya (Generator, 재시도 시) | 통과/탈락 판정 + 구체적 수정 지시 |
| `experiment.md` | 사람 (via Claude Code) | Alex (Planner) | 인스턴트 실험 아이디어 — Planner가 다음 사이클에서 우선 실행 |

### 전략/설정 (docs/)

| 파일 | 역할 | 누가 수정 |
|------|------|----------|
| `docs/strategy.md` | 훅 공식, 슬라이드 구조, 스코어링, 제약조건 | Alex (Planner) — 메트릭 근거 필수 |
| `docs/OVERVIEW.md` | 시스템 전체 구조 설명 | 사람 |
| `docs/program.md` | 아카이브 (에이전트별 프롬프트로 분리됨) | - |

### 데이터

| 파일/디렉토리 | 역할 |
|-------------|------|
| `content/posts.json` | 포스트 정의 (슬라이드, 캡션, 이미지 URL) |
| `content/status.json` | 업로드 상태 + TikTok 메트릭 |
| `content/output/{post_id}/` | 생성된 슬라이드 이미지 (slide_1.png ~ slide_7.png) |
| `content/metrics.db` | SQLite 시계열 메트릭 아카이브 |
| `logs/run-*.log` | 각 사이클 실행 로그 (에이전트별 출력 포함) |
| `logs/experiments.jsonl` | 실험 히스토리 (스코어, keep/revert, append-only) |

### 유틸리티

| 파일 | 역할 |
|------|------|
| `utils/notify.py` | Slack Webhook 브리핑 (한글, 구조화된 보고서) |
| `utils/sam_crop.py` | SAM2 이미지 세그멘테이션 크롭 |

### 임시 (사이클 후 자동 삭제)

| 파일 | 역할 |
|------|------|
| `tmp/debug_fail.png` | 업로드 실패 시 디버그 스크린샷 |
| `tmp/debug_fail_ui.txt` | 업로드 실패 시 UI 덤프 |

---

## 스코어링 (0-100)

TikTok 알고리즘 배포 신호 기반. 각 메트릭을 0-100으로 정규화 후 Tier별 가중합산.

```
Tier 1 (60%) — 배포 확장 핵심
  completion_rate × 0.25    70% = 만점
  share_rate × 0.20         shares/views 1% = 만점
  save_rate × 0.15          saves/views 2% = 만점

Tier 2 (30%) — 중간
  comment_rate × 0.15       comments/views 0.5% = 만점
  slides_viewed × 0.15      7/7장 = 만점

Tier 3 (10%) — 낮음
  like_rate × 0.10          likes/views 5% = 만점
```

---

## Evaluator 검수 기준

### 슬라이드 1 (훅) — 실패 시 전체 reject
- 프레임을 가득 채우는 시각적 임팩트
- 여백/어둠/빈 공간 없음
- "새벽 2시에 스크롤하다 멈출 정도인가?"

### 인접 슬라이드 차이 — 2쌍 이상 유사하면 reject
- 연속 2장이 같은 영역/구도이면 안 됨
- 시각적 여정이 느껴져야 함

### 이미지-텍스트 일치 — 불일치 시 해당 슬라이드 fail
- "손을 봐" → 크롭에 손이 보여야 함
- "하늘이 빨갛다" → 크롭에 하늘이 보여야 함

### 통과 기준
- 5/7 이상 통과 + 슬라이드 1과 7 필수 통과 = PASS
- 크롭 수정 불가 시 "슬라이드 교체" 지시
- 3개 모두 같은 이유로 실패 시 "PAINTING NOT VIABLE" → Planner가 다른 명화 선택

---

## 기술 스택

| 컴포넌트 | 기술 |
|---------|------|
| AI 에이전트 | Claude Code (Opus 4.6, --dangerously-skip-permissions) |
| 이미지 생성 | Gemini API (gemini-2.0-flash-exp-image-generation) |
| 슬라이드 렌더링 | Playwright + HTML/CSS 템플릿 |
| 업로드 자동화 | ADB + UI XML automation (17단계) |
| 메트릭 수집 | TikTok Internal API + Studio Scraper (Playwright/Node) |
| 스케줄링 | cron (06:00, 19:00) |
| 알림 | Slack Incoming Webhook (한글 브리핑) |
| 언어 | Python 3.10 |
