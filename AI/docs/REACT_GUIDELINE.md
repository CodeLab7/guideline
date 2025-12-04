# React Specifics

### Directory Structure

1. All React code will be here: `resources/js/`.
2. **Components**
    1. `components/ui/` - For library-specific components, like shadcn/ui.
    2. `components/shared/` - For project-specific general UI components.
        1. Example: `delete-confirmation.tsx`, `phone-input.tsx`.
    3. `components/specific/` - For more feature-specific components, like:
        1. Example: `team-members-list.tsx`.
    4. `components/` - To provide shared widgets, etc.
        1. Example: `notification-toast.tsx`.
3. **Layouts**
    1. `layout/` - Layouts/shells for our pages.
        1. Example: `auth-layout.tsx` or `UserLayout`.
    2. `layout/partials` - Sections used inside layouts.
        1. Example: `side-bar.tsx`, `header.tsx`, or `user-menu.tsx`.
4. **Hooks**
    1. `hooks/` - Holds all custom React hooks.
        1. Example: `useIsMobile`, `use-appearance`, `use-debounce`.
5. **Pages**
    1. `pages/{module-name}/` - To accommodate page components that will be called by Laravel controllers.
    2. `pages/{module-name}/sections/` - To hold sections from the page.
    3. `pages/{module-name}/partials/` - To hold small slave components that can be used inside that module.
6. **Types**
    1. `types/` - Holds most of the data types that are used across the project or supplied via Laravel APIs.
    2. `types/api.interface.ts` - This file holds all data types that can be provided by Laravel APIs.
    3. `types/general.enum.ts` - This file provides all enums that can be used as options or possible values from APIs.
7. **Utilities**
    1. `utils/` - The most important folder. It holds all common/generalised functions that provide various facilities to the project. It holds multiple files. For example:
        1. `number.enums.ts` - Provides all kinds of number and currency transformations and other functions.
        2. `date.enums.ts` - Provides all date-related functions.
8. **i18n**
    1. Provides translations if the project supports it.

### **UI Standards**

*(UI guidelines remain in the project guideline; only standards stay here.)*

- Use TailwindCSS.
- Use shared or specific components to avoid duplication.
- Use shadcn/ui components where possible to maintain design consistency.
- Style with Tailwind; prefer existing `components/ui/*` and `theme-components/*` before creating new primitives.
- **Responsiveness**:
    - UI must fit screens down to 360px width and larger.
    - Use standard Tailwind breakpoints to manage UI layout.
    - To switch components, use the `useIsMobile` hook to check if the device is mobile or not. Don't hide/show only by CSS.
    - Set heights to accommodate the mobile keyboard (for example, use `dvh` instead of `vh`).
- While designing a new block or page, use the library specified in the project guideline or components inside `/components/*/**` to create them. Don't apply custom styling unless specifically told by the user.
- **Actions** (click, tabbing, navigation) should respond immediately or give the user a sense of action (for example, a spinner on form submission).
    - This does not mean you should include unnecessary animations.
- **Consistency**: Maintain consistent spacing, typography, and icon sets.
    - Icons: Prefer `lucide-react`; fall back to `react-icons` when missing.
- **Accessibility**: No need to introduce accessibility features unless specified by the project guideline. Keep elements/components clean and avoid unnecessary attributes.
- While creating a UI component, make it nestable if necessary. Example:

`<Card>  <CardHeader>    <CardTitle>Title</CardTitle>  </CardHeader>  <CardContent>    Something  </CardContent></Card>`

### **Reusability Mindset**

1. Promote reusability: move cross-module elements into `components/specifics/*`.
2. For small UI components, use the `components/shared/` folder to define components.
3. While working on a component, if you need to create a hook that can be used project-wide, define it inside the `hooks/` folder.
4. If you ever need to define a function that can be used in other areas as well, define it inside the `utils/` folder.
5. For utility functions that can be used across multiple features, put them in the `utils` folder.
6. Before defining a function, check everything inside the `/utils/` folder to see if anything is already available for that.

## Coding Standards

- Use **TypeScript** only.
- Use functional components + hooks.
- Avoid unnecessary renders: use `React.memo`, `useMemo`, and `useCallback` only when needed.
- Use meaningful variable and component names.
- Extract repeated JSX into components.
- Follow Prettier and ESLint configurations for formatting and lint rules.
- Keep components small and focused; extract subcomponents into `/sections/*` or `/partials/*`.
- Avoid deep nesting; prefer early returns and small helpers.
- Keep code readable, reusable, and predictable.
- Prefer clear naming over brevity.
- Keep functions small and focused.
- Avoid duplicate logic - extract services/helpers.

