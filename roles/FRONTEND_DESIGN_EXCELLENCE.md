# FRONTEND_DESIGN_EXCELLENCE.md

> **[v6.16] THE SINGLE CANON OF TASTE TOKENS** (colour, elevation, shadows, status colours, typography, icons, buttons). Other files reference this one, they do not duplicate it. Related canons: geometry — `roles/LAYOUT_INVARIANTS.md`; hero composition — `roles/HERO_ARCHETYPES.md`; ambition/MICRO — `roles/MOTION_AMBITION_DIAL.md`; render measurement — `roles/ROLE_QA_VISUAL.md`. §5 (hero) — archetype selection is delegated to HERO_ARCHETYPES; "mockup on the right" is no longer the default.
# Constitution of frontend visual quality
# Mandatory for @FRONTEND, @DESIGN, @DEV on any UI work
# Source: §7+§9 TECH_PASSPORT + @DESIGN golden library + Refactoring UI

> **Principle:** "A mediocre interface is one where you can tell they tried, but didn't know why."
> A good interface is invisible. The user thinks about the task, not the tool.

---

## HOW TO USE

> **Boundary with `roles/VISUAL_CRAFT_CANON.md` (read it first).** This file holds the **patterns** of the two contours (what a card, a status, a table looks like here). The craft canon holds the **physics** that makes any pattern read as expensive rather than gaudy: restraint (§1), one separation method per surface (§2), one light source (§3), the inverse-area chroma law (§4), the modular type scale (§5), optical alignment (§6), the cheapness detector (§9) and **THE FLOOR** (§11 — the fully specified default when there is no concept). Patterns without craft produce a correct screen that still looks cheap. Do not duplicate the craft rules here; reference them.


**@DESIGN** — read before every SPEC and AUDIT. All decisions must comply with this document.
**@FRONTEND** — read before handing off to @DEV. Visual Quality Gate (§6) is a mandatory checklist.
**@DEV** — read §2–§5 before laying out any screen. This is not optional — it is the standard.
**@QA_ARCH** — verify against §6 Visual Quality Gate alongside the usual vectors.

---

## §1. TWO CONTOURS — TWO PHILOSOPHIES

Never mix them. This is the main cause of "stuffiness": a marketing philosophy on operational screens looks theatrical; an operational one on marketing looks dull.

| Contour | Zone | Philosophy | References |
|---------|------|-----------|-----------|
| **Operational** | `/admin/*`, `/app/*` | Desktop App. Density + clarity. The user works here for hours. | Linear, Stripe Dashboard, Vercel Analytics, Google Calendar |
| **Public site** | `/`, landings, promo | Marketing Page. Focus + emotion. The user decides "do I trust this". | Vercel home, Linear home, Stripe.com, Loom |

**Transfer rule:** glass cards, gradient heroes, reveal animations — **public site only**. On operational screens they create visual noise and slow down work.

---

## §2. OPERATIONAL CONTOUR — TASTE RULES

### 2.1. Elevation (depth layers)

An interface without depth is flat and lifeless. Three levels:

```
Level 0: Application background
  background: #F8F9FA (gray.0)
  NOT white — otherwise cards do not read

Level 1: Cards, Paper, main sheets
  background: #ffffff
  border: 1px solid rgba(0,0,0,0.06)  ← mandatory, otherwise the card "floats"
  box-shadow: 0 1px 2px rgba(0,0,0,0.05)  ← minimal

Level 2: Drawer, Modal, Menu.Dropdown, Popover
  background: #ffffff
  box-shadow: multi-layer (see §2.3)
```

**Forbidden:** white background on white background without a border. This is the most common cause of "mush".

### 2.2. Crisp Cards (cards like cut paper)

```tsx
// ✅ Correct
<Paper
  withBorder
  radius="md"
  p="lg"
  style={{
    background: '#fff',
    borderColor: 'rgba(0,0,0,0.06)',
    boxShadow: '0 1px 2px rgba(0,0,0,0.05)',
  }}
>

// ❌ Incorrect — "soapy" shadow on every card
<Paper shadow="md" p="lg">

// ❌ Incorrect — no border, card "floats" on the grey background
<Paper p="lg">
```

