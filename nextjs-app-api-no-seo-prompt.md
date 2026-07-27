# Next.js Application Engineering Prompt With API Integration But Without SEO

Use this prompt with a coding agent to create any type of Next.js application that follows the same engineering discipline, folder organization, feature-based architecture, API integration style, component structure, styling conventions, TypeScript standards, and maintainability practices as this repository's public Next.js application, but without SEO integration.

The generated app may be a SaaS platform, dashboard, admin interface, e-commerce frontend, CMS frontend, internal tool, AI application, enterprise portal, marketing site, portfolio, marketplace, or product application. The goal is to reproduce the engineering approach and API integration patterns, not the original business logic, branding, or SEO system.

## User Inputs Required

Ask the user for:

1. Project name
2. Business domain
3. Desired functionality and core entities
4. Locales/languages required, if any
5. API base URL or backend integration requirements
6. Authentication requirements, if any
7. Whether API content should be fetched server-side, client-side, or both

Do not ask for SEO requirements. This prompt intentionally excludes SEO architecture.

## Core Stack

Use this stack unless the user requests alternatives:

- Next.js 16 with App Router
- React 19
- TypeScript
- Tailwind CSS v4
- next-intl for optional localization and locale-aware routing
- shadcn/Radix-style UI primitives
- native `fetch` for server-side API data fetching
- Axios for client-side mutations or browser-only API flows
- React Hook Form for forms
- Zod for complex client form validation when needed
- `clsx` + `tailwind-merge` for class composition
- `next/font` for fonts
- Optional: Motion/GSAP/Swiper only when animation or carousel behavior is required

Do not add sitemap generators, robots handlers, metadata builders, Open Graph helpers, Twitter metadata helpers, canonical URL helpers, JSON-LD helpers, schema.org helpers, SEO audit files, or SEO libraries unless the user explicitly overrides this prompt later.

## Target Architecture

Build a Next.js app with:

- App Router under `src/app`
- locale-aware routing when localization is required
- route-group organization by feature
- feature-based source structure under `src/features`
- server-first API data fetching for initial page content
- client-side API hooks only for interactive flows and mutations
- shared UI primitives under `src/components/ui`
- cross-feature shared components under `src/components/shared`
- layout components under `src/lib/Layout`
- central app/environment configuration under `src/lib/conf`
- API service modules under `src/lib/utils/services`
- API payload normalization and defensive parsing
- reusable utilities under `src/lib/utils`
- strict TypeScript types under `src/lib/Types` or colocated feature files
- Server Components by default
- small Client Components only where interactivity is required

The generated project should be modular, typed, accessible, responsive, and ready to connect to real backend APIs, while having no SEO layer.

## Folder Structure

Create this structure:

```text
src/
  app/
    [locale]/                 # include only if i18n is required
      layout.tsx
      globals.css
      global-error.jsx
      global-not-found.tsx
      (<feature>)/
        <route>/page.tsx
        <route>/[slug]/page.tsx
    layout.tsx                # use this instead of [locale]/layout.tsx if no i18n
    globals.css               # use this instead of [locale]/globals.css if no i18n
    global-error.jsx
    not-found.tsx
    favicon.ico
  components/
    ui/
      button.tsx
      input.tsx
      select.tsx
      textarea.tsx
      card.tsx
      dialog.tsx
      dropdown-menu.tsx
      tabs.tsx
      carousel.tsx
    shared/
      AnimatedSection/
        index.ts
        AnimatedSection.tsx
      EmptyState/
        index.ts
        EmptyState.tsx
      PageHeader/
        index.ts
        PageHeader.tsx
      ErrorState/
        index.ts
        ErrorState.tsx
  features/
    <feature>/
      pages/
        <FeaturePage>.tsx
        <FeatureDetailPage>.tsx
        ViewAll<Feature>.tsx
      Components/
        <SectionOrWidget>/
          index.ts
          <SectionOrWidget>.tsx
        <FeatureCard>/
          index.ts
          <FeatureCard>.tsx
        <FeatureForm>/
          index.ts
          <FeatureForm>.tsx
      hooks/
        use<Feature>.ts
      locals/
        en-US.json
        ar-EG.json
      types.ts
  i18n/
    routing.ts
    navigation.ts
    request.ts
    languageNames.ts
  lib/
    conf/
      appConfig.ts
      axiosConfig.ts
    data/
      navigation.ts
      siteSettings.ts
    Layout/
      NavBar/
        index.ts
        NavBar.tsx
        Components/
          DesktopNavBar/
          MobileNavBar/
      Footer/
        index.ts
        Footer.tsx
    Types/
      api.ts
      navItems.ts
      navLinks.ts
      types.ts
    utils/
      services/
        get<Entity>.ts
      media/
        resolveMediaUrl.ts
      hooks/
      formatting/
      validation/
    utils.ts
  messages/
    en-US.json
    ar-EG.json
  global.d.ts
```