### Naming Conventions

- **Files: kebab-case.** Example: `contact-list.tsx`, `user-form.tsx`.
- **Components: PascalCase.** Example: `ContactList`, `UserForm`.
- **Variables & Functions: camelCase.** Example: `totalAmount`, `loadContacts`.
- **Constants: UPPER_SNAKE_CASE.** Example: `MAX_LENGTH`, `API_URL`.

## Best Practices (Must Follow)

- When building pages:
    - Keep `index.tsx` minimal; compose sections from `/sections/*` and support them with `/partials/`.
    - Place module-specific components under the module's `partials/` or `components/`.
    - Keep module-specific helpers in a local `{module-name}-utils.ts` when needed.
        - Example: `/pages/sale/sale-utils.ts` to support sale utilities.
- Use `api.interface.ts` for types for API responses. Don't modify this regularly. Update it only when you have a new structure of data coming from backend to frontend.
    - Keep the structure of data common across multiple features in the root of this file.
- Use `general.enum.ts` for general enums. Don't modify this regularly. Update it when you have new enums coming from backend to frontend.
- Always write human-readable and minimal code to get the result.
    - Avoid over-checking type safety. For example: if a function already validates a parameter, then avoid extra type checks before passing it.
    - Don't over-convert variables. If something is defined with some type, then it will probably always have the same type of data assigned.

## General Guideline

1. Use Ziggy or WayFinder helpers (whatever the project uses) for routing. Avoid hardcoded paths.
2. Navigation: Use Inertia `Link` and router methods for SPA navigation.
3. Flash & Shared Props: Read `flash.*`, `auth.*`, and `permissions` from Inertia page props.
4. For conditional class names or merging multiple class names, use `cn` from utils, which uses the `clsx` library and provides a better function.
5. Prefer Inertia responses over JSON unless you are intentionally building an API endpoint.
6. **Project Structure:** Organize front-end code by domain module. Group pages and components by feature (for example, `resources/js/pages/{module}/` with sub-folders for `sections/` and `partials/`), and use shared directories for reusable components.

### Libraries

1. Check installed libraries and use those wherever possible.
2. Use Lodash functions wherever applicable (especially for math). Install the library if it is not installed.
3. Use `date-fns` to handle dates.
4. Prefer pre-installed libraries before installing a new one.
    - If a library is available to simplify a task, then install it after confirming with the user.

### Form Handling

1. Always destructure `useForm`.
2. Use an input handler if a field change does more than just set data.
    1. Example: `handleEmailInput` will set data and validate the email. If it is not valid, then set an error for that field.
    2. Never use a generic handler that works for more than one field. In that case, use an inline function to set data.
3. Fields should have basic inline validation. In case of failure, use `setError` to show a message.
4. Forms should be submitted from a button click instead of regular form submission.
5. The submission method must check for errors. If errors exist, prevent submission.
6. While working on a big form (for example: an invoice) with repeated field groups (line items):
    1. Create a subcomponent for the repeated field.
    2. Use its own form state to handle setting data (for example: product name, quantity, discount).
    3. On change of a field, it should call the main component's `onChange` function, which replaces the existing row with the new data.

### Component Design Patterns

1. An ideal component should take the minimum props and do one clear job.
2. Module components should be:
    1. **Page components**
        1. Directly called from Laravel controllers.
        2. Have minimal functionality; mostly provide the structure and call the sections.
    2. **Section components**
        1. These are master components that hold most of the business logic.
        2. If a section is bigger, it may call other sections to divide logic.
        3. For smaller and repeatable parts, it should call partials or UI components.
    3. **Partials or UI components**
        1. These are slave components.
        2. They have minimal logic that supports props manipulation and processing before calling prop methods.

### Data Mapping

- Shared props are provided by `HandleInertiaRequests` (`auth`, `companies`, `permissions`, `notifications`, `flash`).
- Pages receive only what they need - map with API resources.

## **Output Validation Before Responding**

- Always run a TypeScript check before finalizing.
- Ensure syntax correctness.
- Remove redundant code.

