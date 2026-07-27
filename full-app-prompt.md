# Full Application Recreation Prompt

Use this prompt with a modern coding agent to create a new product that follows the same engineering approach, architecture, folder organization, and conventions as this repository. The generated project must implement a different business domain while preserving the technical style and team practices.

## User Inputs Required

Ask the user for only these values before building:

1. Project name
2. Business domain
3. Desired functionality and core entities

Derive everything else from the architectural template below.

## Target Outcome

Build a production-ready full-stack monorepo with:

- A public, SEO-focused web application.
- A protected administrative dashboard for managing content and operational data.
- A backend API consumed by the public app.
- A database-backed CMS-style data model with multilingual content support.
- Shared tooling/configuration packages managed from the workspace root.
- Docker-ready deployment configuration.

The new application should feel like it was built by the same engineering team, even when the business domain and feature names are completely different.

## Complete Technology Stack To Use

Use the repository's stack unless the user explicitly requests otherwise:

### Workspace and Build

- Monorepo managed by pnpm workspaces.
- Turborepo for root-level orchestration.
- TypeScript across frontend packages.
- Prettier with import organization and Tailwind class sorting.
- Shared local packages under `packages/*` for reusable UI, ESLint config, and TypeScript config.

### Public Web Application

- Next.js 16 App Router.
- React 19.
- TypeScript.
- next-intl for locale-aware routing, messages, navigation helpers, and middleware.
- Tailwind CSS v4.
- shadcn/Radix-style UI primitives where reusable UI is needed.
- Server Components by default.
- Small Client Components only for interactivity, forms, carousels, animations, or browser-only APIs.
- Native `fetch` on server with `next: { revalidate }` for cache/revalidation behavior.
- Axios only for client-side mutations or client-side APIs.
- Next Metadata API, dynamic sitemap, robots, JSON-LD, Open Graph, Twitter metadata, canonical URLs, and robots metadata.
- next/font for font loading and layout-level font variables.
- Motion/GSAP/Swiper only where animation or carousel behavior is required.

### Dashboard Application

- Laravel 12 backend application.
- Inertia.js with React 19 and Vite.
- TypeScript for dashboard frontend.
- Tailwind CSS v4.
- Radix/shadcn-style UI primitives.
- Laravel Wayfinder or equivalent typed route helpers for frontend route generation.
- React Hook Form and Zod for complex dashboard form validation.
- TanStack React Table for data grids.
- Sonner-style toast notifications.
- CKEditor for rich text where needed.
- FilePond for image/media uploads where needed.
- Spatie Laravel Permission for roles and permissions.
- Laravel queues, events, listeners, mailables, and jobs for asynchronous notifications/email workflows.

### Database and Infrastructure

- MySQL as primary database.
- Laravel migrations and seeders.
- Eloquent ORM.
- Soft deletes for CMS/content entities when appropriate.
- Docker Compose including database, phpMyAdmin, dashboard app, and web app services.

## Repository Shape To Recreate

Use this monorepo organization:

```text
project-root/
  apps/
    web/                    # Next.js public app
    dashboard/              # Laravel + Inertia React admin app
  packages/
    ui/                     # shared React UI primitives/package
    eslint-config/          # shared lint config
    typescript-config/      # shared tsconfig presets
  seo/                      # optional SEO/content audit markdown per page/entity
  docker-compose.yml
  package.json
  pnpm-workspace.yaml
  pnpm-lock.yaml
  turbo.json
```

Root scripts should orchestrate app scripts through Turborepo:

- `dev`: run public web, dashboard Vite, and dashboard Laravel server in parallel.
- `build`: build web and dashboard frontend.
- `start`: start production/runtime servers.
- `lint`: delegate lint to workspaces.
- `format`: run Prettier over TypeScript, TSX, and Markdown.
- `check-types`: delegate TypeScript type checks.

## Application Architecture

Use a pragmatic layered + feature-based architecture:

1. Workspace layer
   - Root build scripts and tooling configuration.
   - Shared packages for UI/config only; avoid business logic in shared packages unless reused by both apps.

2. Public web layer
   - Next.js App Router routes are thin wrappers.
   - Route files export pages and `generateMetadata`, but domain composition lives in `src/features/*`.
   - Server Components fetch page data and compose sections.
   - Client Components are isolated to forms, sliders, animations, and browser APIs.

