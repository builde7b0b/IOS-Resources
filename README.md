# Expo Docs

This folder contains the reusable Expo and iOS foundation docs for this repo.

Use these files in this order:

1. [RULES.MD](./RULES.MD)
   The baseline setup contract for Expo projects. Covers Node, npm, dependency pinning, env rules, verification gates, and release guardrails.
2. [IOS_APP_FOUNDATION.md](./IOS_APP_FOUNDATION.md)
   The structural guide for using this repo as the default foundation for new iOS-first Expo apps.
3. [SCAFFOLD_PROMPTS.md](./SCAFFOLD_PROMPTS.md)
   Copy-paste prompts for recreating this repo shape with fewer dependency issues and less architectural drift.
4. [IOS_TESTFLIGHT_REFERENCE.md](./IOS_TESTFLIGHT_REFERENCE.md)
   The internal iOS release checklist for EAS builds, App Store Connect, TestFlight, and optional Transporter upload.

## What This Folder Is For

Use this folder when you need to:

- bootstrap a new Expo iOS app from a known-good baseline
- avoid React / Expo / React Native version mismatch issues
- preserve the same `app/` plus `src/` architecture across projects
- standardize EAS and TestFlight release flow
- generate new apps from prompts without letting the model improvise the stack

## Recommended Workflow

For a new app:

1. Read [RULES.MD](./RULES.MD).
2. Read [IOS_APP_FOUNDATION.md](./IOS_APP_FOUNDATION.md).
3. Use a prompt from [SCAFFOLD_PROMPTS.md](./SCAFFOLD_PROMPTS.md).
4. Do not accept the scaffold until `npm run typecheck`, `npm run test`, and `npx expo export --platform web` pass.

For an iOS release:

1. Confirm the build still follows [RULES.MD](./RULES.MD).
2. Use [IOS_TESTFLIGHT_REFERENCE.md](./IOS_TESTFLIGHT_REFERENCE.md) as the submission checklist.

## Related Doc

- [../RULES.MD](../RULES.MD): top-level foundation rules for using this repo as the baseline for future apps
