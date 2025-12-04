# React Specifics

### Directory Structure

1. All react code will be here: `resources/js/`
2. **Components**
    1. `components/ui/` - For library specific components. like ShadCN
    2. `components/shared/` - For project specific general UI components 
        1. example: `delete-confirmation.tsx` , `phone-input.tsx`
    3. `components/specific/`  - For more Feature-specific components like: 
        1. example: `team-members-list.tsx`
    4. `components/` - to provide shared widgets etc.
        1. example: `notification-toast.tsx`
3. **Layouts**
    1. `layout/` - For Layouts/Shell for our pages.
        1. Example: `auth-layout.tsx`  or `UserLayout`
    2. `layout/partials` - for sections used inside Layouts
        1. Example: `side-bar.tsx` , `header.tsx` or `user-menu.tsx`
4. **Hooks**
    1. `hooks/` to hold all custom react hooks. 
        1. example: `useIsMobile`, `use-apperance` , `use-debounce`
5. **Pages**
    1. `pages/{module-name}/` - to accomodate pages components that will be called by Laravel controllers.
    2. `pages/{module-name}/sections/` - to hold sections from the page.
    3. `pages/{module-name}/partials/` - to hold small slave components that can be used inside that module.
6. **Types**
    1. `types/` - will hold most of data types which are used across the project or supplied via Laravel APIs.
    2. `types/api.interface.ts`  File will hold all data that can be provided by Laravel APIs.
    3. `types/general.enum.ts`  File will provide all enums that can be used as a options or possible values from APIs.
7. **Utilities**
    1. `utils/` is most important folder which hold all common/generalised functions that provide various facilities to project.  it hold multiple files. For example:
        1. `number.enums.ts` which provide all kind of number and currency transformations and other functions.
        2. `date.enums.ts` to provide all date related functions.
8. **i18n**
    1. To provide translations if project supports it.

### **UI Standards**

*(UI guidelines remain in project guideline — only standards stay here)*

- Use TailwindCSS.
- Use shared or specific components to avoid duplication.
- Use Shadcn/ui components where possible to maintain design consistency.
- Style with Tailwind; prefer existing `components/ui/*` and `theme-components/*` before creating new primitives.
- **Responsiveness**:
    - UI must fits to all screen till 360px and more
    - Use standard Tailwind break-points to manage UI setup
    - To switch components use `useIsMobile` hook to check if the device is mobile or not. don’t hide/show by CSS
    - set heights to accommodate mobile keyboard. for example: dvh instead of vh.
- While designing new block or page. use library specified in project guideline or components inside `/components/*/**`  to create those.  don’t apply custom styling unless specifically told by user.
- **Actions** (click, Tabbing, navigation) should respond immediate or give user sense of action (for example spinner on form submission).
    - This does not mean to include unnecessary animations.
- **Consistency**: Maintain consistent spacing, typography and icon sets.
    - Icons: Prefer `lucide-react`; fall back to `react-icons` when missing.
- **Accessibility** : No need to introduce Accessibility unless specified by project guideline. keep elements/component clean and avoid unnecessary attributes.
- While creating UI Component. make it nestable if necessary. example:
    
    ```jsx
     <Card>
    	 <CardHeader>
    		 <CardTitle>Title</CardTitle>
    	 </CardHeader>
    	 <CardContent>
    		 Something</CardContent 
    	 </CardContent>
     </Card>
    ```
    

### **Reusability Mindset**

1. Promote reusability: move cross-module elements into `components/specifics/*`.
2. For small UI components use `components/shared/` folder to define compone
3. While working on component, If you need to create a hook that can be used project wide. define it inside `hooks/` folder.
4. If you ever need to define a function that can be used in other area as well. define it inside `utils/` folder.
5. For Utility functions that can be used across multiple features, put them in the utils folder.
6. before define function. check everything inside `/utils/` folder if anything available for that.

## Coding Standards

