# INU - Design Guide

> "Apple Glassmorphism x Toss Simplicity" — Clear and smooth UI, like light rising from the deep ocean

- Feature Spec → [1-PLAN.md](1-PLAN.md)
- Screen Design → [2-WIREFRAMES.md](2-WIREFRAMES.md)

---

## 1. Design Philosophy & Principles

### 1.1 Fusion Concept: Glassmorphism + Simplicity

INU's design sits at the intersection of two philosophies.

**From Apple Glassmorphism:**
- Layered depth through blur and transparency
- Frosted glass surfaces floating above the background
- Subtle light refraction and physical dimensionality

**From Toss Simplicity:**
- Radical elimination of decorative elements
- Information hierarchy expressed only through size and weight
- Generous whitespace with mathematical spacing
- Clean sans-serif Korean typography

**The Intersection:**
Glassmorphism delivers *depth and sophistication*, while Toss Simplicity delivers *information clarity*. Glass panels float over Ocean-gradient backgrounds, but the content within those panels is radically clean.

### 1.2 Rising Seal Metaphor → Design Mapping

| Metaphor | Design Element |
| -------- | -------------- |
| **Dive** (Deep Ocean) | Dark mode backgrounds, Ocean color family, layered depth |
| **Rise** (Rising Light) | Sunrise accents, CTA highlights, progress/completion indicators |
| **Surface** (Water Surface) | Glassmorphic cards — translucent boundary between ocean and sky |
| **Seal** (Mascot) | Warm and friendly coaching presence — bridge between serious (goal tracking) and approachable (mascot) |

### 1.3 Four Design Principles — Visual Interpretation

| Principle | Visual Interpretation |
| --------- | --------------------- |
| **Simplicity** | Max 3 levels of visual hierarchy per screen. No decorative elements. Compact card dashboard. |
| **Guidance** | Distinctive visual treatment for coaching bubbles (`🦭 ◁`). Current phase emphasis on Phase Stepper. |
| **Clarity** | Clear typographic hierarchy. Instant recognition via category colors. Explicit layer labels in tree view. |
| **Flow** | Smooth 150–200ms fade transitions. Spatial flow through glass layers. |

### 1.4 Design Anti-Patterns

Things to never do:

- No thick borders or hard outlines (conflicts with glass metaphor)
- No more than 2 accent colors per screen
- No decorative gradients on interactive elements
- No excessive animations: scale, bounce, spring, confetti are all prohibited
- Never use color alone to indicate state (always pair with icon/text)
- No unnecessary decorative icons or illustrations

---

## 2. Color System

### 2.1 Brand Palette — Ocean + Sunrise

#### Ocean (Primary Base)

| Token | Hex | Usage |
| ----- | --- | ----- |
| Ocean-950 | `#0A1628` | Dark mode background base (deepest ocean floor) |
| Ocean-900 | `#0F2137` | Dark mode card/panel background |
| Ocean-800 | `#142E47` | Dark mode input/nested element background |
| Ocean-700 | `#1A3D5C` | Dark mode borders, inactive elements |
| Ocean-600 | `#1F4F73` | Light mode secondary text |
| Ocean-500 | `#2A6B8E` | Primary brand color (surface shimmer) |
| Ocean-400 | `#3D8DB0` | Light mode tertiary text, Info state |
| Ocean-300 | `#5FAFC8` | Dark mode secondary text |
| Ocean-200 | `#8ECFE0` | Sea Foam decorative |
| Ocean-100 | `#C4E8F0` | Sea Mist |
| Ocean-50 | `#EAF6FA` | Light mode background tint |

#### Sunrise (Accent)

| Token | Hex | Usage |
| ----- | --- | ----- |
| Sunrise-900 | `#7A3B1E` | Deep amber |
| Sunrise-800 | `#9C4D25` | Dark amber |
| Sunrise-700 | `#B8612D` | Warm amber |
| Sunrise-600 | `#D47834` | CTA hover |
| Sunrise-500 | `#E8923F` | **Golden Hour — Primary accent** (CTA, active tabs, selected state) |
| Sunrise-400 | `#F0AD5E` | Dark mode primary accent |
| Sunrise-300 | `#F5C88A` | Soft gold |
| Sunrise-200 | `#FAE0B5` | Dawn Glow |
| Sunrise-100 | `#FDF0DB` | Light gold |
| Sunrise-50 | `#FFF8EE` | Warm white tint |

#### Coral (Secondary Accent)

| Token | Hex | Usage |
| ----- | --- | ----- |
| Coral-500 | `#E87461` | Celebration/completion moments (Phase complete, Goal complete) |
| Coral-400 | `#F09080` | Soft coral |
| Coral-300 | `#F5ADA2` | Light coral |

