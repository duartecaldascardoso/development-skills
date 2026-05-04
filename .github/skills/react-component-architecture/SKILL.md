---
name: react-component-architecture
description: Guide for building and refactoring React components into modular, testable units. Use when creating new React UI features or cleaning up oversized components.
---

Use this skill to keep React codebases maintainable as features grow.

## Objectives

1. Keep rendering code simple and focused on UI composition.
2. Move stateful and side-effect logic into custom hooks.
3. Keep types, styles, and tests explicit and local to the component.
4. Expose a clean public API through barrel exports.

## Standard Component Folder Shape

```text
src/components/FeatureWidget/
├── index.ts
├── FeatureWidget.tsx
├── FeatureWidget.types.ts
├── useFeatureWidget.ts
├── FeatureWidget.module.css
└── FeatureWidget.test.tsx
```

## Rules

### 1) `Component.tsx` is presentation-first

- Prefer JSX composition, prop wiring, and event binding.
- Do not embed complex data fetching, large transforms, or long imperative flows.
- Keep conditional rendering easy to scan (`loading`, `error`, `empty`, `ready` states).

### 2) `useComponent.ts` owns behavior

- Place state transitions, effects, async calls, and derived view-model logic in the hook.
- Return a minimal interface consumed by the component.
- Keep side effects explicit; avoid hidden mutation across modules.

### 3) `Component.types.ts` owns local contracts

- Co-locate props and local domain types.
- Keep public and internal types clearly named.
- Prefer precise unions and optionality over `any`.

### 4) Styling stays colocated and scoped

- Prefer CSS Modules or an existing scoped styling convention.
- Keep class names semantic by role (`container`, `header`, `actions`).
- Avoid leaking cross-component style assumptions.

### 5) `index.ts` defines the public API

- Export only what callers should rely on.
- Re-export component and public types.
- Export hook only if intended for external reuse.

## Refactor Workflow

When improving an existing component:

1. Identify mixed concerns inside the `.tsx` file.
2. Extract behavior into `useComponent.ts` without changing UI behavior.
3. Extract and tighten types into `Component.types.ts`.
4. Keep file names and import style consistent with the repository's existing pattern.
5. Add/update tests that cover both rendered states and key behavior paths.

## Quality Bar

- Small, readable render functions.
- Explicit loading/error handling.
- No duplicated transformation logic between component and hook.
- No broad type assertions as shortcuts.
- Clear import boundaries and stable public exports.
