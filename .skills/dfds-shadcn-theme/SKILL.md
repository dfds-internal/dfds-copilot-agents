---
name: dfds-shadcn-theme
description: Set up shadcn/ui with the DFDS Navigator colour theme. Use when adding form components, buttons, inputs, or any shadcn/ui component to a DFDS project and you want them to look like the DFDS design system — sharp corners, DFDS navy/blue palette, and correct typography scale. Covers the full CSS variable map, Tailwind v4 bridge, and DFDS-specific form overrides.
metadata:
  author: dfds-frontend
  version: "1.0"
compatibility: Tailwind CSS v4. shadcn/ui new-york style. cssVariables must be true.
---

# shadcn/ui — DFDS Navigator Theme

Wires shadcn/ui components to the DFDS design system by mapping Navigator semantic colour tokens to shadcn CSS variables. The result: all shadcn components (buttons, inputs, selects, checkboxes, etc.) automatically render in DFDS brand colours with no per-component overrides.

---

## 1 — `components.json` — required config

shadcn must be told to use CSS variables. The `new-york` style is closest to DFDS CVI.

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

> `"css"` must point to your global CSS file. `"cssVariables": true` is non-negotiable — the whole theme depends on it.

---

## 2 — Install shadcn/ui components

```bash
pnpm dlx shadcn@latest init          # initialises shadcn, writes components.json
pnpm dlx shadcn@latest add button checkbox form input label select textarea
```

---

## 3 — Complete theme CSS

Create a `navigator-shadcn-theme.css` file (or paste directly into your global CSS after the Navigator imports). This is the full production-ready theme used in DFDS projects.

