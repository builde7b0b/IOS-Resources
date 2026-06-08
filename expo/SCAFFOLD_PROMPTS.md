# Scaffold Prompts

These prompts are designed to recreate this repo's structure with less drift. Use them in order when bootstrapping a new iOS-first Expo app.

## Prompt 1: Foundation Scaffold

Use this first. It should produce the initial repo shape, dependency policy, and verification scripts.

```text
Create an iOS-first Expo app foundation using Expo SDK 54 and managed workflow.

Hard requirements:
- Use Node 20 with `.nvmrc` set to `20`
- Use `npm` and commit `.npmrc` with `legacy-peer-deps=true`
- Use TypeScript with `strict: true`
- Use Expo Router with `index.ts` importing `expo-router/entry`
- Set `main` to `expo-router/entry`
- Use this initial dependency baseline:
  - expo `~54.0.33`
  - react `19.1.0`
  - react-dom `19.1.0`
  - react-native `0.81.5`
  - react-native-web `0.21.2`
  - expo-router `~6.0.23`
  - react-native-safe-area-context `~5.6.0`
  - react-native-screens `~4.16.0`
  - expo-linking `~8.0.12`
  - expo-secure-store `~15.0.8`
- Include scripts:
  - start
  - ios
  - android
  - web
  - web:port
  - typecheck
  - test
- Create this folder structure:
  - app/_layout.tsx
  - app/(auth)/
  - app/(app)/
  - src/components/
  - src/config/
  - src/lib/
  - src/providers/
  - src/theme/
  - src/types/
  - src/utils/
  - tests/unit/
  - docs/expo/
- Add `babel.config.js`, `tsconfig.json`, `app.json`, `eas.json`, `.env.example`
- In `.env.example`, include:
  - EXPO_PUBLIC_SUPABASE_URL
  - EXPO_PUBLIC_SUPABASE_ANON_KEY
  - EXPO_PUBLIC_PROJECT_SCHEME
  - EXPO_PUBLIC_BYPASS_AUTH=true
- Implement `src/lib/env.ts` so missing backend env vars do not crash local development
- Treat `App.tsx` as unused boilerplate if Expo Router is active

Verification requirements:
- Run and fix issues for:
  - `npm run typecheck`
  - `npm run test`
  - `npx expo export --platform web`
- Do not stop after file generation. Resolve dependency mismatches and runtime issues until the verification commands pass.

Output requirements:
- Summarize the final structure
- Show exact versions used
- List any assumptions explicitly
```

## Prompt 2: Foundation Hardening

Use this right after Prompt 1 if you want the scaffold tightened before feature work.

```text
Harden this Expo iOS foundation without changing the stack.

Tasks:
- Verify Expo Router is the real entrypoint and remove confusion between `index.ts` and `App.tsx`
- Ensure `app.json` has iOS `bundleIdentifier`, scheme, and any required notification privacy strings
- Ensure `eas.json` has `preview` and `production` profiles
- Add a simple provider structure under `src/providers`
- Add typed placeholders in `src/lib`, `src/types`, and `src/utils`
- Add one or two representative unit tests so `npm run test` is not empty
- Add a short `docs/expo/RULES.MD` with runtime, dependency, env, and verification rules
- Run:
  - `npm run typecheck`
  - `npm run test`
  - `npx expo export --platform web`

Constraints:
- Do not add random UI libraries
- Do not add unsupported native packages
- Do not leave version ranges broad if they affect Expo compatibility
- Prefer explicit, boring reliability over clever abstractions
```

## Prompt 3: Product-Specific Scaffold

Use this once the foundation is already stable.

```text
Build the first product slice on top of this existing Expo iOS foundation.

Constraints:
- Preserve the current dependency baseline unless a package is strictly necessary
- Keep Expo managed workflow
- Follow the existing route-group structure in `app/`
- Keep feature code inside `src/`
- Any backend integration must fail soft in local mode if env vars are missing
- Add or update unit tests for new business logic
- Re-run:
  - `npm run typecheck`
  - `npm run test`
  - `npx expo export --platform web`

Before you finish:
- confirm no dependency drift was introduced
- confirm no new native module requires eject/prebuild unless explicitly approved
- summarize what changed in app structure, env requirements, and verification status
```

## Prompt 4: Clone This Repo's Shape Exactly

Use this when you want a near-template reproduction rather than a looser best effort.

```text
Recreate this Expo repo structure as closely as possible for a new iOS-first app foundation:

- Expo SDK 54 managed workflow
- Expo Router entry via `index.ts`
- `app/(auth)` and `app/(app)` route groups
- `src/lib/env.ts` with local-safe env fallback behavior
- `src/components`, `src/providers`, `src/theme`, `src/types`, `src/utils`
- `tests/unit` for logic and contract validation
- `docs/expo` containing rules and iOS release references
- `eas.json` with preview and production submit/build profiles
- strict TypeScript
- `npm` with Node 20 and `.npmrc` peer-resolution guardrails

Do not improvise alternative architecture. Mirror the structure first, then adapt names and product copy.

Success criteria:
- dependency installation completes cleanly
- web export passes
- typecheck passes
- test suite passes
- iOS run command is configured
```

## Minimal Reuse Instruction

If you want a shorter reusable starter prompt, use this:

```text
Scaffold a new iOS-first Expo app using the same foundation rules as the UNREAL repo: Expo SDK 54, Expo Router, TypeScript strict mode, Node 20, npm with `.npmrc` peer-deps guardrail, pinned React/React DOM/React Native Web versions, `app/` plus `src/` architecture, local-safe env fallback, EAS config, tests, and verification gates. Do not stop until `npm run typecheck`, `npm run test`, and `npx expo export --platform web` pass.
```
