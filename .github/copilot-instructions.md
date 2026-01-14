# AI Copilot Instructions

This is a Next.js App Router dashboard application teaching course project for managing invoices and customers.

## Project Structure & Architecture

**Tech Stack:**
- Next.js 15+ (App Router), React 19, TypeScript 5.7, Tailwind CSS, PostgreSQL
- Authentication: next-auth 5.0 beta
- UI Components: Heroicons for icons, Lusitana & Inter fonts (Google Fonts)
- Data Validation: Zod, bcrypt for password hashing
- Package manager: pnpm

**Key Directories:**
- `app/lib/` - Data layer: `data.ts` (async DB queries), `definitions.ts` (TypeScript types), `utils.ts` (formatters), `placeholder-data.ts` (seed data)
- `app/ui/` - UI components: Base components (`button.tsx`, `fonts.ts`), dashboard subdirectory for layout, invoices subdirectory for table/form components
- `app/seed/route.ts` - Database seeding endpoint: creates tables, inserts sample data with bcrypt password hashing
- `app/query/route.ts` - Example query endpoint (currently disabled)

## Data Flow & Patterns

**Database Interaction:**
- Uses `postgres` library with parameterized queries: `sql\`SELECT * FROM table WHERE id = ${id}\``
- All DB functions in `data.ts` are async, with try-catch error handling
- Example pattern: `fetchCardData()` demonstrates parallel query execution with `Promise.all()`
- Amount values stored as integers (cents), formatted to currency using `formatCurrency()` utility
- Amounts in response types use `Omit` and union types (e.g., `LatestInvoiceRaw`)

**Type Definitions Pattern:**
- Strict type hierarchy in `definitions.ts`: Raw DB types separate from UI types (e.g., `LatestInvoiceRaw` vs `LatestInvoice`)
- String union types for status: `'pending' | 'paid'`
- Use `Omit` utility type to transform DB response shapes for frontend consumption

**Component Architecture:**
- Async server components fetch data directly (e.g., `CardWrapper` imports from `data.ts`)
- Client components imported via `'use client'` directive only when needed
- UI components receive data as props, no internal data fetching
- Forms currently placeholder (commented-out functionality in course progression)

## Development Workflow

**Build & Run:**
- `pnpm dev` - Start dev server with Turbopack (ES2020 optimized)
- `pnpm build` - Production build
- `pnpm start` - Run production build
- Database seeding: Access `/seed` route during development to initialize tables and data

**Environment Setup:**
- Requires `POSTGRES_URL` env var with SSL: `{ ssl: 'require' }`
- Path alias `@/` points to root directory (`baseUrl: "."`)

## Coding Conventions

**File & Component Naming:**
- UI components named descriptively: `sidenav.tsx`, `latest-invoices.tsx`, `create-form.tsx`
- Data functions prefixed with `fetch*`: `fetchRevenue()`, `fetchFilteredInvoices()`
- Types named with `Type` suffix when needed for disambiguation: `CustomersTableType`

**Styling:**
- Tailwind CSS classes exclusively (no CSS modules except `home.module.css` for landing page)
- Global styles imported in root layout from `app/ui/global.css`
- Font imports centralized in `app/ui/fonts.ts`, applied via `className={inter.className}`

**Error Handling:**
- DB functions wrap queries in try-catch, log errors, throw descriptive `Error` messages
- Pattern: `console.error('Database Error:', error); throw new Error('Failed to fetch...')`

**Pagination & Utilities:**
- Pagination logic in `generatePagination()` handles ellipsis for 7+ pages
- Utility functions for chart calculations (`generateYAxis()`) accept data and return formatted values
- Date formatting: `formatDateToLocal()` uses `Intl.DateTimeFormat` for locale support

## API Route Conventions

- API routes use `app/query/route.ts` and `app/seed/route.ts` pattern (no middleware layer)
- Responses use `Response.json()` helper
- Seeds use `ON CONFLICT (id) DO NOTHING` to prevent duplicate inserts