If the app does not need internationalization, omit `[locale]`, `i18n`, `messages`, and `locals`, and place routes directly under `src/app`.

Do not create these files unless the user explicitly asks for SEO later:

```text
src/app/robots.ts
src/app/sitemap.ts
src/components/shared/JsonLd.tsx
src/lib/utils/seo/*
```

## App Router Strategy

- Use Next.js App Router.
- Keep route files thin.
- Route files should delegate to feature page components.
- Use route groups such as `(home)`, `(products)`, `(dashboard)`, `(settings)`, `(catalog)`, `(profile)`, `(marketing)`, or domain-specific groups.
- Use `[slug]` or `[id]` dynamic routes for detail pages.
- Route files may resolve params and pass them to feature pages.
- Do not put large UI composition directly in route files.
- Do not export `generateMetadata` unless the user later asks for SEO support.
- Do not add sitemap, robots, canonical, Open Graph, Twitter card, or structured data behavior.

Example route pattern:

```text
app/(products)/products/page.tsx
  -> export { default } from '@/features/products/pages/ProductsPage'

app/(products)/products/[slug]/page.tsx
  -> await params
  -> render <ProductDetailPage slug={slug} />
```

If i18n is enabled:

```text
app/[locale]/(products)/products/page.tsx
  -> export { default } from '@/features/products/pages/ProductsPage'
```

## Layout Strategy

The root layout should:

- Import global CSS.
- Load fonts using `next/font` with `display: 'swap'` and CSS variables.
- Set document language and direction when i18n is enabled.
- Fetch only global non-SEO API data needed by every page, such as settings, navigation, authenticated user shell data, or footer content.
- Keep global API requests small and parallel when independent.
- Render shared `NavBar`, main content area, and `Footer` when appropriate.
- Use `flex min-h-screen flex-col` body layout so footer stays below content.
- Wrap children in `NextIntlClientProvider` only when i18n is enabled.

Do not fetch SEO data in the layout. Do not inject JSON-LD or metadata helpers.

## Server vs Client Component Rules

Default to Server Components.

Use Client Components only for:

- Forms and client-side mutations
- Tabs, dialogs, drawers, popovers, dropdowns, mobile navigation
- Carousels/sliders
- Animation wrappers requiring browser APIs
- Browser-only APIs such as localStorage, matchMedia, clipboard, drag/drop, file previews
- Client-side API calls that must happen after user interaction
- Optimistic UI flows

Keep Client Components small and focused. Do not mark entire pages as `'use client'` unless the whole screen is an interactive tool and cannot be split.

## Feature Architecture

Each feature owns its page composition, components, hooks, and types:

```text
features/<feature>/
  pages/
    <FeaturePage>.tsx
    <FeatureDetailPage>.tsx
    ViewAll<Feature>.tsx
  Components/
    <FeatureHero>/
    <FeatureCard>/
    <FeatureGrid>/
    <FeatureForm>/
    <FeatureFilters>/
  hooks/
    use<Feature>.ts
  types.ts
  locals/
    <locale>.json
```

Rules:

- Feature pages fetch server-side data through `lib/utils/services` modules and compose sections.
- Feature components render UI and receive typed normalized props.
- Feature hooks handle client-side mutations, dropdown option fetching, local interaction state, and browser-only workflows.
- Feature-specific types should be colocated in `features/<feature>/types.ts` when not shared.
- Cross-feature API response or domain types belong in `lib/Types/api.ts` or `lib/Types/types.ts`.
- Shared UI belongs in `components/shared` or `components/ui`, not feature folders.

## API Integration Strategy

Use two API integration styles:

1. Server-side data fetching
   - For page content, detail records, layout data, lists, dashboards, initial filters, and anything needed for first paint.
   - Use native `fetch` in service modules under `lib/utils/services`.
   - Use `next: { revalidate: appConfig.revalidate }` only when backend content can be cached.
   - Use `cache: 'no-store'` for authenticated or rapidly changing private data.

