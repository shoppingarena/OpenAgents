## 📋 **IMPLEMENTATION PLAN**
Based on my analysis, here's the detailed plan to integrate shared packages into main-pwa:
---
🔍 Current State Analysis:
✅ Exists:
- Shared packages (shared-auth, shared-db, shared-ui) are created
- Main-pwa Next.js app is created
- Workspace configuration (pnpm-workspace.yaml) is set up
❌ Missing:
- Shared packages are NOT built (no dist/ directories)
- main-pwa has no dependencies on shared packages
- main-pwa has no i18n configuration (next-intl)
- main-pwa has no Tailwind brand colors configured
- locale/layout.tsx has hardcoded navigation/footer
- Root page shows default Next.js content
- No database or auth integration
---
📝 Step-by-Step Implementation Plan:
Step 1: Build Shared Packages (Estimated: 5 minutes)
- Build shared-auth package: cd packages/shared-auth && pnpm run build
- Build shared-db package: cd packages/shared-db && pnpm run build
- Build shared-ui package: cd packages/shared-ui && pnpm run build
- Verify dist/ directories exist in all packages
Files affected: 0 (build artifacts only)
---
Step 2: Add Shared Package Dependencies to main-pwa (Estimated: 5 minutes)
- Update microservices/main-pwa/package.json:
  - Add @tarrakirja/shared-ui as dependency
  - Add @tarrakirja/shared-auth as dependency
  - Add @tarrakirja/shared-db as dependency
  - Add next-intl for i18n
  - Add @prisma/client for database access
  - Add next-auth for authentication
- Run pnpm install to install dependencies
Files affected:
- microservices/main-pwa/package.json
---
Step 3: Configure Tailwind CSS with Brand Colors (Estimated: 5 minutes)
- Create or update microservices/main-pwa/tailwind.config.ts
- Add brand colors:
  - --brand-primary: #566e16 (Olive Green)
  - --brand-accent: #d65b07 (Coral Orange)
- Add Patrick Hand font configuration
Files affected:
- microservices/main-pwa/tailwind.config.ts (create or update)
---
Step 4: Set up i18n Configuration (Estimated: 10 minutes)
- Create microservices/main-pwa/src/i18n.ts (next-intl config)
- Create microservices/main-pwa/src/messages/fi.json (Finnish translations)
- Create microservices/main-pwa/src/messages/en.json (English translations)
- Create microservices/main-pwa/src/messages/uk.json (Ukrainian translations)
- Update microservices/main-pwa/src/app/[locale]/layout.tsx to use i18n provider
Files affected:
- microservices/main-pwa/src/i18n.ts (create)
- microservices/main-pwa/src/messages/fi.json (create)
- microservices/main-pwa/src/messages/en.json (create)
- microservices/main-pwa/src/messages/uk.json (create)
- microservices/main-pwa/src/app/[locale]/layout.tsx (update)
---
Step 5: Integrate Shared UI Components (Estimated: 10 minutes)
- Update microservices/main-pwa/src/app/[locale]/layout.tsx:
  - Replace hardcoded header with <Header /> from @tarrakirja/shared-ui
  - Replace hardcoded footer with <Footer /> from @tarrakirja/shared-ui
  - Remove inline navigation code
Files affected:
- microservices/main-pwa/src/app/[locale]/layout.tsx (update)
---
Step 6: Update Root Page with Custom Content (Estimated: 5 minutes)
- Update microservices/main-pwa/src/app/[locale]/page.tsx:
  - Replace default Next.js content with Tarrakirja welcome page
  - Add hero section
  - Add navigation links (About, Books, Shop, Blog, Contact)
  - Use brand styling
Files affected:
- microservices/main-pwa/src/app/[locale]/page.tsx (update)
---
Step 7: Set up Database Client (Estimated: 10 minutes)
- Create microservices/main-pwa/src/lib/prisma.ts:
  - Import prisma from @tarrakirja/shared-db
  - Configure for use in main-pwa
- Add environment variable support for DATABASE_URL
Files affected:
- microservices/main-pwa/src/lib/prisma.ts (create)
- microservices/main-pwa/.env.example (create or update)
---
Step 8: Set up Authentication (Estimated: 15 minutes)
- Create microservices/main-pwa/src/app/api/auth/[...nextauth]/route.ts:
  - Use @tarrakirja/shared-auth configuration
  - Configure NextAuth with Prisma adapter
- Create auth utility files for session management
- Add auth provider wrapper
Files affected:
- microservices/main-pwa/src/app/api/auth/[...nextauth]/route.ts (create)
- microservices/main-pwa/src/lib/auth.ts (create)
---
Step 9: Test Integration (Estimated: 10 minutes)
- Start dev server: cd microservices/main-pwa && pnpm run dev
- Verify build succeeds
- Test navigation works
- Test language switching works
- Test shared components render correctly
- Check for errors in browser console
Files affected: 0 (testing only)
---
📊 Summary:
Total Estimated Time: ~75 minutes (1 hour 15 minutes)
Files to Create: 8 files
Files to Update: 3 files
Total Files Affected: 11 files
Dependencies to Add:
- @tarrakirja/shared-ui
- @tarrakirja/shared-auth
- @tarrakirja/shared-db
- next-intl
- @prisma/client
- next-auth
---
⚠️ Potential Issues:
1. Shared packages may have TypeScript errors - Will need to fix as we go
2. Tailwind CSS v4 configuration differences - May need adjustment
3. next-intl configuration for Next.js 16 - Newer version might have breaking changes
4. Client component issues - Shared UI uses "use client" directive
5. Database connection - Will need DATABASE_URL environment variable
---
🎯 Expected Outcome:
After completing all steps:
- ✅ main-pwa will use shared Header and Footer components
- ✅ main-pwa will have working language switching (FI, EN, UK)
- ✅ main-pwa will display custom Tarrakirja content (not default Next.js)
- ✅ main-pwa will have database access via shared-db
- ✅ main-pwa will have authentication via shared-auth
- ✅ Tailwind CSS brand colors will be configured
- ✅ Dev server will run without errors
---
🚦 APPROVAL NEEDED
Do you approve this implementation plan?
Yes - I will proceed with Step 1: Build Shared Packages  
No - Let me know what you'd like to change  
Questions - Let me know if you need clarification on any step
Please confirm and I'll start implementing step by step.
▣  OpenCoder · glm-4.7 · 3m 55s