### 2.2 Semantic Color Tokens

| Token | Purpose | Light Mode | Dark Mode |
| ----- | ------- | ---------- | --------- |
| `--bg-primary` | Page background | `#FFFFFF` | `#0A1628` |
| `--bg-secondary` | Card/panel background | `#EAF6FA` | `#0F2137` |
| `--bg-tertiary` | Nested card/section | `#F5F8FA` | `#142E47` |
| `--bg-glass` | Glassmorphic surface | `rgba(255,255,255,0.72)` | `rgba(15,33,55,0.65)` |
| `--bg-overlay` | Modal backdrop | `rgba(10,22,40,0.45)` | `rgba(0,0,0,0.55)` |
| `--text-primary` | Primary text | `#0F2137` | `#F0F4F7` |
| `--text-secondary` | Descriptions, hints | `#1F4F73` | `#5FAFC8` |
| `--text-tertiary` | Placeholders, disabled | `#3D8DB0` | `#1F4F73` |
| `--text-on-accent` | Text on accent buttons | `#FFFFFF` | `#FFFFFF` |
| `--accent-primary` | CTA, active tabs | `#E8923F` | `#F0AD5E` |
| `--accent-hover` | CTA hover | `#D47834` | `#E8923F` |
| `--border-default` | Card borders | `rgba(42,107,142,0.12)` | `rgba(93,175,200,0.15)` |
| `--border-glass` | Glass card borders | `rgba(255,255,255,0.25)` | `rgba(255,255,255,0.08)` |
| `--border-selected` | Selected node/card | `#E8923F` | `#F0AD5E` |
| `--shadow-card` | Card shadow | `0 2px 8px rgba(10,22,40,0.08)` | `0 2px 8px rgba(0,0,0,0.3)` |
| `--shadow-card-hover` | Card hover | `0 4px 12px rgba(10,22,40,0.10)` | `0 4px 12px rgba(0,0,0,0.35)` |
| `--shadow-modal` | Modal | `0 8px 32px rgba(10,22,40,0.2)` | `0 8px 32px rgba(0,0,0,0.5)` |

### 2.3 Category Colors

> Synced with [1-PLAN.md §6.1](1-PLAN.md) category system.

| Category | Icon | Color Name | Light Hex | Dark Hex (+15% lightness) |
| -------- | ---- | ---------- | --------- | ------------------------- |
| Exercise | 💪 | Sea Foam | `#5C9E9E` | `#6EB3B3` |
| Study | 📚 | Amber Sand | `#C9A86C` | `#D4B87C` |
| Career | 💼 | Slate Blue | `#5E7B8B` | `#7090A0` |
| Finance | 💰 | Terracotta | `#B87C6B` | `#C89080` |
| Relationships | 💕 | Dusty Violet | `#7B6B8D` | `#907FA2` |
| Hobbies | 🎨 | Sage | `#6B9B7A` | `#80B08E` |
| Habits | 🧘 | Soft Blue Grey | `#8FA3B0` | `#A0B5C0` |
| Reading | 📖 | Warm Sand | `#A68B5B` | `#B89E70` |
| Other | ✨ | Muted Lavender Grey | `#9B9EB0` | `#AEB0C2` |

Category colors are used for card left accents, weekly strip dots, tree node headers, and emotion chart lines. When used as background tints: opacity 10% (Light) / 8% (Dark).

### 2.4 Status / Feedback Colors

| Usage | Light | Dark |
| ----- | ----- | ---- |
| Success (toast, completion) | `#34A853` | `#4CC970` |
| Info (toast) | `#2A6B8E` | `#3D8DB0` |
| Warning (toast) | `#E8923F` | `#F0AD5E` |
| Error/Danger (toast, delete) | `#E84855` | `#FF6B75` |
| Essential (Priority) | `#E84855` | `#FF6B75` |
| Optional (Priority) | `#3D8DB0` | `#5FAFC8` |

### 2.5 Emotion Colors

| Emotion | Emoji | Color |
| ------- | ----- | ----- |
| Good | 😊 | `#4CC970` |
| Neutral | 😐 | `#3D8DB0` |
| Tough | 😣 | `#8B7FBF` |
| Motivated | 🔥 | `#E8923F` |

### 2.6 Gradients

