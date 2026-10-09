# Kin's Flow

**Give your AI one guide and your app explains itself**

Kin's Flow is a guide for AI coding tools. Hand it over and the AI adds a safe demo, tours, and help buttons to your app. Anyone can learn it without you there.

> **For developers:** demo accounts with a write guard, data-driven tours, info text, a role flow map, and first-login setup. Works with any stack.

## Get started in one step

Paste this into Claude Code, Cursor, Codex, or any AI coding tool:

```text
Read https://raw.githubusercontent.com/kinperezdev/kin-s_flow_framework/main/kins-flow.md and add Kin's Flow to this app. Show me the flow before building.
```

Or save [`kins-flow.md`](kins-flow.md) in your project and say "add Kin's Flow".

## What the AI adds to your app

| # | Part | What it does | For developers |
|---|------|--------------|----------------|
| 01 | **Demo accounts** | A practice login for each user type. Click anything. Nothing real changes. | Shared sample data, reads match live RLS, write guard on the DB client |
| 02 | **Show me around** | A tour for each user type: the flow, the setup, the daily work. | Steps stored as data, highlights real buttons, tested at 375 x 640 |
| 03 | **Explain this page** | Every page can explain what it's for and what each part does. | Same engine as the tour, one tour per page |
| 04 | **"i" buttons** | Tap the "i" on any card to see what it is and why it matters. | All text in one file, a test fails if one is missing |
| 05 | **How it works** | One screen showing who starts the work and who finishes it. | Roles joined by arrows, "You" marker, once, daily, weekly steps |
| 06 | **First-login setup** | New accounts get the same guide on day one, for real. | First-login flag per user, reuses the setup steps |

## How it works with your AI

1. **Give it to your AI.** Paste the prompt, or save `kins-flow.md` in your project.
2. **Check who does what.** The AI shows the flow first. Fix it there.
3. **Try every role.** Log in to each demo and tap "Show me around". Try it on a phone too.

## The demo never touches real data

- **Demo can't save.** Click anything. Nothing reaches your real records.
- **Same view as real life.** Each role sees only what it would really see.
- **Tested.** A test fails if anything gets through.

```ts
const WRITE_METHODS = new Set(['insert', 'update', 'upsert', 'delete'])

function guardBuilder<T extends object>(builder: T): T {
  return new Proxy(builder, {
    get(target, prop, receiver) {
      const isWrite = typeof prop === 'string' && WRITE_METHODS.has(prop)
      if (isWrite && isDemoMode()) return () => blockedChain()
      const value = Reflect.get(target, prop, receiver)
      return typeof value === 'function' ? value.bind(target) : value
    },
  })
}
```

## The rules the AI follows

- **Flow first:** the AI shows you who does what before it builds.
- **Real button names:** tours only name buttons that exist.
- **Plain words:** "Add students", not "configure entities".
- **Works on phones:** every step is tested on a small screen.
- **One guide, two jobs:** the demo and real setup use the same steps.
- **Ask first:** the AI lists confusing labels and waits for your OK.

## New to these words

| Word | Meaning |
|------|---------|
| Role | A type of user, like manager, staff, or customer. |
| AI coding tool | An AI that writes code for you, like Claude Code or Cursor. |
| Demo account | A practice login with sample data. |
| Onboarding | Setup that helps new people start. |

## Why it exists

Kin showed a client an app with many user types. The screens worked. The room still got lost. Nobody could see who starts the work or where it ends.

The fix wasn't better slides. It was an app that teaches itself, in plain words, with zero risk to real data. The pieces are known. Kin's Flow puts them in order, so an AI can build them for you.

## See it live

**FlowK** is the example. It runs Kin's Flow on itself: tap "Show me around", the "i" buttons, and the "How it works" map.

## Share it

Free to use and share. If it helps you, star the repo and pass it to someone building an app.

---

Made by [Kin Clark Perez](https://kinperez.com)
