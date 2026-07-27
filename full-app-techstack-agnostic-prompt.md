# Full Application Tech-Stack-Agnostic Recreation Prompt

Use this prompt with a coding agent to create a new full-stack application that is architecturally equivalent to the reference repository, while allowing the user to choose any technology stack.

## User Inputs Required

Ask the user for these values:

1. Project name
2. Business domain
3. Desired functionality and core entities
4. Frontend framework
5. Backend framework
6. Database
7. ORM/data-access tool
8. State management solution
9. Styling framework/design system
10. Authentication provider or strategy
11. Deployment platform
12. Additional technologies or constraints

If the user omits optional stack choices, select modern defaults that best fit the chosen frontend/backend frameworks.

## Goal

Create a production-ready monorepo/full-stack project that preserves the reference application's architecture, modularity, feature organization, maintainability, scalability, and engineering practices, but implements the user-selected technology stack and a new product/domain.

Do not preserve brand-specific content or business logic. Preserve the engineering approach.

## Architectural Shape To Preserve

Build an application with these major parts:

1. Public web application
   - SEO-oriented, localized, high-performance public experience.
   - Server-first rendering where supported by the chosen framework.
   - Thin route files that delegate to feature pages/modules.
   - Feature-based page organization.
   - Robust metadata, sitemap, robots, canonical, Open Graph/Twitter, and structured data support.

2. Administrative dashboard
   - Authenticated back-office app for managing domain entities, content, SEO/settings, users/admins, roles, permissions, inquiries/messages, notifications, and media.
   - Feature-based UI modules with list/table + drawer/form flows.
   - Role/permission-filtered navigation.
   - Server-enforced authorization.

3. Backend/API
   - Layered backend architecture with controllers/handlers, service/use-case layer, repository/data-access layer, models/entities, validation schemas/requests, events/jobs/notifications, and configuration.
   - Public API consumed by the public web app.
   - Dashboard routes/actions consumed by the admin UI.

4. Database
   - Relational or document schema adapted to the chosen database, but preserving the domain modeling approach.
   - Base entity records separate from localized/translated content.
   - Media/album records for media-rich entities.
   - Settings/SEO/config records.
   - Admin/user/role/permission records.
   - Notification/message/inquiry records.

5. Workspace/tooling
   - Monorepo or clearly separated apps/packages.
   - Shared UI/config/types packages when stack supports it.
   - Root scripts for dev, build, lint, format, typecheck/test.
   - Docker/deployment config where applicable.

## Stack Selection Mapping

Translate the reference patterns into the selected stack:

### Frontend Framework

If using Next.js, Remix, Nuxt, SvelteKit, SolidStart, Astro, Rails views, Laravel Inertia, Phoenix LiveView, or another framework:

- Use its native routing mechanism.
- Keep route files/controllers thin.
- Put domain composition in feature modules.
- Prefer server rendering/server loading for initial data and SEO.
- Isolate browser-only interactivity into small client components/islands.
- Use the framework's metadata/head primitives for SEO.
- Implement localized routes and message loading if the product is multilingual.

### Backend Framework

For Laravel, NestJS, Express/Fastify, Django, FastAPI, Rails, Spring Boot, ASP.NET, Phoenix, Go, or Rust:

- Create a controller/handler layer for HTTP.
- Create service/use-case interfaces where the language/framework benefits from abstraction.
- Create concrete service implementations.
- Create repositories/data-access objects for persistence.
- Use framework-native dependency injection when available.
- Use request/schema validators before controller logic.
- Use transactions for multi-record writes.
- Use framework-native logging and error handling.
- Use background jobs/events/listeners/mail notifications where needed.

### Database and ORM

For any database/ORM:

- Model each translatable domain entity as:
  - Base entity: language-independent fields.
  - Translation/localization records: locale, slug, title/name, summary/subtitle, body/description, timestamps.
  - Media records: image/file path, sort order, relation to entity.
- Use constraints/indexes for foreign keys, slugs, locale uniqueness, and common lookup columns.
- Use soft delete semantics where supported for CMS/admin-managed entities.
- Seed default settings, roles/permissions, admin user, SEO pages, and sample content.

### Authentication Provider

Whether using sessions, JWT, OAuth/OIDC, Auth.js, Clerk, Supabase Auth, Firebase Auth, custom auth, or framework-native auth:

- Separate public access from admin access.
- Protect dashboard routes/actions.
- Implement role-based and permission-based authorization.
- Share only safe user/permission data to the frontend.
- Enforce permissions on the server; client-side filtering is only UX.
- Include password reset/email verification/profile settings when using first-party auth.

### State Management

Use minimal state by default:

- Server data is source of truth.
- Local component state for drawers, selected rows, popovers, loading flags, success/error flags, and search inputs.
- Form library state for forms.
- URL/query params for pagination and filters.
- Add global state only for cross-cutting concerns that truly need it, such as auth session, theme, sidebar state, or cached client data.

## Tech-Agnostic Folder Organization

Adapt names to the selected stack, but preserve this shape:

```text
project-root/
  apps/
    public-web/
      src/
        routes-or-app/
        components/
          ui/
          shared/
        features/
          <feature>/
            pages-or-screens/
            components/
            hooks-or-composables/
            locals-or-messages/
        i18n/
        config/
        layouts/
        types/
        utils/
          services-or-api/
          seo/
          media/
    dashboard/
      backend/
        controllers-or-handlers/
        requests-or-validators/
        services-or-use-cases/
        interfaces-or-ports/
        repositories-or-adapters/
        models-or-entities/
        events/
        jobs/
        listeners/
        mail-or-notifications/
        middleware/
        providers-or-di/
        traits-or-shared-behaviors/
      frontend/
        components/
        helpers/
        hooks-or-composables/
        layouts/
        pages/
          <feature>/
            View<Feature>/
              View<Feature>
              components/
                <Feature>Table
                <Feature>Form
                Translations
              hooks/
        types/
      routes/
      database/
        migrations-or-schema/
        seeders/
        factories/
      tests/
  packages/
    ui/
    lint-config/
    type-config-or-shared-types/
  docs-or-seo/
  docker-compose.yml
  root package/build config
```

If the chosen ecosystem does not use this exact filesystem convention, implement the closest idiomatic equivalent while preserving boundaries and ownership.

## Design Patterns To Preserve

- Feature-based frontend modules.
- Layered backend architecture.
- Repository pattern for persistence/data access.
- Service/use-case layer between controllers and repositories.
- Dependency inversion through interfaces/ports when supported.
- DTO/schema/request validation before business logic.
- Transaction script pattern inside repositories/use cases for multi-table writes.
- Server-rendered public pages with client-side islands for interactivity.
- CMS-style multilingual entity modeling.
- Permission-driven dashboard navigation and route protection.
- Thin route/page wrappers delegating to feature modules.
- Shared UI primitives and design utilities.
- Defensive API response normalization in frontend data clients.
- Fail-soft public content fetching.
- Centralized configuration wrapper around environment variables.

## Public Web Requirements

Implement the public app with these principles:

1. Routing
   - Locale-aware routes if the product needs multiple languages.
   - Route groups or equivalent organization by feature.
   - Detail pages use slug routes.
   - Static route registry for sitemap generation.

2. Layout
   - Global layout fetches shared settings, navigation data, footer data, SEO defaults, and locale direction.
   - Set language and text direction at the document/root level.
   - Use globally loaded fonts and CSS variables/design tokens.
   - Render shared navigation and footer around page content.

3. Data fetching
   - Create one service/API client module per public entity.
   - Read base URLs and cache/revalidate settings from a central app config.
   - Send locale/language headers for localized content.
   - Normalize varied backend response shapes into stable frontend types.
   - Return safe empty values on failure for non-critical marketing content.
   - Fetch independent data in parallel.

4. SEO
   - Implement page-level metadata generation.
   - Implement item/detail metadata generation.
   - Support fallback from page-specific metadata to global metadata.
   - Support title, description, robots, canonical, Open Graph, Twitter, locale, images, and structured data.
   - Generate robots and dynamic sitemap with static and dynamic entries.

5. UI
   - Use semantic sections and accessible forms.
   - Use reusable UI primitives.
   - Use utility-first styling or selected design system consistently.
   - Use responsive layouts across mobile/tablet/desktop.
   - Use animation sparingly through reusable wrappers.

## Dashboard Requirements

Implement the dashboard with these principles:

1. Route/action organization
   - Group routes/actions by domain entity.
   - Use named routes or typed route helpers when supported.
   - Use middleware/guards for authentication, verification, roles, and permissions.
   - Keep public API routes separate from dashboard routes.