| Name | Value | Usage |
| ---- | ----- | ----- |
| Ocean Gradient (Light) | `linear-gradient(180deg, #EAF6FA 0%, #FFFFFF 100%)` | Light mode page background |
| Ocean Gradient (Dark) | `linear-gradient(180deg, #0A1628 0%, #0F2137 100%)` | Dark mode page background |
| Sunrise Gradient | `linear-gradient(135deg, #E8923F, #E87461)` | Landing CTA, celebration moments (use sparingly) |
| Glass Tint (Light) | `linear-gradient(135deg, rgba(255,255,255,0.15), rgba(255,255,255,0.05))` | Subtle highlight on glass cards |
| Glass Tint (Dark) | `linear-gradient(135deg, rgba(255,255,255,0.06), rgba(255,255,255,0.02))` | Dark mode glass highlight |
| Progress Fill | `linear-gradient(90deg, #2A6B8E, #E8923F)` | Progress bar fill (Ocean → Sunrise) |

---

## 3. Typography

### 3.1 Font Selection

| Usage | Font | Rationale |
| ----- | ---- | --------- |
| **Primary** | **Pretendard** | The standard Korean web font used by Toss. Optimized for screen readability, geometric clarity. Covers Korean + Latin + numerals. Free/open-source. |
| **Monospace** | **JetBrains Mono** | For progress percentages and data values. |
| **Fallback** | `Pretendard, -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif` | |

### 3.2 Type Scale

Based on a 4px grid.

| Token | Size | Line Height | Weight | Usage |
| ----- | ---- | ----------- | ------ | ----- |
| `display` | 32px | 40px | 700 | Landing hero headline |
| `h1` | 24px | 32px | 700 | Page titles |
| `h2` | 20px | 28px | 600 | Section headers |
| `h3` | 17px | 24px | 600 | Card titles ("💪 Running"), modal headers |
| `body-lg` | 16px | 24px | 400 | Primary body text, input text |
| `body` | 15px | 22px | 400 | Default body text, descriptions |
| `body-sm` | 14px | 20px | 400 | Secondary text |
| `caption` | 13px | 18px | 400 | Timestamps, badges, helper text |
| `caption-sm` | 12px | 16px | 500 | Labels ("🔴 Essential"), zoomed-out tree node labels |
| `overline` | 11px | 16px | 600 | Phase step labels, category tags |

### 3.3 Font Weights

| Weight | Usage |
| ------ | ----- |
| 400 (Regular) | Body text, descriptions, inputs |
| 500 (Medium) | Badges, labels, coaching messages, tab labels |
| 600 (SemiBold) | h2, h3, button text, section titles |
| 700 (Bold) | display, h1, coaching bubble emphasis |

### 3.4 Korean Typography Rules

```css
/* Headings: slightly tighter */
letter-spacing: -0.01em;

/* Body: default */
letter-spacing: 0;

/* Korean line breaking: prevent mid-syllable breaks */
word-break: keep-all;

/* Rendering optimization */
text-rendering: optimizeLegibility;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
```

**Line Clamp Patterns:**

| Element | Max Lines |
| ------- | --------- |
| Card WHY text | 1 line (ellipsis) |
| Coaching message | 3 lines |
| Retrospective timeline preview | 2 lines |
| Card title | 1 line (ellipsis) |

---

## 4. Spacing & Grid

### 4.1 Base Unit

**4px**. All spacing values are multiples of 4px.

### 4.2 Spacing Scale

| Token | Value | Usage |
| ----- | ----- | ----- |
| `space-1` | 4px | Inline icon-text gap, badge padding |
| `space-2` | 8px | Compact padding (tree node internals), small gaps |
| `space-3` | 12px | Default element gap, card section divider padding |
| `space-4` | 16px | Card internal padding, standard element gap |
| `space-5` | 20px | Intra-card section spacing |
| `space-6` | 24px | Card-to-card gap, modal internal padding |
| `space-8` | 32px | Section spacing, modal header/footer padding |
| `space-10` | 40px | Page section gap |
| `space-12` | 48px | Major section dividers |
| `space-16` | 64px | Landing page section gaps |
| `space-20` | 80px | Hero section vertical padding |

### 4.3 Responsive Breakpoints

| Name | Width | Layout |
| ---- | ----- | ------ |
| `xs` (Mobile) | `< 640px` | Single column, bottom sticky elements |
| `sm` | `640px` | Single column, slightly wider cards |
| `md` (Tablet) | `768px` | Full-width cards + collapsible panel toggle |
| `lg` (Desktop) | `1024px` | 60:40 split (Card View), full canvas (Tree View) |
| `xl` | `1280px` | Same as lg with more breathing room |
| `2xl` | `1536px` | Max content width container |

### 4.4 Layout Patterns

#### Card View Split (S-CARD, Desktop ≥1024px)

```
|-- 60% Cards (max-w: 600px) --|-- 40% Schedule (max-w: 400px) --|
|       gap: 24px              |                                  |
```

