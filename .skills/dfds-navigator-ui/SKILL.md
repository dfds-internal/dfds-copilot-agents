---
name: dfds-navigator-ui
description: Add the DFDS design system to a web project — includes the DFDS header (NavigationMenu from @dfds-frontend/compass-ui), Navigator component styles and tokens, and shadcn/ui themed to DFDS brand colours. Use when adding a DFDS-branded header, setting up Tailwind v4 with Navigator tokens, or wiring shadcn/ui components to DFDS colours. Covers Next.js App Router and TanStack Start.
metadata:
  author: dfds-frontend
  version: "1.0"
compatibility: Requires @dfds-frontend packages from GitHub Packages. Set up .npmrc first — see dfds-npmrc-setup skill.
---

# DFDS Navigator UI

Adds the DFDS design system — header, tokens, and form components — to any React project.

**Packages:**
- `@dfds-frontend/compass-ui` — `NavigationMenu` (the DFDS top nav/header)
- `@dfds-frontend/navigator-components` — DFDS component library
- `@dfds-frontend/navigator-icons` — DFDS icon set
- `@dfds-frontend/navigator-styles` — Tailwind v4 tokens + fonts

**Framework-specific code snippets:**
- Next.js App Router → [references/nextjs-snippets.md](references/nextjs-snippets.md)
- TanStack Start → [references/tanstack-snippets.md](references/tanstack-snippets.md)

**Related skills:**
- Full shadcn/ui DFDS theme (CSS variables, `@theme inline`, CVI overrides) → see the `dfds-shadcn-theme` skill

---

## Prerequisites

Auth for `@dfds-frontend` packages — read the `dfds-npmrc-setup` skill first.

---

## 1 — Install packages

```bash
pnpm add @dfds-frontend/compass-ui \
         @dfds-frontend/navigator-components \
         @dfds-frontend/navigator-icons \
         @dfds-frontend/navigator-styles
```

---

## 2 — Tailwind v4 CSS setup

In your global CSS entry point, add these imports **in order**:

```css
@import "tailwindcss";
@import "@dfds-frontend/navigator-styles/tailwind/v4";
@import "@dfds-frontend/navigator-styles/fonts";
@import "@dfds-frontend/navigator-components/styles";
```

### Required `@source` directives

Navigator icon `size` prop accepts string values like `"small"` or `"medium"`. Tailwind v4 must scan the package source to emit the matching utility classes — without these lines icon sizing silently breaks:

```css
@source "../node_modules/@dfds-frontend/navigator-components/src";
@source "../node_modules/@dfds-frontend/navigator-icons/dist";
@source "../node_modules/@dfds-frontend/compass-ui";
```

> Adjust the relative path prefix (`../`) to match your CSS file's location relative to `node_modules`.

### SVG inline reset (required)

Tailwind's preflight sets `svg { display: block }`. Navigator icons expect `inline`. Add this to avoid broken icon layout:

```css
@layer base {
  svg {
    display: inline;
  }
}
```

---

## 3 — shadcn/ui — Navigator theme

shadcn/ui must be configured to use CSS variables (`cssVariables: true`) so the Navigator theme overrides work.

### `components.json`

```json
{
  "style": "new-york",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "",
    "css": "src/styles.css",
    "baseColor": "neutral",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "lib": "@/lib",
    "hooks": "@/hooks"
  },
  "iconLibrary": "lucide"
}
```

> Set `"css"` to your global CSS file path. Do **not** change `cssVariables` to `false`.

### Navigator → shadcn CSS variable mapping

Import this after the Navigator imports to wire DFDS tokens into shadcn variables. Create `navigator-shadcn-theme.css` (or copy the block into your global CSS):

