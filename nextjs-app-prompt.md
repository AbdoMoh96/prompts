# Next.js Application Engineering Prompt

Use this prompt with a coding agent to create any type of Next.js application while following the exact engineering approach used by the public web application in this repository. The goal is to reproduce the engineering quality, architecture, organization, and conventions, not the original business logic or visual content.

This prompt works for SaaS platforms, dashboards, admin panels, e-commerce, CMS systems, internal tools, portfolios, marketing sites, AI applications, and enterprise software.

## User Inputs Required

Ask the user for:

1. Project name
2. Business domain
3. Desired functionality and core entities
4. Locales/languages required
5. Whether the app consumes an external API, internal API, CMS, or static data
6. Authentication requirements, if any

## Core Stack

Use this stack unless the user requests alternatives:

- Next.js 16 with App Router.
- React 19.
- TypeScript.
- Tailwind CSS v4.
- next-intl for i18n/routing/messages.
- shadcn/Radix-style UI primitives.
- `fetch` for server-side data access.
- Axios for client-side mutations if needed.
- React Hook Form for forms.
- `clsx` + `tailwind-merge` for class composition.
- Next Metadata API for SEO.
- `next/font` for fonts.
- Optional: Motion/GSAP/Swiper for animation/carousels when the product needs them.

## Folder Structure

Create this structure:

```text
src/
  app/
    [locale]/
      layout.tsx
      globals.css
      global-error.jsx
      global-not-found.tsx
      (<feature>)/
        <route>/page.tsx
        <route>/[slug]/page.tsx
    favicon.ico
    robots.ts
    sitemap.ts
  components/
    ui/
      button.tsx
      input.tsx
      select.tsx
      textarea.tsx
      carousel.tsx
    shared/
      AnimatedSection/
        index.ts
        AnimatedSection.tsx
      JsonLd.tsx
  features/
    <feature>/
      pages/
        <FeaturePage>.tsx
        <FeatureDetailPage>.tsx
      Components/
        <SectionOrWidget>/
          index.ts
          <SectionOrWidget>.tsx
      hooks/
        use<Feature>.ts
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
      axiosConfig.ts
    data/
    Layout/
      NavBar/
      Footer/
    Types/
      api.ts
      types.ts
      navItems.ts
      navLinks.ts
    utils/
      services/
        get<Entity>.ts
      seo/
        buildSeoMetadata.ts
        buildItemSeoMetadata.ts
      media/
        resolveMediaUrl.ts
      hooks/
        useLanguage.ts
    utils.ts
  messages/
    en-US.json
    ar-EG.json
  global.d.ts
```

Adjust feature names to the target product. Preserve the separation of `app`, `features`, `components`, `i18n`, and `lib`.

## App Router Strategy

- Put all routable pages under `src/app/[locale]`.
- Use route groups like `(home)`, `(products)`, `(dashboard)`, `(settings)`, `(marketing)` to organize route segments without affecting URLs.
- Keep route files thin.
- A route file should usually do only one of these:
  - re-export `default` and `generateMetadata` from a feature page, or
  - resolve route params and delegate to a feature detail component.
- Use `[slug]` for human-readable detail routes.
- In Next 16, type route `params` as a Promise when appropriate and `await params` inside page/metadata functions.
- Use `generateStaticParams` at locale layout level to return supported locales.
- Add `global-error.jsx` and `global-not-found.tsx` under `[locale]` for localized error/not-found handling.

Example route philosophy:

```text
app/[locale]/(<feature>)/<route>/page.tsx
  -> export { default, generateMetadata } from '@/features/<feature>/pages/<FeaturePage>'

app/[locale]/(<feature>)/<route>/[slug]/page.tsx
  -> fetch metadata for slug
  -> render <FeatureDetailPage slug={slug} />
```

## i18n Architecture