**Shadows md/lg/xl** — only for modals, Drawer, Popover. Not for regular cards in the flow.

### 2.3. Multi-layer shadows (Crisp, not "soapy")

```ts
// In theme.ts — replace Mantine default shadows
shadows: {
  xs: '0 1px 2px rgba(0,0,0,0.04)',
  sm: '0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04)',
  md: '0 4px 6px rgba(0,0,0,0.05), 0 2px 4px rgba(0,0,0,0.04)',
  lg: '0 10px 15px rgba(0,0,0,0.04), 0 4px 6px rgba(0,0,0,0.02)',
  xl: '0 20px 25px rgba(0,0,0,0.04), 0 10px 10px rgba(0,0,0,0.02)',
}
// Two layers = crispness. One large blurred layer = "soap".
```

### 2.4. Status colours (Google Calendar pattern)

```tsx
// ✅ Correct — light background + accent left border
<Box
  style={{
    background: 'var(--mantine-color-orange-0)',
    borderLeft: '4px solid var(--mantine-color-orange-5)',
    borderRadius: '0 8px 8px 0',
    padding: '8px 12px',
  }}
>
  <Text c="orange.9" size="sm" fw={500}>Rescheduled</Text>
</Box>

// ❌ Incorrect — heavy solid fill
<Badge color="orange" variant="filled">Rescheduled</Badge>
// (appropriate only as a small badge, not as a status card)
```

**Status system (unified across the entire project):**
- 🟢 Success / Active / Completed: `green.0` bg + `green.5` border + `green.9` text
- 🔵 Info / In progress: `blue.0` + `blue.5` + `blue.9`
- 🟡 Pending / Warning: `yellow.0` + `yellow.5` + `yellow.9`
- 🔴 Error / Cancelled / Critical: `red.0` + `red.5` + `red.9`
- ⚫ Inactive / Archive: `gray.0` + `gray.4` + `gray.7`

### 2.5. Micro-typography (Data Hierarchy)

This is what distinguishes a "premium" interface from a "cheap" one. Four levels — always:

```tsx
// Level 1: Screen / section heading
<Text size="lg" fw={600} c="gray.9">Schedule</Text>

// Level 2: Primary data (name, amount, status)
<Text size="sm" fw={500} c="gray.9">John Smith</Text>

// Level 3: Secondary data (date, phone, category)
<Text size="sm" fw={400} c="gray.7">+44 (999) 123-45-67</Text>

// Level 4: Field labels (label)
<Text size="xs" tt="uppercase" fw={600} c="dimmed"
      style={{ letterSpacing: '0.5px' }}>
  Visit date
</Text>
// Label + value always in <Stack gap={2}>
```

**Forbidden:** everything at the same weight and colour. "Grey on grey" — invisible. "Everything bold" — no hierarchy.

### 2.6. Button discipline

```tsx
// ✅ One primary per screen — the main action
<Button variant="filled">Create booking</Button>

// ✅ Secondary actions
<Button variant="default">Cancel</Button>  // white, grey outline
<Button variant="light">Edit</Button>  // translucent accent
<Button variant="subtle">Details</Button>  // text only

// ✅ Icons in buttons
<IconPlus size={16} stroke={1.5} />  // stroke={1.5} — not thick

// ❌ Multiple filled buttons next to each other — visual conflict
// ❌ Icons stroke={2} or size={24} without reason — crude
```

### 2.7. Ghost Hover (a live interface)

Every clickable element responds to hover. Without this the interface feels "frozen".

```tsx
// Table rows / dialog list / cards
<Table.Tr
  style={{ cursor: 'pointer' }}
  styles={{
    tr: {
      '&:hover': { backgroundColor: 'var(--mantine-color-gray-0)' }
    }
  }}
>

// Transition mandatory
style={{ transition: 'background-color 150ms ease' }}
```

### 2.8. Icons (Tabler — the single standard)

```tsx
import { IconCalendar, IconUser, IconCash } from '@tabler/icons-react'

// In text / tables
<IconCalendar size={16} stroke={1.5} />

// In buttons and ActionMenu
<IconDotsVertical size={16} stroke={1.5} />

// In EmptyState (large)
<IconCalendarOff size={48} stroke={1} />  // stroke={1} — lighter at large size

// In ThemeIcon (coloured)
<ThemeIcon variant="light" color="blue" size="xl" radius="md">
  <IconCalendar size={20} stroke={1.5} />
</ThemeIcon>
```

