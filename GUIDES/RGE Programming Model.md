if you haven't read the the first part of [BRM5 RGE 2.0 — Command & Programming Reference.md](https://github.com/borisbruh/BORTECH-SYSTEMS/edit/main/GUIDES/BRM5%20RGE%202.0%20%E2%80%94%20Command%20%2526%20Programming%20Reference.md). 

I would recommend reading that part first before reading this.

---

Otherwise here's how you combine those commands to make RGE do shit.



### RGE Programming Model

## 1. What RGE Programming Actually Is

RGE programming is based around objects, triggers, Trigger Groups, and executable commands.

Unlike a traditional programming language such as Lua, RGE does not primarily use scripts containing functions and variables. Instead, behaviour is constructed by configuring objects and connecting triggers to ordered command sequences.

A typical RGE system can be thought of as:

```text
OBJECTS
  │
  │ player interaction / entity detection
  ▼
TRIGGERS
  │
  │ activation
  ▼
TRIGGER GROUP
  │
  │ execution of commands in order
  ▼
EXECUTABLE COMMANDS
  │
  ├── move
  ├── tween
  ├── wait
  ├── ...
  │
  ▼
OBJECT STATES CHANGES
```

# Example

A button-controlled door could be structured as:

```text
Player
  │
  │ presses button
  ▼
Door Button Trigger
  │
  │ activates
  ▼
"Door_Open" Trigger Group
  │
  ├── Tween Door → Open position
  ├── Wait 5 seconds
  ├── Tween Door → Closed position
  └── Reset Trigger Group
```

The important distinction is that the button itself does not contain the door's behaviour.

The button provides the event.

The Trigger Group contains the behaviour.

The executable commands perform the actions.



## 2. The Four Main Components

Objects, Triggers, Trigger Groups (TG), Executable Commands

- - -

# Objects

Objects are the things being manipulated by RGE.

Examples:

```text
Part
Door
Button
Wedge
Light
Vehicle
Barrier
Model/group
```

Objects can generally be identified by their name and/or UID, depending on the command being used.

Objects provide the physical things that RGE commands operate on.

For example:

```text
Door
```

can be moved, resized, recolored, hidden, etc.

- - -

# Triggers

A Trigger is an object/event that can cause a Trigger Group to activate.

Examples include:

```text
Button activation
Player detection
Vehicle detection
Other trigger conditions
```

A Trigger can be connected to one or more Trigger Groups.

Conceptually:

```text
[BUTTON]
    │
    ├──────► [Door_Open TG]
    │
    └──────► [Room_Light TG]
```

This means one event can cause multiple independent behaviours.

- - -

Trigger Groups

A Trigger Group is an ordered sequence of executable commands.

For example:

```text
Door_Open

1. tween Door ...
2. wait 5
3. tween Door ...
4. reset ...
```

When the Trigger Group activates, its executable commands are processed in order.

Trigger Groups are therefore the closest equivalent to a program/script within RGE.

- - -

Executable Commands

Executable commands are the individual actions performed by a Trigger Group.

For example:

```text
tween
wait
move
...
```

A Trigger Group can combine multiple commands to create more complex behaviour.

For example:

```text
1. tween Door_Left → open
2. tween Door_Right → open
3. wait 5
4. tween Door_Left → closed
5. tween Door_Right → closed
```

The result is a complete door sequence despite there being no traditional "door script."

- - -

## 3. Event → Logic → Action

A useful way to understand RGE is:

```text
EVENT
  ↓
TRIGGER
  ↓
TRIGGER GROUP
  ↓
COMMAND SEQUENCE
  ↓
ACTION
```

Example: Automatic Door

```text
EVENT
Player enters detection area
        ↓
TRIGGER
Player detection trigger
        ↓
LOGIC
Door_Open Trigger Group
        ↓
COMMANDS
Tween door open
Wait
Tween door closed
        ↓
ACTION
Door physically opens and closes
```

This distinction is important because an event and an action are not necessarily the same thing.

A trigger can activate a Trigger Group without directly performing the final action itself.

- - -

## 4. Trigger Group Execution

When a Trigger Group activates, its executable commands run in their configured order.

For example:

```text
Trigger Group: Test

1. command A
2. command B
3. command C
```

will execute conceptually as:

```text
A
↓
B
↓
C
```

