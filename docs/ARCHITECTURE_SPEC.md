# J.Paracosm Game Technical Architecture Specification

## 1. Overview
본 문서는 Core Loop와 UI Flow를 바탕으로 설계된 게임의 기술적 구조(Technical Architecture)를 정의합니다. 시스템은 단일 진실 공급원(Single Source of Truth, SSOT) 원칙을 따르며, 모든 상태 변화는 명시적인 State Transition Logic을 통해서만 발생해야 합니다.

## 2. System Components (Modular Breakdown)
| Component | 책임 (Responsibility) | 기술적 특성 | 주요 인터페이스 |
| :--- | :--- | :--- | :--- |
| **Game State Manager** | 모든 게임 데이터의 SSOT 관리 및 비즈니스 로직 수행. 상태 전이 트리거. | 불변(Immutable) 원칙 준수 필수. 순수 함수 호출 지향. | `handleScoreIncrease(state, points): GameState` |
| **Input Handler** | 사용자 입력 (터치/클릭) 수신 및 정제. 시스템에 적합한 이벤트 객체 생성. | 이벤트를 해석하여 `GameStateManager`의 메서드 호출로 변환. | `onTap(event: InputEvent): void` |
| **View Model / Renderer** | 현재 GameState를 받아 UI 컴포넌트의 속성(Props)으로 매핑. 데이터 로직 처리 금지. | 반응형 프레임워크 기반 (React/Next.js 권장). | `render(state: GameState): ReactNode` |
| **Persistence Layer** | 게임 세션 데이터를 영구적으로 저장 및 복원. | 비동기 I/O 처리 필수. 로컬 스토리지/DB 추상화 계층 제공. | `saveGame(state: GameState): Promise<void>` |

## 3. Data Structures (TypeScript Definition)
*(생략된 인터페이스는 위의 코드 블록을 참조합니다.)*

## 4. Core State Transition Logic Specification

### 4.1. Score Increase System
**[Input]:** `GameState` (현재 상태), `pointsGained: number`
**[Logic]:** 점수 변동 로직 실행 $\rightarrow$ 새로운 `score`와 `playerStats` 업데이트.
**[Constraint]:** `isGameOver` 플래그가 true일 경우, 모든 변화를 막아야 합니다.

### 4.2. Level Progression Logic
**[Input]:** `GameState` (현재 상태)
**[Logic]:** 누적 점수가 다음 레벨 임계치를 초과했는지 검사 $\rightarrow$ 성공 시 능력치 보정 및 자원 지급을 통해 새로운 `GameState` 반환.

---