**Forbidden:** mixing Tabler with other icon libraries without an @ARCH decision.

---

## §3. OPERATIONAL PATTERNS (ready-made solutions)

### 3.1. EmptyState — reference component

```tsx
// Every data screen has an EmptyState. No exceptions.
<Center py={80}>
  <Stack align="center" gap="md">
    <ThemeIcon variant="light" color="gray" size={64} radius="xl">
      <IconCalendarOff size={32} stroke={1} />
    </ThemeIcon>
    <Stack align="center" gap={4}>
      <Text size="lg" fw={600} c="gray.8">No bookings</Text>
      <Text size="sm" c="dimmed" ta="center" maw={280}>
        Create the first booking or wait for an online reservation
      </Text>
    </Stack>
    <Button variant="filled" leftSection={<IconPlus size={16} stroke={1.5} />}>
      Create booking
    </Button>
  </Stack>
</Center>
```

### 3.2. Skeleton — loading reference

```tsx
// Skeleton mirrors content shape — not abstract rectangles
// For a table:
{isLoading ? (
  Array.from({ length: 5 }).map((_, i) => (
    <Table.Tr key={i}>
      <Table.Td><Skeleton height={16} radius="sm" /></Table.Td>
      <Table.Td><Skeleton height={16} w="70%" radius="sm" /></Table.Td>
      <Table.Td><Skeleton height={24} w={80} radius="xl" /></Table.Td>
    </Table.Tr>
  ))
) : /* data */}

// For a card:
<Card withBorder radius="md" p="lg">
  <Group>
    <Skeleton circle height={40} />
    <Stack gap={6} flex={1}>
      <Skeleton height={14} w="60%" radius="sm" />
      <Skeleton height={12} w="40%" radius="sm" />
    </Stack>
  </Group>
</Card>
```

### 3.3. Metric widget (Stripe/Vercel pattern)

```tsx
<Paper withBorder radius="md" p="lg">
  <Stack gap={4}>
    <Group justify="space-between" align="flex-start">
      <Text size="xs" tt="uppercase" fw={600} c="dimmed" style={{ letterSpacing: '0.5px' }}>
        Revenue today
      </Text>
      <ThemeIcon variant="light" color="green" size="sm" radius="md">
        <IconTrendingUp size={14} stroke={1.5} />
      </ThemeIcon>
    </Group>
    <Text size="xl" fw={700} c="gray.9">₽ 84 200</Text>
    <Group gap={4} align="center">
      <IconArrowUp size={14} color="var(--mantine-color-green-6)" stroke={2} />
      <Text size="xs" c="green.6" fw={500}>+12%</Text>
      <Text size="xs" c="dimmed">vs yesterday</Text>
    </Group>
  </Stack>
</Paper>
```

### 3.4. ActionMenu in a table row

```tsx
<Menu shadow="md" width={180} position="bottom-end">
  <Menu.Target>
    <ActionIcon variant="subtle" color="gray" size="sm">
      <IconDotsVertical size={16} stroke={1.5} />
    </ActionIcon>
  </Menu.Target>
  <Menu.Dropdown>
    <Menu.Item leftSection={<IconEdit size={14} stroke={1.5} />}>
      Edit
    </Menu.Item>
    <Menu.Item leftSection={<IconCopy size={14} stroke={1.5} />}>
      Duplicate
    </Menu.Item>
    <Menu.Divider />
    <Menu.Item color="red" leftSection={<IconTrash size={14} stroke={1.5} />}>
      Delete
    </Menu.Item>
  </Menu.Dropdown>
</Menu>
```

### 3.5. Drawer form (right layer)

```tsx
<Drawer
  position="right"
  size="lg"  // md for simple forms, lg for tabs
  title={
    <Group gap="sm">
      <ThemeIcon variant="light" color="blue" size="md" radius="md">
        <IconCalendarPlus size={16} stroke={1.5} />
      </ThemeIcon>
      <Text fw={600} size="md">New booking</Text>
    </Group>
  }
  overlayProps={{ backgroundOpacity: 0.3, blur: 2 }}
>
  {/* Form */}
</Drawer>
```

