# Laravel Specifics

Based on industry standards + Spatie PHP & Laravel AI Guidelines.

## General PHP Practices

- Classes are small, focused, and single-responsibility.
- PSR‑12 formatting (use `./vendor/bin/pint`).
- Use meaningful names; avoid abbreviations.

## Laravel Practices

1. After Post action.  default route will be `redirect()->back()` with flash message unless specified.
2. Don’t create big one function.  use small private functions to support main function.
3. Always write Human readable code.
4. use Laravel standard methods over shortcuts.
5. use Eloquent methods over raw DB Query.
6. don’t use localization unless it’s not specified in Project Guideline.

## Module wise Guideline

### Controllers

- **Practices**:
    - Take standard name for define function:
        - `index` for listing objects
        - `create`  and `edit` to show creation form (mostly included with index)
            - rarely used(Only for big Forms)
        - `store` to create
        - `update` for update
        - `single` for view single object
        - `destroy` to delete object
    - Break down big functions to use same class’s private functions.
        - Private function should very specific. which only take input and return output
        - Don’t handle request inside private function
- **Responsibilities**:
    - Receive the HTTP request.
    - Delegate validation to a **FormRequest**.
    - Delegate authorization to **Policies/Gates**.
    - Delegate domain only logic to a **Service**.
    - CRUD with Model
    - Return an **Inertia** response or redirect.
- **What should NOT be here**:
    - Heavy business logic.
    - Complex conditionals that belong in services.

### Requests

- **Purpose**: Central place for HTTP validation and per-request authorization.
- **Rules**:
    - One FormRequest per intent: e.g. `StoreContactRequest`, `UpdateContactRequest`.
    - Use it for validation rules, messages, and simple authorization checks.
    - Keep them focused on validation, not business logic.

### Models

- **Purpose**: Eloquent models representing domain entities.
- **Best Practices**:
    - When any attribute is default. define here instead of database.
    - Use soft deletes, unless specifically denied.
    - Use Cast as per general use-case. like: JSON to Array, use of Enums, dates, bool or float
- **Allowed**:
    - Relationships.
    - Attribute casts.
    - Query scopes.
    - Light helpers related to persistence.

### Middleware

- **Purpose**: Cross-cutting concerns for HTTP requests.
- **Typical responsibilities**:
    - ID decoding (e.g. `DecodeHashIds`).
    - Inertia shared props (e.g. `HandleInertiaRequests`).
- **Rules**:
    - Must be stateless and fast.
    - Do not put business logic here.

### Services

- **Purpose**: Home of business/domain logic.
- **Rules**:
    - When something is served multiple module. You can create service for that.
    - Each service should focus on a domain or workflow (e.g., `TransactionService`, `ContactService`).
- **Examples**:
    - `TransactionService.php` – create/update transactions along with line items.
    - `ReportService.php` – compute derived financial reports.

### Enums

- **Purpose**: Backed enums for values stored in DB or heavily reused.
- located in `app/Enums/`
- **Rules**:
    - Don’t use enum in database. instead create Enum in Laravel.
    - Create enums for values that are persisted, compared, or rendered often (status, type, segment, currency).
    - Place them under `app/Enums` (use nested folders by domain). Cast them on models via `$casts`.
- **Examples**:
    - `AccountTypeEnum.php`
    - `TransactionTypeEnum.php`
    - `CurrencyEnum.php`

### Traits

- **Purpose**: Shared behavior to be reused in multiple classes.
- Located in `app/Traits`
- **Examples**:
    - `FileUploadTrait.php`
    - `LocalIdHelperTrait.php`

### Migration/Factory & Database design

- Don’t use enum in database. instead use `/app/Enums/` to define enum and cast in Model
- Don’t use cascade and constrains in database. use Laravel Model to define relations
- Use index wherever appropriate
- use Soft-Delete wherever possible.
- Don’t set default value while creating migration.  use Model to define `$attributes`