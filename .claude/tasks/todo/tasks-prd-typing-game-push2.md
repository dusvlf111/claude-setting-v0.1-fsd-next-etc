# Tasks: Typing Game PRD - Push 2

> PRD: `.claude/tasks/todo/prd-typing-game.md`
> Push 범위: 메인 화면 + Dock형 네비게이션 + 게스트 결과 모달 구현 (목데이터 기반)
> 상태: 🔲 진행 중

---

### 에이전트 팀 구성

**팀 구성:** coordinator -> developer + tester
**이유:** 홈 페이지, 인증 상태 UI, 모달 흐름이 동시에 연동되는 3개 이상 컴포넌트 작업

#### 실행 순서
1. **coordinator**: 2.1~2.5를 순서대로 할당
2. **developer**: UI/상태/목데이터 바인딩 구현 + 커밋
3. **tester**: 회귀 테스트 및 반응형 검증
4. **coordinator**: 결과 취합 및 Push 마감

#### 역할별 지시사항
- **developer**: "우중간 Dock 네비게이션을 macOS 하단 독 느낌으로 구현하고, 인증 상태별 Account 표현을 분기."
- **tester**: "게스트/로그인 상태 전환, 모달 노출/닫힘, 첫 입력 가능 상태를 집중 점검."

### 실행 환경 (task-executor 단독 실행 시)

- **사용 가능 도구:** Read, Write, Edit, Bash, Glob, Grep, Task
- **사용 불가 도구:** Skill, Agent
- **이미지 읽기:** Read 도구로 png/jpg 직접 열람 가능
- **병렬 작업:** 불가 (순차 실행)

### 참조 이미지

| 이미지 | 용도 | 관련 작업 |
|--------|------|-----------|
| `.claude/tasks/image copy.png` | 전체 메인 구조 참조 | 2.1, 2.2 |
| `.claude/tasks/main.png` | Dock형 네비/패널 감성 참조 | 2.2 |
| `.claude/tasks/image-2.png` | 비회원 완료 모달 레이아웃 | 2.4 |

### 참조 문서

작업 시작 전 반드시 아래 문서를 `Read`로 읽을 것:

| 문서 | 용도 |
|------|------|
| `src/pages/home/ui/HomePage.tsx` | 기존 메인 화면 구조 파악 |
| `src/features/typing/ui/ArticleDisplay.tsx` | 타자 입력 UI 재사용 확인 |
| `src/app/providers/auth/AuthProvider.tsx` | 로그인 상태 분기 기준 |
| `CLAUDE.md` | i18n 및 FSD 규칙 확인 |

### 적용 규칙 (스킬 요약)

#### 폴더 구조
- 메인 페이지 조립은 `pages/home`, 상호작용 로직은 `features/*`, 공통 UI는 `shared/ui` 배치
- slice 외부에서 segment 직접 import 금지 (`index.ts` 사용)
- 유저 노출 문자열은 하드코딩 금지, 각 slice i18n 리소스로 분리

#### 코드 품질
- 가독성: Dock UI, 모달, 입력 옵션 상태를 별도 훅/컴포넌트로 분리
- 예측 가능성: `isGuest`, `isLoggedIn` 분기명을 명시적으로 사용
- 응집도: 모달 트리거/표시/액션을 한 feature에 결집
- 결합도: 인증 컨텍스트와 UI를 인터페이스 레벨로만 연결

#### React 최적화
- `rerender-memo`: Dock 아이템/Account 아이콘 컴포넌트 memo
- `rendering-hoist-jsx`: 정적 아이콘/장식 레이어 외부 상수화
- `rendering-conditional-render`: 로그인/비로그인 분기는 삼항 렌더링
- `rerender-functional-setstate`: 입력 옵션 변경은 함수형 업데이트 사용

### 관련 파일

- `src/pages/home/ui/HomePage.tsx` - 메인 구성 갱신
- `src/widgets/` - Dock 네비게이션 위젯 추가
- `src/features/auth-by-email/ui/LoginForm.tsx` - 로그인 이동 연결 참조
- `src/shared/ui/` - glass button/panel 재사용

---

## 작업

- [ ] 2.0 메인 화면과 게스트 전환 UX 구현
- [ ] 2.1 메인 랜덤 지문 + 즉시 타이핑 시작 연동 (커밋 단위)
  **작업 상세:** 초기 렌더 시 mock 지문 로드, 첫 키 입력 시 게임 세션 시작 상태 전환
  **참조:** 이미지 `.claude/tasks/image copy.png`
  - [ ] 2.1.T1 테스트 코드 작성
  - [ ] 2.1.T2 테스트 실행 및 검증

- [ ] 2.2 우중간 Dock형 네비게이션 구현 (커밋 단위)
  **작업 상세:** `PvE/PvP/Dashboard`를 반투명 아크릴 패널 위에 배치, hover 시 확대/조명 효과 적용
  **참조:** 이미지 `.claude/tasks/main.png`
  - [ ] 2.2.T1 테스트 코드 작성
  - [ ] 2.2.T2 테스트 실행 및 검증

- [ ] 2.3 Account 상태 분기 UI 구현 (커밋 단위)
  **작업 상세:** 비로그인 시 Login 텍스트, 로그인 시 원형 프로필 아바타로 전환
  **참조:** 문서 `src/app/providers/auth/AuthProvider.tsx`
  - [ ] 2.3.T1 테스트 코드 작성
  - [ ] 2.3.T2 테스트 실행 및 검증

- [ ] 2.4 비회원 완료 모달 구현 (커밋 단위)
  **작업 상세:** 기록(타수/정확도/시간) 표시 + 로그인 유도 버튼 + 닫기/나중에 액션 연결
  **참조:** 이미지 `.claude/tasks/image-2.png`
  - [ ] 2.4.T1 테스트 코드 작성
  - [ ] 2.4.T2 테스트 실행 및 검증

- [ ] 2.5 i18n 및 접근성 마무리 (커밋 단위)
  **작업 상세:** Dock/모달 텍스트 i18n 키화, aria-label/title 정리
  **참조:** 문서 `CLAUDE.md`
  - [ ] 2.5.T1 테스트 코드 작성
  - [ ] 2.5.T2 테스트 실행 및 검증
