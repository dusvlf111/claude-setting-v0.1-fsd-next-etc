# Tasks: Typing Game PRD - Push 1

> PRD: `.claude/tasks/todo/prd-typing-game.md`
> Push 범위: 앱 기반 구축 + API 목데이터 전환 체계 + macOS 유리 질감 디자인 토큰
> 상태: 🔲 진행 중

---

### 에이전트 팀 구성

**팀 구성:** coordinator -> developer + tester
**이유:** 설정/스타일/데이터 계층을 동시에 손대는 다중 파일 작업이며, 구현과 검증 병렬 분리가 필요함

#### 실행 순서
1. **coordinator**: 본 문서 기준으로 작업 1.1~1.4를 developer에 순차 할당
2. **developer**: 작업별 커밋 단위 구현 후 결과 공유
3. **tester**: 각 커밋마다 타입체크/빌드/테스트 수행 후 결함 리포트
4. **coordinator**: 결함 재할당 및 Push 완료 판정

#### 역할별 지시사항
- **developer**: "실제 API 호출부를 대체 가능한 mock adapter 구조로 분리하고, macOS acrylic 스타일 토큰을 전역 스타일에 반영."
- **tester**: "각 단계 종료 시 `npx tsc --noEmit`, `npm run build`, `npm run test -- --run` 실행."

### 실행 환경 (task-executor 단독 실행 시)

- **사용 가능 도구:** Read, Write, Edit, Bash, Glob, Grep, Task
- **사용 불가 도구:** Skill, Agent
- **이미지 읽기:** Read 도구로 png/jpg 직접 열람 가능
- **병렬 작업:** 불가 (순차 실행)

### 참조 이미지

| 이미지 | 용도 | 관련 작업 |
|--------|------|-----------|
| `.claude/tasks/image copy.png` | 전체 레이아웃 베이스 확인 | 1.2, 1.3 |
| `.claude/tasks/main.png` | 메인 화면 톤/배치 보조 참고 | 1.2 |

### 참조 문서

작업 시작 전 반드시 아래 문서를 `Read`로 읽을 것:

| 문서 | 용도 |
|------|------|
| `CLAUDE.md` | FSD, i18n, import 규칙 준수 |
| `ARCHITECTURE.md` | 앱 레이어/라우팅 구조 이해 |
| `.claude/skills/folder-structure/SKILL.md` | 파일 배치 기준/금지 규칙 |
| `.claude/skills/code-quality/SKILL.md` | 가독성/예측가능성/응집도/결합도 기준 |
| `.claude/skills/vercel-react-best-practices/SKILL.md` | 렌더링/리렌더/번들 최적화 기준 |

### 적용 규칙 (스킬 요약)

#### 폴더 구조
- 신규 mock 데이터는 `shared` 또는 feature slice 내부에 위치시키고, 외부 import는 각 slice의 `index.ts` public API로 제한
- 의존 방향은 `app -> pages -> widgets -> features -> entities -> shared`를 유지
- 같은 계층 slice 직접 import 금지, UI 문자열은 i18n 키 기반으로 관리

#### 코드 품질
- 가독성: `mock`/`real` 분기 조건을 명시적 함수로 분리
- 예측 가능성: 환경변수 기반 분기(`VITE_USE_MOCK_API`)는 단일 진입점에서만 수행
- 응집도: API fallback 로직은 `shared/api` 한곳에 모음
- 결합도: 페이지/컴포넌트가 API 타입 세부 구현에 직접 의존하지 않게 추상화

#### React 최적화
- `rerender-memo`: 네비게이션/옵션 바 등 정적 비율이 높은 컴포넌트는 memo 적용
- `rendering-hoist-jsx`: 변하지 않는 장식 JSX, 토큰 상수는 컴포넌트 외부로 호이스팅
- `rendering-conditional-render`: `&&` 체이닝 대신 명시적 삼항으로 분기
- `bundle-barrel-imports`: 필요한 경로 직접 import, 과도한 barrel import 회피

### 관련 파일

- `src/shared/api/httpClient.ts` - API 클라이언트 분기 진입점 (수정)
- `src/shared/config/env.ts` - mock 사용 플래그 (수정)
- `src/app/styles/global.css` - 아크릴/글래스모피즘 전역 토큰 (수정)
- `src/shared/i18n/locales/ko/common.json` - 공용 텍스트 키 (수정)

---

## 작업

- [ ] 1.0 API fallback + glass design 기반 구축
- [ ] 1.1 목데이터 활성화 환경 분기 추가 (커밋 단위)
  **작업 상세:** `VITE_USE_MOCK_API=true`일 때 네트워크 요청 대신 mock adapter를 사용하도록 공통 분기 작성
  **참조:** 문서 `CLAUDE.md`, `src/shared/config/env.ts`
  - [ ] 1.1.T1 테스트 코드 작성
  - [ ] 1.1.T2 테스트 실행 및 검증

- [ ] 1.2 공통 mock 데이터 레지스트리 생성 (커밋 단위)
  **작업 상세:** 홈/PvE/PvP/대시보드에 필요한 최소 더미 데이터 스키마와 샘플 생성 (`shared/api/mock`)
  **참조:** 이미지 `.claude/tasks/image copy.png`
  - [ ] 1.2.T1 테스트 코드 작성
  - [ ] 1.2.T2 테스트 실행 및 검증

- [ ] 1.3 macOS 아크릴 디자인 토큰 추가 (커밋 단위)
  **작업 상세:** 배경 블러, 반투명 패널, 하이라이트/보더, 그림자 규칙을 CSS 변수화 (`--glass-*`, `--dock-*`)
  **참조:** 이미지 `.claude/tasks/main.png`
  - [ ] 1.3.T1 테스트 코드 작성
  - [ ] 1.3.T2 테스트 실행 및 검증

- [ ] 1.4 공통 UI 유틸 클래스 정리 (커밋 단위)
  **작업 상세:** 버튼/카드/패널에 재사용 가능한 glass class를 만들고 기존 컴포넌트에 최소 적용
  **참조:** 문서 `.claude/skills/vercel-react-best-practices/SKILL.md`
  - [ ] 1.4.T1 테스트 코드 작성
  - [ ] 1.4.T2 테스트 실행 및 검증
