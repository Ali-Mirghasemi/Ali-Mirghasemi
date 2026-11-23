# C-RULES.md — Modular C / C++ Coding Rules for AI Agents

> **Owner:** Ali Mirghasemi  
> **Scope:** New and modified C99+, C++11+, embedded firmware, embedded Linux, drivers, reusable libraries, tests, and build tooling.  
> **Purpose:** Preserve existing coding conventions and engineering constraints while allowing the user to activate only selected guideline modules.  
> **Updated:** 2026-11-21  
> **Based on:** C-RULES-updated-v3.md; rules retained and reorganized by responsibility.
> **v6 addition:** Optional `FORMATTER` module for reproducible VS Code / Clang-Format project setup and patch delivery.

## START HERE — Mandatory agent module-selection prompt

> **Agent instruction — run this interaction before editing C/C++ source, build files, or project formatter configuration when this document is provided to a chat, coding agent, or project.** This is a workflow instruction to the agent, not a request to alter the code yet.
>
> **FIRST ACTION:** Ask the user which `C-RULES.md` modules they want applied to the stated scope. Offer **one multi-select question box with checkboxes** if the current AI/chat host genuinely supports interactive input. Include a working **ALL** option that selects or clears all optional modules in one action. If interactive controls are unavailable, show the complete Markdown checklist and the copy/paste selection patterns below. Do **not** claim to have opened an interactive box when the host cannot render one.
>
> **Only skip this question when the user's current instruction already provides an unambiguous module selection** (for example, `Mode: ONLY; Modules: [SYNTAX]`, `Mode: ALL`, or the explicit phrase "apply all C-RULES"). Do not ask again for already supplied choices. If the user only asks you to read or discuss the file, do not block that non-editing request with a mandatory selection question.
>
> **Before starting edits:** Resolve the selected modules and scope, display a short summary, and proceed according to the request. When selection is still missing, **wait for the user's answer**; do not silently default to ALL. Do not require a second confirmation after the user has selected the modules, unless a separate higher-risk action requires approval.

### Question box specification (when the host supports it)

- **Title:** `Select C-RULES modules to apply`
- **Question:** `Which guidelines should I apply to the requested file(s) or project? You may select multiple modules.`
- **Control:** Multi-select **checkboxes**, not a single-choice dropdown. The checkboxes must be actionable and connected to the resulting selection.
- **Select-all control:** `ALL — Apply all modules` selects all 16 optional modules when checked, and clears them when unchecked. If the user deselects an individual module after selecting ALL, clear the ALL checkbox and retain the other individually selected modules.
- **Always on:** `CORE` (correctness, safe scope, compatibility) is mandatory and cannot be unchecked; show it as locked/read-only or explain it beside the form.
- **Options:** `SYNTAX`, `FORMATTER`, `NAMING`, `API`, `CONTROL_FLOW`, `STRINGS`, `MEMORY`, `PERFORMANCE`, `MODULARITY`, `CONFIG`, `STM32`, `BUILD`, `DOCUMENTATION`, `SECURITY`, `TESTING`, `DELIVERY`. Show a short description beside each option using the module registry below.
- **Scope:** Use the file or project already identified in the user's request. Ask for a path only if the target scope is genuinely missing. Never broaden a provided scope.
- **Submit action:** Apply the chosen modules to the provided scope; do not run the task if no valid selection has been submitted. If none of the optional modules is checked, ask whether the user wants `CORE`-only handling rather than assuming ALL.
- **Selection mapping:** When all options remain selected, resolve as `Mode: ALL; Exclude: []`. For a subset, resolve as `Mode: ONLY; Modules: [selected IDs]`. Keep `CORE` active in both modes.
- Use the host's **actual supported** interactive question/form mechanism, if any. Do not invent UI tools or buttons. If a real multi-select cannot be displayed, use the text fallback immediately.

### Text fallback — show this when no checkbox UI is available

Ask the user to select modules by checking the boxes (`[x]`) or to copy and edit the configuration block. Supply the following options:

```text
Select the rules to apply (mark [x]):
[ ] ALL — all optional modules
[ ] SYNTAX         — C/C++ language, formatting, indentation
[ ] FORMATTER      — install VS Code + Clang-Format project settings
[ ] NAMING         — naming conventions and enums
[ ] API            — interfaces, types, structs, public headers
[ ] CONTROL_FLOW   — switches, branches, loops, dispatch
[ ] STRINGS        — formatting, copying, parsing
[ ] MEMORY         — stack, heap, lifetime, concurrency
[ ] PERFORMANCE    — resource and code-size optimization
[ ] MODULARITY     — deduplication, helper extraction, layers
[ ] CONFIG         — features, compile-time/runtime settings
[ ] STM32          — interrupts, Flash storage, HAL/LL
[ ] BUILD          — CMake, project and library structure
[ ] DOCUMENTATION  — Doxygen and comments
[ ] SECURITY       — validation and defensive coding
[ ] TESTING        — tests and verification
[ ] DELIVERY       — patch output and change handoff

CORE is always active. Reply with your checked list and scope.
```

**Copy/paste pattern — only selected modules:**

```text
Use C-RULES.md.
Scope: <file path, list of files, or project>
Mode: ONLY
Modules: [SYNTAX, MODULARITY]
```

**Copy/paste pattern — all modules:**

```text
Use C-RULES.md.
Scope: <file path, list of files, or project>
Mode: ALL
Exclude: []
```

**Copy/paste pattern — all except some modules:**

```text
Use C-RULES.md.
Scope: <file path, list of files, or project>
Mode: ALL
Exclude: [SYNTAX, NAMING]
```

### Startup selection rules

1. When the selection is already supplied by the user, **do not ask again**. This applies equally to explicit module IDs and unambiguous natural-language instructions (for example, "only fix syntax").
2. If no selection was provided and the task requires an edit, ask **once** using a real multi-select UI or the plain-text fallback. Do not change files until the selection is known.
3. Do not fabricate a rendered form or an answer. A Markdown checklist is a **textual fallback**, not a working interactive control.
4. `ALL` means **all applicable modules**, but never authorizes unrelated file edits or changes outside the requested scope. `CORE` is always active.
5. Interpret `Mode: ONLY` as only the named optional modules; interpret `Mode: ALL` with `Exclude` as all applicable optional modules except the excluded ones. Never apply unselected modules implicitly. `FORMATTER` is independent of `SYNTAX` and may create project config files without reformatting source.
6. Once chosen, retain the selection for the current task and its follow-ups unless the user changes it. Do not keep prompting on every file, tool call, or reply.

---

## How to select guideline modules

**Module IDs are the selection interface for agents.** Every normative rule below belongs to the `CORE` module or to one named, independently selectable module. Module selection controls **what the agent is allowed to change or enforce**, not just which chapters the agent reads.

### Selection syntax and defaults

- **When no modules are specified:** For editing tasks, run the **START HERE** selection prompt above and wait for the user to choose; do **not** default to `Mode: ALL`. When the user explicitly chooses ALL, apply `CORE` plus every applicable optional module within the requested scope, without inventing unrelated refactoring work.
- **Apply only certain modules:** `Mode: ONLY` with `Modules: [SYNTAX, MODULARITY]` — apply exactly those listed modules plus `CORE`.
- **Apply all except certain modules:** `Mode: ALL` with `Exclude: [SYNTAX, NAMING]`.
- `Exclude` overrides an explicitly listed module if both are present. `CORE` cannot be excluded; it only enforces safety, non-destructive scope, and compatibility, **not** optional style or architecture changes.
- Treat module IDs as case-insensitive, but write their canonical uppercase spelling in the final handoff.
- If the user requests "only syntax", infer `Mode: ONLY; Modules: [SYNTAX]`. If the user requests "modularity but not syntax", infer `Mode: ONLY; Modules: [MODULARITY]`. If the user asks to **install/create the VS Code formatter** in the project, infer `Mode: ONLY; Modules: [FORMATTER]` unless other modules were explicitly requested.
- Do not silently enable other modules or perform their cleanups. A structural refactor may need the **minimal** local syntax edits required to compile, but it must not trigger a file-wide formatting pass. Conversely, selecting `SYNTAX` does not authorize renaming, changing algorithms, extracting functions, or reorganizing modules. Selecting `FORMATTER` installs formatter **configuration only**; it must not silently format source files or activate `SYNTAX`, `NAMING`, or `MODULARITY`.
- Preserve the project's public API, ABI, binary formats, runtime behavior, and existing uncommitted changes regardless of module selection. If the requested module requires a broader incompatible change, explain the conflict before proceeding.
- For existing source files, apply rules only to the requested files/regions unless the user explicitly authorizes broader scope. For newly written code, use the selected modules and the surrounding project's conventions for unselected areas.
- **Verification is not a rewrite module:** always perform feasible minimum checks on changed code (for example, syntax/build validation or diff inspection), but only **add or restructure tests** when `TESTING` is selected or explicitly requested. Never claim unrun tests succeeded.
- The appendices and examples are reference material; they do not automatically activate other modules.

### Module registry

