# 🧠 State Manager Overview

We’ve built a **State Manager** to control player behavior using a modular and extensible structure. Here's how it works:

---

## 📌 Core Concepts

### 1. Inner States (Enum)
These represent the phase of the current action:
- `Start`
- `Update`
- `End`

### 2. Actions (Enum)
These define **what the player is doing**:
- `IDLE`
- `JUMP`
- `RUN`
- etc...

---

## 🧱 Class: `State`
This is the base class for every action (like `IdleState`, `JumpState`, etc).  
Each state has:
- `onStart()`
- `onUpdate()`
- `onEnd()`
- `checkNextState()`

---

## 🧩 Components (States as Components)

Each component (Idle, Jump, etc.) has:
- `onStart()` — logic that runs once when entering the state
- `onUpdate()` — logic that runs every frame while in the state
- `onEnd()` — logic that runs once when exiting
- `checkNextState()` — checks if we should switch to another state

---

## 🎮 Player Object

In the main Player script:

We store:
- `currentAction` (e.g., `IDLE`, `JUMP`)
- `innerState` (e.g., `Start`, `Update`, `End`)
- `currentStateComponent` (the actual state object like `IdleState`)

### In `update()`:

```csharp
switch (currentAction) {
    case IDLE:
        if (innerState == Start)
            idleState.onStart();
        else if (innerState == Update)
            idleState.onUpdate();
        else if (innerState == End)
            idleState.onEnd();

        idleState.checkNextState();
        break;

    case JUMP:
        if (innerState == Start)
            jumpState.onStart();
        else if (innerState == Update)
            jumpState.onUpdate();
        else if (innerState == End)
            jumpState.onEnd();

        jumpState.checkNextState();
        break;

    // Add more cases for RUN, ATTACK, etc.
}