```css
/*
 * Navigator-inspired shadcn/ui theme
 * Source values from @dfds-frontend/navigator-styles primitive/semantic tokens.
 *
 * Light mode: direct Navigator token mapping.
 * Dark mode: Navigator deep-blue surfaces + white text.
 */

:root {
  --radius: 0rem; /* DFDS CVI uses sharp corners — no rounding */

  /* ── Surfaces ─────────────────────────────────────────────── */
  --background: rgb(255 255 255);       /* white — form field bg */
  --foreground: rgb(0 43 69);           /* blue-900 / text default */
  --card: rgb(255 255 255);
  --card-foreground: rgb(0 43 69);
  --popover: rgb(255 255 255);
  --popover-foreground: rgb(0 43 69);

  /* ── Brand / action ───────────────────────────────────────── */
  --primary: rgb(26 99 245);            /* blue-500 / primary default */
  --primary-foreground: rgb(255 255 255);
  --secondary: rgb(229 244 255);        /* blue-50 */
  --secondary-foreground: rgb(0 43 69);
  --accent: rgb(235 255 100);           /* safety-yellow-300 / accent primary */
  --accent-foreground: rgb(0 43 69);

  /* ── Neutral UI ───────────────────────────────────────────── */
  --muted: rgb(227 234 237);            /* grey-100 */
  --muted-foreground: rgb(77 94 107);   /* grey-700 / text light */
  --border: rgb(199 209 214);           /* grey-200 / border light */
  --input: rgb(199 209 214);
  --ring: rgb(75 170 255);              /* blue-400 / focus ring */

  /* ── Feedback ─────────────────────────────────────────────── */
  --destructive: rgb(225 37 25);        /* red-600 / danger default */
  --destructive-foreground: rgb(255 255 255);
  --warning: rgb(193 155 6);            /* yellow-600 / warning default */
  --warning-foreground: rgb(79 63 3);
  --success: rgb(20 159 101);           /* green-600 / success default */
  --success-foreground: rgb(255 255 255);
  --info: rgb(26 99 245);               /* blue-500 / info default */
  --info-foreground: rgb(255 255 255);

  /* ── Charts (Navigator palette) ──────────────────────────── */
  --chart-1: rgb(26 99 245);
  --chart-2: rgb(20 159 101);
  --chart-3: rgb(193 155 6);
  --chart-4: rgb(225 37 25);
  --chart-5: rgb(75 170 255);

  /* ── Sidebar ──────────────────────────────────────────────── */
  --sidebar: rgb(255 255 255);
  --sidebar-foreground: rgb(0 43 69);
  --sidebar-primary: rgb(26 99 245);
  --sidebar-primary-foreground: rgb(255 255 255);
  --sidebar-accent: rgb(235 255 100);
  --sidebar-accent-foreground: rgb(0 43 69);
  --sidebar-border: rgb(199 209 214);
  --sidebar-ring: rgb(75 170 255);
}

/* ── Dark mode ────────────────────────────────────────────────── */
/* Navigator-inspired: deep blue surfaces + white text            */
.dark {
  --background: rgb(0 43 69);           /* blue-900 / deep-blue bg */
  --foreground: rgb(255 255 255);
  --card: rgb(3 54 109);                /* blue-800 */
  --card-foreground: rgb(255 255 255);
  --popover: rgb(3 54 109);
  --popover-foreground: rgb(255 255 255);

  --primary: rgb(75 170 255);           /* blue-400 — lighten for dark bg */
  --primary-foreground: rgb(0 43 69);
  --secondary: rgb(8 60 166);           /* blue-700 */
  --secondary-foreground: rgb(255 255 255);
  --accent: rgb(235 255 100);           /* safety-yellow stays */
  --accent-foreground: rgb(0 43 69);

  --muted: rgb(60 73 83);               /* grey-800 */
  --muted-foreground: rgb(172 186 195); /* grey-300 */
  --border: rgb(255 255 255 / 0.12);
  --input: rgb(255 255 255 / 0.2);
  --ring: rgb(75 170 255);

  --destructive: rgb(251 177 181);      /* red-200 for dark contrast */
  --destructive-foreground: rgb(73 12 8);
  --warning: rgb(255 221 102);          /* yellow-300 */
  --warning-foreground: rgb(114 91 4);
  --success: rgb(141 221 168);          /* green-300 */
  --success-foreground: rgb(5 108 74);
  --info: rgb(117 190 255);             /* blue-300 */
  --info-foreground: rgb(0 43 69);

  --chart-1: rgb(117 190 255);
  --chart-2: rgb(141 221 168);
  --chart-3: rgb(255 221 102);
  --chart-4: rgb(253 109 104);
  --chart-5: rgb(209 235 255);

  --sidebar: rgb(3 54 109);
  --sidebar-foreground: rgb(255 255 255);
  --sidebar-primary: rgb(75 170 255);
  --sidebar-primary-foreground: rgb(0 43 69);
  --sidebar-accent: rgb(8 60 166);
  --sidebar-accent-foreground: rgb(255 255 255);
  --sidebar-border: rgb(255 255 255 / 0.12);
  --sidebar-ring: rgb(75 170 255);
}

/* ── Tailwind v4 bridge ───────────────────────────────────────── */
/* Expose CSS vars as Tailwind colour tokens (bg-primary, text-foreground, etc.) */
@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
  --color-warning: var(--warning);
  --color-warning-foreground: var(--warning-foreground);
  --color-success: var(--success);
  --color-success-foreground: var(--success-foreground);
  --color-info: var(--info);
  --color-info-foreground: var(--info-foreground);
  --color-chart-1: var(--chart-1);
  --color-chart-2: var(--chart-2);
  --color-chart-3: var(--chart-3);
  --color-chart-4: var(--chart-4);
  --color-chart-5: var(--chart-5);
  --color-sidebar: var(--sidebar);
  --color-sidebar-foreground: var(--sidebar-foreground);
  --color-sidebar-primary: var(--sidebar-primary);
  --color-sidebar-primary-foreground: var(--sidebar-primary-foreground);
  --color-sidebar-accent: var(--sidebar-accent);
  --color-sidebar-accent-foreground: var(--sidebar-accent-foreground);
  --color-sidebar-border: var(--sidebar-border);
  --color-sidebar-ring: var(--sidebar-ring);
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
}
```