2. Client-side API calls
   - For forms, mutations, user-triggered actions, file uploads, optimistic flows, dependent selects, and browser-only APIs.
   - Use Axios configured in `lib/conf/axiosConfig.ts`.
   - Encapsulate client API state in feature hooks.

Do not call APIs ad hoc inside random components. Centralize API URL construction and response normalization.

## Server Data Service Pattern

Create one service module per backend resource under `lib/utils/services`.

Each service module should:

- Read API base URL, storage URL, app URL, environment, and revalidation setting from `appConfig`.
- Define TypeScript query, response, record, and pagination types.
- Include a `normalize<Entity>` helper that converts backend records into stable frontend types.
- Include a `parse<Entity>Payload` helper that handles varied response shapes:
  - raw arrays
  - `{ data: [] }`
  - `{ data: {} }`
  - `{ data, meta }`
  - `{ data, pagination }`
  - `{ <entities>: [] }`
  - direct single objects
- Export a list function such as `getEntities(query, locale)`.
- Export a detail function such as `getEntityBySlug(slug, locale)` or `getEntityById(id, locale)` when relevant.
- Use `new URL()` and `url.searchParams.set()` for query parameters.
- Strip trailing slashes from base URLs before endpoint concatenation.
- Encode path params with `encodeURIComponent`.
- Set `Accept-Language` header when locale exists.
- Return empty arrays, empty pagination, or `null` when config is missing, response is not OK, or parsing fails for non-critical public content.
- Throw or return structured errors only for authenticated/private app flows where failure must be visible.

Example service responsibilities:

```text
lib/utils/services/getProducts.ts
  -> type ProductsQuery
  -> type ProductsPagination
  -> type ProductsResponse
  -> normalizeProduct(value)
  -> parseProductsPayload(payload)
  -> getProducts(query, locale)
  -> getProductBySlug(slug, locale)
```

## Client API Hook Pattern

For interactive client flows, create feature hooks such as `useContact`, `useCheckout`, `useProfileForm`, `useProductActions`, or `useReservation`.

Hook shape:

- local state object with:
  - `isSubmitting`
  - `isSuccess`
  - `error`
  - optional loading flags such as `isLoadingOptions`
  - optional fetched option arrays
- `submit<Entity>(payload)` mutation function
- `fetch<Options>()` or `load<Dependencies>()` function if needed
- Axios error recognition with `axios.isAxiosError`
- response message extraction from `error.response?.data?.message`
- fallback to `error.message` or `Unknown error`
- reset success/error state before submit
- return boolean success for forms that need reset behavior

Rules:

- Keep API hooks feature-local.
- Do not expose raw Axios responses to components unless needed.
- Keep payload types explicit.
- Trim and normalize form values before calling hooks.
- Keep server validation authoritative.

## Configuration Management

Create `src/lib/conf/appConfig.ts`:

```text
type AppEnv = 'development' | 'staging' | 'production';

type AppConfig = {
  appUrl: string;
  apiUrl: string;
  storageUrl: string;
  env: AppEnv;
  revalidate: number;
};
```

Rules:

- Read from environment variables.
- Parse numeric values defensively.
- Default revalidate to `0` when missing or invalid.
- Keep public-safe variables under `NEXT_PUBLIC_*` only when browser code needs them.
- Never expose tokens or secrets through public env vars.
- If `apiUrl` is missing, server fetchers for public/non-critical data should return safe empty values.

Create `src/lib/conf/axiosConfig.ts`:

- Configure Axios base URL from `appConfig.apiUrl`.
- Add default headers only if safe.
- Add auth headers only through secure client/session mechanisms requested by the user.
- Do not hard-code secrets.

## Media URL Strategy

Create `src/lib/utils/media/resolveMediaUrl.ts`.

Rules:

- Accept empty, relative, absolute, or storage-relative paths.
- Return empty string or undefined for empty input.
- Preserve absolute `http://` and `https://` URLs.
- Prefix storage-relative paths with `appConfig.storageUrl`.
- Strip duplicate slashes safely.
- Use this helper for images, avatars, thumbnails, videos, downloadable files, and Open-in-new-tab links.

This media helper is for API media only. Do not connect it to SEO image generation.

## Localization And API Language

If localization is enabled:

- Use `next-intl`.
- Define locale routing in `i18n/routing.ts`.
- Use locale prefix `as-needed` unless requirements differ.
- Set `<html lang dir>`.
- Keep global messages in `messages/<locale>.json`.
- Keep feature-local messages under `features/<feature>/locals/<locale>.json` when useful.
- Use locale names/directions from `i18n/languageNames.ts`.
- Send `Accept-Language` headers to API fetches.
- Keep translation fields from backend normalized into stable frontend fields.

