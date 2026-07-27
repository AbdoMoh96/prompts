# Next.js Application Engineering Prompt Without API Or SEO Integration

Use this prompt with a coding agent to create any type of Next.js application that follows the same engineering discipline, feature organization, component architecture, styling approach, TypeScript conventions, and maintainability standards as this repository's public Next.js application, but without backend API integration and without SEO integration.

The generated app may be a SaaS UI, dashboard, admin panel, portfolio, marketing site, landing page, internal tool, AI app prototype, design system demo, CMS-like static interface, or any other client/server-rendered Next.js product. The goal is to reproduce the engineering approach and code organization, not the original business logic, brand, API layer, or SEO system.

## User Inputs Required

Ask the user for:

1. Project name
2. Business domain
3. Desired functionality and core screens
4. Locales/languages required, if any
5. Authentication requirements, if any
6. Whether content should be static, local JSON, generated mock data, or user-entered client state

Do not ask for API endpoints, CMS URLs, or SEO requirements. This prompt intentionally excludes those systems.

## Core Stack

Use this stack unless the user requests alternatives:

- Next.js 16 with App Router
- React 19
- TypeScript
- Tailwind CSS v4
- next-intl for optional localization and locale-aware routing
- shadcn/Radix-style UI primitives
- React Hook Form for forms
- Zod for complex form validation when needed
- `clsx` + `tailwind-merge` for class composition
- `next/font` for fonts
- Optional: Motion/GSAP/Swiper only when animation or carousel behavior is required

Do not add Axios, React Query, SWR, tRPC, GraphQL clients, REST clients, CMS SDKs, sitemap generators, robots handlers, metadata builders, Open Graph helpers, Twitter metadata helpers, JSON-LD helpers, or SEO libraries unless the user explicitly overrides this prompt.

## Target Architecture

Build a Next.js app with:

- App Router under `src/app`
- locale-aware route segment when localization is required
- route-group organization by feature
- feature-based source structure under `src/features`
- shared UI primitives under `src/components/ui`
- cross-feature shared components under `src/components/shared`
- app-wide layout components under `src/lib/Layout`
- central app configuration under `src/lib/conf`
- local/static data modules under `src/lib/data`
- reusable utilities under `src/lib/utils`
- strict TypeScript types under `src/lib/Types` or colocated feature files
- Server Components by default
- small Client Components only where interactivity is required

The generated project should be modular and scalable even without API or SEO layers.

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
  features/
    <feature>/
      pages/
        <FeaturePage>.tsx
        <FeatureDetailPage>.tsx
      Components/
        <SectionOrWidget>/
          index.ts
          <SectionOrWidget>.tsx
        <FeatureCard>/
          index.ts
          <FeatureCard>.tsx
      hooks/
        use<Feature>.ts
      data/
        <feature>.ts
      types.ts
      locals/
        en-US.json
        ar-EG.json
  i18n/
    routing.ts
    navigation.ts
    request.ts
    languageNames.ts
  lib/
    conf/
      appConfig.ts
    data/
      navigation.ts
      siteSettings.ts
      mockData.ts
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
      navItems.ts
      navLinks.ts
      types.ts
    utils/
      hooks/
      media/
        resolveLocalMediaPath.ts
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

Do not create these files unless the user explicitly asks for backend/API integration later:

```text
src/lib/conf/axiosConfig.ts
src/lib/utils/services/*
src/lib/api/*
src/server/*
```

## App Router Strategy

- Use Next.js App Router.
- Keep route files thin.
- Route files should delegate to feature page components.
- Use route groups such as `(home)`, `(dashboard)`, `(settings)`, `(catalog)`, `(profile)`, `(marketing)`, or product-specific feature groups.
- Use `[slug]` dynamic routes only for local/static detail data or purely frontend-generated routes.
- Do not fetch external APIs in route files.
- Do not export `generateMetadata` unless the user later asks for SEO support.
- Do not add sitemap, robots, canonical, Open Graph, Twitter card, or structured data behavior.

Example route pattern:

```text
app/(products)/products/page.tsx
  -> export { default } from '@/features/products/pages/ProductsPage'

app/(products)/products/[slug]/page.tsx
  -> resolve slug from params
  -> render <ProductDetailPage slug={slug} /> using local/static data
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
- Render a shared `NavBar`, main content area, and `Footer` when appropriate.
- Use `flex min-h-screen flex-col` body layout so footer stays below content.
- Use local site settings from `src/lib/data/siteSettings.ts` rather than API calls.
- Wrap children in `NextIntlClientProvider` only when i18n is enabled.

Do not fetch remote data in the layout. Use local config, static arrays, or locally imported content modules.

## Server vs Client Component Rules

Default to Server Components.

Use Client Components only for:

- Forms
- Tabs, dialogs, drawers, popovers, dropdowns, mobile navigation
- Carousels/sliders
- Animation wrappers requiring browser APIs
- Local interactive state
- Browser-only APIs such as localStorage, matchMedia, clipboard, drag/drop, file previews
- Client-side mock workflows or optimistic UI demos

Keep Client Components small and focused. Do not mark entire pages as `'use client'` unless the whole screen is an interactive tool and cannot be split.

## Feature Architecture

Each feature should own its page composition, components, local data, hooks, and types:

```text
features/<feature>/
  pages/
    <FeaturePage>.tsx
    <FeatureDetailPage>.tsx
  Components/
    <FeatureHero>/
    <FeatureCard>/
    <FeatureGrid>/
    <FeatureForm>/
  hooks/
    use<Feature>.ts
  data/
    <feature>.ts
  types.ts
  locals/
    <locale>.json
```

Rules:

- Feature pages compose sections and pass local/static data to components.
- Feature components render UI and receive typed props.
- Feature hooks handle local interaction state and form workflows.
- Feature data files contain static arrays, mock records, demo content, or generated seed data.
- Feature types should be colocated in `types.ts` when only used by that feature.
- Shared domain-agnostic UI belongs in `components/shared` or `components/ui`, not feature folders.

## Local Data Strategy

Because this prompt excludes API integration, use local data sources:

- Static TypeScript arrays/objects under `features/<feature>/data` or `lib/data`.
- Local JSON files when content is large or translation-heavy.
- Generated mock data functions for dashboards, charts, tables, or prototypes.
- Client-side local state for user-created temporary records.
- URL search params for shareable filters/pagination when useful.

Data rules:

- Define TypeScript types for every record shape.
- Keep mock/static data realistic but domain-generic.
- Never hard-code original repository business content or branding.
- For detail pages, find records by `slug` from local arrays.
- If a record is not found, render the app's not-found behavior.
- Keep local data modules pure and side-effect free.

Example local detail flow:

```text
features/products/data/products.ts
  -> export const products = [...]
  -> export function getProductBySlug(slug: string) { ... }

features/products/pages/ProductDetailPage.tsx
  -> calls getProductBySlug(slug)
  -> renders notFound() if missing
