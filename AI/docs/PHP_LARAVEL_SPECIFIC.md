# Laravel Implementation Guidelines (AGENT Spec)

Framework-specific rules for how the AI coding agent must structure and write Laravel + PHP code.  
These build on the global behaviour guidelines and Spatie PHP & Laravel AI Guidelines.

---

## 1. General PHP Practices

When writing PHP code:

- Keep classes **small, focused, and single-responsibility**.
- Follow **PSR-12** formatting.
  - Prefer: `./vendor/bin/pint` for code style.
- Use **clear, meaningful names**.
  - Avoid abbreviations and cryptic short names.
  - Method and variable names should reflect intent.

---

## 2. General Laravel Practices

When implementing Laravel code:

1. After successful `POST` actions, the default behaviour is:
   - `return redirect()->back()->with('status', '...');`
   - Include a meaningful **flash message** unless the user specifies a different flow.
2. Avoid large, monolithic functions:
   - Use **small private methods** to support main public methods.
   - Each private method should do one clear thing.
3. Always write **human-readable** code:
   - Prefer clarity to cleverness.
4. Prefer **Laravel’s standard APIs** over shortcuts or custom helpers, unless the project defines them.
5. Prefer **Eloquent** over raw DB queries:
   - Only use raw queries if absolutely required and justified.
6. Do **not** introduce localization/i18n unless:
   - The project guidelines explicitly mention it.
   - Or the user directly asks for it.

---

## 3. Module-wise Guidelines

The following rules define responsibilities per layer.  
When in doubt, move logic “downwards” (controller → service → model) to keep controllers thin.

---

### 3.1 Controllers

#### Purpose

HTTP entrypoints that coordinate requests, validation, authorization, and responses.

#### Naming & Methods

Use standard controller method names:

- `index` – list objects.
- `create` – show creation form (if separate; often merged into `index` for small UIs).
- `edit` – show edit form.
- `store` – create a new resource.
- `update` – update an existing resource.
- `single` – show a single resource.
- `destroy` – delete a resource.

#### Structure & Rules

- Keep controller methods **short** and **orchestrating** only.
- Break down complex logic by creating **private methods** on the same controller when needed:
  - Private methods should:
    - Take inputs.
    - Return outputs.
    - Not read from the request directly (no `$this->request` or `request()` in private methods).
- Controllers should:
  - Receive the HTTP request (type-hinted where possible).
  - Delegate **validation** to a `FormRequest`.
  - Delegate **authorization** to Policies or Gates.
  - Delegate **business/domain logic** to a **Service**.
  - Perform CRUD using **Eloquent models**.
  - Return either:
    - An **Inertia** response, or
    - A **redirect** (often `redirect()->back()` with flash).

#### What must NOT live in Controllers

- Heavy business logic.
- Complex branching or workflows.
- Reused domain logic that belongs in Services.

---

### 3.2 Requests (FormRequest)

#### Purpose

Central place for **HTTP validation** and **per-request authorization**.

#### Rules

- Use **one FormRequest per intent**, e.g.:
  - `StoreContactRequest`
  - `UpdateContactRequest`
- Use FormRequests for:
  - Validation rules.
  - Validation messages.
  - Simple authorization checks (`authorize()`).
- Do **not** put business logic here.
- Keep them **focused**:
  - No persistence logic.
  - No complex domain decisions.

---

### 3.3 Models

#### Purpose

Eloquent models that represent domain entities and database tables.

#### Best Practices

- If an attribute has a **default value**, define it on the model:
  - Use `$attributes` or accessors instead of DB-level defaults where possible.
- Use **soft deletes** unless the project explicitly forbids it.
- Use `$casts` to ensure correct types for:
  - JSON → array.
  - Enums.
  - Dates / datetimes.
  - Boolean.
  - Float / decimal, etc.

#### Allowed Content in Models

- Relationships (`hasOne`, `hasMany`, `belongsTo`, etc.).
- Attribute casts (`$casts`).
- Query scopes (`scope...` methods).
- Light helpers related to persistence (e.g., small methods that express common queries or computed attributes).

