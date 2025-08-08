# 🧠 State Manager Overview

We’ve built a **State Manager** to control player behavior using a modular and extensible structure. Here's how it works:

---

## 📌 Core Concepts

### 1. Inner States (Enum)
These represent the phase of the current action lifecycle:
- `Start` — initialization logic runs once when entering the state
- `Update` — main logic runs every frame while in the state
- `End` — cleanup logic runs once when exiting the state

### 2. Actions (Enum)
These define **what the player is doing**:
- `IDLE`
- `JUMP`
- `RUN`
- etc...

---

## 🧱 Class: `State`

This is the abstract base class for every action (like `IdleState`, `JumpState`, etc). Each state must implement:

- `OnStart()` — called once when entering the state
- `OnUpdate()` — called every frame while the state is active
- `OnEnd()` — called once when exiting the state
- `CheckNextState()` — checks and decides if we should transition to another state
- `InformParent()` — informs the parent state machine about the current state (used during state transitions)

---

## 🧩 Components (States as Components)

Each component (Idle, Jump, etc.) implements the `State` interface, providing:

- `OnStart()` — logic that runs once when entering the state
- `OnUpdate()` — logic that runs every frame while in the state
- `OnEnd()` — logic that runs once when exiting the state
- `CheckNextState()` — logic to determine if a state change should occur
- `InformParent()` — a method called when the state becomes active to notify or initialize communication with the parent `StateMachine`

---

## ⚙️ State Machine Component (`FSM`)

The `FSM` (Finite State Machine) component **stores and manages the current active state** and the **inner lifecycle phase** (`Start`, `Update`, `End`) of that state.

- **Properties:**
  - `currentState` — the active `State` object
  - `innerState` — tracks the current phase (`Start`, `Update`, `End`) of the active state

- **Methods:**
  - `ChangeState(State newState)`  
    Transitions between states gracefully by:
    1. Calling `OnEnd()` on the current state (if any)
    2. Assigning the new state as `currentState`
    3. Setting `innerState` to `Start`
    4. Calling `InformParent()` on the new state to notify it of the transition

  - `ForceState(State newState)`  
    Immediately sets the new state, resets `innerState` to `Start`, **without calling any lifecycle methods**. Use with caution.

- **Lifecycle:**
  The FSM advances `innerState` (from `Start` to `Update` to `End`) internally as the state executes its lifecycle.

---

## 🎮 Player Object Usage

The Player script or other systems interact with the FSM by invoking methods on the current state via the FSM. The Player **does not store or manage `innerState` directly**, but relies on the FSM to handle state phases.

Example `Update()` logic:

```csharp
switch (currentAction) {
    case IDLE:
        if (fsm.innerState == InnerState.Start)
            idleState.OnStart();
        else if (fsm.innerState == InnerState.Update)
            idleState.OnUpdate();
        else if (fsm.innerState == InnerState.End)
            idleState.OnEnd();

        idleState.CheckNextState();
        break;

    case JUMP:
        if (fsm.innerState == InnerState.Start)
            jumpState.OnStart();
        else if (fsm.innerState == InnerState.Update)
            jumpState.OnUpdate();
        else if (fsm.innerState == InnerState.End)
            jumpState.OnEnd();

        jumpState.CheckNextState();
        break;

    // Add more cases for RUN, ATTACK, etc.
}