## State Management

Use minimal state:

- Server API data is source of truth for initial page render.
- Client hooks own mutation/loading/error state.
- Local React state owns UI controls such as drawers, tabs, popovers, selected rows, and active filters.
- React Hook Form owns form fields.
- URL search params own shareable filters, sorting, search, and pagination.
- Do not add Redux/Zustand/Jotai unless cross-page state is genuinely required.
- If the API is private and highly interactive, consider SWR/React Query only when the user explicitly asks for client cache behavior.

## Authentication Flow

If the user asks for authentication:

- Create feature-scoped auth screens under `features/auth`.
- Create protected route groups under `app/(protected)` or `app/[locale]/(protected)`.
- Use middleware or server checks appropriate to the auth method.
- Use server-side session checks for protected Server Components where possible.
- Use Axios/fetch auth headers only through secure session/provider APIs.
- Share only safe user/session data to client components.
- Enforce protected route boundaries server-side.
- Use client hooks for login/register/logout/profile update if those are API mutations.

If the user does not ask for authentication, do not add auth.

## Form Architecture

For forms:

- Mark form components with `'use client'`.
- Use React Hook Form.
- Use Zod when validation is non-trivial.
- Use typed form values and typed API payloads.
- Use semantic labels with `htmlFor`.
- Use accessible field errors.
- Trim text before submission.
- Convert optional empty strings to `null` when API expects nullable values.
- Keep submit logic in feature hooks.
- Show success and error states.
- Reset forms after successful submit when appropriate.
- Map server validation errors into fields when API returns structured errors.

For public forms requiring anti-abuse protection, add reCAPTCHA/hCaptcha only if the user explicitly asks or backend contract requires it.

## Tables, Lists, Filters, And Pagination

For API-backed lists:

- Define typed query objects.
- Use server fetchers for initial list data.
- Use URL search params for page, search, filters, sort, and tab state.
- Use backend pagination metadata normalized into stable frontend pagination type.
- Debounce client search before changing URL params.
- Render accessible empty states.
- Render loading states for client transitions where needed.
- Keep filters feature-owned.
- Do not duplicate backend pagination state in global stores.

For private dashboards/admin-style screens:

- Consider server-rendered initial data plus client-triggered refresh after mutations.
- Use selected row state for view/edit drawers.
- Use confirmation popovers/dialogs for destructive actions.
- Keep delete/update/create calls in feature hooks or explicit client action handlers.

## UI Component Architecture

- Put generic primitives in `components/ui`.
- Put reusable composed components in `components/shared`.
- Put feature-specific sections, cards, grids, forms, filters, and widgets in `features/<feature>/Components`.
- Use Radix/shadcn patterns for accessible primitives.
- Components should accept typed normalized props.
- Components should not call APIs directly unless they are tiny client components whose only job is a user-triggered mutation.
- Use `index.ts` files for feature component exports.
- Keep animation logic reusable through `AnimatedSection` or similar wrappers.

## Styling Conventions

- Tailwind CSS utility-first styling.
- Use `cn(...inputs)` helper from `src/lib/utils.ts`.
- Use design-token color names in classes.
- Avoid random one-off hex values inside components.
- Use semantic sections and clear heading hierarchy.
- Use `container mx-auto px-4` for standard page sections.
- Use responsive breakpoints consistently: `md`, `lg`, `xl`.
- Use focus-visible ring styles for controls.
- Use rounded cards, borders, shadows, and spacing utilities consistently.
- Use global CSS only for tokens, base styles, fonts, CSS variables, and truly global behavior.
- Keep component styling colocated in class names.

## Accessibility Practices

- Use semantic HTML landmarks and sections.
- Preserve heading hierarchy.
- Provide meaningful alt text for API images.
- Use empty alt text for decorative images.
- Associate labels with inputs.
- Use accessible primitives for dialogs, selects, popovers, menus, tabs, and carousels.
- Add `aria-label` to icon-only buttons.
- Preserve keyboard navigation.
- Use visible focus states.
- Respect RTL direction when localization includes RTL languages.
- Do not rely on color alone for validation or status.
- Announce submit errors and successes where appropriate.

## Responsive Design Patterns