---

## 4 — DFDS CVI overrides (form typography)

These unlayered rules enforce Navigator typography scale on form elements. Add them **after** the theme block, in the same CSS file or imported after it. They are intentionally **not** inside `@layer` so they beat Tailwind utility classes.

```css
/* Page canvas: light grey (grey-50), form fields stay white via --background */
body {
  background-color: rgb(242 246 248); /* grey-50 */
}

/* Labels: Navigator body-md medium */
label {
  font-size: 0.875rem;
  font-weight: 500;
}

/* Form description + error messages: Navigator body-xs */
[id$="-form-item-description"],
[id$="-form-item-message"] {
  font-size: 0.75rem;
  font-weight: 400;
  margin-top: 2px;
}

/* Remove bottom margin on the input directly before an error message */
:has(+ [id$="-form-item-message"]) {
  margin-bottom: 0px;
}

/* Radix Select inserts a hidden native <select> between the trigger and the
   message, so targeting by ID suffix is needed */
button[id$="-form-item"] {
  margin-bottom: 0px;
}
```

---

## 5 — Import order in your global CSS

```css
/* 1. Tailwind base */
@import "tailwindcss";

/* 2. Navigator tokens + fonts (must come before the theme) */
@import "@dfds-frontend/navigator-styles/tailwind/v4";
@import "@dfds-frontend/navigator-styles/fonts";
@import "@dfds-frontend/navigator-components/styles";

/* 3. shadcn/ui Navigator theme */
@import "./navigator-shadcn-theme.css";
```

> If you don't use `@dfds-frontend` Navigator packages, skip steps 2 and import the theme directly after Tailwind.

---

## 6 — Token reference

| Token | Light value | Usage |
|---|---|---|
| `--primary` | `rgb(26 99 245)` | Button bg, checkbox checked, focus states |
| `--primary-foreground` | `rgb(255 255 255)` | Text on primary bg |
| `--foreground` | `rgb(0 43 69)` | Body text, labels |
| `--background` | `rgb(255 255 255)` | Input / card background |
| `--muted` | `rgb(227 234 237)` | Disabled state bg |
| `--muted-foreground` | `rgb(77 94 107)` | Placeholder text, helper text |
| `--border` | `rgb(199 209 214)` | Input borders |
| `--ring` | `rgb(75 170 255)` | Focus ring |
| `--destructive` | `rgb(225 37 25)` | Error state, destructive button |
| `--accent` | `rgb(235 255 100)` | Safety-yellow accent (use sparingly) |
| `--radius` | `0rem` | All components — no rounding |

---

## 7 — Usage in Tailwind classes

After adding `@theme inline`, you can use these as Tailwind utilities:

```tsx
// Text colours
<p className="text-foreground">Body text</p>
<p className="text-muted-foreground">Helper text</p>

// Backgrounds
<div className="bg-background">Form card</div>
<div className="bg-muted">Disabled area</div>

// Borders
<div className="border border-border">Outlined box</div>

// Primary button equivalent (shadcn Button variant="default" does this automatically)
<button className="bg-primary text-primary-foreground hover:bg-primary/90">
  Submit
</button>

// Feedback colours
<span className="text-destructive">Error message</span>
<span className="text-success">Success message</span>
<span className="text-warning">Warning message</span>
```

---

## Key rules

| Rule | Why |
|---|---|
| `cssVariables: true` in `components.json` | Without this, shadcn uses hardcoded Tailwind colours — theme won't apply |
| `--radius: 0rem` | DFDS CVI uses sharp corners on all components |
| CVI overrides are unlayered | They must beat Tailwind `@layer utilities` to override font sizing |
| `@theme inline` block | Required in Tailwind v4 to expose CSS vars as `bg-*`/`text-*` tokens |
| Never override `--primary` per-component | Change the token once, all components update |