- Left: Scrollable card list, 24px gap between cards
- Right: Sticky weekly strip + action list
- Divider: 1px border or 24px gap

#### Tree View Canvas (S-TREE, full width)

```
|---------- 100% Canvas ----------|
|  Toolbar: top-left ([+ New Goal])|
|  Zoom: top-right                 |
|  Legend: bottom-left             |
|  Minimap: bottom-right           |
```

- Canvas-to-viewport padding: 16px
- Minimum node spacing: 80px horizontal (Goal nodes), 160px (between categories)

#### Modal Layout

```
|-- overlay (full screen) --|
|    |-- modal card --|     |
|    | max-w: 680px   |     |   ← S-DETAIL
|    | max-w: 480px   |     |   ← S-CHANGE, S-PHASE confirmation
|    | p: 24-32px     |     |
|    | radius: 16px   |     |
```

- Modal padding: 24px sides, 32px top, 24px bottom
- Mobile: full-width, 16px side padding, bottom-sheet style

#### Page Container

- Max content width: `max-w-[1400px]` centered
- Page horizontal padding: 24px (Desktop), 16px (Mobile)
- Header height: 56px (Desktop), 48px (Mobile)
- Sub-nav height: 44px

### 4.5 Component Dimensions

| Element | Value |
| ------- | ----- |
| Goal card border-radius | 12px |
| Goal card internal padding | 16px |
| Tree node min-width | 120px |
| Tree node border-radius | 8px |
| Tree node internal padding | 8–12px |
| Button border-radius | 8px |
| Input border-radius | 8px |
| Modal border-radius | 16px |
| Toast border-radius | 8px |
| Badge/tag border-radius | 6px |

---

## 5. Glassmorphism Effects

### 5.1 Glass Levels

| Level | Usage | BG Opacity (Light) | BG Opacity (Dark) | Blur |
| ----- | ----- | ------------------- | ------------------ | ---- |
| **Glass-1** (Subtle) | Navigation bar, weekly strip | 0.60 | 0.50 | 16px |
| **Glass-2** (Default) | Goal cards, schedule panel | 0.72 | 0.65 | 20px |
| **Glass-3** (Prominent) | Modals, coaching bubbles | 0.85 | 0.78 | 24px |
| **Glass-4** (Solid) | Dropdowns, tooltips, context menus | 0.92 | 0.88 | 12px |

### 5.2 Glass Card (Default — Glass-2)

```css
/* Light Mode */
.glass-card {
  background: rgba(255, 255, 255, 0.72);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: 12px;
  box-shadow:
    0 2px 8px rgba(10, 22, 40, 0.06),
    0 0 1px rgba(10, 22, 40, 0.1);
}

/* Dark Mode */
.glass-card-dark {
  background: rgba(15, 33, 55, 0.65);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 12px;
  box-shadow:
    0 2px 8px rgba(0, 0, 0, 0.25),
    0 0 1px rgba(255, 255, 255, 0.05);
}
```

### 5.3 Modal Overlay

```css
.modal-overlay {
  background: rgba(10, 22, 40, 0.45);    /* Light */
  /* background: rgba(0, 0, 0, 0.55); */ /* Dark */
  backdrop-filter: blur(4px);
}
```

### 5.4 Navigation Bar (Glass-1)

```css
.glass-nav {
  background: rgba(255, 255, 255, 0.80);
  backdrop-filter: blur(16px) saturate(1.2);
  border-bottom: 1px solid rgba(42, 107, 142, 0.08);
  position: sticky;
  top: 0;
  z-index: 50;
}
```

### 5.5 Coaching Bubble Glass (Sunrise Tint)

```css
.coaching-bubble {
  background: rgba(232, 146, 63, 0.08);
  backdrop-filter: blur(8px);
  border: 1px solid rgba(232, 146, 63, 0.15);
  border-radius: 12px;
  padding: 12px 16px;
}
```

The Seal's voice carries Sunrise warmth, visually distinguishing it from the Ocean-toned glass cards.

### 5.6 Tree Node Glass

```css
.tree-node {
  background: rgba(255, 255, 255, 0.85);       /* Light */
  /* background: rgba(15, 33, 55, 0.78); */    /* Dark */
  border: 1px solid rgba(42, 107, 142, 0.12);
  border-radius: 8px;
  box-shadow: 0 1px 4px rgba(10, 22, 40, 0.06);
}

.tree-node-selected {
  border: 2px solid var(--accent-primary);
}

/* Shared WHY node — double border effect */
.tree-node-shared-why {
  border: 2px solid var(--accent-primary);
  box-shadow:
    inset 0 0 0 2px rgba(232, 146, 63, 0.2),
    0 2px 8px rgba(10, 22, 40, 0.08);
}
```