```css
:root {
  --radius: 0rem; /* DFDS uses sharp corners */

  /* Surfaces */
  --background: rgb(255 255 255);
  --foreground: rgb(0 43 69);       /* blue-900 */
  --card: rgb(255 255 255);
  --card-foreground: rgb(0 43 69);
  --popover: rgb(255 255 255);
  --popover-foreground: rgb(0 43 69);

  /* Brand */
  --primary: rgb(26 99 245);        /* blue-500 */
  --primary-foreground: rgb(255 255 255);
  --secondary: rgb(229 244 255);    /* blue-50 */
  --secondary-foreground: rgb(0 43 69);
  --accent: rgb(235 255 100);       /* safety-yellow */
  --accent-foreground: rgb(0 43 69);

  /* Neutral */
  --muted: rgb(227 234 237);        /* grey-100 */
  --muted-foreground: rgb(77 94 107);
  --border: rgb(199 209 214);       /* grey-200 */
  --input: rgb(199 209 214);
  --ring: rgb(75 170 255);          /* blue-400 */

  /* Feedback */
  --destructive: rgb(225 37 25);
  --destructive-foreground: rgb(255 255 255);
  --success: rgb(20 159 101);
  --success-foreground: rgb(255 255 255);
  --warning: rgb(193 155 6);
  --warning-foreground: rgb(79 63 3);
}

.dark {
  --background: rgb(0 43 69);
  --foreground: rgb(255 255 255);
  --card: rgb(3 54 109);
  --card-foreground: rgb(255 255 255);
  --popover: rgb(3 54 109);
  --popover-foreground: rgb(255 255 255);
  --primary: rgb(75 170 255);
  --primary-foreground: rgb(0 43 69);
  --secondary: rgb(8 60 166);
  --secondary-foreground: rgb(255 255 255);
  --accent: rgb(235 255 100);
  --accent-foreground: rgb(0 43 69);
  --muted: rgb(60 73 83);
  --muted-foreground: rgb(172 186 195);
  --border: rgb(255 255 255 / 0.12);
  --input: rgb(255 255 255 / 0.2);
  --ring: rgb(75 170 255);
  --destructive: rgb(251 177 181);
  --destructive-foreground: rgb(73 12 8);
}
```

Add to your global CSS after the Navigator imports:

```css
@import "./navigator-shadcn-theme.css";
/* or inline the block above */
```

---

## 4 — DFDS Header (`NavigationMenu`)

The header uses `NavigationMenu` from `@dfds-frontend/compass-ui`. It handles the full DFDS masthead: logo, top nav tabs, optional mega-menu, and aside menu.

### Core pattern

```tsx
import type { NavigationMenuProps } from "@dfds-frontend/compass-ui";
import { NavigationMenu } from "@dfds-frontend/compass-ui";

type TabKey = "HOME" | "ABOUT"; // add one key per top-level tab

const TABS: NavigationMenuProps<TabKey, never>["menuConfig"] = {
  HOME: {
    label: "Home",
    link: "/",
    megaMenu: [],
    asideMenu: [],
  },
  ABOUT: {
    label: "About",
    link: "/about",
    megaMenu: [],
    asideMenu: [],
  },
};
```

### Active tab resolution

```tsx
function resolveActiveTab(pathname: string): TabKey {
  const tabKeys = Object.keys(TABS) as TabKey[];
  for (const key of tabKeys) {
    if (key === "HOME") continue; // HOME is the fallback
    if (pathname.startsWith(TABS[key].link)) return key;
  }
  return "HOME";
}
```

### Render

```tsx
<NavigationMenu
  logoType="regular"     // "regular" | "white" | "compact"
  logoHref="/"
  defaultActiveTab={activeTab}
  menuConfig={TABS}
  actionDispatch={{}}    // pass handlers for action buttons if needed
/>
```

See [references/nextjs-snippets.md](references/nextjs-snippets.md) for the complete Next.js `Header.tsx` using `usePathname`.  
See [references/tanstack-snippets.md](references/tanstack-snippets.md) for the TanStack Start version using `useLocation`.

---

## 5 — Install shadcn/ui form components

```bash
pnpm dlx shadcn@latest add button checkbox form input label select textarea
```

Components land in `src/components/ui/`. They use `--primary`, `--border`, etc. from your Navigator theme — no extra config needed.

---

## 6 — Hide empty mega-menu bar (optional)

If tabs have no mega-menu content, the bar still renders as empty space. Hide it:

```css
@media (min-width: 1024px) {
  nav#mega-menu:has(#mega-menu-left:empty):has(#mega-menu-right:empty) {
    display: none !important;
    padding: 0 !important;
  }
  [role="separator"]:has(
    + nav#mega-menu:has(#mega-menu-left:empty):has(#mega-menu-right:empty)
  ) {
    display: none !important;
  }
}
```

---

## Key rules

| Rule | Why |
|---|---|
| `cssVariables: true` in `components.json` | shadcn must use CSS vars for the theme to apply |
| `@source` directives in CSS | Without them, Tailwind v4 drops Navigator icon size classes |
| SVG `display: inline` reset | Tailwind preflight breaks Navigator icon layout otherwise |
| `--radius: 0rem` | DFDS brand uses sharp corners |
| Never use default shadcn colours | Always override via Navigator theme CSS variables |