| Module ID | Area | What the agent may address when selected |
| --- | --- | --- |
| `CORE` | Mandatory baseline | Correctness, safety, permissions, scope control, compatibility; always active |
| `SYNTAX` | Language and formatting | C/C++ version compatibility, braces, whitespace, indentation, cast and pointer spacing, alignment, line wrapping |
| `FORMATTER` | VS Code / Clang-Format setup | Create or merge `.clang-format` and `.vscode/settings.json`; optionally deliver a ready-to-apply project patch |
| `NAMING` | Symbols and enums | Public/private naming conventions, enum names/values |
| `API` | Public interfaces and type contracts | Fixed-width types, struct/union behavior, public headers, parameter and binary interface contracts |
| `CONTROL_FLOW` | Algorithms and dispatch behavior | Guard clauses, branches, generic switches, dispatch tables, loops, pointer iteration |
| `STRINGS` | String operations | Formatting, safe copying, bounded parsing and termination |
| `MEMORY` | Lifetime, RAM and concurrency | Stack/heap, buffers, resource ownership, ISR/task/thread safety |
| `PERFORMANCE` | Low-level optimization | Arithmetic and execution/size tradeoffs supported by measurement |
| `MODULARITY` | Architecture and reuse | Function extraction, deduplication, modular layers, owner libraries, reuse of macros/helpers |
| `CONFIG` | Feature/config management | Compile-time switches, user config defaults, runtime config |
| `STM32` | STM32-specific practices | ISR dispatch, Flash database choices, STM32Cube callback decisions, HAL versus LL |
| `BUILD` | Build/package organization | CMake, libraries/submodules, source/test layout |
| `DOCUMENTATION` | Comments/documentation | Doxygen comments and meaningful developer notes |
| `SECURITY` | Defensive coding and validation | Input validation, error paths, defensive and reliability reviews |
| `TESTING` | Test design and validation | Focused tests, edge cases, on-target/native validation |
| `DELIVERY` | Agent workflow and handoff | Patch construction, test-file separation, change reporting, deliverable review |

### Example requests to an AI agent

**Formatting only — no architecture changes:**

```text
Use C-RULES.md.
Scope: Src/StreamBuffer.c
Mode: ONLY
Modules: [SYNTAX]
Do not apply NAMING, MODULARITY, or any other optional module.
Do not change behavior or rename symbols.
```

**Modularity only — preserve current formatting:**

```text
Use C-RULES.md.
Scope: Src/Codec.c and Src/Codec.h
Mode: ONLY
Modules: [MODULARITY]
Extract repeated algorithms and improve boundaries where justified.
Do not apply SYNTAX or reformat unrelated code.
```

**All guidelines except styling:**

```text
Use C-RULES.md.
Scope: the requested C/C++ project
Mode: ALL
Exclude: [SYNTAX, NAMING]
Preserve existing formatting and symbol names.
```

**Install the VS Code formatter in a project without editing C/C++ code:**

```text
Use C-RULES.md.
Scope: the current C/C++ project
Mode: ONLY
Modules: [FORMATTER]
Create/merge the project .clang-format and .vscode/settings.json.
Deliver changed files and a patch if applicable.
Do not format existing source files.
```

**Install the formatter and format a single authorized file:**

```text
Use C-RULES.md.
Scope: project formatter config + Src/Main.c
Mode: ONLY
Modules: [FORMATTER, SYNTAX]
Set up the formatter, then format only Src/Main.c.
Preserve all other files and behavior.
```

### Agent activation report

At the beginning of an editing task, obtain the module selection via **START HERE** if the user has not already provided it; then resolve and retain `Scope`, `Mode`, `Active modules`, and `Excluded modules`. Report these in the final handoff along with changed files and tests actually run. Do not claim an unselected module was fully applied.

---

---

## M00 — `CORE`: Mandatory contract and engineering priorities

### Mandatory agent contract

**These are implementation instructions, not optional style suggestions.** Before editing, read this file, the affected code, the relevant configuration files, and the current project instructions. Do not invent APIs or assume a library function exists without checking its actual header.

**Priority when instructions conflict:**

1. Correctness, memory safety, defined language behavior, platform requirements, and explicit user instructions.
2. Specific instructions for the current task and current project's documented exceptions.
3. This document's mandatory rules.
4. The surrounding first-party code's conventions, where this document does not specify a rule.
5. General ecosystem conventions.

If two requirements cannot both be satisfied, **explain the conflict and the minimal proposed exception before proceeding**. Do not silently discard a rule to make an edit easier. Preserve the external API/ABI and observable behavior unless the user explicitly authorizes a change.

#### Change control — mandatory

- **Edit only the requested function, block, module, or immediately necessary supporting code.** Never rewrite unrelated files, reformat an entire repository, or regenerate a codebase without permission.
- If an additional edit is genuinely required, identify the file, reason, compatibility impact, and smallest safe change. Ask before expanding scope when feasible.
- Inspect the current authoritative source and existing Git diff before changing code. Do not use an old archive or remembered version as the patch baseline.
- Prefer a **small unified diff / `.patch`** or only the affected edited files, according to the task. Avoid full-repository archives unless requested.
- Do not delete user work, overwrite uncommitted changes, perform destructive Git operations, alter third-party code, or deploy to a device/server without authorization.
- **Develop and test locally first.** Production, VPS, and device deployment require explicit approval. Never report tests or benchmarks as passed unless actually run.
- Preserve public names, binary layouts, protocol formats, ownership contracts, configuration semantics, and concurrency behavior unless change is approved.
- When a request is ambiguous but the safe scope is clear, use the smallest compatible change and document any assumption.

### Engineering priorities

For each implementation, aim for these goals **in this order**:

1. Correct operation, predictable failure behavior, and safety.
2. Portability across embedded and desktop targets; minimum avoidable dependencies.
3. Stable interfaces, easy integration, modularity, and independent library reuse.
4. Low RAM consumption, low stack usage, low flash/ROM size, and reasonable CPU cost.
5. Short, understandable, testable code without repeated algorithms.
6. Configurability and practical fallback paths for unsupported or unavailable features.

**Optimize for the actual platform.** Do not assume a function-pointer table, a shift, a macro, or an explicit `memcpy` is always faster or smaller. The compiler, target ABI, optimization flags, branch behavior, and storage placement determine that. Prefer the owner's pattern when results are equivalent; benchmark or inspect generated code when performance is important.

Design for worst-case input, failures, resource limits, concurrency, and reasonable future scaling. Introduce fallbacks only when they are supported, explicit, and testable; never hide a real error behind an unverified fallback.

**Module boundary:** These priorities guide decisions only within the selected task and modules. They do not authorize formatting, renaming, optimization, modularity, or other work from an inactive module.

---

## M01 — `SYNTAX`: Language compatibility and formatting

### Language levels

- **C:** C99 is the minimum supported baseline, not an upper limit. Keep code compatible with C99 unless the project explicitly selects a newer standard. Stay aware of C11/C17/C23 features and improvements, and use them when the target compiler, toolchain, and project configuration support them. Do not silently raise the project's required C standard or use newer features in a build that still claims C99 compatibility.
- **C++:** C++11 is the minimum supported baseline, not an upper limit. Do not impose a maximum C++ standard. Follow the project's configured standard and use newer standard features when supported by the selected compiler and toolchain. Never require a standard older than C++11. Do not silently change the project's configured standard; if a change is needed, explain the required version and compatibility impact.
- Keep a C library usable from C++ using `extern "C"` guards in each applicable public header.
- Honor the target compiler and its extensions/limitations. Avoid extensions unless already required and explicitly supported.

### Whitespace and statements

- Use **4 spaces** per indentation level. **STM32Cube-generated code is an exception: follow its existing 2-space indentation** and preserve regeneration-safe `USER CODE` sections.
- Use braces for **every** `if`, `else`, `for`, `while`, `do`, and similar control-flow body, even if it is only one statement.
- Put the opening brace on the control line and the closing brace on its own line. Put `else` on a **new line**, never on `} else {`.
- Use one space after commas and after casts: `(uint8_t*) ptr` and `(void*) ptr`.
- Prefer a maximum of **132 characters per source line**; split longer expressions and declarations sensibly.
- Do not declare multiple variables in one declaration, even when the types match.
- Align related `=` initializers vertically when it improves readability; align members in structures and, where practical, across related structures in the same file.
- Indent `case` and `default` labels **one complete indentation level** inside the `switch` body; never align a `case` label with the `switch` statement itself. Indent statements under each label one additional level. One level is **4 spaces** normally or **2 spaces** in STM32Cube-generated files using the existing 2-space style. Prefer spaces, not literal tab characters.
- Do not automatically reformat untouched lines.

```c
/** @brief Read one byte when data is available. */
uint8_t Stream_readReady(StreamBuffer* stream) {
    if (stream == NULL) {
        return 0;
    }

    if (Stream_available(stream) == 0) {
        return 0;
    }

    return 1;
}
```

```c
if (conditionA &&
    conditionB &&
    conditionC
) {
    // Code
}
else {
    // Alternative
}
```

### Switch indentation examples

**Normal project (4 spaces per level):**

```c
switch (state) {
    case State_Idle:
        Device_stop();
        break;

    case State_Running:
        Device_start();
        break;

    default:
        break;
}
```

**STM32Cube-generated source with 2-space indentation:**

```c
switch (state) {
  case State_Idle:
    Device_stop();
    break;

  default:
    break;
}
```

For VS Code, the matching Clang-Format option is `IndentCaseLabels: true`, together with the applicable `IndentWidth`. Clang-Format handles formatting only; it does not apply non-syntax modules.

---

## M02 — `NAMING`: Naming conventions and enum values

### Naming conventions