- Design mobile-first.
- Use stacked mobile layouts and multi-column desktop layouts.
- Use responsive typography sizes.
- Keep navigation split into desktop and mobile components.
- Use drawers/sheets for mobile menus when needed.
- Use scroll-safe containers for tables and wide content.
- Test empty, loading, error, short-content, and long-content states.

## Error Handling Strategy

- Public non-critical API fetchers should fail soft with empty arrays or null.
- Critical authenticated/private fetchers should show visible error states.
- Client hooks should expose error strings.
- Use `axios.isAxiosError` for Axios errors.
- Extract backend messages from structured response payloads.
- Do not expose stack traces or secrets.
- Add shared `ErrorState` component for recoverable UI errors.
- Use not-found behavior for missing detail records.

## TypeScript Conventions

- Use explicit types for props, API records, query objects, response objects, pagination, form values, nav items, and config objects.
- Use PascalCase for components and types.
- Use camelCase for variables and functions.
- Use `type` aliases for payloads and component props unless interface extension is useful.
- Avoid `any` except inside isolated API normalizer/parser boundaries.
- Return typed values from normalizers.
- Use `unknown` at boundaries and narrow it.
- Use `as const` for static config arrays and literal option sets.
- Keep route prop types explicit.
- Colocate feature-specific types in `features/<feature>/types.ts`.
- Put cross-feature shared API types in `lib/Types/api.ts`.

## Import Conventions

- Use `@/` alias for source imports.
- Group imports as framework/external, internal aliases, then relative imports.
- Let Prettier organize imports.
- Prefer direct imports over large barrels when importing heavy libraries.
- Avoid circular imports between features.
- Avoid importing feature internals from other features; promote truly shared pieces to `components/shared`, `lib/Types`, or `lib/utils`.

## Error Boundaries, Not Found, And Loading States

- Provide global error and not-found files under the active app route structure.
- Add route-level `loading.tsx` for API-backed pages with noticeable latency.
- Use Suspense around independent slow sections when useful.
- For detail pages, render not-found when API returns 404 or normalized detail is null.
- Client hooks should expose loading/error flags for mutations and browser-triggered requests.
- Use skeletons or accessible loading states for tables, cards, and detail panels.

## Performance Rules

- Server Components by default.
- Avoid clientifying full pages.
- Keep client islands small.
- Use server fetchers for first paint.
- Use `Promise.all` for independent API calls.
- Use revalidation for public cacheable data.
- Use `no-store` for authenticated/private data.
- Use `next/font` with `display: 'swap'`.
- Use dynamic imports for heavy client-only widgets.
- Avoid duplicate serialization of large data into client components.
- Debounce search and filter requests.
- Keep icon imports direct when possible.
- Use responsive images/media and normalized media URLs.

## Testing Approach

If adding tests:

- Test API payload normalizers for varied backend response shapes.
- Test media URL resolver behavior.
- Test form validation schemas.
- Test API hooks for success/error state transitions with mocked Axios.
- Test list filtering/search URL behavior.
- Test interactive components such as dialogs, tabs, mobile nav, and forms.
- Test empty, error, loading, and not-found states.
- Run typecheck, lint, format, and production build before completion.

## Implementation Checklist

For each requested feature:

1. Create route group and thin route file under `app`.
2. Create feature page under `features/<feature>/pages`.
3. Create feature components under `features/<feature>/Components`.
4. Create server API service under `lib/utils/services`.
5. Create API record/query/response/pagination types.
6. Create normalizer and parser helpers.
7. Create client mutation hook if needed.
8. Create form components and validation if needed.
9. Add media resolver usage for images/files.
10. Add locale messages if i18n is enabled.
11. Add navigation/footer entries if relevant.
12. Add loading/error/not-found states.
13. Verify format, lint, typecheck, and build.

## Strict Exclusions

Do not implement these unless the user explicitly asks later:

- SEO metadata builders
- `generateMetadata`
- sitemap generation
- robots.txt generation
- Open Graph metadata
- Twitter card metadata
- canonical URL generation
- JSON-LD structured data
- schema.org helpers
- SEO audit files
- SEO libraries
- page-specific SEO CMS fields
- metadata fallback systems

## Final Agent Instructions

- Build a real API-integrated Next.js architecture, not a visual-only mock.
- Preserve the reference engineering approach: feature-based, typed, accessible, responsive, server-first, API-normalized, and modular.
- Include API integration through centralized service modules and client hooks.
- Exclude SEO architecture entirely.
- Keep route files thin.
- Keep Client Components small.
- Use shared UI primitives and feature-owned components.
- Verify with real commands and report results.
