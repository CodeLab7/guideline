# Laravel Specifics

Based on industry standards and Spatie PHP & Laravel AI Guidelines.

## General PHP Practices

- Classes are small, focused, and single responsibility.
- PSR-12 formatting (use `./vendor/bin/pint`).
- Use meaningful names; avoid abbreviations.

## Laravel Practices

1. After a POST action, the default route should be `redirect()->back()` with a flash message unless otherwise specified.
2. Don't create large single functions; use small private functions to support the main function.
3. Always write human-readable code.
4. Use Laravel's standard methods over shortcuts.
5. Use Eloquent methods over raw DB queries.
6. Don't use localization unless it is specified in the project guidelines.

## Module-wise Guidelines

### Controllers

- **Practices**:
    - Use standard names to define functions:
        - `index` for listing objects.
        - `create` and `edit` to show the creation/edit form (mostly included with `index()` or `single()` rarely used, only for large forms).
        - `store` to create.
        - `update` to update.
        - `single` to view a single object.
        - `destroy` to delete an object.
    - Break down large functions by using the same class's private functions.
        - Private functions should be very specific; they should only take input and return output.
        - Don't handle the request inside private functions.
- **Responsibilities**:
    - Receive the HTTP request.
    - Delegate validation to a **FormRequest**.
    - Delegate authorization to **Policies/Gates**.
    - Delegate domain-only logic to a **Service**.
    - Perform CRUD with the model.
    - Return an **Inertia** response or redirect.
- **What should NOT be here**:
    - Heavy business logic.
    - Complex conditionals that belong in services.

### Requests

- **Purpose**: Central place for HTTP validation and per-request authorization.
- **Rules**:
    - One FormRequest per intent, e.g., `StoreContactRequest`, `UpdateContactRequest`.
    - Use it for validation rules, messages, and simple authorization checks.
    - Keep them focused on validation, not business logic.

### Models

- **Purpose**: Eloquent models representing domain entities.
- **Best Practices**:
    - When any attribute has a default value, define it here instead of in the database.
    - Use soft deletes, unless specifically denied.
    - Use casts according to general use cases, like JSON to array, enums, dates, bool, or float.
- **Allowed**:
    - Relationships.
    - Attribute casts.
    - Query scopes.
    - Light helpers related to persistence.

### Middleware

- **Purpose**: Cross-cutting concerns for HTTP requests.
- **Typical responsibilities**:
    - ID decoding (e.g., `DecodeHashIds`).
    - Inertia shared props (e.g., `HandleInertiaRequests`).
- **Rules**:
    - Must be stateless and fast.
    - Do not put business logic here.

### Services

- **Purpose**: Home of business/domain logic.
- **Rules**:
    - When something is shared across multiple modules, you can create a service for that.
    - Each service should focus on a domain or workflow (e.g., `TransactionService`, `ContactService`).
- **Examples**:
    - `TransactionService.php` – create/update transactions along with line items.
    - `ReportService.php` – compute derived financial reports.

### Enums

- **Purpose**: Backed enums for values stored in the DB or heavily reused.
- Located in `app/Enums/`.
- **Rules**:
    - Don't use enums in the database; instead, create enums in Laravel.
    - Create enums for values that are persisted, compared, or rendered often (status, type, segment, currency).
    - Place them under `app/Enums` (use nested folders by domain). Cast them on models via `$casts`.
- **Examples**:
    - `AccountTypeEnum.php`
    - `TransactionTypeEnum.php`
    - `CurrencyEnum.php`

### Traits

- **Purpose**: Shared behavior to be reused in multiple classes.
- Located in `app/Traits`.
- **Examples**:
    - `FileUploadTrait.php`
    - `LocalIdHelperTrait.php`

### Migrations/Factories & Database Design

- Don't use enums in the database; instead, use `/app/Enums/` to define enums and cast them in the model.
- Don't use cascade and constraints in the database; use Laravel models to define relations.
- Use indexes wherever appropriate.
- Use soft deletes wherever possible.
- Don't set default values in migrations; use the model to define `$attributes`.

