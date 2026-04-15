# Tasks: Typing Game PRD - Push 3

> PRD: `.claude/tasks/todo/prd-typing-game.md`
> Push 범위: PvE 선택 플로우(작품 -> 챕터 -> 랭킹 -> 시작) + 목데이터 기반 리더보드
> 상태: 🔲 진행 중

---

### 에이전트 팀 구성

**팀 구성:** coordinator -> developer + tester
**이유:** 페이지 전환 애니메이션, 목록 상태, 리더보드 데이터, 시작 규칙이 결합된 중간 규모 작업

#### 실행 순서
1. **coordinator**: 작업 3.1~3.5 분배
2. **developer**: 기능 구현 및 단계별 커밋
3. **tester**: 상태 전이 케이스와 시각 회귀 점검
4. **coordinator**: 승인 후 다음 Push 진행

#### 역할별 지시사항
- **developer**: "작품/챕터 선택 상태 머신을 구현하고 API 미연결 구간은 mock 리더보드로 채움."
- **tester**: "선택 단계별 UI 위치 이동, 버튼 활성화 조건, 첫 입력 자동 시작을 중점 검증."

### 실행 환경 (task-executor 단독 실행 시)

- **사용 가능 도구:** Read, Write, Edit, Bash, Glob, Grep, Task
- **사용 불가 도구:** Skill, Agent
- **이미지 읽기:** Read 도구로 png/jpg 직접 열람 가능
- **병렬 작업:** 불가 (순차 실행)

### 참조 이미지

| 이미지 | 용도 | 관련 작업 |
|--------|------|-----------|
| `.claude/tasks/image copy.png` | 작품/챕터/랭킹 섹션 전환 구조 | 3.1, 3.2 |
| `.claude/tasks/image.png` | 리스트/패널 밀도, 패딩 감성 보조 | 3.2 |

### 참조 문서

작업 시작 전 반드시 아래 문서를 `Read`로 읽을 것:

| 문서 | 용도 |
|------|------|
| `src/features/demo/config/articles.ts` | 지문/작품 mock 구조 참고 |
| `src/features/typing/model/useTyping.ts` | 첫 입력 시작 조건 연계 |
| `src/app/providers/router/AppRouter.tsx` | PvE 라우팅 구조 반영 |
| `CLAUDE.md` | FSD/i18n/경로 규칙 확인 |

### 적용 규칙 (스킬 요약)

#### 폴더 구조
- PvE UI는 `pages` + `features` 조합으로 작성, 작품/챕터/랭킹 데이터는 기능 slice 내부에 우선 배치
- 3회 이상 재사용 시 `shared`로 승격, 그전에는 slice 내부 콜로케이션 유지
- 동일 계층 간 직접 import 금지

#### 코드 품질
- 가독성: `selectedWork`, `selectedChapter`, `canStart` 상태를 명시 변수로 분리
- 예측 가능성: 단계 전환 함수(`selectWork`, `selectChapter`, `resetSelection`) 명시
- 응집도: PvE 선택 로직은 단일 훅으로 응집
- 결합도: 리더보드 UI는 데이터 소스(mock/real)와 분리

#### React 최적화
- `rerender-memo`: 작품/챕터 리스트 아이템 컴포넌트 memo
- `rendering-hoist-jsx`: 반복되는 라벨/아이콘 상수화
- `rerender-derived-state`: 버튼 활성화는 원시 상태 대신 파생 boolean 사용
- `async-parallel`: 향후 real API 연결 대비 작품/랭킹 병렬 fetch 인터페이스 설계

### 관련 파일

- `src/pages/` - PvE 페이지 추가/수정
- `src/features/typing/` - 입력 시작 이벤트 연동
- `src/features/demo/` - mock 작품/챕터 재사용 또는 확장
- `src/shared/api/` - mock 리더보드 provider

---

## 작업

- [ ] 3.0 PvE 선택 및 시작 플로우 구현
- [ ] 3.1 작품 목록 중심 배치 + 선택 시 우측 이동 애니메이션 (커밋 단위)
  **작업 상세:** 초기 중앙 배치, 작품 선택 후 위치 전환 및 챕터 패널 등장
  **참조:** 이미지 `.claude/tasks/image copy.png`
  - [ ] 3.1.T1 테스트 코드 작성
  - [ ] 3.1.T2 테스트 실행 및 검증

- [ ] 3.2 챕터 선택 후 리더보드/시작 버튼 노출 (커밋 단위)
  **작업 상세:** 챕터 선택 시 작품 목록 숨김, 우측 리더보드 표시, 시작 버튼 활성화
  **참조:** 이미지 `.claude/tasks/image.png`
  - [ ] 3.2.T1 테스트 코드 작성
  - [ ] 3.2.T2 테스트 실행 및 검증

- [ ] 3.3 PvE 리더보드 목데이터 구현 (커밋 단위)
  **작업 상세:** API 미연결 상태에서 mock ranking 데이터 제공, 순위/타수/닉네임 표시
  **참조:** 문서 `src/features/demo/config/articles.ts`
  - [ ] 3.3.T1 테스트 코드 작성
  - [ ] 3.3.T2 테스트 실행 및 검증

- [ ] 3.4 싱글 게임 첫 입력 시작 규칙 구현 (커밋 단위)
  **작업 상세:** 별도 타이머 없이 첫 타건 시점에 게임 세션 시작
  **참조:** 문서 `src/features/typing/model/useTyping.ts`
  - [ ] 3.4.T1 테스트 코드 작성
  - [ ] 3.4.T2 테스트 실행 및 검증

- [ ] 3.5 glass 스타일 일관성 적용 (커밋 단위)
  **작업 상세:** 작품/챕터/랭킹 패널에 반투명 아크릴 톤, blur, border, shadow 공통 적용
  **참조:** 이미지 `.claude/tasks/image copy.png`
  - [ ] 3.5.T1 테스트 코드 작성
  - [ ] 3.5.T2 테스트 실행 및 검증
