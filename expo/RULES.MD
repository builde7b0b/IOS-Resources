# Expo Project Rules (Baseline)

Use this file as the default setup contract for all new Expo projects.

Related docs:

- [App Foundation Rules](../RULES.MD)
- [iOS App Foundation](./IOS_APP_FOUNDATION.md)
- [Scaffold Prompts](./SCAFFOLD_PROMPTS.md)
- [iOS TestFlight Reference](./IOS_TESTFLIGHT_REFERENCE.md)

## 1) Runtime Requirements

- Use Node `20.x` (minimum `20.19.4`).
- Commit `.nvmrc` with:
  - `20`
- In `package.json`, always set:
  - `"engines": { "node": ">=20.19.4" }`

## 2) Package Manager Rules

- Use `npm` unless the project explicitly standardizes on another manager.
- Commit `.npmrc` with:
  - `legacy-peer-deps=true`
- Rationale: avoids Expo Router / React Native peer-resolution deadlocks that are common during bootstrap.

## 3) Expo Bootstrap Standard

- Create app with TypeScript template.
- Set `main` to `expo-router/entry` when using Expo Router.
- Add `babel.config.js` with `presets: ['babel-preset-expo']`.
- Add scripts at minimum:
  - `start`
  - `ios`
  - `android`
  - `web`
  - `typecheck`
  - `test`

## 4) Required Web Dependencies

For SDK 54-based projects, explicitly pin:

- `react`: `19.1.0`
- `react-dom`: `19.1.0`
- `react-native-web`: `0.21.2`

Do not leave these floating with broad `^` ranges during initial setup.

## 5) Required Core Expo Dependencies

Always include if app uses safe area + routing + auth flows:

- `expo-router`
- `react-native-safe-area-context`
- `react-native-screens`
- `expo-linking`
- `expo-secure-store`

If app uses notifications:

- `expo-notifications`
- `expo-device`

## 6) Install Order Rules

1. `nvm use 20`
2. `npm install`
3. `npx expo install <expo-native-packages>`
4. `npm install <non-expo packages>`

If resolver fails anyway:

- Re-run with existing `.npmrc` (`legacy-peer-deps=true`) instead of forcing ad-hoc install flags each time.

## 7) Verification Gate (Must Pass)

Run before starting development:

- `npm ls react react-dom react-native-web --depth=1`
- `npm run typecheck`
- `npm run test`
- `npm run web`

If local port conflicts occur, also provide:

- `npm run web:port`
- Suggested implementation:
  - `RCT_METRO_PORT=8081 EXPO_NO_PORT_RETRY=1 expo start --web --port 8081`

## 8) Environment & Secrets Rules

- Keep client-safe values in `.env` under `EXPO_PUBLIC_*`.
- Never expose server secrets (OpenAI keys, service-role keys) in client env.
- Commit `.env.example` with placeholders.
- Include `EXPO_PUBLIC_BYPASS_AUTH=true` for local MVP testing.
- Before production builds, set `EXPO_PUBLIC_BYPASS_AUTH=false`.
- If backend setup is optional during local QA, implement explicit offline fallbacks and never hard-crash on missing backend env vars.
- Bypass Mode Contract:
  - `EXPO_PUBLIC_BYPASS_AUTH=true` => local-only persistence and local AI fallback; no remote Supabase writes.
  - `EXPO_PUBLIC_BYPASS_AUTH=false` => authenticated Supabase writes only.

## 9) Runtime Safety Rules

- Never leave async screen actions unguarded.
  - Wrap actions in `try/catch`.
  - Show user-facing status text instead of throwing.
- Validate required input before API calls (example: `releaseStatement`, `goal`, and `HH:mm` time values).
- Treat web as first-class.
  - Guard native-only APIs with platform checks/fallbacks.
  - Use `npx expo export --platform web` to quickly surface real Metro/runtime errors.

## 10) Supabase Edge Function Rules

- Every HTTP edge function must include CORS by default.
  - Return `Access-Control-Allow-Origin`.
  - Return `Access-Control-Allow-Headers` including: `authorization, x-client-info, apikey, content-type`.
  - Return `Access-Control-Allow-Methods` including: `POST, OPTIONS`.
- Every function must explicitly handle preflight:
  - `if (req.method === 'OPTIONS') return preflightResponse`.
- Every function must reject unsupported methods with `405`.
- Centralize CORS headers/helpers in `supabase/functions/_shared.ts` and import into every function.
- After CORS or function changes, redeploy all affected functions and verify from browser origin.
- Edge functions should be fail-soft for MVP UX:
  - If upstream AI provider fails or key is missing, return a safe fallback payload with `200` for supported requests.
  - Do not return `500` for expected external-provider outages in user-facing generation flows.
