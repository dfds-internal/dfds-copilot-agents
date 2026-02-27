# DFDS Navigator UI — TanStack Start Snippets

Ready-to-paste code for TanStack Start + TanStack Router projects.

> **Note:** TanStack Start requires Node 22+ (for `node:sqlite`) and `pnpm` only.

---

## `src/styles.css`

```css
@import "tailwindcss";
@import "@dfds-frontend/navigator-styles/tailwind/v4";
@import "@dfds-frontend/navigator-styles/fonts";
@import "@dfds-frontend/navigator-components/styles";
@import "../themes/shadcn-navigator/navigator-shadcn-theme.css";

/* Required: Tailwind v4 must scan these for Navigator icon size prop values */
@source "../node_modules/@dfds-frontend/navigator-components/src";
@source "../node_modules/@dfds-frontend/navigator-icons/dist";
@source "../node_modules/@dfds-frontend/compass-ui";

body {
  margin: 0;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* SVG inline reset — prevents Tailwind preflight breaking Navigator icon layout */
@layer base {
  svg {
    display: inline;
  }
}

/* Hide empty mega-menu bar */
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

## `src/components/Header.tsx`

Uses `useLocation` from `@tanstack/react-router`.

```tsx
import { useLocation } from "@tanstack/react-router";
import type { NavigationMenuProps } from "@dfds-frontend/compass-ui";
import { NavigationMenu } from "@dfds-frontend/compass-ui";

type TabKey = "HOME" | "ABOUT"; // extend with your own tabs

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

const TAB_KEYS = Object.keys(TABS) as TabKey[];

function resolveActiveTab(pathname: string): TabKey {
  for (const key of TAB_KEYS) {
    if (key === "HOME") continue;
    if (pathname.startsWith(TABS[key].link)) return key;
  }
  return "HOME";
}

export default function Header() {
  const { pathname } = useLocation();
  const activeTab = resolveActiveTab(pathname);

  return (
    <NavigationMenu
      logoType="regular"
      logoHref="/"
      defaultActiveTab={activeTab}
      menuConfig={TABS}
      actionDispatch={{}}
    />
  );
}
```

---

## `src/routes/__root.tsx`

The TanStack Start HTML shell — renders the header and mounts the app.

```tsx
import { HeadContent, Scripts, createRootRoute } from "@tanstack/react-router";
import Header from "../components/Header";
import appCss from "../styles.css?url";

export const Route = createRootRoute({
  head: () => ({
    meta: [
      { charSet: "utf-8" },
      { name: "viewport", content: "width=device-width, initial-scale=1" },
      { title: "My DFDS App" },
    ],
    links: [{ rel: "stylesheet", href: appCss }],
  }),
  shellComponent: RootDocument,
});

function RootDocument({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <head>
        <HeadContent />
      </head>
      <body>
        <Header />
        {children}
        <Scripts />
      </body>
    </html>
  );
}
```

---

## `vite.config.ts`

```typescript
import { defineConfig } from "vite";
import { tanstackStart } from "@tanstack/react-start/plugin/vite";
import viteReact from "@vitejs/plugin-react";
import viteTsConfigPaths from "vite-tsconfig-paths";
import tailwindcss from "@tailwindcss/vite";
import { nitro } from "nitro/vite";
import { fileURLToPath, URL } from "url";

export default defineConfig({
  resolve: {
    alias: {
      "@": fileURLToPath(new URL("./src", import.meta.url)),
    },
  },
  plugins: [
    viteTsConfigPaths({ projects: ["./tsconfig.json"] }),
    tailwindcss(),
    tanstackStart(),
    nitro({
      rollupConfig: {
        external: (id: string) => id.startsWith("node:"),
      },
    }),
    viteReact(),
  ],
});
```

---

## `src/lib/utils.ts`

```typescript
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

---

## `components.json`

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

---

## Key differences from Next.js

| | TanStack Start | Next.js App Router |
|---|---|---|
| Active pathname | `useLocation()` from `@tanstack/react-router` | `usePathname()` from `next/navigation` |
| HTML shell | `__root.tsx` `shellComponent` | `app/layout.tsx` |
| CSS import | `styles.css?url` in `head()` | `import "./globals.css"` in layout |
| Header component | Plain function (no `"use client"`) | Needs `"use client"` directive |
| Dev command | `NODE_OPTIONS=--experimental-sqlite vite dev` | `next dev` |

---

## `package.json` — DFDS + TanStack dependencies

```json
{
  "dependencies": {
    "@dfds-frontend/compass-ui": "^5.3.0",
    "@dfds-frontend/navigator-components": "^0.5.2",
    "@dfds-frontend/navigator-icons": "^1.3.1",
    "@dfds-frontend/navigator-styles": "^1.1.0",
    "@tanstack/react-router": "^1.132.0",
    "@tanstack/react-start": "^1.132.0",
    "@tanstack/router-plugin": "^1.132.0",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "tailwind-merge": "^3.3.0"
  },
  "devDependencies": {
    "@tailwindcss/vite": "^4.1.18",
    "tailwindcss": "^4.1.18",
    "nitro": "3.0.1-alpha.2"
  }
}
```

---

## Quick-start checklist

- [ ] `.npmrc` configured (see `dfds-npmrc-setup` skill)
- [ ] `pnpm install` succeeds with no 401 errors
- [ ] `src/styles.css` has all Navigator `@import` and `@source` lines
- [ ] `components.json` has `"cssVariables": true`
- [ ] `__root.tsx` renders `<Header />` in the shell
- [ ] Navigator theme CSS variables file is created and imported
- [ ] DFDS header renders with logo and nav tabs
- [ ] shadcn form fields show sharp corners and DFDS blue primary
