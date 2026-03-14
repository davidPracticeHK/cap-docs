# Skill Blueprint: CAP Java Error Handling Assistant

## 1) Source Material Summary (from `Java/Messaging/reference.md`)

This material explains how CAP Java handles errors and user-facing messages:

- Use **exceptions** (especially `ServiceException`) when processing must stop and transaction rollback is required.
- Use the **Messages API** when you want to collect `error`, `warn`, `info`, or `success` messages during request handling.
- `messages.throwIfError()` lets you collect multiple validation errors first, then abort once at the end.
- Message text supports **formatting and localization**.
- CAP provides built-in translations for validation messages; apps can override text via `messages.properties`.
- Message `target` is key for SAP Fiori field-level display and differs for CRUD vs bound actions/functions.
- Error responses can be customized through `ERROR_RESPONSE` handlers on `ApplicationLifecycleService`.

## 2) How to Generate an Agent Skill from this Material

Use this as a repeatable process to convert docs into a usable Codex skill.

### Step A — Define scope and outcome

Create a skill focused on one clear job:

- **Job**: Help developers choose between exceptions and Messages API in CAP Java, and produce correct code snippets.
- **Outputs**:
  - Recommended pattern (hard stop vs collected validation)
  - Example Java snippet
  - Guidance on localization and `target`
  - Optional error handler customization snippet

### Step B — Extract decision rules from the material

Capture if/then rules the agent can apply:

1. If request must stop immediately → throw `ServiceException`.
2. If multiple validation issues should be reported together → use `Messages` + `throwIfError()`.
3. If draft messages should not persist → set `transition(true)`.
4. If message must map to UI field → add `target(...)` correctly.
5. If default runtime message text is insufficient → override in `messages.properties`.
6. If protocol response needs custom shaping → use `ERROR_RESPONSE` handler.

### Step C — Package the rules as a skill contract

Build `SKILL.md` with:

- **When to use**
- **Inputs expected from user** (event type, entity, desired UX)
- **Workflow** (decision tree)
- **Output format** (short explanation + snippet + caveats)
- **Safety checks** (preserve message code/target when rewriting errors)

### Step D — Add reusable templates

Include copy/paste snippets for:

- `ServiceException` usage
- `Messages` collection + `throwIfError()`
- CRUD and action/function target examples
- `ERROR_RESPONSE` handler override

### Step E — Validate the skill

Test with prompts like:

- “I need to return 3 validation errors in one response for a bound action.”
- “I need a field-level error for `title` in Fiori draft.”
- “Override default `@assert.range` message for one entity.”

A good skill should produce consistent, context-aware code and mention trade-offs.

## 3) Example `SKILL.md` Starter

```markdown
# CAP Java Error & Messaging Skill

## When to use
Use this skill when the user asks how to implement CAP Java error handling, validation messaging, localization, or Fiori message targeting.

## Inputs to request
- Event type (CRUD / bound action / function)
- Whether processing should continue or abort
- Need for field-level UI targeting
- Need for localization/custom text

## Workflow
1. Determine stop-now vs collect-and-throw-later strategy.
2. Choose `ServiceException` or `Messages` pattern.
3. Add `target(...)` if UI field mapping is needed.
4. Add localization/custom text guidance if requested.
5. If response customization requested, provide `ERROR_RESPONSE` handler example.

## Output format
- Decision summary (2-4 bullets)
- Java snippet (minimal and compilable)
- Notes: persistence of draft messages, localization, and target semantics

## Guardrails
- Prefer `ServiceException` for immediate abort.
- For aggregated validation errors, use `messages.error(...)` + `messages.throwIfError()`.
- When replacing protocol messages, preserve original code and target where possible.
```

## 4) Practical Tip

If you want this to be immediately usable by Codex, place the finished skill at:

- `$CODEX_HOME/skills/cap-java-error-messaging/SKILL.md`

and keep examples concise so the skill stays fast and predictable.
