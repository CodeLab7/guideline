# React Implementation Guidelines (AGENT Spec)

Framework-specific rules for how the AI coding agent must structure and write React + TypeScript code under `resources/js`.

These build on the global behaviour guidelines and Laravel/Inertia integration.

---

## 1. Technology & Coding Standards

When writing React code:

- Use **TypeScript** only (`.tsx` / `.ts`).
- Use **functional components + hooks**; do not use class components.
- Keep components **small and focused**:
  - Extract subcomponents into `/sections/*` or `/widgets/*` as soon as they grow.
  - Avoid deep JSX nesting; prefer early returns and small helpers.
- Keep code **readable, reusable, and predictable**:
  - Prefer clear naming over short naming.
  - Avoid clever but confusing patterns.
- Avoid duplicate logic:
  - Extract shared logic into **helpers**, **hooks**, or **utils**.
- Avoid unnecessary renders:
  - Use `React.memo`, `useMemo`, and `useCallback` **only when needed** and with a clear performance reason.
- Follow project **Prettier** and **ESLint** configurations for formatting and lint rules.

### 1.1 Naming Conventions

- **Files (components, hooks, utilities, etc.):** `kebab-case`
  - `contact-list.tsx`, `user-form.tsx`, `use-debounce.ts`.
- **React Components:** `PascalCase`
  - `ContactList`, `UserForm`.
- **Variables & Functions:** `camelCase`
  - `totalAmount`, `loadContacts`.
- **Constants:** `UPPER_SNAKE_CASE`
  - `MAX_LENGTH`, `API_URL`.

---

## 2. Project Structure & Directory Layout

All React code lives under: `resources/js/`.

Organize code by **domain module**, then by type (pages, sections, widgets), and use shared directories for cross‑module reuse.

### 2.1 Components

Base path: `resources/js/components/`

- `components/ui/`
  - Library-specific primitives and wrappers (e.g. shadcn/ui components).
  - Use these as building blocks for higher-level components.
- `components/shared/`
  - Project-level general UI components reusable across modules.
  - Examples:
    - `delete-confirmation.tsx`
    - `phone-input.tsx`
- `components/specific/`
  - Feature- or domain-specific components reused in multiple places.
  - Example:
    - `team-members-list.tsx`
- `components/`
  - Shared widgets/helpers that don’t fit into `ui/`, `shared/`, or `specific/`.
  - Example:
    - `notification-toast.tsx`.

### 2.2 Layouts

Base path: `resources/js/layout/`

- `layout/`
  - Layouts / shells for pages.
  - Examples:
    - `auth-layout.tsx`
    - `UserLayout.tsx`
- `layout/sections/`
  - Layout sections used inside layouts.
  - Examples:
    - `side-bar.tsx`
    - `header.tsx`
- `layout/widgets/`
  - hold slave small widget component
  - example:
    - `user-menu.tsx`

### 2.3 Hooks

Base path: `resources/js/hooks/`

- `hooks/`
  - All **custom React hooks** that are reusable across the project.
  - Examples:
    - `use-is-mobile.ts` (or equivalent file for `useIsMobile`)
    - `use-appearance.ts`
    - `use-debounce.ts`

### 2.4 Pages

Base path: `resources/js/pages/`

- `pages/{module-name}/`
  - Page components called by Laravel controllers (Inertia pages).
  - Example:
    - `pages/sale/index.tsx`
- `pages/{module-name}/sections/`
  - Section components for that module (larger logical pieces of the page).
- `pages/{module-name}/widgets/`
  - Small, module-level components used **only** inside that module.

For module-specific helpers:

- Use a local `{module-name}-utils.ts` inside the module:
  - Example: `/pages/sale/sale-utils.ts`.

### 2.5 Types

Base path: `resources/js/types/`

- `types/`
  - Common data types used across the project or supplied via APIs.
- `types/api.interface.ts`
  - All types that represent data returned from Laravel APIs.
  - Treat this as a **central contract file**:
    - Do not modify frequently.
    - Only update when the backend data structure changes.
    - Keep shared structures at the root of this file.
- `types/general.enum.ts`
  - General enums used as options or values from APIs.
  - Update only when new enums are introduced from backend to frontend.
- Naming
  - use `Props` while defining props for component.
  - use `{Module}Fillable` while defining form field’s type

### 2.6 Utilities

Base path: `resources/js/utils/`