### 3.6. HoverCard (Zero-Click Context)

```tsx
<HoverCard width={240} shadow="md" withArrow openDelay={300}>
  <HoverCard.Target>
    <Text
      component="span"
      style={{ cursor: 'pointer', textDecoration: 'underline dotted' }}
      c="blue.6"
    >
      John Smith
    </Text>
  </HoverCard.Target>
  <HoverCard.Dropdown>
    <Stack gap="xs">
      <Group gap="sm">
        <Avatar size="sm" radius="xl" color="blue">JS</Avatar>
        <div>
          <Text size="sm" fw={600}>John Smith</Text>
          <Text size="xs" c="dimmed">+44 (999) 123-45-67</Text>
        </div>
      </Group>
      <Divider />
      <Group justify="space-between">
        <Text size="xs" c="dimmed">LTV</Text>
        <Text size="xs" fw={500}>₽ 42 000</Text>
      </Group>
      <Group justify="space-between">
        <Text size="xs" c="dimmed">Next visit</Text>
        <Text size="xs" fw={500}>May 12</Text>
      </Group>
    </Stack>
  </HoverCard.Dropdown>
</HoverCard>
```

---

## §4. PUBLIC SITE — TASTE RULES

### 4.1. Unified background (no "zebra")

```css
/* One background field per page */
.page-bg {
  background: linear-gradient(135deg, #0f0f1a 0%, #1a1a2e 50%, #0d0d1a 100%);
  min-height: 100vh;
}

/* Sections are separated by spacing and cards — NOT by alternating backgrounds */
.section { padding: 80px 0; background: transparent; }
.section-alt { padding: 80px 0; background: transparent; }
/* ❌ .section-alt { background: #1a1a2e } — "zebra" is forbidden */
```

### 4.2. Glass cards (public site only)

```css
.glass-card {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: var(--radius-lg);
  box-shadow:
    0 4px 24px rgba(0, 0, 0, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}
```

### 4.3. Hero pedestal

```css
.hero-mockup-pedestal {
  background: linear-gradient(135deg,
    rgba(var(--accent-rgb), 0.1) 0%,
    rgba(var(--accent-rgb), 0.05) 100%
  );
  border: 1px solid rgba(var(--accent-rgb), 0.2);
  border-radius: var(--radius-xl);
  box-shadow:
    0 0 60px rgba(var(--accent-rgb), 0.15),
    0 20px 60px rgba(0, 0, 0, 0.3);
  padding: 24px;
}
```

### 4.4. Public site tokens (`:root`)

```css
:root {
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 16px;
  --radius-xl: 24px;

  --shadow-sm: 0 2px 8px rgba(0,0,0,0.15);
  --shadow-md: 0 8px 24px rgba(0,0,0,0.2);
  --shadow-lg: 0 20px 60px rgba(0,0,0,0.3);

  --card-glow-hover: 0 0 40px rgba(var(--accent-rgb), 0.2);
  --glow-accent: 0 0 60px rgba(var(--accent-rgb), 0.15);

  --transition-smooth: all 200ms cubic-bezier(0.4, 0, 0.2, 1);
  --max-w-content: 1200px;
}
```

### 4.5. Reveal animations (public site only, transform+opacity only)

```ts
// useReveal.ts
export function useReveal() {
  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            entry.target.classList.add('reveal-visible')
            observer.unobserve(entry.target)  // once is enough
          }
        })
      },
      { threshold: 0.1 }
    )
    document.querySelectorAll('.reveal').forEach(el => observer.observe(el))
    return () => observer.disconnect()
  }, [])
}
```

```css
/* DEPRECATED PATTERN — do not use in the document flow (violates LAYOUT_INVARIANTS §11).
   Current canon: prism-reveal--fade (opacity-only) + motion-islands for transform. */
.reveal-legacy-do-not-use {
  opacity: 0;
  transition: opacity 500ms ease;
}
.reveal-legacy-do-not-use.is-visible {
  opacity: 1;
}
```

**Current reveal (reference implementation):** `prism-reveal--fade`, hook `useRevealOnScroll`, `theme/motion.css` + `motion-islands.css`. See `roles/COMPONENT_REGISTRY.md` §4.

