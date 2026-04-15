# Tasks: Typing Game PRD - Push 4

> PRD: `.claude/tasks/todo/prd-typing-game.md`
> Push 범위: PvP 모드 선택/매칭/라운드 + 미니맵/멀티 커서 + Dashboard 기본 화면 (모두 목데이터)
> 상태: 🔲 진행 중

---

### 에이전트 팀 구성

**팀 구성:** coordinator -> developer + tester
**이유:** PvP 상태 흐름과 시각화(미니맵/커서), 대시보드 요약까지 포함한 고복잡도 작업

#### 실행 순서
1. **coordinator**: 4.1~4.6 순차 할당 및 중간 검수
2. **developer**: 모드 선택/라운드 흐름/목데이터 시각화 구현
3. **tester**: 시나리오 테스트(단문/장문/타이머/결과) 및 반응형 검증
4. **coordinator**: KPI 이벤트 로깅 체크 후 Push 종료

#### 역할별 지시사항
- **developer**: "PvP를 소나기 게임이 아닌 단문/장문 배틀로 구현하고, 실시간 기능은 mock tick으로 대체."
- **tester**: "라운드 타이머 종료 전환, 미니맵 진행도 표시 정확성, 멀티 커서 렌더링 안정성을 검증."

### 실행 환경 (task-executor 단독 실행 시)

- **사용 가능 도구:** Read, Write, Edit, Bash, Glob, Grep, Task
- **사용 불가 도구:** Skill, Agent
- **이미지 읽기:** Read 도구로 png/jpg 직접 열람 가능
- **병렬 작업:** 불가 (순차 실행)

### 참조 이미지

| 이미지 | 용도 | 관련 작업 |
|--------|------|-----------|
| `.claude/tasks/pvp_select.png` | PvP 모드 선택 화면 구조 | 4.1 |
| `.claude/tasks/image-1.png` | 타자 배틀 + 우측 미니맵 레이아웃 | 4.2, 4.3 |
| `.claude/tasks/image copy.png` | 공통 게임창 톤/간격 보정 | 4.5 |

### 참조 문서

작업 시작 전 반드시 아래 문서를 `Read`로 읽을 것:

| 문서 | 용도 |
|------|------|
| `src/features/auth-by-oauth/ui/GoogleLoginButton.tsx` | 버튼/상태 UI 패턴 참조 |
| `src/features/typing/ui/HiddenInput.tsx` | 입력 포커스 및 키 처리 패턴 |
| `src/app/routes/paths.ts` | PvP/대시보드 라우트 추가 기준 |
| `CLAUDE.md` | i18n, FSD, import 규칙 점검 |

### 적용 규칙 (스킬 요약)

#### 폴더 구조
- PvP 인터랙션은 `features` 중심으로, 페이지 조립은 `pages`에서 수행
- 멀티 진행도/커서 mock 모델은 엔티티 또는 feature model에 위치
- `shared`는 범용 미니맵 UI/유틸만 포함

#### 코드 품질
- 가독성: PvP 상태를 `mode -> matching -> playing -> result` 단계 enum으로 관리
- 예측 가능성: 타이머 tick/update를 순수 함수로 분리
- 응집도: 라운드 계산 로직과 표시 컴포넌트를 가까이 배치
- 결합도: 네트워크 실시간 의존 대신 mock provider 인터페이스 사용

#### React 최적화
- `rerender-memo`: 미니맵 참가자 핀, 커서 레이어 memo
- `rendering-content-visibility`: 긴 본문/장문 모드 렌더링 최적화
- `rerender-functional-setstate`: 타이머 감소와 진행도 업데이트 함수형 setState
- `bundle-dynamic-imports`: 대시보드 차트성 컴포넌트는 필요 시 지연 로딩

### 관련 파일

- `src/pages/` - PvP, Dashboard 페이지 추가/수정
- `src/features/typing/` - 입력/진행도 연결
- `src/features/demo/` - mock match/leaderboard 데이터 확장
- `src/app/providers/router/AppRouter.tsx` - 신규 라우트 연결

---

## 작업

- [ ] 4.0 PvP 및 Dashboard 목데이터 기반 구현
- [ ] 4.1 PvP 모드 선택(단문/장문) 화면 구현 (커밋 단위)
  **작업 상세:** `소나기` 표현 제거, 배틀 모드 2종 카드 선택 UI 구성
  **참조:** 이미지 `.claude/tasks/pvp_select.png`
  - [ ] 4.1.T1 테스트 코드 작성
  - [ ] 4.1.T2 테스트 실행 및 검증

- [ ] 4.2 PvP 배틀 화면 + 우측 미니맵 구현 (커밋 단위)
  **작업 상세:** 좌측 타자 영역, 우측 미니맵 패널 배치 및 반투명 아크릴 스타일 적용
  **참조:** 이미지 `.claude/tasks/image-1.png`
  - [ ] 4.2.T1 테스트 코드 작성
  - [ ] 4.2.T2 테스트 실행 및 검증

- [ ] 4.3 멀티 진행도/커서 목데이터 시뮬레이션 (커밋 단위)
  **작업 상세:** 참가자별 진행률, 커서 위치를 mock tick으로 갱신하여 시각화
  **참조:** 이미지 `.claude/tasks/image-1.png`
  - [ ] 4.3.T1 테스트 코드 작성
  - [ ] 4.3.T2 테스트 실행 및 검증

- [ ] 4.4 라운드 종료 후 다음 게임 타이머 구현 (커밋 단위)
  **작업 상세:** 카운트다운 0초 시 자동 전환, 이벤트 로깅(`round_countdown_started`) 연결
  **참조:** 문서 `src/app/routes/paths.ts`
  - [ ] 4.4.T1 테스트 코드 작성
  - [ ] 4.4.T2 테스트 실행 및 검증

- [ ] 4.5 Dashboard 기본 화면 목데이터 구성 (커밋 단위)
  **작업 상세:** 내 기록/통계/최근 플레이 3섹션 skeleton + mock 데이터 바인딩
  **참조:** 이미지 `.claude/tasks/image copy.png`
  - [ ] 4.5.T1 테스트 코드 작성
  - [ ] 4.5.T2 테스트 실행 및 검증

- [ ] 4.6 i18n/접근성/회귀 정리 (커밋 단위)
  **작업 상세:** PvP/대시보드 신규 문구 키화, 포커스 이동 및 키보드 조작 검증
  **참조:** 문서 `CLAUDE.md`
  - [ ] 4.6.T1 테스트 코드 작성
  - [ ] 4.6.T2 테스트 실행 및 검증