3. Dashboard presentation layer
   - Inertia pages under `resources/js/pages/<feature>/<ViewFeature>`.
   - Each feature owns its table, drawer/form, translation subcomponents, and hooks.
   - Shared layout/sidebar/navigation/UI primitives live under `resources/js/components`, `resources/js/layouts`, and `resources/js/hooks`.

4. Backend HTTP layer
   - Laravel controllers receive validated requests, call service interfaces, and return Inertia pages or JSON.
   - Dashboard routes are grouped by domain feature and protected by `auth:admin`, `verified`, roles, and permissions.
   - Public web API routes are separate and use locale middleware.

5. Backend application/service layer
   - Define service interfaces in `app/Interfaces`.
   - Implement services in `app/Services` with `*ServiceImp` naming.
   - Services call repositories; controllers should not contain persistence details.

6. Backend persistence layer
   - Repositories in `app/Repositories` encapsulate Eloquent queries, transactions, media cleanup, and model persistence.
   - Use Eloquent relationships and transactions for multi-table writes.
   - Use `Log` in repositories when persistence operations fail, then rethrow.

This is not strict Clean Architecture. It is a layered Laravel architecture with repository/service separation plus feature-based React organization.

## Naming and Folder Conventions

### General

- Feature folders use domain nouns: `services`, `events`, `equipment`, `contact`, etc. Replace these with new domain entities.
- React components use PascalCase filenames and PascalCase component names.
- Route folders use URL-shaped lowercase/kebab-case names.
- Hooks use `useFeatureName` naming.
- Utility functions use verb-object names such as `getServices`, `buildSeoMetadata`, `resolveMediaUrl`.
- Type names use PascalCase.
- API payload normalization helpers use `normalize<Entity>` and `parse<Entity>Payload` patterns.

### Public Web

Use:

```text
apps/web/src/
  app/
    [locale]/
      layout.tsx
      globals.css
      global-error.jsx
      global-not-found.tsx
      (<feature>)/<route>/page.tsx
      (<feature>)/<route>/[slug]/page.tsx
    robots.ts
    sitemap.ts
  components/
    ui/
    shared/
  features/
    <feature>/
      pages/
      Components/
      hooks/
      locals/
  i18n/
  lib/
    conf/
    data/
    Layout/
    Types/
    utils/
      services/
      seo/
      media/
      hooks/
```

Important public-web conventions:

- `app/[locale]/.../page.tsx` stays thin and delegates to `features/<feature>/pages/*`.
- `features/<feature>/pages/*` fetches server data and composes feature sections.
- `features/<feature>/Components/*` contains reusable sections, sliders, cards, and presentational components.
- Feature-local translation JSON goes under `features/<feature>/locals/*` when needed.
- Global messages live in `src/messages/<locale>.json`.
- Shared navigation/footer layout lives under `lib/Layout`.
- API client/server data functions live under `lib/utils/services`.
- SEO helpers live under `lib/utils/seo`.
- Media URL normalization lives under `lib/utils/media`.

### Dashboard

Use:

```text
apps/dashboard/
  app/
    Console/Commands/
    Events/
    Http/
      Controllers/
        Api/Web/
        Dashboard/
      Middleware/
      Requests/
        Auth/
        Dashboard/
        Settings/
    Interfaces/
    Jobs/
    Listeners/
    Mail/
    Models/
      Lang/
    Providers/
    Repositories/
    Services/
    Traits/
  bootstrap/app.php
  config/
  database/
    factories/
    migrations/
    seeders/
  resources/
    js/
      components/
      helpers/
      hooks/
      layouts/
      pages/
      types/
      app.tsx
      ssr.tsx
    views/
  routes/
    api.php
    web.php
    dashboard/
      auth.php
      dashboard.php
      settings.php
  tests/
    Feature/
    Unit/
```

Dashboard page feature pattern:

```text
resources/js/pages/<feature>/View<Feature>/
  index.ts
  View<Feature>.tsx
  hooks/use<Feature>Form.ts
  Components/
    <Feature>Datatable/
      index.ts
      <Feature>Datatable.tsx
    <Feature>Form/
      index.ts
      <Feature>Form.tsx
      Components/Translations/
        index.ts
        Translations.tsx
```

## Public Web Architecture Details

### Routing