- `utils/`
  - Shared utility functions used by multiple modules.
  - Before creating a new helper, **check this folder first**.
  - Examples:
    - `number.enums.ts` – number and currency formatting/transformations.
    - `date.enums.ts` – date-related helpers.

### 2.7 i18n

- `i18n/` (or project-specific path)
  - Only present / used if the project supports translations.
  - Follow project guideline when present; otherwise, do not introduce i18n.

---

## 3. UI & Styling Standards

- Use **TailwindCSS** for styling.
- Prefer existing primitives and patterns before introducing new ones:
  - Use `components/ui/*` and any `components/shared/*` provided.
- Use **project defined library** (e.g. shadcn/ui )where possible to maintain design consistency.
- Do not add custom styling when an existing component or pattern can achieve the result.
- Take minimalistic approach unless specified.
- New blocks/pages:
  - Build them primarily with components from `/components/*/**` and project-specified UI libraries.

### 3.1 Responsiveness & UX

- Design for screens down to **360px width** and larger.
- Use standard Tailwind breakpoints.
- For mobile/desktop behaviour:
  - Use the `useIsMobile` hook when behaviour (not just styling) should differ by device.
  - Avoid relying purely on CSS hide/show for behaviour changes.
- Consider mobile keyboard:
  - Prefer `dvh` over `vh` for heights where relevant.
- **User feedback**:
  - Actions (clicks, navigation, tabbing, form submissions) must:
    - Respond immediately, or
    - Provide visual feedback (e.g., button loading state/spinner).
  - Avoid unnecessary or heavy animations.

### 3.2 Consistency & Icons

- Maintain consistent spacing, typography, and icon sets.
- Icons:
  - Prefer `lucide-react`.
  - Fall back to `react-icons` only when needed.

### 3.3 Accessibility

- Do not add extra accessibility features unless the project guidelines require it.
- Keep markup clean; avoid unnecessary attributes.

### 3.4 Nestable Components

Design components that can be composed, for example:

`<Card><CardHeader><CardTitle>Title</CardTitle></CardHeader><CardContent>Something</CardContent></Card>`

---

## 4. Component Architecture & Reusability

### 4.1 Reusability Mindset

- Move cross-module elements into:
  - `components/shared/` for generic UI.
  - `components/specific/` for domain-specific shared components.
- When designing a component:
  - If it can be reused in other modules, avoid binding it tightly to a single page’s logic.
- Hooks:
  - If a hook can be reused across the project, put it in `hooks/`.
- Utilities:
  - If a function can be reused in multiple areas, put it in `utils/`.
  - Check `/utils/` before creating new helpers.

### 4.2 Component Roles

Module components should follow a clear hierarchy:

1. **Page Components**
   - Live under: `pages/{module-name}/`.
   - Directly called from Laravel controllers (Inertia).
   - Responsibilities:
     - Provide overall page structure.
     - Wire up sections, layout, and main data.
     - Keep logic minimal.
2. **Section Components**
   - Live under: `pages/{module-name}/sections/`.
   - Responsibilities:
     - Hold most of the module’s business/UI logic.
     - Coordinate data fetching and state for that part of the page.
     - May call other section components to split up larger flows.
3. **Widget Components**
   - Live under:
     - `pages/{module-name}/widgets/` for module-local partials, or
     - `components/*` for app-level components.
   - Responsibilities:
     - “Slave” components with minimal logic.
     - Accept props, handle minor local state, and call prop functions.
     - Focus on rendering and simple interactions.

### 4.3 Change & Refactoring Rules

- When modifying a component, update all affected files across the project structure (page → sections → widgets/shared). Do not limit updates to a single file.
- Before modifying a form or component, analyze all usage points in the module/project. Apply the change consistently across all shared flows unless explicitly told otherwise.
  - e.g. If a form is shared between “create” and “edit”, and the user asks to add a field, add it for both flows unless the user explicitly says otherwise.
- If a component contains strongly branched logic (e.g., repeated `if (isEdit)` patterns), split the logic into separate components. Select the appropriate component at a higher level instead of branching inside one component.

---

### 4.4 Component Design Principles

- Core Philosophy “**minimum props, maximum flexibility**”
- When creating or refactoring a component, expose only minimal required props.
- Prefer composition (children, nested components, callback props) over adding multiple tightly coupled props.
- Do not pass large objects or full global state; pass only the smallest data subset required.