#### What to Avoid in Models

- Heavy domain workflows.
- Cross-module orchestration.
- Controller-like responsibilities.

---

### 3.4 Middleware

#### Purpose

Handle cross-cutting concerns for HTTP requests.

#### Typical Responsibilities

- ID decoding (e.g. `DecodeHashIds`).
- Injecting shared props for Inertia (e.g. `HandleInertiaRequests`).
- Simple request pre- or post-processing that does not depend on domain rules.

#### Rules

- Middleware must be:
  - **Stateless**.
  - **Fast**.
- Do **not** put business logic or heavy domain branching here.
- Do **not** perform persistent side effects unrelated to the request pipeline.

---

### 3.5 Services

#### Purpose

Home for **business/domain logic** and workflows.

#### Rules

- Create a **Service** when logic:
  - Is reused in multiple places/modules; or
  - Represents a clear domain process or workflow.
- Each service should map to a **domain** or **workflow**, e.g.:
  - `TransactionService`
  - `ContactService`
  - `ReportService`
- Services may:
  - Coordinate multiple models.
  - Implement domain rules.
  - Perform validations that are domain-related (not just HTTP-related).
  - Encapsulate multi-step operations (e.g. create a transaction and its line items).

#### Examples

- `TransactionService.php`:
  - Create, update, or delete transactions with their line items in a single workflow.
- `ReportService.php`:
  - Compute derived financial reports using stored data.

---

### 3.6 Enums

#### Purpose

Backed enums for values **stored in the DB** or reused heavily in the application.

#### Location

- All enums are in `app/Enums/`.
- Use nested folders by domain when helpful:
  - `app/Enums/Accounting/AccountTypeEnum.php`

#### Rules

- Do **not** use database-level enums in migrations.
  - Instead, define PHP enums and cast them in the model.
- Create enums for values that are:
  - Persisted (status, type, segment, currency, etc.).
  - Frequently compared.
  - Often rendered in the UI.
- Cast enums on models via `$casts`.

#### Examples

- `AccountTypeEnum.php`
- `TransactionTypeEnum.php`
- `CurrencyEnum.php`

---

### 3.7 Traits

#### Purpose

Shared behaviour reused across multiple classes.

#### Location

- Place all traits under `app/Traits/`.
- Use domain-relevant names and folders when necessary.

#### Examples

- `FileUploadTrait.php` – shared helpers for file uploads.
- `LocalIdHelperTrait.php` – shared helpers for local ID encoding/decoding.

#### Rules

- Use traits to share **small, cohesive behaviours**.
- Avoid “god traits” that mix unrelated responsibilities.
- Prefer services or composition when behaviour becomes complex.

---

### 3.8 Migrations, Factories, and Database Design

#### General Rules

- Do **not** use DB enums:
  - Represent enums as PHP enums under `/app/Enums/`.
  - Cast them on the model via `$casts`.
- Avoid cascade and complex constraints at DB level:
  - Model relationships and behaviour in **Laravel models**.
  - Use Laravel events, observers, and services instead of DB triggers.
- Use **indexes** wherever appropriate:
  - For foreign keys.
  - For frequently queried columns.
- Use **soft deletes** wherever possible:
  - Prefer soft deletes as the default deletion strategy.
- Do **not** set default values in migrations:
  - Define defaults in the model’s `$attributes` or via accessors.

---

## 4. Agent Behaviour Summary for Laravel

When working on Laravel code, the AI coding agent must:

1. Respect **layer responsibilities**:
  - Controllers: thin, orchestration only.
  - Requests: validation + simple authorization.
  - Services: business logic and workflows.
  - Models: persistence + light helpers.
  - Middleware: cross-cutting, stateless concerns.
2. Prefer **Eloquent + Laravel conventions** over custom patterns unless the project clearly defines otherwise.
3. Use **enums, soft deletes, casts**, and sensible defaults on models instead of DB-level enums and defaults.
4. Keep code **small, readable, and consistent** across all modules.