- Use Next.js App Router.
- Put all public pages under `app/[locale]`.
- Use route groups such as `(home)`, `(services)`, `(events)`, `(contact)`, `(policy)` to group code without affecting URLs.
- Use `[slug]` routes for detail pages.
- Use `generateStaticParams` in the root locale layout to generate one route tree per locale.
- Use `next-intl` routing with locale prefix `as-needed`, default locale, and explicit locales.
- Use middleware/proxy to apply locale routing to all non-API, non-static asset paths.

### Layout Strategy

- Root locale layout is an async Server Component.
- It fetches shared global data: language, site settings, SEO, navigation content, footer content, and section visibility.
- It sets `<html lang dir>` using locale metadata.
- It applies a globally loaded font through `next/font` and CSS variables.
- It wraps the app in `NextIntlClientProvider`.
- It renders shared `NavBar`, main content, and `Footer`.
- It injects global JSON-LD into `<head>` when provided by the backend.

### Data Fetching

- Server-side page data uses small service functions under `lib/utils/services`.
- Service functions read base URLs and revalidation settings from `lib/conf/appConfig.ts`.
- Use `fetch` with:
  - `Accept-Language` header when locale-specific data is needed.
  - `next: { revalidate: appConfig.revalidate }` for ISR-style cache control.
- Normalize API payloads defensively because the backend may return arrays, paginated `{ data, meta }`, singular `{ data }`, named arrays, or direct objects.
- Return empty arrays or `null` on missing config or failed fetch rather than crashing marketing pages.
- Detail fetchers should try direct slug endpoint first, then locale fallback, then list lookup fallback.
- Use `Promise.all` for independent fetches such as sitemap dynamic entries.

### State Management

- Prefer server data and props over global client state.
- Use local React state for client-only interactions.
- Use React Hook Form for forms.
- Avoid introducing a global state manager unless product requirements demand it.

### Client Components

Use `'use client'` only for:

- Forms.
- Carousels/sliders.
- Animation components needing browser APIs.
- reCAPTCHA or third-party browser scripts.
- Navigation menus that need client interaction.

Keep server/client boundaries clean. Do not pass large duplicated data into client components. Keep interactive components small and focused.

### SEO

Implement robust SEO as architecture, not an afterthought:

- `generateMetadata` per page.
- Shared `buildSeoMetadata` helper.
- Item-level `buildItemSeoMetadata` helper for detail pages.
- General-page SEO fallback when page-specific SEO is inactive/missing.
- Canonical URL generation with locale prefix handling.
- robots parsing from CMS-controlled strings.
- Open Graph and Twitter card fields.
- JSON-LD component that safely injects structured data.
- `robots.ts` and dynamic `sitemap.ts`.
- Sitemap includes static route entries plus dynamic entity detail entries fetched from APIs.
- Use `dynamic = 'force-dynamic'` for sitemap if backend content changes outside Next builds.

### Styling and UI

- Tailwind CSS utility-first styling.
- Use semantic sections with responsive utility classes.
- Use project-specific design tokens as Tailwind color names in classes.
- Use `cn()` helper with `clsx` + `tailwind-merge` for conditional class composition.
- Use shadcn/Radix-style UI primitives for Button, Input, Select, Textarea, Carousel, etc.
- Use accessibility-friendly labels, `htmlFor`, `aria-label`, alt text, visible focus rings, semantic headings, and keyboard-friendly primitives.
- Use responsive layouts with `container`, `mx-auto`, `px-4`, `grid`, `flex`, and breakpoint prefixes.
- Keep animation wrappers reusable, e.g. `AnimatedSection` around sections.

## Dashboard Architecture Details

### Laravel Routing

- Root `web.php` redirects `/` to dashboard home.
- Dashboard routes are under `/dashboard` and protected by `auth:admin` and `verified`.
- Group routes by controller, prefix, and route name.
- Protect each action with granular permissions such as:
  - `view <entities>`
  - `create <entities>`
  - `update <entities>`
  - `delete <entities>`
  - `upload files`
- Use admin-only role middleware for admin/role management.
- Public API routes live in `routes/api.php` and are not part of the dashboard route group.
- Public API routes use `SetApiLocale` middleware and `Accept-Language` to return translated content.

### Backend Controllers

