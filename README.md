# Kin's Flow

**Give your AI one guide and your app explains itself**

Kin's Flow is a guide for AI coding tools. Hand it over and the AI adds a safe demo, tours, and help buttons to your app. Anyone can learn it without you there.

> **For developers:** offline demo accounts, a "?" help menu, data-driven tours, info text, a role flow map, and first-login setup. Works with any stack.

## Get started in one step

Paste this into Claude Code, Cursor, Codex, or any AI coding tool:

```text
Read kins-flow.md from https://github.com/kinperezdev/kin-s_flow_framework and add Kin's Flow to this app. Show me the flow before building.
```

Or save [`kins-flow.md`](kins-flow.md) in your project and say "add Kin's Flow".

## What the AI adds to your app

| # | Part | What it does | For developers |
|---|------|--------------|----------------|
| 01 | **Demo accounts** | A practice login for each user type. Click anything. Nothing real changes. | Shared sample data, reads match live RLS, runs offline on in-memory data |
| 02 | **Show me around** | A tour for each user type: the setup and the daily work. Replay it from the "?" at the top. | Steps stored as data, highlights real buttons, tested at 375 x 640 |
| 03 | **Explain this page** | Every page can explain what it's for and what each part does, from the "?" menu. | Same engine as the tour, one tour per page |
| 04 | **"i" buttons** | Tap the "i" on any card to see what it is and why it matters. | All text in one file, a test fails if one is missing |
| 05 | **How it works** | One screen showing who starts the work and who finishes it. | Roles joined by arrows, "You" marker, once, daily, weekly steps |
| 06 | **First-login setup** | New accounts get the same guide on day one, for real. | First-login flag per user, spotlights the real form, typing keeps working |

## How it works with your AI

1. **Give it to your AI.** Paste the prompt, or save `kins-flow.md` in your project.
2. **Check who does what.** The AI shows the flow first. Fix it there.
3. **Try every role.** Log in to each demo and tap "Show me around". Try it on a phone too.

## The demo never touches real data

- **Demo runs offline.** Every click works on sample data in the browser. Nothing reaches your real records.
- **Same view as real life.** Each role sees only what it would really see.
- **Tested.** A test fails if the demo makes any network call.

```ts
export function routeDemoTraffic<T extends SupabaseClient>(client: T): T {
  const from = client.from.bind(client)
  client.from = (table: string) =>
    isDemoMode() ? createDemoQuery(table, loadDemoStore) : from(table)

  const rpc = client.rpc.bind(client)
  client.rpc = (...args) =>
    isDemoMode() ? Promise.resolve({ data: null, error: null }) : rpc(...args)

  return client
}
```

## One "?" for all help

A "?" button at the top of every page opens three guides: **Show me around**, **Explain this page**, and **How it works**. It works for demo and real accounts, so nobody has to hunt for help.

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

**[FlowK](https://flowkin.kinperez.com)** is the example. It runs Kin's Flow on itself: tap "Show me around", the "i" buttons, and the "How it works" map.

## Share it

Free to use and share under the [MIT License](LICENSE). If it helps you, star the repo and pass it to someone building an app.

---

Made by [Kin Clark Perez](https://kinperez.com)