- Client-side callers should also include graceful fallback behavior if function invocation fails.

## 11) Supabase Data Mapping Rules

- UI view models may use camelCase, but Supabase write payloads must map to DB column names (`snake_case`) before insert/upsert.
- Keep write mappers explicit and typed; do not spread UI objects directly into Supabase writes.
- Validate `on_conflict` keys against actual unique constraints before shipping.

## 12) Expo UI Compatibility Rules

- Only adopt React Native / Expo-compatible libraries in managed workflow.
- Do not add SwiftUI/iOS-native-only libraries unless a prebuild/eject strategy is explicitly chosen.
- Keep MVP UI upgrades visual-first (theme, typography, spacing, component consistency) unless a full IA redesign is approved.

## 13) Production Readiness Rules

Before first production build:

- `npx eas build:configure`
- Ensure iOS bundle identifier is set in `app.json` / app config.
- Confirm privacy strings for notifications/camera/mic if used.
- Run final checks:
  - `npm run typecheck`
  - `npm run test`

## 14) Non-Negotiables

- Do not develop Expo SDK 54+ on Node 18.
- Do not mix incompatible `react`/`react-dom` versions.
- Do not leave web dependencies implicit if web testing is required.
- Do not ship without passing the verification gate.
- Do not ship with `EXPO_PUBLIC_BYPASS_AUTH=true`.
- Do not ship browser-called Supabase functions without CORS + OPTIONS handling.

## 15) Ritual UX Data Integrity Rules

- Normalize identity/release text before composing fallback AI strings.
  - Strip leading `I am` / `I'm` so outputs never become `I am I'm ...`.
  - Strip trailing punctuation before recomposing complete sentences.
- Daily loop completion must be idempotent by date.
  - If today is already saved, hide/disable the "Lock in loop" action and show a locked state badge.
- Flow-mode progression requires validation at each step.
  - Do not allow step advance from Release/Identity screens with empty input.

## 16) Reminder UX Persistence Rules

- Reminder UI must always render one of these states:
  - `loading`
  - `saved preference`
  - `empty with clear CTA`
- Hydrate reminder input from saved preference on screen load.
- Local fallback reads must support legacy key shapes during development migrations (for example `pushToken` -> `push_token`).

## 17) Brand System Rules

- Use centralized tokens in `src/theme/tokens.ts` and do not hardcode off-brand colors in screens.
- Current core brand palette:
  - Midnight Void `#090611`
  - Aura Violet `#8D5BFF`
  - Electric Orchid `#D875FF`
  - Moon White `#F4F1FF`
  - Soft Rose Glow `#FFB7E8`
- UI should preserve dark-first depth with soft glow accents and high-contrast text.

## 18) Flow-First Ritual Rules

- Today screen defaults to `flow` mode for first-time and routine use.
- Flow mode must keep per-step validation gates:
  - Release required before step 2.
  - Identity required before step 3.
  - Mood required before generation.
- Keep command mode available as an explicit toggle, never remove it silently.

## 19) Loop History Interaction Rules

- Loop history cards on Today must be interactive.
- Tap behavior uses inline expand/collapse (same card) with deterministic keying:
  - key format: `${ritual_date}-${created_at}`.
- Expanded view must include release, identity, guidance, completion timestamp, and streak.

## 20) Guided Handoff + Transition Rules

- After successful ritual generation, show a primary CTA to Journal.
- After successful loop lock, show a short transition overlay and keep explicit CTA routing to Journal.
- Loading/transition overlays must be non-blocking and fail-safe:
  - no hidden state traps,
  - no suppression of error status messages.

## 21) Journal Technique Rules (MVP)

- NOT technique template mode must include:
  - release line (`I am NOT...`)
  - install line (`I AM...`)
  - aligned action line
- Template mode is additive to free-form journaling, not a replacement.
- Saved journal content continues to use existing `journal_entries.content` storage shape.

## 22) 3-6-9 Progress Rules

- Daily quotas are fixed:
  - morning: 3
  - midday: 6
  - evening: 9
- Counters increment only after journal save succeeds.
- Progress keying must reset deterministically by local day (`YYYY-MM-DD`).
- In local bypass mode, persistence must stay local-only.

## 23) Audio MVP Rules

- Audio scope is playback-only for MVP.
- Use Expo-compatible audio playback with local bundled files.
- Required states in UI:
  - loading
  - playing
  - paused
  - ended
  - error fallback
- No recording permissions or voice synthesis in MVP audio scope.