### 5.7 Performance Guidelines

- `backdrop-filter` has GPU cost — apply only to elements within the viewport
- Tree view at zoom-out (50%): simplify node glass to `background: var(--bg-secondary)` without blur
- Mobile: reduce blur from 20px to 12px
- Fallback for browsers without `backdrop-filter`: `background: var(--bg-secondary)` (opaque)

---

## 6. Component Styles

### 6.1 Buttons

| Variant | Usage | Spec |
| ------- | ----- | ---- |
| **Primary (CTA)** | "Start", "Save", "Apply" | `bg: Sunrise-500`, `text: white`, `font: body weight 600`, `padding: 10px 20px`, `radius: 8px`, `hover: bg Sunrise-600`, `cursor: pointer` |
| **Secondary** | "Edit", "Cancel", "View Journey" | `bg: transparent`, `border: 1px solid var(--border-default)`, `text: var(--text-primary)`, `hover: bg Ocean-50/Ocean-800`, `cursor: pointer` |
| **Ghost** | "Re-record", tab switching | `bg: transparent`, `text: var(--text-secondary)`, `hover: bg Ocean-50/Ocean-800`, no border, `cursor: pointer` |
| **Danger** | "Delete" | `bg: transparent`, `border: 1px solid Error`, `text: Error`, `hover: bg rgba(error, 0.08)`, `cursor: pointer` |
| **Icon** | Zoom `[+][-]`, close `✕` | `bg: transparent`, `padding: 8px`, `radius: 6px`, `hover: bg var(--bg-secondary)`, `cursor: pointer` |
| **Dashed** | "+ Add New Goal" card | `border: 1.5px dashed var(--border-default)`, `bg: transparent`, `radius: 12px`, `text: var(--text-secondary)`, `hover: border-color accent`, `cursor: pointer` |

**Button Sizes:**

| Size | Height | Usage |
| ---- | ------ | ----- |
| `sm` | 28px | Inline buttons, compact |
| `md` | 36px | Default |
| `lg` | 44px | Primary CTA |

### 6.2 Cards

| Variant | Usage | Features |
| ------- | ----- | -------- |
| **Goal Card (Active)** | S-CARD in-progress | Glass-2, solid rounded corners, category emoji header, inline action checklist |
| **Goal Card (Completed)** | S-CARD completed goals | `border-style: dashed`, `opacity: 0.7`, no inline actions |
| **Goal Card (Creating)** | Tree view inline creation | Dashed border + shimmer background |
| **Feature Card** | S-LND landing | Glass-2, icon top-center, centered text |
| **Reflection Card** | S-RETRO timeline | Glass-1, left emotion-color accent bar, date+mood header |

### 6.3 Modals

- **Overlay**: `--bg-overlay` + `backdrop-filter: blur(4px)`
- **Container**: Glass-3, `max-w` 680px or 480px, `border-radius: 16px`, `--shadow-modal`
- **Close button**: Top-right, `✕` icon button
- **Internal tab bar**: Underline style
- **Full modal** (S-WEL): No close button, gradient background

### 6.4 Inputs

```css
.input {
  background: var(--bg-secondary);
  border: 1px solid var(--border-default);
  border-radius: 8px;
  padding: 10px 12px;
  font: body;
  color: var(--text-primary);
}

.input:focus {
  border-color: var(--accent-primary);
  outline: none;
  box-shadow: 0 0 0 2px rgba(232, 146, 63, 0.2);
}
```

- **Inline input** (Action add): Embedded within card, no border when unfocused
- **Textarea**: Same styling, `min-height: 80px`, `resize: vertical`

### 6.5 Tabs

All tabs use a unified **underline style**.

| Tab Type | Usage | Underline |
| -------- | ----- | --------- |
| Main nav | `📝 Retrospective` `🎯 Goals` `📅 Calendar` | 2px, `Sunrise-500` |
| Sub toggle | `📋 Cards` / `🌳 Tree` | 2px, `Sunrise-500` |
| Card view tabs | `In Progress` / `Completed Goals` | 2px, `Sunrise-500` |
| Modal tabs | S-DETAIL `Info` `Reflection Notes` `Change History` | 2px, `Sunrise-500` |

- Active: `font-weight: 600` + underline + `var(--text-primary)`
- Inactive: `font-weight: 400` + no underline + `var(--text-secondary)`
- Underline transition: 150ms ease
- `cursor: pointer`

### 6.6 Progress Bar

