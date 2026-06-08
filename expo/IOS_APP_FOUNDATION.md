# iOS App Foundation

Use this repo as the default structure for new iOS-first Expo apps that need to move fast without getting trapped in dependency churn.

## What This Foundation Standardizes

- Expo SDK 54 managed workflow
- React 19 + React Native 0.81 compatibility
- Expo Router entrypoint via `index.ts`
- strict TypeScript
- local-first development with optional backend wiring
- EAS build and TestFlight release path

## Foundation File Contract

Every new app should preserve these files unless there is a concrete reason to change them:

- `package.json`
- `app.json`
- `eas.json`
- `babel.config.js`
- `tsconfig.json`
- `index.ts`
- `.env.example`
- `.nvmrc`
- `.npmrc`

## Foundation Directory Contract

```text
app/
  _layout.tsx
  (auth)/
  (app)/
src/
  components/
  config/
  lib/
  providers/
  theme/
  types/
  utils/
assets/
tests/unit/
docs/expo/
supabase/
  functions/
  migrations/
```

## Entry and Routing Rules

- `index.ts` should import `expo-router/entry`.
- Treat `app/` as the real application shell.
- Do not build new features in `App.tsx` when Expo Router is active.
- Keep route groups explicit, usually:
  - `(auth)` for sign-in and callbacks
  - `(app)` for the main signed-in experience

## Dependency Rules

Use the versions already proven in this repo as the initial baseline:

- `expo`: `~54.0.33`
- `react`: `19.1.0`
- `react-dom`: `19.1.0`
- `react-native`: `0.81.5`
- `react-native-web`: `0.21.2`
- `expo-router`: `~6.0.23`

Native or Expo-owned packages should be installed with `npx expo install`.

Non-Expo packages should be added only after the base verification gate passes.

## Environment Rules

Client-safe values only:

- `EXPO_PUBLIC_SUPABASE_URL`
- `EXPO_PUBLIC_SUPABASE_ANON_KEY`
- `EXPO_PUBLIC_PROJECT_SCHEME`
- `EXPO_PUBLIC_BYPASS_AUTH`

Behavior contract:

- `EXPO_PUBLIC_BYPASS_AUTH=true`: local persistence and safe fallback behavior
- `EXPO_PUBLIC_BYPASS_AUTH=false`: real auth and backend writes

## Implementation Rules That Prevent Early Bugs

- Fail soft when backend env is missing during local setup.
- Keep data mappers explicit between UI objects and backend payloads.
- Add platform guards for native-only APIs.
- Treat web as a validation surface, not an afterthought.
- Keep async user actions wrapped in `try/catch`.
- Add at least smoke-level unit tests for core transforms and contracts.

## Scaffold Sequence

1. Create the app with a TypeScript Expo template.
2. Install and pin the known-good dependency set.
3. Switch to Expo Router entrypoint.
4. Create the `app/` and `src/` structure before feature work.
5. Add `.env.example`, `.nvmrc`, `.npmrc`, `eas.json`, and verification scripts.
6. Run the full verification gate.
7. Only then begin app-specific feature generation.

## Verification Gate

Run all of these after initial scaffold and after any dependency changes:

```bash
npm ls react react-dom react-native react-native-web --depth=1
npm run typecheck
npm run test
npx expo export --platform web
```

Optional local smoke commands:

```bash
npm run ios
npm run web
```

## When to Fork This Foundation

Fork this baseline only if one of these is true:

- the app requires bare/prebuild-only native modules
- the app is Android-first instead of iOS-first
- the app intentionally avoids Expo Router
- the backend model is fundamentally different from optional local-first bootstrapping