- Use `next-intl`.
- Define routing in `src/i18n/routing.ts` with explicit locales, default locale, and `localePrefix: 'as-needed'` unless requirements differ.
- Export a `sitePages` array from routing config for sitemap static entries.
- Use middleware/proxy to apply i18n routing to all non-API/static paths.
- Keep global messages in `src/messages/<locale>.json`.
- Keep feature-local messages in `features/<feature>/locals/<locale>.json` when feature copy is substantial.
- Provide language names/directions in `i18n/languageNames.ts` or equivalent.
- Use locale direction (`ltr`/`rtl`) to set `<html dir>`.
- Send `Accept-Language` headers to APIs for localized content.

## Root Layout Strategy

The root locale layout must be an async Server Component that:

- Imports global CSS.
- Loads fonts using `next/font` with `display: 'swap'` and CSS variable names.
- Calls `getLanguage()` or equivalent to determine `langKey`, current backend language, and text direction.
- Fetches global data needed by every page, such as settings, navigation items, footer data, SEO defaults, and feature visibility.
- Generates global metadata through `generateMetadata` and shared SEO helpers.
- Sets `<html lang={langKey} dir={direction}>`.
- Injects global JSON-LD if available.
- Wraps children in `NextIntlClientProvider`.
- Renders shared `NavBar`, `children`, and `Footer`.
- Keeps the body as a flex column with `min-h-screen` so footer stays below content.

## Server vs Client Component Rules

Default to Server Components.

Use Client Components only for:

- Forms and field interactions.
- Carousels/sliders.
- Animation wrappers requiring browser APIs.
- Browser-only scripts such as reCAPTCHA.
- Menus, drawers, tabs, or mobile navigation requiring state.
- Hooks using `useState`, `useEffect`, `useMemo`, `useCallback`, browser APIs, or event listeners.

Keep client components small. Pass only the data they need. Avoid moving whole pages to the client just because one child is interactive.

## Feature Architecture

Each feature owns its page composition and local components:

```text
features/<feature>/
  pages/
    <FeaturePage>.tsx
    View<Feature>.tsx
    ViewAll<Feature>.tsx
  Components/
    <HeroOrSection>/
      index.ts
      <HeroOrSection>.tsx
    <CardsOrSlider>/
      index.ts
      <CardsOrSlider>.tsx
  hooks/
    use<Feature>.ts
  locals/
    <locale>.json
```

Rules:

- Pages fetch server data and compose sections.
- Components render sections/cards/widgets and receive normalized props.
- Hooks handle client-side mutations or interaction state, not server page loading.
- Index files re-export components for clean imports.
- Avoid putting feature-specific business logic in global components.

## Data Fetching Pattern

Create one service module per backend resource under `lib/utils/services`.

Each service module should:

- Read API base URL and revalidation interval from `appConfig`.
- Define TypeScript query/response/pagination types.
- Include a `normalize<Entity>` helper that converts API records into stable frontend types.
- Include a `parse<Entity>Payload` helper that handles varied response shapes:
  - raw arrays
  - `{ data: [] }`
  - `{ data: {} }`
  - `{ data, meta }`
  - `{ data, pagination }`
  - `{ <entities>: [] }`
  - direct single objects
- Export a list function such as `getEntities(query, locale)`.
- Export a detail function such as `getEntityBySlug(slug, locale)` when relevant.
- Use `fetch` with `next: { revalidate: appConfig.revalidate }` on the server.
- Set `Accept-Language` when locale exists.
- Return empty arrays or null when config is missing, response is not OK, or parsing fails.
- Use direct slug endpoint first, then fallback to locale-less request, then list lookup if necessary.

For client-side mutations, create feature hooks using Axios and local state:

- `isSubmitting`
- `isSuccess`
- `error`
- optional loading flags for dropdown/options data
- `submit<Entity>()`
- `fetch<Options>()` if needed

## State Management

- Use server-rendered data as the source of truth.
- Use local state for UI interactions.
- Use React Hook Form for forms.
- Do not add Redux/Zustand/Jotai unless cross-page state is genuinely required.
- Use URL params for pagination, filtering, and shareable state.
- Use `useMemo` only for non-trivial derived data.
- Use `useCallback` for callbacks passed to hooks/effects or stable children.

## API Integration Conventions