```css
.progress-bar {
  height: 6px;
  background: var(--bg-tertiary);
  border-radius: 3px;
  overflow: hidden;
}

.progress-bar-fill {
  height: 100%;
  background: linear-gradient(90deg, #2A6B8E, #E8923F);
  border-radius: 3px;
  transition: width 200ms ease;
}
```

- Percentage text: `body-sm` weight 600, right-aligned or inline

### 6.7 Phase Stepper

```css
.stepper-dot-completed {
  background: var(--accent-primary);
  width: 10px;
  height: 10px;
  border-radius: 50%;
}

.stepper-dot-current {
  background: var(--accent-primary);
  width: 12px;
  height: 12px;
  border-radius: 50%;
  box-shadow: 0 0 0 3px rgba(232, 146, 63, 0.2);
}

.stepper-dot-upcoming {
  background: transparent;
  border: 2px solid var(--border-default);
  width: 10px;
  height: 10px;
  border-radius: 50%;
}

.stepper-line-completed { height: 2px; background: var(--accent-primary); }
.stepper-line-upcoming { height: 2px; background: var(--border-default); }
```

### 6.8 Checkboxes

| State | Spec |
| ----- | ---- |
| Unchecked | 18px, `border: 2px solid var(--border-default)`, `radius: 4px`, `cursor: pointer` |
| Checked | `bg: Ocean-500`, white checkmark, `border: none` |
| Checked text | `text-decoration: line-through`, `color: var(--text-tertiary)` |
| Shared action | `🔗` 12px to the right |
| Scheduled | `📅` 12px to the right |

### 6.9 Toast Notifications

- Position: top-center, stacked
- Container: Glass-4, `padding: 12px 16px`, `radius: 8px`, `--shadow-card`
- Left accent bar: 3px colored bar (Success=green, Info=blue, Warning=gold, Error=red)
- Auto-dismiss: Success 3s, Info 4s, Warning 5s, Error manual close
- Appear: fade-in 150ms, Disappear: fade-out 150ms

### 6.10 Badges / Tags

| Type | Spec |
| ---- | ---- |
| Category tag | Emoji + text, `bg: category-color/10%`, `text: category-color`, `radius: 6px`, `padding: 2px 8px` |
| Essential badge | `🔴 Essential`, `bg: error/10%`, `text: error` |
| Optional badge | `⚪ Optional`, `bg: var(--bg-tertiary)`, `text: var(--text-secondary)` |
| Connection badge | `🔗 3`, small circular badge — used on shared WHY nodes |

### 6.11 Dropdown / Context Menu

- Glass-4, `radius: 8px`, `--shadow-modal`
- Item: `padding: 8px 12px`, emoji + text, `hover: bg var(--bg-secondary)`, `cursor: pointer`
- Divider: 1px `var(--border-default)`, 4px vertical margin

### 6.12 Skeleton Shimmer