- Inject service interfaces through constructor property promotion.
- Dashboard `index` actions return Inertia pages with paginated data.
- Create/update/delete actions return JSON responses with a message and changed entity.
- Wrap mutations in `try/catch`.
- Return generic support-oriented messages on server errors.
- Use Symfony HTTP status constants.
- Avoid persistence logic in controllers.

### Services and Repositories

- Each domain entity gets:
  - `App\Interfaces\<Entity>Service`
  - `App\Services\<PluralEntity>ServiceImp`
  - `App\Repositories\<PluralEntity>Repository`
  - Dashboard controller.
  - Public API controller/service/repository if public-facing.
- Bind every service interface to its implementation in `AppServiceProvider::boot()`.
- Services are thin orchestration wrappers over repositories.
- Repositories own transactions, Eloquent queries, slug generation, relationship writes, file cleanup, and logging.
- Use `DB::transaction` for writes spanning base entity, translations, media albums, notifications, or related tables.
- Use `firstOrFail`/`findOrFail` for required records.
- Use `orderByDesc('id')` for default listing order.
- Use pagination for dashboard lists.

### Database Design

Use a CMS-friendly relational model:

- Base entity table contains language-independent fields such as id, image/thumbnail, optional link, soft deletes, timestamps.
- Translation table per translatable entity: `<entities>_lang`.
- Translation records contain entity foreign key, `lang`, `slug` where relevant, title/name/subtitle/description fields, timestamps.
- Album/media table per media-rich entity: `<entities>_album` with entity foreign key and `path`.
- Separate tables for settings/data, contacts/inquiries, reserves, SEO pages, notifications, admins, roles, permissions, cache, and jobs.
- Define relationships in Eloquent models:
  - `translations(): hasMany(...)`
  - `translation($locale = null)` with fallback locale.
  - `album(): hasMany(...)` where applicable.
- Use seeders to create default admin, settings, SEO pages, policy content, and sample/reference content.

### Authentication and Authorization

- Use separate `admin` guard with `admins` provider and Admin model.
- Use session authentication for dashboard.
- Include login, registration, email verification, password reset, password confirmation, profile update, and password update flows.
- Use Spatie permissions for role/permission checks.
- Share authenticated admin and permissions to Inertia through `HandleInertiaRequests`.
- Filter sidebar/navigation items client-side using shared permission names, while enforcing the same permissions server-side on routes.
- Cache permissions through Spatie defaults.

### Dashboard Frontend

- Use Inertia `createInertiaApp` with dynamic page resolution from `resources/js/pages/**/index.ts`.
- Each page exports through an `index.ts` file.
- Use `Head` for per-page titles.
- Use `AppLayout` with breadcrumb arrays.
- Use `AppSidebar` with permission-filtered nav items.
- Use typed shared props from `resources/js/types/index.d.ts`.
- Use route helpers from generated route modules instead of hard-coded dashboard URLs where possible.
- Data tables use TanStack React Table, manual server pagination, search input with debounce, and Inertia router reloads preserving state/scroll.
- Entity forms open in right-side drawers using Vaul/Radix drawer primitives.
- Forms support create, update, and view modes through `mode: 'update' | 'view'` on selected row objects.
- Form hooks encapsulate Zod schema, React Hook Form setup, server error mapping, and submit helpers.
- Multilingual fields are edited through a Translations subcomponent/list.
- Use toasts for success/error feedback.
- Use axios for JSON mutations from the Inertia frontend.

## Validation Strategy

- Backend validation uses Laravel FormRequest classes under `app/Http/Requests`.
- Use one Store and one Update request per entity.
- Validate nested translation arrays using `translations.*.<field>` rules.
- Use `Rule::unique` for translated unique fields like titles/slugs.
- Provide custom messages for user-facing validation errors.
- Frontend dashboard validation uses Zod + React Hook Form for immediate feedback.
- Public forms can use React Hook Form rules directly when simple.
- Server validation remains authoritative.
- Map server validation errors back into form fields.

## Error Handling and Logging

- Public Next fetchers fail soft: return empty collections or null and avoid breaking public pages when APIs are unavailable.
- Client-side hooks store `isSubmitting`, `isSuccess`, `error`, and loading flags.
- Axios errors are recognized with `axios.isAxiosError` and user-facing messages are extracted from response payloads.
- Dashboard mutation controllers catch exceptions and return generic JSON error messages with HTTP 500.
- Repositories log exception messages with Laravel `Log::error` before rethrowing.
- Dashboard forms show validation/server messages through field errors and toast notifications.
- Avoid leaking stack traces or internal exception details to public clients.