| Construct | Required pattern | Example |
| --- | --- | --- |
| Source/header filenames | `PascalCase` | `StreamBuffer.c`, `InputStream.h` |
| Struct/union/typedef type | `PascalCase`, optionally library-prefixed | `StreamBuffer`, `Codec_Frame` |
| Public functions | `<Lib>_<name>` or `<Lib>_<Section>_<name>` | `Stream_writeBytes`, `Menu_Main_onPressed` |
| Local variables / parameters | `camelCase` | `strLen`, `packetSize` |
| File-private globals | `camelCase`; no `__` prefix | `streamDriver` |
| Static / file-private functions | Same naming pattern as other functions: `<Lib>_<name>` or `<Lib>_<Section>_<name>`; no `__` prefix | `Stream_findEnd`, `Menu_Main_update` |
| Constant objects | `UPPER_CASE` | `VERSION`, `MAX_FRAME_SIZE` |
| Configuration / feature macros | `UPPER_CASE` | `STREAM_WRITE_LIMIT`, `VERSION_CODE` |
| Function-like public macros | function naming pattern | `Stream_write(...)` |
| Private helper macros | `__` + library/function pattern; this is the only construct in this ruleset that uses the `__` prefix | `__Stream_helperWrite(...)` |
| Function-pointer typedefs | meaningful name ending in `Fn` | `Math_OperationFn`, `Key_ReadPinFn` |

**Portability note:** Identifiers starting with `__` are reserved to the implementation in C++ and by common C toolchain rules. In this ruleset, the owner-specific `__` prefix is reserved **only for private helper macros**; never add it to static functions, file-private globals, types, or ordinary identifiers. This macro convention retains the owner's established style, but it still has portability implications. For a *new* highly portable public-facing library, flag this conflict and request approval for a non-reserved private-macro naming pattern rather than silently changing the established convention. Preserve existing identifiers for compatibility.

Use meaningful names; do not shorten names at the cost of clarity. Prefer consistent capitalization with the existing library over inventing new variants.

### Enum naming and values

- Name each enum type using `PascalCase`.
- Name every enumerator using `<EnumType>_<PascalCaseProperty>`. The enumerator must begin with the exact enum type name, followed by one underscore and a `PascalCase` property name.
- For enums representing error codes or error states, assign an explicit value to **every** enumerator. Never rely on the compiler's automatic incrementing for these enums; preserve stable values when they are part of an external interface or stored data.
- Use enum constants as meaningful indexes when initializing or accessing arrays and function-pointer dispatch tables instead of unexplained numeric indexes. When an enum is used as a direct array index, ensure its values and table size are compatible, and validate the index before access.
- Keep the enum's values aligned with the actual protocol/API contract when values are externally visible.

```c
typedef enum {
    WeekDay_Saturday,
    WeekDay_Sunday,
    // ...
} WeekDay;
```

---

## M03 — `API`: Types, data contracts, and public headers

### Types and declarations

- Use `<stdint.h>` fixed-width integer types (`uint8_t`, `int16_t`, `uint32_t`, etc.) for data whose width matters. **Do not use arbitrary `int`, `long`, `short`, or `unsigned` for project-defined storage or protocols.**
- Exceptions are language/ABI-mandated interfaces, such as `int main(...)`, a libc callback with an `int` signature, or standard `size_t`, `ptrdiff_t`, `uintptr_t`, and `bool` where semantically appropriate. Do not replace an external ABI's types with incompatible ones.
- Never assume that `sizeof(int)`, pointers, alignment, or endianness is identical across targets.
- Always write pointer declarators as `Type* pointer`, not `Type *pointer`.
- For pointer arguments, prefer `uint8_t* data` rather than `uint8_t data[]`; pass explicit length/capacity where needed.
- Prefer `const` for read-only input and constant lookup tables. Use `static const` when possible to keep tables private and eligible for read-only storage.
- Never declare multiple variables on one line.
- Do not define storage-owning global variables in public headers. Avoid `extern` variable declarations there: keep mutable state private in one source file and expose intentional getters/setters or an instance context. Allow a declared shared symbol only when truly needed for the ABI or a documented hardware/linker requirement.
- Declare public APIs in headers; keep file-private data and functions `static` in source files. Static/file-private functions follow the same naming convention as other functions (`<Lib>_<name>` or `<Lib>_<Section>_<name>`); do not add a `__` prefix. Do not expose a private symbol merely to avoid writing a small accessor.

```c
typedef struct {
    uint8_t         Enabled;
    uint16_t        Timeout;
    const uint8_t*  Data;
} Stream_Config;

static int32_t streamDriver;

static const uint32_t MAX_FRAME_SIZE = 256U;

uint16_t strLen  = 0;
uint16_t dataLen = 0;

/** @brief Callback that combines two float values. */
typedef float (*Math_OperationFn)(float a, float b);
```

### Structs, unions, memory operations, and APIs

- Apply the same rules to **unions** as to structures, unless language semantics make a rule inapplicable.
- Pass structures and unions **by pointer by default**, never by value merely for convenience. Use `const Type*` for read-only inputs and `Type*` for outputs/in-out values. Exception: a documented tiny trivial helper type when ABI measurements justify it.
- Do not ordinarily return a struct/union by value; use a caller-provided output pointer or a result/status code. A deliberately small **trivial helper value**, approximately four bytes or less, may be returned by value when its target ABI makes this efficient; document the exception.
- Avoid APIs with many parameters: prefer **four or fewer** inputs when practical. Group coherent optional/configuration parameters in a named configuration struct; do not create one-off structures purely to meet an arbitrary count.
- **For trivially copyable C structs/unions**, use `memset` / `Mem_set` to clear and `memcpy` / `Mem_copy` to copy, instead of `= {0}` or direct struct assignment, according to the owner's preference. Check the actual `Str` API and ordering of arguments before using `Mem_*`.
- Do not use `memset`/`memcpy` to initialize or copy C++ objects that have nontrivial constructors, destructors, ownership, invariants, virtual functions, or other nontrivial semantics. In C++11, use their proper constructors, assignments, and destructors. Correctness overrides the C-struct convention.
- Never assume a raw byte copy is safe for owned resources, pointer-rich types, volatile registers, overlapping regions, or object representation with architecture-specific padding. Use `memmove` for overlapping memory.
- Align members in structures for readability; also check **physical alignment, packing, natural ABI layout, and padding** for memory-mapped devices and binary protocols.
- Use explicit serialization (`Stream`, `Codec`, etc.) for transport data; do not serialize structs by dumping their raw object bytes unless layout/endian guarantees are documented and tested.
- Use a **designated initializer** for C99 structs when initializing specific fields. Named fields prevent mistakes if the structure changes. **C++11 does not support C99 designated initializers**; use a constructor, named setter assignments, or the project's documented initialization pattern instead.

```c
typedef struct {
    int16_t X;
    int16_t Y;
} Point;

/* C99 only: */
Point point = {
    .X = 10,
    .Y = 20,
};

/* For clearing an existing trivial C object: */
Mem_set(&point, 0, sizeof(point));
```

The initialization and clearing operations above demonstrate *different cases*, not instructions to clear an object immediately after initializing it. The `Mem_set` call assumes `Str` has been integrated, and the target C struct has a safe all-zero representation for the intended state.

### Public C header template

```c
#ifndef EXAMPLE_H_
#define EXAMPLE_H_

#include <stdint.h>

#ifdef __cplusplus
extern "C" {
#endif

typedef struct {
    void* Context;
} Example_Driver;

/**
 * @brief Initialize an Example component using a caller-owned driver.
 * @param driver Driver configuration; must remain valid as documented.
 * @return Zero on success, or the library's documented error code.
 */
uint8_t Example_init(const Example_Driver* driver);

#ifdef __cplusplus
}
#endif

#endif // EXAMPLE_H_
```

For new portable public headers, prefer non-reserved include guards such as `EXAMPLE_H_`. Some older owner libraries use `_STREAM_H_`-style guards; preserve those in existing files unless the user authorizes a compatibility-sensitive rename.

### Header inline functions

- Before adding an inline function to a **C header**, check the actual compiler's C99 inline support and linkage semantics. Do not assume GNU89 and C99 `inline` behave identically. Where compatibility is uncertain, use a small normal function or a safe function-like macro only after considering side effects and code size.
- For **C++11** headers, inline function definitions may be placed toward the **end of the header**, after the required type and function declarations, following the owner's preferred layout. Do not expose a C++-only inline definition inside an `extern "C"` block unless its linkage is intentionally valid.
- Avoid putting a non-`static` C inline definition into multiple translation units without a well-defined external definition/linkage plan.

---

## M04 — `CONTROL_FLOW`: Dispatch, loops, and pointer iteration

### Control flow and dispatch

- Prefer **guard clauses** and straightforward execution paths over deeply nested `if`/`else if` chains.
- Use `switch` when selecting among a finite set of related states. **Always include a `default:`** case, even for an enum; handle invalid or future values safely.
- Consider a **function-pointer array / dispatch table** when many cases implement the same signature, particularly for parsers, handlers, states, and protocols. Consult `NMEA`, `Reflex`, and `Key` before creating a new pattern.
- Keep dispatch tables `static const` when appropriate; check index bounds, null entries, selected features, table placement, and the target's indirect-call cost.
- Do not introduce function pointers merely to avoid a small `switch` when their flash/RAM or indirection cost is worse.
- Replace duplicated branches with arithmetic, a boolean expression, or a loop condition when semantics are identical and simpler.
- Prefer mathematical expressions to branching for simple algorithms, as demonstrated by `Stream` availability/space logic; guard against overflow and invalid inputs.