```css
.skeleton {
  background: linear-gradient(90deg,
    var(--bg-secondary) 25%,
    var(--bg-tertiary) 50%,
    var(--bg-secondary) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s ease-in-out infinite;
  border-radius: 4px;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

---

## 7. Iconography

### 7.1 Emoji-First Icon System

INU uses native emoji as its primary icon system.

**Rationale:**
- Instant recognition, zero load time, cross-platform
- Warm and friendly feel (consistent with Seal mascot)
- Zero design asset maintenance burden
- Natural visual language for users in their 20s

**Cross-platform consistency:** Use Twemoji as fallback/polyfill, or accept platform-native rendering.

### 7.2 Category Emoji (Fixed 9)

`💪` `📚` `💼` `💰` `💕` `🎨` `🧘` `📖` `✨`

- In cards: 20px
- In tree nodes: 24px
- Compact/inline: 16px
- Always paired with category text label (no standalone emoji — accessibility)

### 7.3 Emotion Emoji (Fixed 4)

`😊 Good` `😐 Neutral` `😣 Tough` `🔥 Motivated`

- Selection UI: 4 equally-sized rounded-rectangle buttons, emoji (28px) + label below
- Selected state: `border: 2px solid emotion-color`, `bg: emotion-color/10%`

### 7.4 Functional Emoji

| Emoji | Usage |
| ----- | ----- |
| 🦭 | Mascot, coaching messages, brand |
| 📝 | Retrospective tab |
| 🎯 | Goals tab, WHY node prefix |
| 📅 | Calendar tab, scheduled action badge |
| 🔗 | Shared actions, Cross-Goal connections |
| ✨ | Refine/elaborate |
| 📍 | Progress indicator |
| 🎉 | Phase/Goal completion |
| ⚠️ | Warning, delete confirmation |
| 📌 | Manual position pin (tree view) |
| 🤔 | WHY Depth question |
| 🌟 | Core WHY reached (5/5) |

### 7.5 Custom UI Icons

Non-emoji UI icons use the **Lucide** library.

- Style: 1.5px stroke, rounded line caps
- Consistent with Pretendard's geometric feel
- Usage: Close (X), Arrows (ChevronLeft/Right), Check, Plus, Trash, Pencil, ZoomIn/ZoomOut, Maximize

### 7.6 Icon Size Scale

| Size | Value | Usage |
| ---- | ----- | ----- |
| `icon-xs` | 14px | Inline badges (🔗, 📅) |
| `icon-sm` | 16px | Compact contexts, action icons |
| `icon-md` | 20px | Default icon size, card headers |
| `icon-lg` | 24px | Tree nodes, section headers |
| `icon-xl` | 32px | Emotion selection buttons |
| `icon-2xl` | 48px | Landing page features, empty states, 404 |

---

## 8. Motion & Animation

### 8.1 Minimalist Principle

INU's motion **should not draw attention to itself**. It should be minimal enough that the user never consciously notices the animations.

**Allowed:**
- `opacity` (fade)
- `background-color`, `border-color`, `color` transitions
- `cursor: pointer`
- Subtle `box-shadow` change (hover)
- `outline` / focus ring
- `width` transition (progress bar)
- Skeleton shimmer (only allowed repeating animation)

**Prohibited:**
- `transform: scale()` of any kind
- `transform: translateY()` (slide-up/down)
- `spring`, `bounce` easing
- Confetti, particles, sparkle effects
- Excessive shadow changes
- Transitions exceeding 300ms

### 8.2 Easing & Duration

**Easing:** `ease` or `cubic-bezier(0.4, 0, 0.2, 1)` — applied uniformly across all transitions.

| Token | Value | Usage |
| ----- | ----- | ----- |
| `duration-instant` | 100ms | Checkbox, hover color, focus ring |
| `duration-fast` | 150ms | Tab underline, button hover, toast exit |
| `duration-normal` | 200ms | View fade, card hover shadow, progress bar, toast entrance |
| `duration-moderate` | 300ms | Modal overlay+card fade-in, dark mode transition |

### 8.3 Transition Patterns

| Interaction | Treatment |
| ----------- | --------- |
| Main section switch (Retrospective ↔ Goals ↔ Calendar) | Current fade-out 150ms → new section fade-in 150ms (total 300ms) |
| Goals sub-toggle (Cards ↔ Tree) | Current fade-out 100ms → new view fade-in 100ms (total 200ms) |
| Modal open | Overlay fade-in 150ms + modal fade-in 150ms (simultaneous, total ~200ms) |
| Modal close | Reverse fade-out 150ms |
| Checkbox toggle | Instant color transition (100ms) |
| Hover | `cursor: pointer` + subtle `box-shadow` change (150ms) |
| Progress bar | `width` transition 200ms ease |
| Tree Auto-Layout | Existing node repositioning 200ms + new node fade-in 200ms |
| Card inline action check | Checkbox 100ms → progress bar width 200ms → right panel instant sync |
| Weekly strip date switch | Date highlight 100ms → action list fade transition 100ms |
| Mobile summary bar toggle | Content fade-in/out 200ms |

### 8.4 prefers-reduced-motion Support

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 9. Dark Mode

### 9.1 Implementation Strategy

- CSS custom properties (§2.2 semantic tokens) + Tailwind CSS 4 `dark:` variant
- User setting: **System** (default) / Light / Dark — selected in S-SET settings page
- Storage: `localStorage` + Supabase profile
- Flash prevention: Inline `<script>` in `<head>` to set theme class before paint

### 9.2 Element-by-Element Transformation

| Element | Light | Dark |
| ------- | ----- | ---- |
| Page background | `#FFFFFF` | `#0A1628` |
| Background gradient | `Ocean-50 → white` | `Ocean-950 → Ocean-900` |
| Glass card surface | `rgba(255,255,255,0.72)` | `rgba(15,33,55,0.65)` |
| Modal surface | `rgba(255,255,255,0.85)` | `rgba(15,33,55,0.78)` |
| Input background | `Ocean-50` | `Ocean-800` |
| Overlay | `rgba(10,22,40,0.45)` | `rgba(0,0,0,0.55)` |
| Primary text | `#0F2137` | `#F0F4F7` (warm off-white, not pure white) |
| Secondary text | `Ocean-600` | `Ocean-300` |
| Placeholder text | `Ocean-400` | `Ocean-600` |
| Default border | `rgba(42,107,142,0.12)` | `rgba(93,175,200,0.15)` |
| Glass border | `rgba(255,255,255,0.25)` | `rgba(255,255,255,0.08)` |
| Selected border | `Sunrise-500` | `Sunrise-400` (slightly brighter) |
| Card shadow | `rgba(10,22,40, 0.06–0.12)` | `rgba(0,0,0, 0.25–0.4)` |
| Category colors | Original hex | +15% lightness |
| Category background tint | 10% opacity | 8% opacity |
| Primary accent | `Sunrise-500` | `Sunrise-400` |