2. Page pattern
   - Each manageable entity gets a `View<Entity>` page.
   - Page renders layout, breadcrumbs, data table, and drawer/modal form.
   - Page owns `selectedRow` and `isFormOpen` state.
   - Table owns search, pagination, row actions, and delete confirmation.
   - Form owns create/update/view behavior and translation editing.

3. Tables
   - Server-driven pagination.
   - Debounced search routed through query params.
   - Manual pagination state tied to backend paginator.
   - View, edit, delete actions.
   - Permission-aware action visibility if required.

4. Forms
   - Use schema validation on client where framework supports it.
   - Use server validation as source of truth.
   - Map server errors back into fields.
   - Support translations/locales as nested data.
   - Support media uploads and cleanup flows.
   - Use success/error toasts.

5. Shared data
   - Share authenticated user/admin, permissions, supported locales, logos/settings, sidebar state, and theme/appearance to dashboard frontend.
   - Filter sidebar items based on shared permissions.

## Backend Requirements

For every domain entity, create a complete vertical slice:

1. Data model/schema
   - Base entity.
   - Translation/localization model/table if translatable.
   - Media/album model/table if media-rich.
   - Timestamps, soft deletes, relationships, constraints.

2. Request validation
   - Store/create schema.
   - Update schema.
   - Custom messages where user-facing.
   - Nested validation for translations.

3. Controller/handler
   - Index/list returns dashboard page data or JSON depending on route type.
   - Create/update/delete delegate to service/use case.
   - Public API list/detail endpoints expose localized content.
   - Return clear success responses and generic failure responses.

4. Service/use-case
   - Encapsulate application workflow.
   - Depend on repository/data-access abstraction.
   - Keep controllers thin.

5. Repository/data access
   - Encapsulate queries and persistence.
   - Search translated fields where applicable.
   - Generate slugs from localized titles/names.
   - Use transactions for multi-record changes.
   - Clean up temp media references after success.
   - Log and rethrow persistence failures.

6. Events/jobs/notifications
   - Use event/listener/job/mail patterns for contact forms, reservations, notifications, or any asynchronous side effect.
   - Keep side effects outside core controller logic.

## Validation, Errors, and Logging

- Validate requests at the boundary before service logic.
- Keep client validation for UX only.
- Server validation is authoritative.
- Use structured error responses with `message` and `errors` where appropriate.
- Public content fetchers should fail soft for non-critical content.
- Admin mutations should show explicit errors/toasts.
- Log backend exceptions with framework logger.
- Avoid exposing stack traces or secrets to clients.

## Security Requirements

- Protect dashboard by authentication.
- Add role and permission checks per route/action.
- Use CSRF/session protections when applicable.
- Validate uploads and accepted media types.
- Clean temporary files after persistence.
- Keep secret env vars server-side only.
- Use anti-abuse protection such as reCAPTCHA/hCaptcha/rate limiting on public mutation forms.
- Enforce authorization server-side even when UI hides actions.
- Sanitize/serialize structured data and rich text safely.

## Performance Requirements

- Prefer server-rendered public pages.
- Cache/revalidate public content where framework supports it.
- Fetch independent data in parallel.
- Keep client bundles small by isolating client-only code.
- Use optimized fonts/media.
- Paginate dashboard lists.
- Debounce search.
- Cache permissions/roles where provider supports it.
- Avoid global state for server data unless chosen stack requires client cache.

## Testing and Workflow

Generate root scripts/tasks for:

- Development servers.
- Build.
- Start.
- Lint.
- Format.
- Typecheck/static analysis.
- Backend tests.
- Frontend tests if test framework selected.

Create tests for:

- Authentication flows.
- Dashboard authorization.
- Settings/profile flows.
- Critical entity CRUD.
- Public API list/detail responses.
- Public form submission side effects.

Before completion, run real verification commands supported by the chosen stack and report exact results.

## Final Agent Instructions

- Ask only for the requested inputs.
- Choose idiomatic libraries for the selected stack, but preserve architecture and boundaries.
- Build a full vertical slice for each requested core entity.
- Keep route files/controllers thin.
- Keep feature modules cohesive.
- Use centralized config and environment handling.
- Implement real auth, authorization, validation, SEO, and dashboard management patterns.
- Do not generate a visual-only clone. Generate an architecturally equivalent application.
