# TikTok Art Autoresearch System

Codex를 중심으로 운영하는 TikTok 아트 콘텐츠 자동화 워크스페이스다. 에이전트 프롬프트는 `agents/`에 분리되어 있고, 실행 스크립트는 `codex exec`로 각 프롬프트를 순차 실행한다.

- 계정: `@oysterlab7`
- 포맷: 포토 슬라이드쇼 7장, `1024x1536`
- 주요 진입점: `run.sh`, `run_scout_cycle.sh`, `run_scout_optimize.sh`

## 실행 구조

### 메인 사이클

`run.sh`

1. `agents/metrics.md` 실행
2. `agents/plan.md` 실행
3. `agents/generate.md` 실행
4. `agents/evaluate.md` 실행
5. `verdict.json` 기준으로 업로드
6. Slack 알림 및 임시 파일 정리

Generator와 Evaluator는 최대 3회까지 재시도한다.

### 스카웃 사이클

`run_scout_cycle.sh`

1. `agents/analyze.md`
2. `agents/scout.md`
3. `agents/scout_produce.md`
4. `agents/generate.md`
5. `agents/evaluate.md`
6. 업로드
7. `utils/report.py`

### 스카웃 최적화

`run_scout_optimize.sh`

1. 폰 잠금 해제 및 TikTok 진입
2. `agents/scout_runner.md`
3. `agents/scout_scorer.md`
4. 반복 실행으로 탐색 간격 최적화
5. 폰 잠금 및 Slack 알림

## Codex 기준 운영 원칙

- 이 저장소는 Git 저장소가 아니므로 스크립트에서 `codex exec --skip-git-repo-check`를 사용한다.
- 모든 스크립트는 현재 파일 위치를 기준으로 루트 경로를 계산한다.
- Codex 바이너리는 기본적으로 `PATH`에서 찾고, 필요하면 `CODEX_BIN` 환경 변수로 덮어쓴다.
- 에이전트 프롬프트는 `agents/*.md` 파일을 그대로 Codex 입력으로 사용한다.

## 주요 파일

| 파일 | 역할 |
|------|------|
| `create.py` | 콘텐츠 생성 로직 |
| `create.py.best` | 마지막 개선 버전 백업 |
| `prepare.py` | 업로드, 폰 제어, 메트릭 수집 등 자동화 |
| `run.py` | 평가, keep/revert, 상태 조회, 업로드 명령 |
| `notes.md` | 사이클 로그와 인사이트 |
| `plan.json` | 이번 사이클 계획 |
| `verdict.json` | QA 결과와 수정 지시 |
| `content/status.json` | 업로드 상태와 TikTok 메트릭 |

## 기술 스택

| 컴포넌트 | 기술 |
|---------|------|
| 에이전트 실행 | Codex CLI |
| 슬라이드 렌더링 | Playwright + HTML/CSS |
| 업로드 자동화 | ADB + UI XML automation |
| 메트릭 수집 | TikTok Internal API + Playwright/Node |
| 알림 | Slack Incoming Webhook |
| 언어 | Python 3.x |