---

## §5. VISUAL LITERACY — REFERENCES FOR EACH SCREEN TYPE

> **[v6.20]** The table below covers OPERATIONAL screen types. The reference for the PUBLIC SITE is the project world: `docs/artifacts/VISUAL_CONCEPT_*` + world recipe `roles/CONCEPT_DNA_LIBRARY.md` (Tier 0 in `roles/ROLE_DESIGN.md`). A public-site screen whose "reference = Linear" violates Law 28.

Before any new screen — name the reference. @DEV must understand **the level**.

| Screen type | Reference | What we take |
|------------|---------|------------|
| Dashboard with metrics | Stripe Dashboard / Vercel Analytics | White cards, trend arrows, sparkline |
| Record table | Linear Issues | Dense rows, hover, status tags on the left |
| Kanban | Linear / Bitrix24 | Fixed columns 280-320px, ghost on DnD |
| Schedule grid | Google Calendar | Status colours, drag-ghost, click → Popover |
| Chat / Inbox | Intercom | 3 columns, bubbles, quick replies, inspector |
| Form in Drawer | Notion / Linear | Clean fields, labels on top, validation inline |
| Entity card | Stripe Customer / Linear Issue | Tabs, summary header, linked tables |
| PWA home | Apple Wallet / Revolut | Ticket with gradient, bottom nav, safe-area |
| Marketing hero | Linear.app / Vercel.com | 7/5 grid, mockup on the right, one H1 |
| Landing sections | Stripe.com | Unified background, cards with border, no "zebra" |

---

## §6. VISUAL QUALITY GATE (mandatory check before @DEV)

@FRONTEND and @DESIGN run this checklist before passing the task to @DEV.
@QA_ARCH verifies this on audit alongside business vectors.

```
OPERATIONAL CONTOUR (admin/app):
□ Application background gray.0 (#F8F9FA), not white
□ Cards white with a thin border (1px rgba(0,0,0,0.06))
□ Shadows xs/sm on cards, md+ only on Drawer/Modal
□ Statuses: light background + left border (not badge-filled across the entire card)
□ 4 typography levels: heading / data / secondary / label
□ Field labels: uppercase, xs, dimmed, letterSpacing
□ One primary CTA per screen
□ Icons Tabler, stroke={1.5}, size=16/18
□ Hover on all clickable elements (transition 150ms)
□ EmptyState with icon + text + CTA on every list
□ Skeleton mirrors content shape (not an abstract rectangle)
□ ActionMenu (three dots) in every table row
□ Drawer for forms, Modal only for confirm
□ Reference product named (Linear/Stripe/etc.)

PUBLIC SITE (marketing):
□ Single page background — no "zebra"
□ Glass only here, not on operational screens
□ Reveal in flow: **opacity-only** (`prism-reveal--fade`) — canon `LAYOUT_INVARIANTS` §11; transform only inside motion island
□ Carousel/autoplay: motion island + fixed height + autoplay only in-viewport
□ Component Map in DESIGN_SPEC — `COMPONENT_REGISTRY.md` §5
□ One H1 per page
□ Hero mockup/pedestal present
□ Tokens from :root — no magic px values in components
□ VISUAL_CONCEPT exists; screen palette/fonts match the world (substitutions are documented)
□ TASTE GATE: no cliché C1–C10 (`roles/VISUAL_CONCEPT_PROTOCOL.md` §4); exactly one signature element present
□ Mantine de-branded (§8.1): headings/radius/shadows/Button — world language, not defaults
```

---

## §7. PROJECT DESIGN TOKEN (fix at the start of each project)

Before the first screen @FRONTEND creates `docs/artifacts/DESIGN_TOKENS_[PROJECT].md`:

```markdown
# DESIGN_TOKENS: [Project name]

## Identity
Feeling: [one word — e.g. "Professional", "Warm", "Bold"]
Reference: [specific product — Linear/Stripe/Notion/Revolut]

## Palette
Primary: [hex] — accent, CTA
Primary-light: [hex] — soft variant
Gray-0: [hex] — application background
Gray-border: [hex] — card border

## Typography
Font: [Inter / Plus Jakarta Sans / other]
Scale: xs=12 sm=14 md=16 lg=18 xl=20 (or custom)

## Radii
sm: 6px | md: 10px | lg: 16px | xl: 24px (or custom)

## Shadows (Crisp)
xs: 0 1px 2px rgba(0,0,0,0.04)
sm: 0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04)

## Contour
Admin/App: light operational zone
Marketing: [dark/light + accent]
```

