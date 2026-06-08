# iOS TestFlight + Transporter Reference

Use this as the release checklist for internal iOS testing.

## Important Before TestFlight Submit

In `eas.json`, replace:

- `REPLACE_WITH_ASC_APP_ID`

with your real App Store Connect app ID.

## Internal iOS Build Commands (Ready)

```bash
npx eas login
npx eas build:configure
npx eas build -p ios --profile preview
npx eas submit -p ios --profile preview
```

## If Uploading Manually with Transporter

1. Download the generated `.ipa` artifact from EAS Build.
2. Open **Transporter** on macOS and sign in with your App Store Connect account.
3. Drag the `.ipa` into Transporter.
4. Click **Deliver**.
5. Wait for App Store Connect processing, then assign internal testers in TestFlight.

## Pre-Submit Guardrails

- Ensure `EXPO_PUBLIC_BYPASS_AUTH=false` for release candidate builds.
- Use Node 20 (`nvm use 20`).
- Run checks before build:

```bash
npm run typecheck
npm run test
npx expo export --platform web
```

## Audio Permission Note (MVP)

- Playback-only audio does **not** require microphone permission strings.
- Keep microphone keys out of `Info.plist` unless recording is added later.
