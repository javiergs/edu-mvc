# MVC Skeleton

A small **Java Swing teaching example** showing how a desktop application can separate user interaction, background processing, and presentation using **Model–View–Controller (MVC)** and event notifications.

The project also illustrates the **Observer** and **Singleton** patterns, along with Java's `Runnable` interface.

## What does it do?

The window is titled **Skeleton**. It contains a **Connection** menu, a **Help** menu, a central display, and a status bar.

- **Connection → Start** starts publishing the current timestamp approximately once per second.
- **Connection → Stop** pauses timestamp publication.
- Each new timestamp is appended to a text area; an adjacent panel changes to a random background color.
- The status bar displays the most recent timestamp.
- **Help → About** shows an information dialog.

The engine thread starts when the window is constructed, but initially waits for **Start** before publishing updates.

This is a demonstration of architectural concepts, **not** an implementation of Nielsen's usability heuristics.

## Architecture

```text
                  menu actions
  User ─────────────────────────► Controller
                                    │
                                    ▼
                                   Main
                                    │
                             pause / resume
                                    ▼
                              Engine (Runnable)
                                    │
                             timestamp events
                                    ▼
                           Blackboard (Singleton)
                             /             \
                     property change   property change
                          ▼                  ▼
                      ViewPanel           StatusBar
```

| Class | Responsibility |
| --- | --- |
| `Main` | Constructs the JFrame, menus, and application components |
| `Controller` | Handles Start, Stop, and About menu actions |
| `Engine` | Runs on a background thread and produces timestamps |
| `Blackboard` | Shares state and broadcasts property-change notifications |
| `ViewPanel` | Displays incoming values and changes color |
| `StatusBar` | Displays the latest timestamp |

**Patterns and concepts:** MVC-inspired separation of responsibilities, Observer through `PropertyChangeListener`, Singleton through `Blackboard.getInstance()`, and multithreading through `Runnable`.

## Requirements

- **JDK 21**
- **Apache Maven**
- A graphical desktop environment (the application uses Swing)

## Build and run

From the repository root:

```bash
mvn clean package
java -cp target/classes Main
```

The project uses only the Java standard library and requires no third-party dependencies.

## Project structure

```text
pom.xml
src/
└── main/
    └── java/
        ├── Main.java
        ├── Controller.java
        ├── Engine.java
        ├── Blackboard.java
        ├── ViewPanel.java
        └── StatusBar.java
```

## Known limitations and teaching opportunities

The code is intentionally compact. Before treating it as a production-ready application, consider the following:

1. **Swing thread safety:** `Engine` fires property-change events from its worker thread; listeners currently update Swing components directly rather than scheduling updates on the Event Dispatch Thread (EDT).
2. **Engine shutdown bug:** `Engine.stop(boolean)` assigns `this.stop = this.stop` instead of using its parameter. This method is not currently invoked by the menu; the menu's **Stop** action pauses publishing.
3. **Thread visibility:** the engine's pause and stop flags are not declared `volatile` or otherwise synchronized.
4. **Startup and shutdown:** the window is created outside the EDT, and there is no explicit worker-thread shutdown sequence.
5. **Singleton initialization:** `Blackboard.getInstance()` is not thread-safe if first called concurrently.
6. **Testing and packaging:** no automated tests or named Java packages are included.

These are useful starting points for exercises on Java concurrency, GUI event dispatch, and software design.