## Configuration Management

- Use environment variables for app URL, API URL, storage URL, environment, revalidate interval, database settings, Docker ports/volumes, and third-party keys.
- Public Next config should centralize environment access in `lib/conf/appConfig.ts`.
- Use `NEXT_PUBLIC_*` only for values safe to expose to the browser.
- Use Laravel `.env` for backend secrets/config.
- Docker Compose should be driven by environment variables.
- Next config should support standalone output and monorepo file tracing root.

## Security Practices

- Dashboard routes protected by authentication, email verification, role middleware, and permission middleware.
- Public mutation forms include reCAPTCHA v3 or equivalent anti-abuse mechanism.
- Server validates all incoming data through FormRequest classes.
- Use generic error responses for server failures.
- Do not expose secrets through public env variables.
- Use session auth for dashboard and Laravel CSRF protection for web/Inertia routes.
- Use permission-filtered UI only as convenience; enforce authorization on server routes.
- Use sanitized JSON-LD rendering component; never inject arbitrary script strings without controlled serialization.
- Use file upload constraints and cleanup temp files after successful persistence.

## Performance Practices

- Server Components by default in public app.
- Use ISR/revalidation for public API fetches.
- Parallelize independent requests with `Promise.all`.
- Use dynamic imports only for heavy client-only features if needed.
- Keep client bundles small by isolating client interactivity.
- Use `next/font` with `display: 'swap'`.
- Use responsive images/media and media URL normalization.
- Use debounced search in dashboard lists.
- Paginate dashboard listings.
- Use permission cache through Spatie.
- Use Tailwind utilities and reusable primitives instead of large bespoke CSS.

## Testing and Quality Gates

Include tests and scripts consistent with the stack:

- Laravel PHPUnit feature tests for auth, settings, dashboard access, and critical domain flows.
- Laravel unit tests for isolated logic where useful.
- TypeScript `tsc --noEmit` checks for both apps.
- ESLint per workspace.
- Prettier formatting with organize-imports and Tailwind class sorting.
- Production builds for Next and Vite.
- Use root Turborepo commands to orchestrate checks.

Before declaring completion, run the strongest available checks:

```text
pnpm run format
pnpm run lint
pnpm run check-types
pnpm run build
```

If backend dependencies are installed, also run:

```text
cd apps/dashboard && php artisan test
```

## Feature Implementation Template

For every new domain entity requested by the user, create a vertical slice across both apps when relevant:

1. Database
   - Base entity migration.
   - Translation table migration if multilingual.
   - Album/media table if media-rich.
   - Relationships and soft deletes.
   - Seeder with representative data.

2. Backend
   - Model and Lang model.
   - FormRequests for store/update.
   - Repository with listing, slug lookup, create, update, delete.
   - Service interface and implementation.
   - Dashboard controller.
   - Public API controller/service/repository if public.
   - Routes with permissions.
   - Service binding in provider.

3. Dashboard frontend
   - Inertia page under `resources/js/pages/<feature>/View<Feature>`.
   - Data table with search, pagination, view/edit/delete actions.
   - Drawer form with create/update/view mode.
   - Translation editor for multilingual fields.
   - Hook with Zod schema and server error mapping.
   - Sidebar nav item guarded by permission.
   - TypeScript types.

4. Public web frontend
   - Route group under `app/[locale]/(<feature>)`.
   - Thin route wrapper.
   - Feature page under `features/<feature>/pages`.
   - Presentational components under `features/<feature>/Components`.
   - Service fetcher under `lib/utils/services` with payload normalization.
   - Metadata generation using shared SEO helpers.
   - Locale messages.

## Final Agent Instructions

When implementing from this prompt:

- Do not copy the original business logic or brand-specific content.
- Preserve architecture, conventions, and engineering practices.
- Replace all domain names, route names, permissions, models, and UI copy with the new business domain.
- Keep pages thin and features modular.
- Keep backend controllers thin, services/repositories explicit, and validation centralized.
- Build both public site and dashboard unless the user explicitly scopes one out.
- Verify with real commands and report exact changed files and command results.