```

## State Management

Use minimal state:

- Server-render local data by importing static modules.
- Use local React state for UI interactions.
- Use React Hook Form for form state.
- Use Zod for complex schemas.
- Use URL search params for filters, sorting, tabs, and pagination when the state should be shareable.
- Use localStorage only for explicitly persistent client preferences such as theme, sidebar state, recently viewed items, or drafts.
- Do not add global state managers unless the product genuinely needs cross-page state.

If a global store is required, keep it small and explicit:

- UI preferences
- authenticated session display data
- current workspace/project selection
- temporary offline/demo entities

Do not use global state as a substitute for feature ownership.

## Authentication Flow

If the user asks for authentication:

- Create feature-scoped auth screens under `features/auth`.
- Create protected route groups under `app/(protected)` or `app/[locale]/(protected)`.
- Use middleware or server checks appropriate to the selected auth method.
- Keep session/user display data typed.
- Enforce protected route boundaries server-side when possible.
- If no real auth provider is configured, create clearly labeled mock auth for prototyping only.

If the user does not ask for authentication, do not add auth.

## Form Architecture

For forms:

- Mark the form component with `'use client'`.
- Use React Hook Form.
- Use Zod when validation is non-trivial.
- Use typed form values.
- Use semantic labels with `htmlFor`.
- Use accessible errors tied to fields.
- Trim text before local submission.
- Store submitted data locally only if the product requires demo behavior.
- Show success and error states.
- Reset form on successful submission when appropriate.
- Keep submit logic in a feature hook when the form is complex.

Do not submit to external APIs. If the UI requires a submit action, simulate success locally and clearly keep code structured so real integration can be added later.

## Tables, Lists, and Filters

For data-heavy apps:

- Use typed local records.
- Use feature-owned table/list components.
- Use debounced local search for large local lists.
- Use memoized filtering/sorting when non-trivial.
- Use URL search params for shareable filters and pagination.
- Use accessible empty states.
- Use action buttons with `aria-label` for icon-only controls.
- Keep row actions scoped to local/demo behavior unless a backend is added later.

## UI Component Architecture

- Put generic primitives in `components/ui`.
- Put reusable composed components in `components/shared`.
- Put feature-specific sections, cards, grids, forms, and widgets in `features/<feature>/Components`.
- Use Radix/shadcn patterns for accessible primitives.
- Components should accept typed props.
- Components should not import unrelated feature data.
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
- Provide meaningful alt text for content images.
- Use empty alt text for decorative images.
- Associate labels with inputs.
- Use accessible primitives for dialogs, selects, popovers, menus, tabs, and carousels.
- Add `aria-label` to icon-only buttons.
- Preserve keyboard navigation.
- Use visible focus states.
- Respect RTL direction when localization includes RTL languages.
- Do not rely on color alone for validation or status.

## Responsive Design Patterns

- Design mobile-first.
- Use stacked mobile layouts and multi-column desktop layouts.
- Use responsive typography sizes.
- Keep navigation split into desktop and mobile components.
- Use drawers/sheets for mobile menus when needed.
- Use scroll-safe containers for tables and wide content.
- Test empty, loading, short-content, and long-content states.

## TypeScript Conventions

- Use explicit types for props, local records, form values, nav items, and config objects.
- Use PascalCase for components and types.
- Use camelCase for variables and functions.
- Use `type` aliases for payloads and component props unless interface extension is useful.
- Avoid `any`.
- Use `unknown` at boundaries and narrow it.
- Use `as const` for static config arrays and literal option sets.
- Keep route prop types explicit.
- Colocate feature-specific types in `features/<feature>/types.ts`.
- Put cross-feature shared types in `lib/Types`.

## Import Conventions

- Use `@/` alias for source imports.
- Group imports as framework/external, internal aliases, then relative imports.
- Let Prettier organize imports.
- Prefer direct imports over large barrels when importing heavy libraries.
- Avoid circular imports between features.
- Avoid importing feature internals from other features; promote truly shared pieces to `components/shared`, `lib/data`, or `lib/utils`.

## Environment Configuration

Create `src/lib/conf/appConfig.ts` for non-secret public configuration only:

- `appName`
- `appUrl` if needed for links
- `env`
- feature flags
- UI defaults

Do not include API URLs, storage URLs, CMS URLs, tokens, or secret values. Those are intentionally out of scope.

## Error Boundaries, Not Found, and Loading States

- Provide global error and not-found files under the active app route structure.
- Add route-level `loading.tsx` only where local computation, dynamic imports, or UI transitions benefit from it.
- Use Suspense for lazy/heavy client components when useful.
- For local detail pages, render not-found when slug does not match local data.
- Client hooks should expose loading/error flags only for real local async behavior such as simulated submit, file preview, or deferred search.

## Performance Rules

- Server Components by default.
- Avoid clientifying full pages.
- Keep client islands small.
- Use static local imports for data.
- Use dynamic imports for heavy client-only widgets.
- Use `next/font` with `display: 'swap'`.
- Avoid huge local JSON bundles; split large mock data by feature.
- Use memoization only for expensive local filtering/sorting.
- Debounce local search when lists are large.
- Keep icon imports direct when possible.
- Use responsive images and avoid unbounded media sizes.

## Testing Approach

If adding tests:

- Test feature data helpers such as slug lookup and filters.
- Test form validation schemas.
- Test interactive components such as dialogs, tabs, mobile nav, and forms.
- Test empty states and not-found behavior.
- Run typecheck, lint, format, and production build before completion.

## Implementation Checklist

For each requested feature:

1. Create route group and thin route file under `app`.
2. Create feature page under `features/<feature>/pages`.
3. Create feature components under `features/<feature>/Components`.
4. Create local static/mock data under `features/<feature>/data` or `lib/data`.
5. Create feature types.
6. Add local hooks for interactive behavior.
7. Add form components and validation if required.
8. Add locale messages if i18n is enabled.
9. Add navigation/footer entries if relevant.
10. Add loading/error/not-found states if relevant.
11. Verify format, lint, typecheck, and build.

## Strict Exclusions

Do not implement these unless the user explicitly asks later:

- REST API integration
- GraphQL integration
- Axios config
- React Query/SWR data fetching
- CMS SDK integration
- Database integration
- Server actions that persist externally
- Sitemap generation
- robots.txt generation
- Metadata builders
- Open Graph/Twitter metadata
- canonical URLs
- JSON-LD structured data
- SEO audit files
- revalidation/ISR API fetch behavior

## Final Agent Instructions

- Build a real Next.js app architecture, not a visual-only mock.
- Preserve the reference engineering approach: feature-based, typed, accessible, responsive, server-first, and modular.
- Use local/static data instead of APIs.
- Exclude SEO architecture entirely.
- Keep route files thin.
- Keep Client Components small.
- Use shared UI primitives and feature-owned components.
- Verify with real commands and report results.
