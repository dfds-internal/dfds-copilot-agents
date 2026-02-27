# DFDS Navigator UI — Next.js App Router Snippets

Ready-to-paste code for Next.js 16 App Router projects.

---

## `app/globals.css`

```css
@import "tailwindcss";
@import "@dfds-frontend/navigator-styles/tailwind/v4";
@import "@dfds-frontend/navigator-styles/fonts";
@import "@dfds-frontend/navigator-components/styles";
@import "../styles/navigator-shadcn-theme.css";

/* Required: Tailwind v4 must scan these for Navigator icon size prop values */
@source "../../node_modules/@dfds-frontend/navigator-components/src";
@source "../../node_modules/@dfds-frontend/navigator-icons/dist";
@source "../../node_modules/@dfds-frontend/compass-ui";

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

Uses `usePathname` (Client Component) to resolve the active tab.

```tsx
"use client";

import { usePathname } from "next/navigation";
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
  const pathname = usePathname();
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

## `app/layout.tsx`

```tsx
import type { Metadata } from "next";
import Header from "@/components/Header";
import "./globals.css";

export const metadata: Metadata = {
  title: "My DFDS App",
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <Header />
        {children}
      </body>
    </html>
  );
}
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
    "css": "app/globals.css",
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

## `package.json` — DFDS dependencies to add

```json
{
  "dependencies": {
    "@dfds-frontend/compass-ui": "^5.3.0",
    "@dfds-frontend/navigator-components": "^0.5.2",
    "@dfds-frontend/navigator-icons": "^1.3.1",
    "@dfds-frontend/navigator-styles": "^1.1.0",
    "class-variance-authority": "^0.7.1",
    "clsx": "^2.1.1",
    "tailwind-merge": "^3.3.0"
  }
}
```

---

## Quick-start checklist

- [ ] `.npmrc` configured (see `dfds-npmrc-setup` skill)
- [ ] `pnpm install` succeeds with no 401 errors
- [ ] `globals.css` has all Navigator `@import` and `@source` lines
- [ ] `components.json` has `"cssVariables": true`
- [ ] `Header.tsx` is a Client Component (`"use client"`)
- [ ] Navigator theme CSS variables are imported
- [ ] DFDS header renders with the correct logo and nav tabs
- [ ] shadcn form fields show sharp corners (`--radius: 0rem`) and DFDS blue primary
