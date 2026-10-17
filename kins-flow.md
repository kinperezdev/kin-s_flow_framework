# Kin's Flow Method

A guide for AI coding agents. Follow it to build a complete guided demo and onboarding system into a multi-role app.

> **Not a developer? Here is the short version.** Give this file to an AI coding tool (like Claude Code, Cursor, or Codex) and ask it to "add Kin's Flow" to your app. It will first show you a simple list of who does what in your app, for you to check. Then it adds: practice logins that can't change your real data, step-by-step tours for each kind of user, a "?" help menu on every page, small "i" buttons that explain each card, one screen that shows how everyone's work connects, and a guided setup for brand-new accounts.

Designed by Kin Clark Perez after a client demo went wrong. The individual techniques (sample data, product tours, contextual help, onboarding checklists) are well known. The method is how they fit together: one role-based system, built flow first, written in plain words, and reused for real onboarding.

Live example: the FlowK site. It runs its own "Show me around" tour, "i" buttons, and "How it works" map.

---

## When to use it

Use Kin's Flow when an app has two or more roles and someone has to understand it fast: a client demo, a pilot, a handover, or new accounts logging in for the first time.

When the user says "add Kin's Flow", "add DemoFlow", or points you at this file, build all six parts below. Do not ship a subset unless the user asks for one.

## Before you write code

1. **Map the flow.** Read the codebase and write down who starts the process, every role that touches it in order, and who finishes it. One sentence per role: what they do and where they tap.
2. **List what each role sets up once, does daily, and does weekly.** Every item gets a tap path, like `Settings → Students → Add Student`.
3. **Verify every button name against the code.** If the guide says "Send Report", that exact label must exist on screen. Never invent labels.
4. **Show the user the map before building.** It is the source of truth for all six parts.

## The six parts

### 1. Demo accounts

One demo account per role, all sharing one consistent set of sample data, so the same customer, request, or order appears across every role's view.

- **The demo runs fully offline.** Wrap the single database client once. In demo mode, send every call to in-memory sample tables instead of the network: reads, writes, rpc, edge functions, realtime channels, auth lookups, and storage uploads. Live mode passes straight through. Do not rely on per-screen demo branches or on RLS alone, because a demo tab can inherit a real session.
- **Scope reads like production.** The in-memory query filters rows the same way the live row-level security (RLS, the database rules that decide who can see which rows) does. A staff member sees only their assigned rows. A customer sees only their own orders.
- **Every write works against the sample data.** Create, edit, and delete change the in-memory tables so the demo feels real, and every role sees the change. Return fresh array copies so screens re-render. A page reload resets the demo.
- **Load the sample data lazily** (dynamic import) so live users never download it.
- **Prove it with a test** that turns demo mode on, runs every call type, and asserts `fetch` is never called.

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

Wrap `functions.invoke`, `channel`, `auth.getUser`, and storage writes the same way.

Before demo accounts ship to production, check that RLS is on for every table and that the anonymous role has no write policies.

### The "?" help menu

One "?" button in the top bar, on every page, for demo **and** real accounts. It opens a small menu that holds the guides from parts 2, 3, and 5:

| Item | One-line hint under it |
|------|------------------------|
| Show me around | A quick tour of your main pages |
| Explain this page | What every button here does |
| How it works | What each role does, step by step |

- One place to find help. Do not scatter tour buttons across pages.
- Close the menu on an outside tap or Escape. Use `aria-haspopup="menu"` and `role="menuitem"`.
- Make the tap target at least 44 x 44 px.
- Disable it while the app is locked (for example, while a new user must set a password first).
- The last step of every tour says: "Tap the ? at the top any time to see this again."

### 2. "Show me around" tour per role

A guided overlay that runs once per role automatically in the demo (remember it per browser session), and replays from the "?" menu. Order:

1. **Welcome, short.** One line: "Here is a quick tour of your part as the Manager. It takes a minute." Do not list the whole flow here. The full flow lives in "How it works" (part 5).
2. **Set up first.** The one-time setup in order. Open each settings tab and spotlight it, with a short "How to do it" list.
3. **Every day.** Page by page: open the page, spotlight the real button, list the steps.
4. **Finish.** "You're all set. Tap the ? at the top any time to see this again."

