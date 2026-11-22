# Entregable · Sesión 3 — Copilotos IA

- **Nombre / usuario:**  Felipe Velandia / FelipeDevIos
- **Fecha de entrega:**  23 Jun
- **Repo auditado en la Parte A** (solo tipo/contexto, NO el código): _p. ej. "monorepo TypeScript de trabajo, ~8 meses"_

---

## 1. Hallazgos de la auditoría (Parte A)

> 3-5 cosas que el agente **no pudo inferir** del código y que tendrías que decirle explícitamente.
> Redáctalas para que otra persona las entienda sin contexto adicional. **Sin código propietario ni secretos.**

1. Arquitectura en tests
2. Detalles de funcionamiento del proyecto pero en pequeños detalles
3. Algunos detalles de la parte gráfica que conforman el proyecto
4.
5.

---

## 2. SKILL.md de la skill creada (Parte B)

> Pega aquí el contenido completo de tu `.claude/skills/<nombre-skill>/SKILL.md`
> (o enlaza al archivo en tu repositorio sandbox).

```markdown
---
name: pr-architecture-review
description: Reviews PR code changes against the RobotsBattle iOS architecture. Generates a Mermaid diagram of new features, validates architectural integrity (MVC, protocols, delegation), and provides improvement suggestions based on SOLID principles and software lifecycle best practices. Use when reviewing pull requests, analyzing new features, or when the user asks to review incoming changes.
---

# PR Architecture Review

## When to Use

Trigger this skill when the user asks to:
- Review a PR or incoming code changes
- Analyze new features for architectural compliance
- Generate a visual diagram of new code
- Validate that changes follow project conventions

## Repository Architecture Reference

**Pattern**: MVC with Protocol-Based Delegation

```
RobotsBattleiPhone/
├── Models/          → Data models (Robot, Prize, BattleCell, Cell protocol)
├── Game/            → Game logic (Game class + Extensions)
├── Helper/          → Utilities (Position, DistanceCalculator, Records)
├── View/            → UICollectionView cells + XIBs
├── Base.lproj/      → Storyboards
├── BoardViewController.swift        → Main ViewController
└── BoardViewController+Extensions.swift → VC helper methods
```

**Core Architectural Rules:**
1. `Cell` protocol defines position-based behavior for all board elements
2. `Game` class owns game state; communicates to ViewController via `UpdateViewStateProtocol` and `TimeManageProtocol`
3. Models are value types (structs) except `Robot` (class, due to mutation in game loop)
4. `Records` is a singleton for analytics/metrics
5. UI updates flow through delegation, never directly from Game to UIKit
6. Position navigation uses Pythagorean theorem via `Distance.Calculator`
7. Board limits defined as global constant `boardLimits = (min: 1, max: 7)`
8. Extensions organize responsibilities (e.g., `Game+Extensions`, `BoardViewController+Extensions`)

## Workflow

### Step 1: Gather the PR Diff

Obtain the code changes using one of:
- `gh pr diff <PR_NUMBER>` for GitHub PRs
- `git diff main...HEAD` for local branch changes
- `git diff` for uncommitted changes

### Step 2: Generate Mermaid Feature Diagram

Create a Mermaid class/sequence diagram showing:
- New types introduced and their relationships
- How new code connects to existing architecture
- Data flow between new and existing components

Use this template:

```markdown
## Feature Architecture Diagram

\`\`\`mermaid
classDiagram
    direction TB

    %% Existing types (context)
    class ExistingType {
        <<existing>>
    }

    %% New types introduced
    class NewType {
        <<new>>
        +property: Type
        +method()
    }

    ExistingType <|-- NewType : relationship
\`\`\`
```

For behavioral changes, prefer sequence diagrams:

```markdown
\`\`\`mermaid
sequenceDiagram
    participant VC as BoardViewController
    participant G as Game
    participant NewComponent
    
    VC->>G: action()
    G->>NewComponent: newBehavior()
    NewComponent-->>G: result
    G-->>VC: updateBoard()
\`\`\`
```

### Step 3: Architecture Integrity Check

Evaluate each change against these criteria:

| Criteria | Pass/Fail | Notes |
|----------|-----------|-------|
| Respects MVC separation | | |
| Uses protocols for communication | | |
| Models remain UI-independent | | |
| Game logic stays in Game class/extensions | | |
| ViewController only handles UI | | |
| New helpers are stateless or justified | | |
| Extensions are used for organization | | |
| Naming follows existing conventions | | |
| No circular dependencies introduced | | |
| Unit testable (dependencies injectable) | | |

**Red flags to watch for:**
- UIKit imports in Model or Game files
- Direct ViewController references from Game logic
- New singletons without justification
- Breaking the `Cell` protocol contract
- Bypassing delegation for UI updates
- Hardcoded values that should reference `boardLimits`

### Step 4: Best Practices & Lifecycle Recommendations

Provide a prioritized list of improvements:

**Format:**
```
### Recommendations

#### Critical (must fix before merge)
- [ ] Issue description → Suggested fix

#### Improvements (should fix)
- [ ] Issue description → Suggested fix

#### Nice to have (optional)
- [ ] Issue description → Suggested fix
```

**Evaluate against:**

1. **SOLID Principles**
   - Single Responsibility: Does each type have one reason to change?
   - Open/Closed: Can behavior be extended without modifying existing code?
   - Liskov Substitution: Do subtypes honor protocol contracts?
   - Interface Segregation: Are protocols focused and minimal?
   - Dependency Inversion: Do high-level modules depend on abstractions?

2. **Software Lifecycle**
   - Testability: Are new components unit testable? Are tests included?
   - Maintainability: Is the code readable and self-documenting?
   - Scalability: Will this work if the board grows or rules change?
   - Documentation: Are public interfaces documented with `///` comments?

3. **Swift/iOS Conventions**
   - Prefer structs over classes when possible
   - Use `private` access control by default
   - Follow existing naming patterns in the project
   - Avoid force unwraps; use guard/optional binding

## Output Template

Structure your review as:

```markdown
# PR Review: [Feature Name]

## 1. Feature Diagram
[Mermaid diagram here]

## 2. Architecture Compliance
[Table with pass/fail evaluation]

### Summary
[Brief paragraph: does this PR maintain architectural integrity?]

## 3. Recommendations
[Prioritized list organized by severity]

## 4. Verdict
- [ ] Ready to merge
- [ ] Needs minor changes
- [ ] Needs architectural revision
```

---

## 3. Diario de decisiones

*Skill creada:* PR reviewer

*Decisiones de diseño tomadas:*
	create  a mermaid schema analyzing the new feature
	inspect the incoming changes based on the repository architecture and evaluating that the new features dont brake the architecture
	create a list of possible chages based on the best software creation practices and software life cycle

*Qué me resultó fácil:*
	La descripción para la creacion del Skill

*Qué me resultó ambiguo o difícil de decidir:*
	para refinar, el como activar la skill

*Tiempo real invertido:*
	3 minutos ocmo maximo

*Qué probarías si tuvieras más tiempo:*
	crear varios Pr para probar y refinar el fincionamiento de la skill

*¿Usaste IA para crear la skill?* Use AI para crear la skill, muy poco refinamiento
-

### Resultado de la prueba (Paso 8)

- ¿Se activó cuando lo esperabas? Si
- ¿El resultado fue el que querías? Si
- Si no, ¿qué crees que falló? (no la "arregles" — documenta el primer intento)