- Centralize environment-derived config in `lib/conf/appConfig.ts`.
- Centralize Axios base config in `lib/conf/axiosConfig.ts`.
- Strip trailing slashes from base URLs before building endpoint URLs.
- Use `new URL()` for query params in server fetchers.
- Encode slugs/path params with `encodeURIComponent`.
- Use `Accept-Language` for localized API responses.
- Do not throw from non-critical marketing fetchers; fail soft.
- Do not expose server-only secrets in `NEXT_PUBLIC_*` variables.

## Authentication Flow

If the app needs authentication:

- Keep public pages accessible unless product says otherwise.
- Protect authenticated route groups through middleware/server checks.
- Keep auth session retrieval server-side when possible.
- Share only safe user/session data to client components.
- Keep login/register/reset flows feature-scoped under `features/auth`.
- Use server actions/API routes or external provider SDK according to chosen auth system.
- Enforce authorization on server boundaries, not only in client UI.

If the app is purely public/marketing, do not add auth just because this prompt mentions it.

## Middleware Usage

- Use middleware/proxy for locale negotiation and route prefix handling.
- Exclude API routes, `_next`, `_vercel`, and paths containing file extensions from locale middleware.
- Add auth checks in middleware only when route-level server checks are insufficient or when redirect UX requires it.

## SEO and Metadata Strategy

Implement SEO deeply:

1. Shared metadata builder
   - Accept global/general SEO and page-specific SEO.
   - Use page-specific values when active.
   - Fall back to global values.
   - Normalize empty strings to undefined.
   - Parse robots strings into `{ index, follow }`.
   - Resolve media URLs for OG/Twitter images.
   - Prefix canonical URLs with locale when needed.
   - Support page numbers in canonical URLs for paginated pages.

2. Detail metadata builder
   - Build title/description from normalized entity fields.
   - Use entity image for OG/Twitter image.
   - Include entity route type and slug.
   - Use locale-aware canonical URLs.

3. Page usage
   - Every routable page exports `generateMetadata` when SEO matters.
   - Detail pages fetch entity by slug in `generateMetadata` and return empty metadata if not found.

4. Structured data
   - Use a `JsonLd` shared component.
   - Accept JSON object or serialized JSON-LD from trusted backend/CMS.
   - Serialize safely before injection.

5. Sitemap and robots
   - `robots.ts` returns crawl rules and sitemap location.
   - `sitemap.ts` returns static entries plus dynamic detail entries fetched in parallel.
   - Filter out records without slugs.
   - Set change frequency and priority intentionally.

## Styling Conventions

- Tailwind utility-first classes.
- Reusable `cn(...inputs)` helper using `clsx` and `tailwind-merge`.
- Use design-token color names, not random hex values in components.
- Prefer semantic HTML sections with clear heading hierarchy.
- Use `container mx-auto px-4` layout pattern for page sections.
- Use responsive breakpoints consistently: `md`, `lg`, `xl`.
- Use focus-visible ring classes for form controls.
- Use rounded cards, borders, shadows, and spacing utilities consistently.
- Keep CSS modules/global CSS for global tokens and truly global behavior only.
- Keep component styling colocated in class names.

## UI Component Architecture

- Put generic primitives in `components/ui`.
- Put cross-feature composed components in `components/shared`.
- Put domain-specific components inside `features/<feature>/Components`.
- Use Radix/shadcn patterns for accessible primitives.
- Components should accept typed props and not fetch data unless they are page/server composition components.
- Use `index.ts` files to re-export feature components.
- For animations, wrap sections in shared animation components rather than duplicating animation logic.
- For carousels/sliders, isolate Swiper/client logic in a Client Component.

## Form Architecture

For public forms:

- Mark form component with `'use client'`.
- Use React Hook Form.
- Use translated validation messages.
- Use semantic labels with `htmlFor`.
- Trim text before submission.
- Store optional empty strings as `null` if API expects nullable values.
- Load reCAPTCHA/hCaptcha scripts only when configured.
- Clean up third-party scripts/badges on unmount when appropriate.
- Use a custom hook for submission state and API calls.
- Show field-level validation errors and general submit errors.
- Reset form on successful submission.

For complex app forms:

- Use schema validation (Zod or equivalent).
- Use controlled components via `Controller` when using custom Select/Rich Text components.
- Map server errors to fields.
- Keep submit payload construction explicit.

## Error Boundaries and Loading States

- Provide global error and not-found files under the locale segment.
- Add route-level `loading.tsx` where data latency is visible and streaming helps UX.
- Use Suspense boundaries around independent slow sections when beneficial.
- For non-critical public content, render empty/fallback sections rather than crashing.
- For detail pages, use framework not-found behavior when the primary record does not exist.
- Client hooks should expose loading and error flags.

## Performance Rules

- Server Components by default.
- Avoid clientifying whole pages.
- Use `Promise.all` for independent server fetches.
- Start fetches high in the tree when multiple children need them.
- Use `next/font` with `display: 'swap'`.
- Keep static imports analyzable.
- Use dynamic imports for heavy browser-only widgets.
- Avoid barrel imports from large icon/component libraries when direct imports are possible.
- Use ISR/revalidation for CMS/API content.
- Avoid duplicate serialization of large data into client props.
- Use debouncing for client search/filter requests.
- Optimize media URLs and avoid unbounded image sizes.

## Accessibility Practices

- Use semantic HTML landmarks and sections.
- Maintain heading hierarchy.
- Provide alt text for meaningful images and empty alt for decorative images.
- Associate labels with inputs.
- Use accessible Radix primitives for dialogs, selects, popovers, menus, and carousels where possible.
- Add `aria-label` to icon-only buttons.
- Preserve keyboard navigation.
- Use visible focus states.
- Respect `dir="rtl"` for RTL locales.
- Do not rely on color alone for validation/errors.

## TypeScript Conventions

- Use explicit exported types for API query/response/pagination data.
- Use PascalCase for types and components.
- Use `type` aliases for object payloads unless interfaces are more appropriate for extension.
- Avoid `any`; if consuming unpredictable API payloads, isolate `any` inside parser/normalizer functions and return typed data.
- Type route props explicitly.
- Type component props near the component.
- Prefer readonly/const data for static config arrays.
- Use `as const` for site route registries and literal option arrays.

## Import Conventions

- Use `@/` alias for source imports.
- Group imports roughly as framework/external, then internal aliases, then relative imports.
- Let Prettier organize imports.
- Import directly from component files/packages where it improves bundle analyzability.
- Avoid circular feature imports.

## Environment Configuration

Create `appConfig` with:

- `appUrl`
- `apiUrl`
- `storageUrl`
- `env`
- `revalidate`

Rules:

- Parse numeric env vars defensively.
- Use safe defaults for missing optional values.
- Do not crash on missing API URL; return empty data from fetchers.
- Only expose public-safe variables through `NEXT_PUBLIC_*`.

## Testing Approach

If adding tests:

- Test payload normalizers because APIs can return varied shapes.
- Test SEO metadata builders for fallback and locale canonical behavior.
- Test forms for validation and successful submission states.
- Test routing helpers and sitemap generation where practical.
- Add component tests for reusable interactive components.
- Run typecheck, lint, format, and production build before completion.

## Implementation Checklist

For each requested feature/entity:

1. Add route group and route file under `app/[locale]`.
2. Add feature page under `features/<feature>/pages`.
3. Add feature components under `features/<feature>/Components`.
4. Add data service under `lib/utils/services`.
5. Add TypeScript API types under `lib/Types` or colocated in service file.
6. Add metadata generation using shared SEO helpers.
7. Add messages/locales.
8. Add navigation/footer entries if relevant.
9. Add form hook and client form component if mutations are needed.
10. Add loading/error/not-found behavior if relevant.
11. Update sitemap route registry for public pages.
12. Verify with format, lint, typecheck, and build.

## Final Agent Instructions

- Do not create a visual-only clone.
- Preserve engineering approach and architecture.
- Keep route files thin.
- Keep pages server-first.
- Isolate client interactivity.
- Normalize API data defensively.
- Implement SEO and i18n as first-class architecture.
- Use accessible, responsive, utility-first UI.
- Verify with real commands and report results.