- Use **TypeScript** only.
- Use functional components + Hooks.
- Avoid unnecessary renders: use `React.memo`,`useMemo`, `useCallback` only when needed.
- Use meaningful variable and component names.
- Extract repeated JSX into components.
- Follow Prettier and ESLint configurations for formatting and lint rules.
- Keep components small and focused; extract subcomponents into `/sections/*` or `/partials/*`
- Avoid deep nesting; prefer early returns and small helpers.
- Keep code readable, reusable, and predictable.
- Prefer clear naming over brevity.
- Keep functions small and focused.
- Avoid duplicate logic → extract services/helpers.

### Naming Conventions

- **Files: kebab-case.** Example: `contact-list.tsx`, `user-form.tsx`
- **Components: PascalCase.** Example: `ContactList`, `UserForm`
- **Variables & Functions : camelCase,** Example: `totalAmount`, `loadContacts`
- **Constants: UPPER_SNAKE_CASE,** Example: `MAX_LENGTH`, `API_URL`

## Best Practices (Must Follow)

- When building pages:
    - Keep `index.tsx` minimal; compose sections from `/sections/*`  and supported by `/partials/`
    - Place module-specific components under the module’s `partials/` or `components/`.
    - Keep module-specific helpers in a local `{module-name}-utils.ts` when needed.
        - example: `/pages/sale/sale-utils.ts` to support sale utilities.
- `api.interface.ts` for types for API responses. Don’t modify this regularly. Update only when you have new structure of data coming from backend to frontend.
    - Keep the structure of data common across multiple features in the root of this file.
- `general.enum.ts` for general Enums. Don’t modify this regularly. Update it when you have new Enums coming from backend to frontend.
- Always write human readable and minimum code to get result.
    - Avoid over check of type-safety. (For example: if function already validate parameter then avoid type-safety before passing them).
    - Don’t over convert variables, if something defined to some type. then it’ll probably will be same type’s data assigned.

## General Guideline

1. use Ziggy or WayFinder helpers, Whatever project uses for routing. Avoid hardcoded paths.
2. Navigation: Use Inertia `Link` and router methods for SPA navigation.
3. Flash & Shared Props: Read `flash.*`, `auth.*`, and `permissions` from Inertia page props.
4. on conditional ClassName or multiple class-name merge, use `cn` from utils which use `clsx` library and provide better function.
5. Prefer Inertia responses over JSON unless building an API endpoint intentionally.
6. **Project Structure:** Organize front-end code by domain module. Group pages and components by feature (e.g. `resources/js/pages/{module}/` with sub-folders for `sections/` and `partials/`), and use shared directories for reusable components.

### Libraries

1. Check installed libraries and use those wherever possible.
2. use Lodash functions wherever applicable (specially for math). (install library if not installed)
3. use date-fns to handle dates.
4. prefer pre-installed libraries before installing new one.
    - if library available to simplify task. then install it after confirm with user.

### Form Handling

1. Always destructor `useForm` 
2. use input handler if field change do more than just set data
    1. example: `handleEmailInput`  will set data and validate email. if not valid then set error for that field.
    2. never use generic handler which works for more than one field. in that case, use inline function to set data
3. Fields should have basic inline validation. in case of failure, use `setError`to show message.
4. Form should be submitted from button click instead of regular Form submission.
5. submission method must check for error. If error exists, prevent to submit
6. While working on Big form (for example: invoice) with repeated field groups (line items)
    1. Create sub-component for repeated field
    2. use own form to handle set data ( ex: product name, quantity, discount)
    3. on change of field, it should submit to main component’s `onChange` function. which replace existing row with new data. 

### Component designing Patterns

1. Ideal component should take minimum props and generate to the point.
2. Module components should be
    1. **page components**
        1. directly called from Laravel Controllers.
        2. have minimum functionality.  more like provide the structure and calls the sections
    2. **section components**
        1. These are master components hold most of business logics
        2. if section is bigger, it may call other section to devide logic
        3. For smaller and repetable part it should call partials or UI components
    3. **partials or UI components**
        1. These are slave components.
        2. Have minimum logic that support props manipulation and process before calling prop methods.

### Data Mapping

- Shared props are provided by `HandleInertiaRequests` (`auth`, `companies`, `permissions`, `notifications`, `flash`).
- Pages receive only what they need — map with API Resources.

## **Output Validation Before Responding**

- Always run typescript check before finalize
- Ensure syntax correctness
- Remove redundant code