Only 1 command, "wait" can affect when subsequent commands execute. All other commands execute basically instantly.

For example:

```text
1. tween Door 3 seconds
2. wait 5 seconds
3. tween Door 3 seconds
5. wait 5 seconds
```

can be understood as a timeline:

```text
OPEN

instantly tween command executes, which starts the animation
 ▼
|+|─|───── 3 second animation ─────►|─|──────────────────|─|─|───────────────────────────────|─|────────────────────|─|

instantly after, wait command executes, which starts wait timer
   ▼
|─|+|────── 5 second wait ──────────|─|─────────────────►|─|─|───────────────────────────────|─|────────────────────|─|

CLOSE

after the 5 seconds wait, instantly tween command executes, which starts the animation
                                                            ▼
|─|─|───────────────────────────────|─|──────────────────|+|─|───── 3 second animation ─────►|─|────────────────────|─|

instantly after, wait command executes, which starts wait timer
                                                              ▼
|─|─|───────────────────────────────|─|──────────────────|─|+|────── 5 second wait ──────────|─|───────────────────►|─|
```

## 5. Trigger Group State

Trigger Groups have their own state.

A Trigger Group will become INACTIVE when triggered and will need to be reset before it can be activated normally again.

For example:

```text
ACTIVE
   │
   │ trigger activates TG
   ▼
INACTIVE / EXECUTING
   │
   │ commands finish
   ▼
[STATE DEPENDS ON TG CONFIGURATION]
   │
   │ reset
   ▼
ACTIVE
```

This is important when designing reusable systems.

For example, a door that should work repeatedly will need:

```text
Button
  ↓
Door_Open_&_Close TG
  ↓
Opens
  ↓
Waits
  ↓
Closes
  ↓
Reset TG
```

## 6. Trigger Groups as Functions or Small Programs

A useful way to think about a Trigger Group is:

A Trigger Group is a function or small RGE program that runs when a linked trigger activates it.

For example:

```text
TG: Emergency_Lights

1. Color Light_A Red
2. Color Light_B Red
3. Color Light_C Red
4. Wait 1
5. Color Light_A Dim-Red
6. Color Light_B Dim-Red
7. Color Light_C Dim-Red
8. Wait 1
9. ...
```

A more complicated system can use multiple Trigger Groups:

```text
             ┌──► TG - Door_Open
Button ──────┤
             └──► TG - Door_Lights
```

This allows individual pieces of behaviour to be separated rather than putting everything into one enormous Trigger Group.

- - -

## 7. Console vs Trigger Group Programming

RGE has an important distinction between commands entered through the console and commands that can be executed by a Trigger Group.

For example:

```text
CONSOLE
    │
    ├── Create/configure objects
    ├── Create/configure triggers
    ├── Configure Trigger Groups
    └── Insert executable commands
```

Whereas:

```text
TRIGGER GROUP
    │
    └── Executes supported runtime commands
```

Not every command available through the console should automatically be assumed to be valid as a Trigger Group executable command.

Command-context compatibility should therefore be checked against the command compatibility reference.

- - -

## 8. Building an RGE System

Most RGE systems can be approached in this order:

```text
1. Define requirements
        ↓
2. Create/Design and configure objects
        ↓
3. Define the event(s)
        ↓
4. Create/configure triggers
        ↓
5. Create Trigger Groups
        ↓
6. Add executable commands
        ↓
7. Connect triggers → Trigger Groups
        ↓
8. Configure reset/loop/state behaviour
        ↓
9. Test the system
```

For example, when building a door:

```text
DEFINE REQUIREMENTS
├── Door object, size {0.5, 8, 4}
├── Detection trigger, area {10, 10, 10}
└── Desired opened & closed positions, move door by 5 stds sideways.

BUILD
├── Create/Design & Configure Door
└── Create Detection Trigger Area

PROGRAM
├── Create & Configure Door_Open TG 
├── Add tween
├── Add wait
├── Add Activate Door_Close TG
└── Add reset (always last)
├── Create & Configure Door_Close TG
├── Add tween
├── Add wait
└── Add reset (always last)

CONNECT
└── Button → Door_Open

TEST
├── Enter Detection area
├── Verify opening
├── Verify timing
├── Verify closing
└── Verify it can be triggered again
```

- - -