---

## 5. Forms & State Handling

Primarily for Inertia `useForm` or project-standard form hooks.

1. **Always destructure** `useForm`:
   - Example: `const { data, setData, errors, setError, processing, reset } = useForm<Fillable>(...);`
2. Input handlers:
   - If a field change does more than just `setData`, create a **field-specific handler**:
     - Example: `handleEmailInput`:
       - Sets data.
       - Validates email.
       - On failure, calls `setError` for that field.
   - Never use a generic multi-field handler for complex logic; for simple `setData` use inline lambdas.
3. Validation:
   - Provide basic inline validation at field level.
   - On validation failure, use `setError` to show messages.
4. Submission:
   - Submit forms via **button click handler**, not bare HTML form submit.
   - Submission logic must:
     - Check for errors.
     - Prevent submission if errors exist.
5. Complex Forms (e.g., invoices with line items):
   - Create a **subcomponent** for repeated field groups (e.g. a line item).
   - Each subcomponent:
     - Manages its own local form state for fields (product name, quantity, discount, etc.).
       - Example: `const { data, setData } = useForm<LineItemFillable>(...);`
     - Calls the parent’s `onChange` with updated row data.
   - Parent component:
     - Replaces the relevant row with updated data in the main array/state.

### 5.1 Validation Utilities (Agent)

- Use `utils/validation.utils.ts` as the single source for all validation helpers.
- Implement validation helpers as pure functions: input → boolean or simple typed result.
- When a validation rule is reusable, add or update it in `utils/validation.utils.ts` instead of redefining it.
- Do not duplicate validation logic anywhere else in the codebase.

---

## 6. Routing, Inertia Integration & General Frontend Guidelines

1. **Routing helpers**:
   - Use **Ziggy** or **WayFinder** (as used by the project) for route generation.
   - Avoid hardcoded paths.
2. **Navigation**:
   - Use Inertia `Link` and Inertia router methods for SPA navigation.
3. **Shared props**:
   - Read `flash.*`, `auth.*`, and `permissions` from Inertia page props.
   - Shared props are provided by `HandleInertiaRequests`:
     - `auth`, `companies`, `permissions`, `notifications`, `flash`.
4. **Conditional classes**:
   - Use `cn` from `utils` for class name merging.
   - `cn` is powered by `clsx` (or similar) and should be the standard for conditional classes.
5. **Response types**:
   - Prefer Inertia responses over JSON from backend when building pages.
   - JSON responses are only for intentional API endpoints.
6. **Project structure**:
   - Organize frontend code by **domain module**:
     - `resources/js/pages/{module}/` with `sections/` and `widgets/`.
     - Use shared directories for cross-module components.

---

## 7. Libraries

1. Before adding a new dependency:
   - Check installed libraries and **reuse** them where possible.
2. Use **Lodash** functions where applicable (especially for math and collection helpers):
   - If not installed but clearly beneficial, propose installing it to the user first.
3. Use **date-fns** for date handling.
4. Prefer existing/pre-installed libraries before adding new ones.
5. Only install a new library after confirming with the user.

---

## 8. Data Mapping & Types

- Pages should receive only the data they actually need:
  - Map data from backend API resources to the page props.
- Use `types/api.interface.ts`:
  - As the source of truth for API response types.
  - Keep common/shared structures at its root.
- Use `types/general.enum.ts`:
  - For enums used across features.
  - Only extend it when new backend enums are introduced.

---

## 9. Output Validation Before Responding

Before finalizing code:

- Ensure **TypeScript** passes type checking.
- Ensure syntax correctness.
- Remove unused imports and redundant code.
- Keep the final output **minimal and human-readable**:
  - No extra type checks when a parameter is already validated.
  - No unnecessary conversions when the type is stable and controlled.

---

## 10. Agent Behaviour Summary for React

When working on React code, the AI coding agent must:

1. Respect the **directory structure** and keep modules self-contained.
2. Use **TypeScript, functional components, and hooks** as the default.
3. Build UIs with **Tailwind**, **shadcn/ui**, and existing components before inventing new primitives.
4. Keep **page components thin**, with most logic in sections, hooks, services, or utils.
5. Centralize **types**, **enums**, and **utilities** to avoid duplication.
6. Integrate correctly with **Inertia**, **Ziggy/WayFinder**, and **shared props**.
7. Always aim for **readable, reusable, and minimal** React code.