---

## §8. MANTINE DE-BRANDING — Mantine as engine, not as face [v6.20]

> Acceptance criterion: **the screen is not recognisable as "a Mantine site"**. If you place a screenshot from the Mantine documentation next to our screen — only the behaviour is shared, not the face. Default Mantine blue/radii/shadows on the public site = 🔴 in Visual Quality Gate (§6).

### 8.1 Mandatory theme overrides (minimum for any project)

```ts
// theme.ts — values FROM VISUAL_CONCEPT (project world), not invented
export const theme = createTheme({
  fontFamily: 'var(--font-body)',                     // from the world
  headings: { fontFamily: 'var(--font-display)', fontWeight: '600' },
  primaryColor: 'brand',
  colors: { brand: brandScale }, // 10 steps from --accent of the world (generate-colors or manual)
  defaultRadius: 'md',
  radius: { xs:'…', sm:'…', md:'…', lg:'…', xl:'…' }, // radius language of the world (0 for SWISS/GLOSSY, 16 for SOFT/POP)
  shadows: { /* shadow recipe of the world: "sheet on desk" / hard offset / pedestal */ },
  components: {
    Button:{ styles:{ root:{ /* button language of the world: uppercase/letter-spacing/clip-path/border */ } } },
    Paper:{ styles:{ root:{ /* surface of the world */ } } },
    Input:{ styles:{ input:{ /* field of the world: blank line / instrument / soft frame */ } } },
  },
});
```

Plus `cssVariablesResolver` — pipe world tokens (`--accent`, `--ink`, `--ease-*`) into Mantine variables: tokens live in one place (`index.css` of the world), Mantine consumes them.

### 8.2 World effect kit — a separate layer

`frontend/src/theme/effects.css` — only world effect-kit classes (`.gloss`, `.seal`, `.btn-br`, …) from `roles/CONCEPT_DNA_LIBRARY.md`. Mantine components receive effects via `className`, not inline copy-paste. On RESKIN this file is replaced in full (Swap map, layer 4) — that is "skin separate from skeleton".

### 8.3 Map of underutilised Mantine power (use, do not reinvent)

| Feature | Where it wins |
|---------|--------------|
| Styles API (`styles`/`classNames` on slots) | any component → world language without forking |
| `cssVariablesResolver` + CSS variables | single token source; RESKIN = replace token file |
| Polymorphism `component=`/`renderRoot` | button-link, card-article — without wrapper hacks |
| `Spotlight` | Cmd+K search — ready-made, styleable to the world |
| `Carousel` (embla) | carousels with `LAYOUT_INVARIANTS` §11 contract instead of hand-rolled |
| `NumberFormatter` / `tabular-nums` | money/metrics without "jumping" numbers |
| `Menu`/`HoverCard`/`Popover` layers | z-index from Mantine scale — fits `LAYOUT_INVARIANTS` §12 |
| `Transition`/`Collapse` | enter/exit and accordions without `height:auto` animation |
| `use-move`, `use-resize-observer`, `use-intersection` | drag sliders, in-view autoplay — hooks instead of reinvention |

### 8.4 Public site: right to go headless

For public-site pages with a strong effect-kit it is acceptable: Mantine — behaviour (Modal/Drawer/Carousel/Spotlight), face — own T1-primitives on world tokens. Fixed by @ARCH in the epic in one line; mixing two style design-systems on one screen is forbidden.

---

Reference: `roles/ROLE_DESIGN.md` · `roles/ROLE_FRONTEND.md` · `roles/DOMAIN_STANDARDS.md` · `roles/TECH_PASSPORT_FRONTEND_UI_LOGIC.md` §7+§9 · `roles/TEMPLATE_DESIGN_UX.md` · `roles/CONCEPT_DNA_LIBRARY.md` · `roles/VISUAL_CONCEPT_PROTOCOL.md`
Version: 1.1 | 2026-07-06