```c
/** @brief Return one when the state is cleared, otherwise zero. */
uint8_t Device_isCleared(uint8_t state) {
    return state == 0;
}

/** @brief Advance to the first terminator or matching character. */
const char* Parser_findEnd(const char* str, char needle) {
    while (*str != '\0' && *str != needle) {
        str++;
    }

    return str;
}
```

```c
typedef void (*Command_HandlerFn)(void);

static const Command_HandlerFn COMMAND_HANDLERS[] = {
    Command_start,
    Command_stop,
    Command_reset,
};

/** @brief Dispatch a command if its index is valid. */
uint8_t Command_execute(uint8_t command) {
    if (command >= (sizeof(COMMAND_HANDLERS) / sizeof(COMMAND_HANDLERS[0])) ||
        COMMAND_HANDLERS[command] == NULL
    ) {
        return 0;
    }

    COMMAND_HANDLERS[command]();
    return 1;
}
```

The dispatch example assumes the three handler functions have already been declared. For a new implementation, use the existing library's error/result type rather than introducing an arbitrary `uint8_t` return code.

### Loops, pointers, and arrays

- Prefer **pointer iteration** for sequential array traversals when the algorithm does not need an index.
- Use `for` with an index when the index itself is needed for mathematics, mapping, checking another array, or protocol semantics.
- Keep pointer bounds and one-past-the-end behavior well-defined; do not increment past the valid range.
- Avoid 2D/3D arrays unless they are genuinely appropriate for mathematical, matrix, lookup, or protocol data. Do not reshape an existing public data layout without permission.
- For **collections of strings**, prefer `const char* const STRINGS[]` over padded `const char STRINGS[N][WIDTH]` when strings are variable length and the target's memory layout supports it.
- Check actual ROM/RAM cost. Pointer tables can consume more memory on some 64-bit targets or for many very short fixed-width strings. On Harvard architectures, account explicitly for flash address spaces / `PROGMEM` requirements.
- Use `sizeof(ptr[0])` instead of `sizeof(*ptr)` when computing element size from a pointer, consistent with this style. Use `sizeof(array)` only when the expression is an actual array in scope; never mistake a pointer's size for its allocation length.

```c
static const char* const DEVICE_STATES[] = {
    "Idle",
    "Active",
    "Error",
};

/** @brief Count matching bytes without needing an array index. */
uint32_t Buffer_count(const uint8_t* data, uint32_t len, uint8_t value) {
    uint32_t count = 0;

    if (data == NULL) {
        return 0;
    }

    const uint8_t* end = data + len;

    while (data < end) {
        count += *data == value;
        data++;
    }

    return count;
}
```

The example above requires `data` to point to at least `len` valid bytes whenever `data != NULL`; null data returns zero. Production functions must validate or document their pointer/length preconditions and must never perform invalid pointer arithmetic.

---

## M05 — `STRINGS`: String formatting and copying

### String formatting, copying, and termination

#### Formatting

- **Never use `sprintf` or `vsprintf`.** Prefer the owner's `Str` numeric-conversion functions when available and suitable, especially on constrained systems.
- Otherwise use `snprintf` or an existing bounded formatting helper. **Never pass bare `sizeof(buffer)` to `snprintf` in this codebase**: follow the owner's defensive `sizeof(buffer) - 1U` convention for a real fixed-size array, and explicitly terminate the final buffer byte.
- This is an intentional owner convention motivated by experience with nonconforming toolchains. Standard-conforming `snprintf` already terminates when `n > 0`; passing `sizeof(buffer) - 1U` reserves an *additional* byte. Do not mistake the convention for a requirement of the C standard.
- For pointer destinations, use the **actual supplied capacity**, not `sizeof(pointer)`. Check for zero/one-byte capacity before formatting and handle truncation/error explicitly.
- When `snprintf`'s return value matters, check negative/error and truncation relative to the **size argument actually passed**. The C library's return type is `int` by contract.

```c
char buffer[32];

/* Only call when the buffer has at least two bytes. */
int32_t written = snprintf(buffer, sizeof(buffer) - 1U, "%lu", (unsigned long) value);
buffer[sizeof(buffer) - 1U] = '\0';

if (written < 0 || (uint32_t) written >= (sizeof(buffer) - 1U)) {
    // Handle formatting failure or truncation.
}
```

Here `(unsigned long)` is required by the `%lu` format ABI; do not use a mismatched format specifier for a fixed-width argument. Prefer `<inttypes.h>` `PRIu32` (or the existing `Str` function) where available. The sample is illustrative and must be placed inside an appropriate function with declared `value` and a failure policy.

#### Copying and parsing strings

- `strncpy` does **not guarantee** a trailing `\0` if the source occupies the whole copy count. When used, always reserve capacity and set the terminator explicitly.
- A bounded copy with explicit length handling, or a proven `Str` function, is often clearer than `strncpy` (which also pads with zeros). Check source and destination lengths.
- Never read beyond an input buffer looking for a terminator; for protocol payloads, carry an explicit length.
- Validate all externally supplied lengths, offsets, and conversion results before indexing or allocating.

```c
char name[32];

strncpy(name, source, sizeof(name) - 1U);
name[sizeof(name) - 1U] = '\0';
```

---

## M06 — `MEMORY`: Lifetime, stack, and concurrency

### Memory lifetime, stack, and concurrency

- On embedded targets, **do not use `malloc`, `calloc`, `realloc`, or `free`** unless sufficient memory exists, the design requires dynamic allocation, and the user approves the tradeoff.
- Prefer fixed buffers, explicit owner-provided memory, and deterministic initialization/deinitialization.
- Always assess stack consumption in embedded projects. Review large local arrays and temporary structs, nested call depth, recursion, library calls, interrupt nesting, and each RTOS task's configured stack. Pay particular attention to small MCU stacks and interrupt paths.
- If local variables or the call path could cause stack exhaustion, inform the user and increase the appropriate stack allocation to a justified size (for example, the RTOS task stack or the main/interrupt stack configured by startup code, the linker script, or project settings). Validate the change with available stack-usage reports, linker information, runtime high-water marks/watermarks, or stress tests; do not guess that the default is sufficient.
- Do not move large local objects into static/global storage merely to reduce stack use unless their lifetime, reentrancy, multiple-instance behavior, ISR access, and concurrency are safe.
- Static/global buffers reduce stack use but can break reentrancy, recursion, interrupts, multiple instances, and multithreading. Prefer caller-owned buffers, an instance context, or a documented allocator when those issues matter.
- Audit ownership, lifetime, pointer validity, double free, use-after-free, dangling callbacks, and memory-mapped buffers.
- Watch for race conditions between threads/tasks, ISRs, DMA, and callbacks. Define who owns each mutable resource and which synchronization is required.
- `volatile` alone is **not** an inter-thread synchronization mechanism; use architecture-supported atomics, locks, critical sections, or memory barriers as appropriate.
- Define ISR-safe and non-ISR-safe APIs explicitly. Avoid blocking, complex logging, and unbounded work in interrupt handlers.
- Check alignment before pointer casts and memory access. For unaligned wire data, use byte-wise parsing or bounded copies; never assume an MCU tolerates unaligned loads.

---

## M07 — `PERFORMANCE`: Low-level arithmetic and size optimization

### Arithmetic and low-level optimization

- Prefer mathematical formulas and constant-time index calculations when they are correct for all defined input ranges.
- For integer division or multiplication by powers of two, **consider** shifts (`>>`, `<<`) to reduce work on small MCUs. Apply only when signedness, rounding, overflow, and integer promotions preserve semantics.
- Replace `x % POWER_OF_TWO` with `x & (POWER_OF_TWO - 1U)` only for suitable **unsigned** values and a nonzero power-of-two divisor. A compiler may already optimize `%`.
- Avoid repeated expensive `%` and `/` in hot paths; move invariant work out of loops and reuse results when practical.
- Prefer simple expressions over redundant branching, but do not replace a readable checked operation with an unsafe trick.
- Do not use bitwise tricks on signed negative values without an explicit platform contract.
- Confirm optimizations using the target compiler, `-Os` / `-O2`, benchmarks, disassembly, or linker map when the cost matters. Track **flash, RAM, stack, and execution time**, not source-line count alone.

```c
/* Example: unsigned ring-buffer wrap for a proven power-of-two capacity. */
#define RING_CAPACITY  64U
#define RING_MASK      (RING_CAPACITY - 1U)

uint32_t nextPos = (currentPos + 1U) & RING_MASK;
```

Ensure the ring buffer's usable capacity, full/empty conventions, and `currentPos` range are correctly defined before using this formula.

---

## M08 — `MODULARITY`: Function reuse and layered architecture

### Reuse existing utilities before writing replacements

Before implementing a generic helper, inspect the available project dependencies and the owner's libraries:

| Need | Inspect first |
| --- | --- |
| Bounded strings, numeric text conversion, memory functions | [`Str`](https://github.com/Ali-Mirghasemi/Str) |
| Variadic mapping, generated declarations, preprocessor composition | [`Macro`](https://github.com/Ali-Mirghasemi/Macro) |
| FIFO/ring-like byte streams, transport abstraction, endian-safe I/O | [`Stream`](https://github.com/Ali-Mirghasemi/Stream) |
| Protocol layers and serialization | [`Codec`](https://github.com/Ali-Mirghasemi/Codec) |
| Commands, CLI parsing, dispatch | [`CmdManager`](https://github.com/Ali-Mirghasemi/CmdManager) |
| NMEA parsing and field dispatch | [`NMEA`](https://github.com/Ali-Mirghasemi/NMEA) |
| Buttons, input events, callback/driver patterns | [`Key`](https://github.com/Ali-Mirghasemi/Key) |
| Type metadata, function-pointer dispatch, alignment patterns | [`Reflex`](https://github.com/Ali-Mirghasemi/Reflex) |
| Logging | [`Log`](https://github.com/Ali-Mirghasemi/Log) |
| Multi-library integrations | [`Embedded-Libs`](https://github.com/Ali-Mirghasemi/Embedded-Libs) |

- **Inspect headers and implementations, not just README examples.** Check configuration guards, signatures, return codes, dependencies, compile options, license, and platform support.
- When incorporating an existing owner library into a Git project, prefer a **Git submodule** to copying its source; pin the intended commit and document update procedures. Do not silently advance an existing submodule.
- Do not add a library when a small existing helper already solves the problem or when its dependency cost would be unreasonable.
- If no logger is present and logging is needed, evaluate the owner's `Log` library first. Avoid logging in constrained or time-critical code when it affects timing or memory.

### Reuse algorithms, helpers, and macros intelligently

- Actively look for repeated code and repeated algorithms. Extract shared logic into an independent helper/function instead of maintaining copied implementations. This can reduce duplicated machine code and Flash usage.
- When an algorithm has a common core and only a few variable parts, parameterize those parts (for example, with arguments or an appropriate callback) so one function can serve multiple call sites. Do this only when the abstraction remains clear and does not increase code size or runtime cost unnecessarily.
- If **more than roughly 2–3 nontrivial lines** of algorithmic code appear in multiple places, consider a private helper or shared utility. Prefer a function when it can be cleanly extracted and doing so improves testability or code size.
- Keep a function approximately **20–35 lines or less** when a natural split exists. Do not fragment a coherent small algorithm merely to meet a line count.
- Prefer a plain helper function for logic that can be expressed as one; avoid repeated copy/paste code.
- Use the `inline` specifier **only for a function confirmed to have exactly one use/call site** in the project. Do not mark functions used from multiple places as `inline`. Remember that `inline` is not a guarantee that the compiler will inline the function; inspect generated code or measure Flash size when the optimization matters.
- Use the owner's `Macro` library for boilerplate that cannot be factored into ordinary functions: repeated type declarations, generated protocol entries, table builders, and repetitive compile-time mappings.
- Check macro argument evaluation count, operator precedence, name capture, side effects, debugger usability, diagnostics, C99 portability, and code growth. Use parentheses and `do { ... } while (0)` for new statement-like macros when safe and compatible with existing calling patterns.
- Do not use a clever macro when it hides behavior, duplicates evaluation, or increases code size significantly.
- Follow patterns like `MACRO_FOR(...)` and the layer implementation helpers in `Codec` when they match the task; avoid writing a second incompatible macro framework.

```c
/* Local use is appropriate when the generated entries truly share a pattern. */
#define __IMPL_HANDLER_DECL(NAME)    extern const Codec_LayerImpl Layer_ ##NAME;
#define __IMPL_HANDLER_ITEM(NAME)    &Layer_ ##NAME,

MACRO_FOR(__IMPL_HANDLER_DECL, Ping, Pong, Heartbeat)

static const Codec_LayerImpl* const HANDLERS[] = {
    MACRO_FOR(__IMPL_HANDLER_ITEM, Ping, Pong, Heartbeat)
};

#undef __IMPL_HANDLER_ITEM
#undef __IMPL_HANDLER_DECL
```

The example is illustrative and assumes `Codec_LayerImpl`, `Macro.h`, and those symbols already exist in the integration. For a public library, evaluate the reserved-identifier issue noted in the `NAMING` module.

### Library architecture and portability

- Build independent, reusable components with narrow and stable public APIs. Make dependencies optional or explicit.
- Follow the **HAL / driver / context** model used in `Stream`, `Key`, and similar libraries. Separate portable logic from hardware/OS operations, which should be passed through driver callbacks or platform adapters.
- Support multiple independent library instances where sensible; avoid unnecessary process-wide global state.
- Separate `init`, operation, error handling, and `deinit` paths. Define ownership of pointers, buffers, callbacks, and context data.
- Handle partial I/O, disconnects, timeouts, resource exhaustion, recoverable errors, and unavailable peripherals explicitly when applicable.
- Avoid `#ifdef` forests scattered through business logic. Keep platform-specific code in small adapters and feature-specific modules.
- Maintain portability across reasonable compilers, word sizes, endian modes, alignment policies, Linux/Windows hosts, and supported embedded architectures.
- For shared protocols, explicitly define integer widths, byte order, frame layout, and checksum/CRC policy.

---

## M09 — `CONFIG`: Feature flags and configuration

### Feature flags and configuration

- Prefer **compile-time `#define` feature flags** for optional MCU features, hardware-dependent code, fixed build selections, and disabled paths that must not consume flash.
- Prefer **typed `static const` values** for values reused in many places when a compile-time preprocessor constant or integer constant expression is not required.
- Avoid magic numbers. Give semantic constants names and units (`TIMEOUT_MS`, `MAX_FRAME_SIZE`, etc.). A literal `0` or `1` is acceptable for obvious boolean/bounds logic, not as an undocumented protocol constant.
- **Fully guard optional implementations, helper declarations, static data, includes, and calls** so that disabling a feature avoids compiling irrelevant code and does not produce unused-function warnings.
- Do not guard only an early return while leaving the entire feature's code compiled unnecessarily.

```c
/** @brief Execute an optional feature. */
uint8_t Feature_run(void) {
#ifndef CONFIG_FEATURE
    return 0;
#else
    // All code and symbols here must be available only when enabled.
    return Feature_execute();
#endif // CONFIG_FEATURE
}
```

For feature-specific dependencies, also place the function definition and its related helpers inside an outer `#if CONFIG_FEATURE` where appropriate; avoid referring to undefined APIs in disabled builds.

#### Separate defaults from user settings

- Follow the established `StreamConfig.h` + `StreamUserConfigTemplate.h` pattern.
- Ship safe defaults in a library-owned configuration header. Keep user overrides in a **separate, optional** file (for example `StreamUserConfig.h`), protected by `#ifndef` defaults.
- Do not overwrite local user configuration when updating a library.
- Prefer explicit feature dependencies and `#error` for unsupported combinations rather than silently enabling expensive functionality.
- Preserve build configuration compatibility with existing projects and toolchains.

#### Runtime configuration when practical

When the target supports it, provide an intentional way to set and inspect configuration without recompiling:

- Desktop/embedded Linux/Windows: command-line arguments, environment variables, and/or a config file.
- Embedded targets: EEPROM, reserved flash/config partition, or filesystem when available.
- Keep **compile-time capacity and feature switches** separate from **runtime adjustable values**.
- Validate versions, ranges, defaults, corruption, partial writes, persistence, and backward compatibility. Define precedence among config sources; do not invent persistent writes implicitly.

---

## M10 — `STM32`: Interrupts, internal Flash, HAL/LL

### STM32 interrupt dispatch

- In STM32 microcontroller projects, do not use `if` / `else` branching inside interrupt handlers or callbacks that execute in interrupt context. Use `switch` / `case` for branching and dispatch by peripheral instance, interrupt source, or discrete state.
- Include a `default:` case and handle unknown or unsupported instances safely.
- Keep each case short. Delegate substantial processing to a separate function when that keeps the interrupt handler bounded and easier to review.
- Use the exact peripheral base-address symbols provided by the selected MCU headers or the project. Do not assume that symbol spellings are identical across STM32 families.

```c
switch ((uint32_t) huart->Instance) {
    case USART1_BASE:
        break;

    case USART_2_BASE:
        break;

    // ...

    default:
        break;
}
```

### STM32 internal Flash as temporary storage or EEPROM emulation

- When an embedded project will use internal MCU Flash for temporary storage, persistent variables, EEPROM emulation, or a database, discuss the storage layout with the user **before designing the database structure**. Do not silently choose a Flash reservation strategy.
- Present these two primary approaches and ask which one the user wants:
  1. **Reserve a region at the end of Flash:** reduce the Flash region available to the application in the linker configuration so the application image cannot overlap the reserved storage.
  2. **Reserve a dedicated erase page/sector:** place the storage in a suitable Flash page/sector, preferring the smallest erase unit that can safely hold the required data to avoid wasting application Flash. Some device memory maps have mixed erase-unit sizes (for example, 4 KB, 16 KB, and 128 KB); these are illustrative only. Verify the exact erase geometry for the selected MCU in its reference manual/datasheet—never assume the same layout across STM32 families or even all parts in one family.
- Update the relevant linker description (`.ld` for GNU linker scripts or `.sct` for ARM scatter-loading, as applicable) so the linker knows the reserved region and cannot place application code or data there. Check the final linker map and memory boundaries.
- A compiler-specific section attribute, such as `__attribute__((section(".flash_database")))` where supported, may be used to place the database object in a named section. The linker script must also map that section to the intended memory region; the attribute alone does not reserve Flash or prevent overlap. Confirm the syntax for the selected compiler.
- To avoid consuming two Flash erase pages, consider an append-only/sequential-write or snapshot storage design when the data model and Flash erase/program constraints permit it. Review this reference implementation before adapting the approach: [FLASH Database Advance example](https://github.com/Nirasystem/ARM-Courses/blob/main/Sections/FLASH/Examples/FLASH-Database-Advance/Core/Src/main.c).
- For every Flash-storage design, account for erase granularity, program alignment/width, endurance, interrupted writes and power loss, recovery from partially written records, and data-version compatibility.
- Before implementing the database, explicitly ask the user which storage layout/approach to use. Explain the Flash space cost and linker changes for each viable option. Do not begin database-structure implementation until the choice is clear.

### STM32Cube HAL and LL guidance

- In STM32CubeMX projects, for configuration or initialization code that runs only once during startup, recommend using the LL API instead of HAL when it is suitable for the peripheral and can reduce code size or unnecessary abstraction.
- Treat this as a recommendation, not an automatic migration. Preserve generated-code boundaries and `USER CODE` sections. Check clock setup, interrupt configuration, peripheral state, and interactions with any HAL handle before mixing LL register operations with HAL runtime operations.
- Do not replace HAL throughout a project merely because LL is available. Explain the specific initialization path where LL is beneficial and keep the existing architecture unless the user approves a broader change.

---

## M11 — `BUILD`: CMake, repository structure, and submodules

### CMake, library layout, and submodules

Use the library structure already employed by `Stream` unless the current project has an established compatible layout:

```text
LibraryName/
├── CMakeLists.txt
├── README.md
├── CHANGES.md                 # Or PATCHES.md, if already adopted
├── Src/
│   ├── LibraryName.h
│   ├── LibraryName.c
│   ├── LibraryNameConfig.h
│   └── LibraryNameUserConfigTemplate.h
├── Examples/
└── tests/                     # Tests are separate from production sources
```

- Supply a working **CMake integration** for new libraries; a normal C implementation should support a **static** target, independent from a host application. A header-only library like `Macro` or `Log` may correctly use an `INTERFACE` target instead.
- Match existing `Stream` CMake conventions: public include path, target naming, project-as-subproject support, optional examples/tests, and cross-compiler selection when appropriate.
- Keep generated/build artifacts out of Git and patches.
- For project dependencies, use Git submodules rather than copied vendored files when the owner's library is required and submodules fit the project constraints.
- New file names use `PascalCase`; platform/generated/third-party files retain their existing names.
- Add or adjust `README.md`, `CHANGES.md`, or `PATCHES.md` **only as needed**. Do not create `README_<patch>.md` or a separate patch-notes file for every change.
- Keep production source, scripts, scripts' helpers, tests, and test helpers in distinct directories.

---

## M12 — `DOCUMENTATION`: Comments and Doxygen

### Comments and documentation

- **Every added or modified function must have a Doxygen-style comment.** For public functions include `@brief`, relevant `@param`, `@return`, ownership, threading, and error behavior.
- For short private functions, a concise `/** @brief ... */` is sufficient if parameters and semantics are obvious. Never add redundant comments that repeat the code statement-by-statement.
- Use meaningful comments for algorithmic assumptions, units, bit masks, tricky memory layouts, and expected fallbacks.
- Document private helper macros when their side effects or invariants are not obvious.
- Preserve important existing comments and the current documentation structure.

```c
/**
 * @brief Copy at most capacity bytes from an input frame.
 * @param dst Destination storage; writable for capacity bytes.
 * @param capacity Destination byte capacity.
 * @param src Source bytes; readable for length bytes.
 * @param length Requested bytes to copy.
 * @return Number of bytes copied; zero when input is invalid.
 * @note No memory allocation; not safe for overlapping buffers.
 */
uint32_t Buffer_copy(uint8_t* dst, uint32_t capacity, const uint8_t* src, uint32_t length);
```

---

## M13 — `SECURITY`: Defensive coding and reliability

### Security, reliability, and defensive checks

- Treat all bytes from UART, network, files, DMA, flash, and user input as untrusted until validated.
- Validate lengths and indexes **before** arithmetic, copies, dereferences, casts, and dispatch-table lookups. Check integer wraparound before calculating `offset + length` or `count * elementSize`.
- Require capacity-aware output APIs; specify whether lengths include a terminating NUL, and never confuse bytes with elements.
- Detect truncated input/output, partial reads/writes, unsupported opcodes, unexpected enum values, corrupted state, and unavailable resources.
- Prefer explicit error/result types and deterministic cleanup; do not silently ignore return codes.
- Check endianness and alignment, especially when parsing packed payloads or accessing peripherals.
- Keep mutable shared state synchronized; review callback reentrancy and lifetime during deinitialization.
- Avoid undefined behavior from signed overflow, shifts by invalid counts, pointer arithmetic outside objects, strict-aliasing violations, misaligned casts, and copying nontrivial C++ types.
- Respect existing timeouts, watchdog service patterns, interrupt latency constraints, and fail-safe modes.
- Preserve third-party library syntax/style; **never edit or reformat third-party code** without explicit permission. If a third-party bug is suspected, contain it with a minimal local adapter or propose an upstream fix.

---

## M14 — `TESTING`: Test planning, edge cases, and verification

### Testing and review expectations

- Prefer a **test or test module** for every changed or new function/module. Follow the repo's existing framework and test layout rather than introducing a new framework gratuitously.
- Test normal behavior plus boundary lengths (`0`, `1`, capacity, capacity+1), null inputs where permitted, error codes, malformed/truncated frames, feature toggles, and unsupported values.
- For ring buffers/streams: test empty/full/wraparound, read/write limits, endian modes, and mathematical invariants.
- For parsers/dispatch tables: test every enabled message/handler, out-of-range indexes, disabled options, and null callbacks.
- For structs and byte formats: test `sizeof`, alignment, serialization compatibility, field order where externally contracted, and target endianness.
- For concurrency: test or reason explicitly about ISR/task interactions, locking, reentrancy, cancellation, and deinit during outstanding work.
- Build with the project's compiler options and minimum language level, ideally on **native and target cross-toolchains**. Use warnings, sanitizers, static analysis, and target-size/stack checks where available and appropriate.
- For performance work, compare a baseline with the proposed change using representative workloads and the actual target profile; report what improved and what regressed.
- Never hide failing tests, modify tests solely to accommodate a bug, or claim unexecuted validation succeeded.

---

## M15 — `DELIVERY`: Agent workflow, patch hygiene, handoff

### Required agent workflow and handoff

For every implementation task in which `DELIVERY` is selected, follow these steps while enforcing **only the active modules**:

1. **Inspect:** Read the requested code, nearby callers, relevant public headers, config flags, build rules, tests, project instructions, and current working tree status. Inspect relevant reusable owner libraries when `MODULARITY` is active.
2. **Scope:** Identify the smallest edit set, the selected modules, the explicitly excluded modules, and the required compatibility guarantees.
3. **Design:** Check bounds, lifetimes, concurrency, stack/heap impact, flash/RAM effects, feature guards, portability, and fallback behavior insofar as these are relevant to the selected changes. This review must not turn into an unrelated refactor.
4. **Platform-specific decisions (only when `STM32` is active and relevant):** Ask the user whether the STM32Cube HAL `RegisterCallback` mechanism should be used before choosing or changing the callback architecture. Check the relevant HAL version and its `USE_HAL_<PERIPHERAL>_REGISTER_CALLBACKS` setting. For internal Flash storage, ask the user which reservation/storage approach to use before implementing the database; see `STM32`.
5. **Implement:** Apply only selected modules. When `STM32` is active, consider LL for one-time STM32CubeMX initialization and describe the tradeoff; otherwise do not change HAL/LL usage under the guise of formatting.
6. **Validate:** Always run feasible minimum checks for changed code and inspect the diff (`git diff --check`, relevant syntax/build checks). Add or update focused test cases and run broader suites when `TESTING` is selected or the user explicitly asks for tests. Check stack usage and linker maps when the selected changes make them relevant. Report checks that could not be run.
7. **Deliver:** Provide the requested patch/files, actual validation results, active and excluded module IDs, and unresolved risks. Never suggest that unselected rules were automatically enforced.

#### Patch hygiene

- Prefer small, independently reviewable patches. If multiple patches are requested, keep each patch focused and compatible with the authoritative baseline and previous accepted patches.
- Make patches robust with correct paths and context; verify `git apply --check` when the source tree is available.
- **Keep generated test files separate from the embedded project by default.** When a delivered patch includes multiple new test files, host-side test harnesses, test fixtures, or test-only scripts, place them in a separate sibling directory outside the embedded project tree (for example, `../<ProjectName>-tests/`) rather than adding many test files to the firmware project. Keep production-source changes in the project patch and deliver the external tests separately, with clear instructions for running them against the project.
- This separation is for newly generated test artifacts; do not move, rename, or restructure tests that already belong to the project. If the project already has an established test directory or the user explicitly requests in-project tests, follow that convention. Keep tests that must be built or run on the target in the project's existing test mechanism when necessary, and explain why they cannot be externalized.
- Do not omit meaningful tests merely to keep the embedded project small. Preserve test coverage while keeping host-only test infrastructure outside the firmware tree.
- Never include `__pycache__/`, `.pyc`, IDE caches, generated build files, temporary logs, binary dumps, or unrelated formatting changes.
- Update the existing `CHANGES.md` / `PATCHES.md` when appropriate. Reference it from the existing `README.md`; do not create patch-specific README files.
- Do not commit, push, alter remote branches, or deploy unless the user requests it.

When `TESTING` is not selected, this module controls the **packaging** of any tests requested or already needed, but does not authorize generating new unrelated test suites.

### Final agent response format

Keep the handoff short and evidence-based:

- **Changed:** exact functions/files, and why.
- **Compatibility:** public API/ABI/config/protocol behavior and any deliberate exception.
- **Validation:** commands actually run and actual results; mark unrun checks explicitly.
- **Resources/risks:** relevant RAM/flash/stack/concurrency/portability caveats.
- **Artifact:** patch or changed-file links when a deliverable was requested.

### Module-aware compliance checklist

Check `CORE` and **only the explicitly active module items**. Checklist entries do not activate another module. For `Mode: ONLY; Modules: [SYNTAX]`, for example, do not enforce `NAMING`, `MODULARITY`, `DOCUMENTATION`, or any other optional module.

- [ ] **[CORE]** Only authorized first-party files/regions were changed; no unrelated rewrite, unsafe behavior, destructive Git action, or deployment.
- [ ] **[SYNTAX]** C99 / C++11 are minimum supported baselines; newer versions respect the selected toolchain, and unrelated formatting is untouched.
- [ ] **[SYNTAX]** Correct indentation (4 spaces; 2 in STM32Cube-generated files), braces, separate `else`, pointer/cast spacing, and 132-column preference.
- [ ] **[SYNTAX]** `case` / `default` are indented exactly one level deeper than `switch`; case statements have one additional level.
- [ ] **[FORMATTER]** `.clang-format` and `.vscode/settings.json` are created/merged safely, `case` labels are indented, formatter selection is correct, and no unauthorized bulk formatting occurred.
- [ ] **[NAMING]** Public/static function names, private macro prefixes, variable names, enum type/enumerator names, and explicit error enum values follow the naming rules.
- [ ] **[API]** Fixed-width types, struct/union parameters and copies, ABI contracts, header declarations, and C/C++ inline/linkage behavior are correct.
- [ ] **[CONTROL_FLOW]** Guard clauses, dispatch/loop decisions, `default:` labels, and bounds checks follow the applicable rules.
- [ ] **[STRINGS]** No unsafe `sprintf`/`vsprintf`; formatting/copies use bounded buffers, correct termination, and explicit truncation handling.
- [ ] **[MEMORY]** Stack use is assessed; dangerous allocations/lifetimes, buffer ownership, ISR/task concurrency, and stack exhaustion risks are addressed.
- [ ] **[PERFORMANCE]** Numerical optimizations preserve unsigned/signed semantics and have evidence where size/speed materially matters.
- [ ] **[MODULARITY]** Owner libraries were checked; repeated algorithms are factored appropriately; layers/HAL boundaries and single-use `inline` rule are respected.
- [ ] **[CONFIG]** Disabled features compile out unused code; defaults, user overrides, and runtime configuration remain separate and validated.
- [ ] **[STM32]** Interrupt paths use required `switch` dispatch and verified MCU symbols; callback registration and internal-Flash strategy were confirmed with the user where relevant; LL was evaluated for suitable one-time initialization.
- [ ] **[BUILD]** CMake/library integration and submodules respect the project; generated/test artifacts remain appropriately separated.
- [ ] **[DOCUMENTATION]** Applicable Doxygen and explanatory comments were added without unnecessary commentary.
- [ ] **[SECURITY]** Untrusted input, integer overflow, aliasing, error paths, and reliability concerns were reviewed within the requested scope.
- [ ] **[TESTING]** Focused tests cover normal and boundary/error cases; toolchains and results are reported accurately.
- [ ] **[DELIVERY]** Patch is minimal and verified against the correct baseline; host-side test artifacts are delivered outside the embedded project when appropriate; active modules and any skipped checks are reported.

---

## M16 — `FORMATTER`: VS Code / Clang-Format project setup and patch delivery

This is an **opt-in project configuration module**. It creates or updates formatter files so that the user can press **Alt + Shift + F** in VS Code on Windows to format the active C/C++ document using the project's coding style. This keyboard shortcut invokes VS Code's formatter, **not an AI agent**. The agent's responsibility is to create/configure the formatter **beforehand** when this module is selected.

### Activation, scope, and deliverables

- Trigger this module when the user selects `FORMATTER`, requests a project VS Code formatter, or explicitly asks for formatter files in a patch. Do **not** require `SYNTAX` to activate it.
- When `Mode: ONLY; Modules: [FORMATTER]`, **create or merge configuration only**. Do not run clang-format across source files, change identifiers or APIs, refactor algorithms, change build flags, install software, or perform unrelated cleanups.
- In the selected project root, create or update: **`.clang-format`** and **`.vscode/settings.json`**. The first controls formatting style; the second selects the Microsoft C/C++ formatter for C/C++ files and points it at the project style. Optionally include **`.vscode/extensions.json`** as a recommendation if the project already uses extension recommendations or the user asks.
- If the user requests a `.patch`, add the formatter files to the patch with the correct project-relative paths. Provide the actual new/modified files as alternatives when Git or a patch baseline is unavailable. **Never invent a successful patch application or conflict-free merge.**
- Inspect existing formatter files, `.vscode/settings.json`, `.editorconfig`, nearest nested `.clang-format` files, Git status, and project conventions before writing. **Merge existing JSON/JSONC settings without overwriting unrelated keys**, and do not replace a valid project formatter unilaterally; describe any conflict that needs a user decision.
- Add these files to the repo as **version-controlled project configuration**, not global VS Code user settings. Do not touch VS Code's global settings or keyboard shortcuts unless explicitly asked.
- Avoid enabling format-on-save or project-wide formatting as a side effect. Ensure `editor.formatOnSave` is not newly enabled for C/C++; when it is already explicitly enabled, preserve the user's setting and call out that difference rather than silently overriding it.
- Do not modify third-party/vendor, generated, or uncommitted files without explicit authorization. Default installation changes only configuration files.

### Default `.clang-format` (project root)

Use the following as the **baseline**, then adapt only for the target clang-format version or previously approved project exceptions. Ensure it remains parseable by the installed tool. It encodes the current `SYNTAX` layout conventions, but **does not cause `SYNTAX` to run on existing code**.

```yaml
# C-RULES.md FORMATTER-module project profile for C and C++.
# Place this file in the project root. Formatting applies to source files in
# this directory and its subdirectories unless a nearer .clang-format exists.
# Preferred tool: clang-format 18+; validate against the installed binary.
# This controls whitespace/layout only, not semantic or naming rules.
---
BasedOnStyle: LLVM
Language: Cpp
Standard: Latest

# Indentation and width (STM32Cube-generated source uses a local override).
UseTab: Never
IndentWidth: 4
TabWidth: 4
ContinuationIndentWidth: 4
ColumnLimit: 132
IndentCaseLabels: true
IndentCaseBlocks: false
IndentExternBlock: NoIndent
IndentPPDirectives: None
AccessModifierOffset: -4

# Braces on the same line, but 'else' starts on its own line.
BreakBeforeBraces: Custom
BraceWrapping:
  AfterCaseLabel: false
  AfterClass: false
  AfterControlStatement: Never
  AfterEnum: false
  AfterExternBlock: false
  AfterFunction: false
  AfterNamespace: false
  AfterStruct: false
  AfterUnion: false
  BeforeCatch: true
  BeforeElse: true
  BeforeWhile: false
  IndentBraces: false
  SplitEmptyFunction: false
  SplitEmptyRecord: false
  SplitEmptyNamespace: false

# Braces remain on separate lines even for short functions or control bodies.
AllowShortBlocksOnASingleLine: Never
AllowShortFunctionsOnASingleLine: None
AllowShortIfStatementsOnASingleLine: Never
AllowShortLoopsOnASingleLine: false
AllowShortCaseLabelsOnASingleLine: false
AllowShortEnumsOnASingleLine: false

# Align adjacent declarations / structure members, assignments, and defines.
AlignConsecutiveDeclarations: Consecutive
AlignConsecutiveAssignments: Consecutive
AlignConsecutiveMacros: Consecutive
AlignConsecutiveBitFields: Consecutive

# Pointer spelling: Type* ptr, and cast spelling: (uint8_t*) ptr.
PointerAlignment: Left
DerivePointerAlignment: false
ReferenceAlignment: Pointer
SpaceAfterCStyleCast: true
SpaceBeforeParens: ControlStatements
SpaceBeforeAssignmentOperators: true
SpacesInParentheses: false
SpaceInEmptyParentheses: false
SpacesInSquareBrackets: false
BreakBeforeBinaryOperators: None

# Avoid surprise changes in include order, comments, and generated files.
SortIncludes: Never
IncludeBlocks: Preserve
ReflowComments: false
FixNamespaceComments: false
MaxEmptyLinesToKeep: 1
InsertNewlineAtEOF: true
...
```

Key intentions: 4 spaces; literal spaces instead of tabs; `case` / `default` labels **indented one level** relative to the `switch`; statements one further level; 132-column preference; opening brace on control/function line, `else` on its own line; `Type* ptr`, `(Type*) value`; aligned consecutive declarations/assignments; disable automatic include sorting. Keep indentation and generated-source exceptions consistent with `SYNTAX` when *formatting is actually requested*.

**Important:** The YAML `Standard: Latest` affects parsing/formatting assumptions only. It **does not set the C++ compiler standard or the project's minimum C99 / C++11 compatibility**. Keep CMake/toolchain language levels unchanged unless separately authorized.

### Default `.vscode/settings.json` (merge into project settings)

Use these keys as the baseline. If the file exists, **merge** instead of overwriting; VS Code's `settings.json` may use JSONC comments and trailing commas. Preserve unrelated existing values and user-established overrides.

```json
{
  "C_Cpp.formatting": "clangFormat",
  "C_Cpp.clang_format_style": "file",
  "[c]": {
    "editor.defaultFormatter": "ms-vscode.cpptools",
    "editor.insertSpaces": true,
    "editor.tabSize": 4,
    "editor.detectIndentation": false,
    "editor.formatOnSave": false
  },
  "[cpp]": {
    "editor.defaultFormatter": "ms-vscode.cpptools",
    "editor.insertSpaces": true,
    "editor.tabSize": 4,
    "editor.detectIndentation": false,
    "editor.formatOnSave": false
  }
}
```

- Required extension: **Microsoft C/C++**, ID `ms-vscode.cpptools` (installed/selected by the user in VS Code).
- When multiple C/C++ formatters are installed, the `[c]` and `[cpp]` `editor.defaultFormatter` fields avoid the formatter selection prompt. Set `C_Cpp.formatting` to `clangFormat` and `C_Cpp.clang_format_style` to `file` to load `.clang-format`.
- Only add `C_Cpp.clang_format_path` when a specific external `clang-format` binary is necessary and its executable path has been confirmed; do not hardcode a machine-specific Windows path in a reusable project template.
- The default Windows shortcut for **Format Document** is **Shift + Alt + F**. On macOS it is **Shift + Option + F**; on many Linux keymaps it is **Ctrl + Shift + I**. If the user's key binding differs, ask them to check VS Code's Keyboard Shortcuts for `Format Document` rather than changing keybindings without authorization.

### STM32Cube-generated code: optional 2-space local override

The main formatter uses 4 spaces. For a directory containing **only** STM32Cube-generated files that consistently use 2 spaces, the following optional **child** `.clang-format` can inherit the main style:

```yaml
# Optional ONLY in a directory containing exclusively 2-space STM32Cube-generated files.
# Copy to that exact directory after inspecting its source and regeneration behavior.
BasedOnStyle: InheritParentConfig
IndentWidth: 2
ContinuationIndentWidth: 2
TabWidth: 2
IndentCaseLabels: true
```

- Place the override **inside the actual generated-code directory**; do not put it in `Core/Src/`, `Src/`, or another mixed directory merely by assumption. Otherwise it would also change user-maintained files there.
- If generated and handwritten code are in the **same directory or file**, a directory-level `.clang-format` cannot automatically distinguish them. Respect CubeMX `USER CODE` sections and regeneration-safe boundaries; suggest range formatting or explicitly protected regions instead of applying the 2-space override indiscriminately.
- Never blindly format vendor HAL/LL, CMSIS, third-party modules, or regenerated files. Warn when the formatting selection could modify generated code beyond user-owned sections.

### Agent checklist for creating or patching a formatter

1. **Inspect** `.clang-format`, `_clang-format`, nested formatter files, `.vscode/settings.json`, `.editorconfig`, installed available formatting tools, and `git status` in the actual target project.
2. **Choose** a minimal project-level configuration based on the two templates above. If existing style differs, identify the conflict and preserve intentional user or repository settings unless an override was requested.
3. **Create/merge** `.clang-format` and `.vscode/settings.json`. Respect JSONC syntax and existing settings; do not overwrite the whole file when only a few keys need changes. Do not enable format-on-save by default.
4. **Validate** the installed tool with `clang-format --version`. From the project root, use `clang-format --style=file --assume-filename=Src/Main.c --dump-config` (with an actual project-appropriate path) to check configuration parsing and option support. Use `clang-format --style=file <sample-file>` without `-i` to **preview** the proposed output; `clang-format --style=file --dry-run --Werror <sample-file>` additionally checks whether that file is already formatted and may fail simply because formatting is needed. Never confuse an unformatted sample with invalid YAML. If no binary is available, validate YAML/JSON syntactically where possible and explicitly report that actual formatting could not be tested. Adjust only incompatible options and document differences.
5. **Verify VS Code** settings syntax and formatter selection. If interactive VS Code is unavailable, state that Alt + Shift + F behavior requires verification in the user's own editor and do not claim the shortcut was tested.
6. **Deliver** `.clang-format` and `.vscode/settings.json` as project-relative files or a verified unified patch. Check `git diff --check` and, when a repository baseline is available, `git apply --check` or equivalent non-destructive validation. Do not commit/push unless explicitly requested.
7. **Report** active modules, configured formatter/version (if checked), exact changed files, merge/conflict decisions, validation commands/results, and any generated-code limitations.

### User request patterns

**Only add the formatter; do not format any source:**

```text
Use C-RULES.md.
Scope: current C/C++ project
Mode: ONLY
Modules: [FORMATTER]
Install project-level Clang-Format for VS Code using C-RULES style.
Create or merge .clang-format and .vscode/settings.json.
Deliver a patch and the modified files. Preserve existing code.
```

**Include formatter setup in a patch that changes code:**

```text
Use C-RULES.md.
Scope: specified source changes plus project formatter settings
Mode: ONLY
Modules: [FORMATTER, SYNTAX, DELIVERY]
Apply SYNTAX only to the requested changed code.
Include .clang-format and .vscode/settings.json in the same patch.
No bulk formatting or unrelated refactoring.
```

### Formatter limitations

`clang-format` enforces **layout**, not semantic C-RULES requirements. It cannot reliably insert all required control-flow braces, rename functions/enumerators, enforce enum error values, detect stack overflow, convert ISR `if` chains into `switch`, verify internal-Flash sectors, extract duplicate algorithms, or add Doxygen comments. Those belong to their own modules. Some custom line-breaking preferences (such as placing a multi-line condition's closing `)` on its own line) may not be reproduced exactly in every clang-format version. Test formatting on real representative code; do not promise perfect equivalence without that test.

**Official references:** [Clang-Format configuration](https://clang.llvm.org/docs/ClangFormat.html), [Clang-Format style options](https://clang.llvm.org/docs/ClangFormatStyleOptions.html), [VS Code C/C++ formatting](https://code.visualstudio.com/docs/cpp/cpp-ide), [VS Code default keyboard shortcuts](https://code.visualstudio.com/docs/reference/default-keybindings).

---

## Appendix — Reference implementations and provenance

These links are style and architecture references; **task instructions in this document override outdated or inconsistent examples in older commits**. Inspect the current code before copying or modifying a pattern.

- [Owner profile](https://github.com/Ali-Mirghasemi) and [profile README](https://github.com/Ali-Mirghasemi/Ali-Mirghasemi/blob/main/README.md) — focus on embedded firmware, Linux/networking, cross-platform portability, and efficient reusable C.
- [`Stream/Src/StreamBuffer.h`](https://github.com/Ali-Mirghasemi/Stream/blob/master/Src/StreamBuffer.h) and [`Stream/Src/StreamBuffer.c`](https://github.com/Ali-Mirghasemi/Stream/blob/master/Src/StreamBuffer.c) — names, pointer APIs, context objects, memory drivers, HAL and stream/buffer patterns.
- [`Stream/Src/StreamConfig.h`](https://github.com/Ali-Mirghasemi/Stream/blob/master/Src/StreamConfig.h) and [`StreamUserConfigTemplate.h`](https://github.com/Ali-Mirghasemi/Stream/blob/master/Src/StreamUserConfigTemplate.h) — separated defaults/user feature configuration.
- [`Stream/CMakeLists.txt`](https://github.com/Ali-Mirghasemi/Stream/blob/master/CMakeLists.txt) — static library, subproject, and cross-target integration patterns.
- [`Str/Src/Str.h`](https://github.com/Ali-Mirghasemi/Str/blob/master/Src/Str.h) — `Mem_copy`, `Mem_set`, parsing, and string/numeric utility declarations.
- [`Macro/Src/Macro.h`](https://github.com/Ali-Mirghasemi/Macro/blob/main/Src/Macro.h) — `MACRO_FOR`, argument mapping, generated boilerplate, and macro conventions.
- [`NMEA/Src/NMEA.c`](https://github.com/Ali-Mirghasemi/NMEA/blob/main/Src/NMEA.c) — table-driven field/message parsing and feature gating.
- [`Reflex/Src/Reflex.c`](https://github.com/Ali-Mirghasemi/Reflex/blob/master/Src/Reflex.c) — function-pointer tables and alignment/type handling.
- [`Key/Src/Key.h`](https://github.com/Ali-Mirghasemi/Key/blob/master/Src/Key.h) — driver callbacks and `Fn` typedef naming.
- [`Codec/Src/CodecMacro.h`](https://github.com/Ali-Mirghasemi/Codec/blob/master/Src/CodecMacro.h) — reuse of `Macro` for generated serialization/layer logic.
- [`Log/Src/Log.h`](https://github.com/Ali-Mirghasemi/Log/blob/main/Src/Log.h) and [`Embedded-Libs`](https://github.com/Ali-Mirghasemi/Embedded-Libs) — logging configuration and Git-submodule-based library composition.
- [FLASH Database Advance example](https://github.com/Nirasystem/ARM-Courses/blob/main/Sections/FLASH/Examples/FLASH-Database-Advance/Core/Src/main.c) — sequential/snapshot-style internal-Flash database storage example; inspect and adapt it only after verifying the target MCU's erase geometry and power-loss requirements.

---

**Agent operating principle:** Make the smallest correct change. Reuse existing libraries. Keep ownership, boundaries, and configurations explicit. Optimize with evidence, preserve portability, and never surprise the owner with unrelated edits.