Subtle inner glow for dark mode cards:
```css
box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.04);
```

### 9.3 Special Cases

| Element | Dark Mode Treatment |
| ------- | ------------------- |
| Tree view canvas | Dark background + subtle grid dots. Nodes become the primary light sources |
| Emotion chart | Dots/lines slightly brighter. Chart grid at `rgba(255,255,255,0.06)` |
| Progress bar | Gradient slightly more vivid |
| Skeleton shimmer | `Ocean-900` → `Ocean-800` gradient |
| Landing hero | Gradient reversed — darker at bottom, lighter at top (looking up from the ocean floor toward the surface) |

### 9.4 Mode Transition

- Transition `background-color`, `color`, `border-color` with 300ms fade
- Do not apply transition to `backdrop-filter` (performance)

---

## 10. Illustration & Brand Expression

### 10.1 Seal Mascot (🦭) Usage Guidelines

| Context | Expression | Size |
| ------- | ---------- | ---- |
| Coaching bubble | Emoji 🦭 + speech triangle `◁` | 24px |
| Empty states | Emoji 🦭 + guidance text | 48px |
| Landing hero | Custom illustration (looking upward — Rising metaphor) | ~200px |
| 404 page | Emoji 🦭 + ❓ | 48px |
| Welcome modal | Emoji 🦭 | 48px |
| AI loading | Emoji 🦭 + shimmer | 24px |

### 10.2 Coaching Bubble Component

```
Layout:
  🦭 [24px] [4px gap] ◁ [Glass bubble + message text]

Bubble:
  - background: §5.5 coaching-bubble glass spec
  - border-radius: 12px
  - padding: 12px 16px
  - max-width: 400px (desktop), full-width - 60px (mobile)
  - font: body, weight 400
  - color: var(--text-primary)

Triangle (◁):
  - CSS triangle, pointing toward the seal
  - 8px wide, 12px tall
  - Same background color as bubble

Loading:
  - Skeleton shimmer inside bubble (seal emoji stays visible)

AI failure fallback:
  - Same visual appearance, rule-based message replaces AI message
  - No indication that it's a fallback (seamless substitution)
```

### 10.3 Empty State Pattern

All empty states follow the same structure:

```
[Centered vertically and horizontally]
  🦭 (48px)
  [16px gap]
  "Primary message" (h2, weight 600)
  "Secondary message" (body, weight 400, --text-secondary)
  [24px gap]
  [CTA Button — Primary]
```

| Screen | Primary | Secondary | CTA |
| ------ | ------- | --------- | --- |
| Card View | "No goals yet" | "Want to create your first goal?" | [+ Create Goal] |
| Tree View | "Want to create your first goal?" | Coaching bubble: "Your tree starts here!" | [▶ + New Goal] |
| Retrospective | "No records yet" | "A small record is the start of self-understanding." | [Start First Record] |

### 10.4 Celebration Moments

On Phase/Goal completion:

- 🎉 emoji + congratulatory message text (`h2`, weight 700, centered)
- Coaching bubble with encouraging message
- **No animation** — expressed cleanly through text and emoji only

### 10.5 Brand Voice (UI Copy Tone)

| Attribute | Guideline |
| --------- | --------- |
| **Tone** | Warm, supportive, conversational Korean (해요체 / polite informal) |
| **Length** | Always concise. Max 2 sentences for coaching messages |
| **Questions** | "What would you like to start?", "How was your day?" |
| **Encouragement** | "You're doing well!", "You made it to the end." |
| **Information** | "This phase took 10 days" |
| **Guidance** | "Let's start with ~ first" |

### 10.6 Illustration Guidelines (Future Custom Art)

- Style: Flat illustration with subtle gradients, Ocean/Sunrise palette
- Seal character: Round and simple. Expressive eyes, simple flippers without detail
- Poses: Diving, rising, floating, celebrating, thinking
- Use cases: Landing hero, onboarding, major achievements, marketing

---

*Document version: v1.0*
*Last modified: January 31, 2026*
*v1.0: Initial design guide based on 2-WIREFRAMES.md v4.4. Apple Glassmorphism + Toss Simplicity fusion concept. Ocean + Sunrise palette. Light/Dark mode. Minimalist motion principles.*
