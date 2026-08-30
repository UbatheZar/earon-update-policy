# earon-update-policy

Remote update policy for the EarOn app. This repository contains **one file that matters**:
`update-policy.json`. The app fetches it at cold start and compares its own integer build
number against the two thresholds.

```json
{
  "minimumBuild": 0,
  "recommendedBuild": 0
}
```

- **`minimumBuild`** — any installed build *below* this number is hard-blocked at launch and
  sent to the store. `0` = gate asleep.
- **`recommendedBuild`** — any installed build *below* this number sees a dismissible
  update prompt. `0` = prompt asleep.
- Build numbers are the platform integers (Android `versionCode` / iOS `CFBundleVersion`),
  **not** the dotted marketing version.

## Rules

1. **Editing this file changes production behaviour for every user, immediately** (minus a
   few minutes of CDN cache). Raise a threshold only on purpose, with the target build
   already live on both stores.
2. `minimumBuild` must never exceed the build currently available on the stores — that
   would lock users out with no exit.
3. Mistakes are reversible: `git revert`, and affected users recover on next app launch.
   No app release is needed to fix a wrong threshold.
4. The app **fails open**: if this file is unreachable or malformed, no gate fires. Deleting
   or breaking this file silently disables the mechanism — it never bricks the app.
5. Unknown extra keys are ignored by the app. Do not rely on them.

Write access to this repository is the update kill-switch. Keep it restricted.