Each step is data, not markup:

```ts
interface TourStep {
  title: string
  body: string
  points?: string[]
  pointsTitle?: string
  view?: string
  settingsTab?: string
  target?: string
  skipIfMissing?: boolean
  fill?: boolean
}
```

**Phone-proof it.** On phones, scroll each highlighted element above the bottom tour card: measure the real card rect, add a temporary end-of-page spacer if needed, re-check for a few seconds, and use `visualViewport` height. Steps that live in the mobile menu open the menu and wait until the item has slid in. Audit every step at 375 x 640.

### 3. "Explain this page"

A per-page tour on every page and every settings section, opened from the "?" menu. Same engine as part 2, shorter: what this page is for, then each main area and button on it.

Drop any step whose target is not on screen right now (a role without that button, an empty list), so the tour never points at nothing. Keys like Escape and the arrow keys must not fire while the user is typing in a field.

### 4. "i" info buttons

A small "i" on every card and stat. Tapping it shows two lines:

- **What it is:** "Orders placed today."
- **What it's for:** "To get the team ready for the day."

Keep all the text in one map keyed by card title. Add a source-scan test that finds every card title in the code and fails if one has no entry, so no card ships without its "i".

```ts
export const INFO: Record<string, { about: string; purpose: string }> = {
  'Orders Today': {
    about: 'Orders placed today.',
    purpose: 'To get the team ready for the day.',
  },
}
```

### 5. "How it works" overview

One overlay that shows the whole system across roles. It is a plain text card, not an auto-playing walkthrough.

- **Who sets it up** comes first (for example, the Owner sets up the workspace, then the Manager invites the team).
- **The flow:** numbered roles joined by arrows. Each shows what they do and where they tap. Mark the viewer's own role with "You".
- **Tap a role** to see its steps grouped by **Once**, **Daily**, and **Weekly**, each with a tap path.

### 6. First-login setup

The same guide, reused for real accounts. When an account logs in for the first time, mark onboarding pending for that user, and run the hands-on setup: the "Set up first" steps from their role, done for real this time. Clear the flag when they finish or skip. Store it per user ID and wrap storage access in try/catch. Never run it in demo mode.

Make it hands-on, not a slideshow:

- **The page stays usable.** Use a lighter backdrop and let taps through to the page.
- **Spotlight the real form**, not just the menu item that opens it. Steps with `fill: true` show "Fill this in now. Tap Next when you are done."
- **Dock the card** in the bottom-right corner on desktop so it never covers the form, and add a spacer under the page so the last field can scroll clear of it.
- **Typing always works.** The guide's keyboard shortcuts ignore keys pressed inside inputs, textareas, and selects.

---

## Writing rules

- **Plain words.** Write for someone who has never used software like this. Name the concrete thing and the action: "Add the students", not "Configure entities."
- **No abstract verbs** like describe, configure, manage, leverage.
- **Button names match the screen exactly.**
- **Short.** One idea per sentence. Lists over paragraphs.
- **Ask before sweeping rewrites.** If you find many confusing labels in the app, list them and wait for the user's go-ahead. Change only what they approve.

## Done checklist

- [ ] Flow map written and approved by the user
- [ ] Demo account per role, shared sample data, reads scoped like RLS
- [ ] Demo runs offline: every call on the single DB client goes to in-memory data, with a test that asserts `fetch` is never called
- [ ] "?" menu in the top bar with Show me around, Explain this page, How it works, for demo and real accounts
- [ ] "Show me around" per role: short welcome, Set up first, Every day, real buttons spotlighted, finish points to the "?"
- [ ] "Explain this page" on every page and settings section, skipping steps not on screen
- [ ] "i" on every card, one text map, source-scan test
- [ ] "How it works" overlay: setup roles, arrowed flow, You marker, Once / Daily / Weekly
- [ ] First-login onboarding for new accounts: real form spotlighted, card docked, typing works
- [ ] Every tour step checked at 375 x 640
- [ ] Every button name verified against the code

---

Kin's Flow Method by Kin Clark Perez. Free to use, share, and adapt.
