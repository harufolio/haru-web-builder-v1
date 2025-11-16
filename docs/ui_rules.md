# UI Component Implementation Rules

## Purpose
This document defines strict implementation rules for generating components from `03_integrate_web.json`. All AI-generated components MUST follow these rules without exception.

---

## 1. JSON-First Principle

**RULE:** `03_integrate_web.json` is the single source of truth.
- ✅ Every field in JSON MUST be rendered
- ❌ NEVER add fields not present in JSON
- ❌ NEVER omit fields present in JSON
- ❌ NEVER simplify or abstract JSON data

**Example:**
```json
// JSON has this
{
  "heading": "Data infrastructure that builds itself",
  "buttons": {
    "primary": { "text": "Get Started", "href": "/signup" }
  }
}

// Component MUST render exactly this
<h1>{data.heading}</h1>
<a href={data.buttons.primary.href}>{data.buttons.primary.text}</a>
```

---

## 2. Style Conversion Rules

### 2.1 Font Size Ranges → Tailwind Responsive Classes

**RULE:** Range notation `"60-72px"` means mobile-to-desktop progression.

| JSON Value | Tailwind Classes |
|-----------|------------------|
| `"60-72px"` | `text-6xl md:text-7xl` |
| `"48-56px"` | `text-5xl md:text-6xl` |
| `"36-42px"` | `text-4xl md:text-5xl` |
| `"24-32px"` | `text-2xl md:text-3xl` |
| `"16-18px"` | `text-base md:text-lg` |
| `"14-16px"` | `text-sm md:text-base` |

**Example:**
```json
// JSON
{ "style": { "heading": { "fontSize": "60-72px" } } }

// Component
<h1 className="text-6xl md:text-7xl">...</h1>
```

### 2.2 Colors → Exact Hex Values

**RULE:** Use exact hex colors from JSON, wrap in Tailwind arbitrary values.

```json
// JSON
{ "style": { "heading": { "color": "#FF6635" } } }

// Component (✅ CORRECT)
<h1 className="text-[#FF6635]">...</h1>

// Component (❌ WRONG)
<h1 className="text-orange-500">...</h1>  // Generic color names forbidden
```

### 2.3 Spacing → Tailwind Utilities

| JSON Value | Tailwind Class |
|-----------|---------------|
| `"16px 32px"` | `px-8 py-4` |
| `"24px"` | `p-6` |
| `"80px 0"` | `py-20 px-0` |
| `"0 auto"` | `mx-auto my-0` |

### 2.4 Border Radius → Tailwind Utilities

| JSON Value | Tailwind Class |
|-----------|---------------|
| `"8px"` | `rounded-lg` |
| `"12px"` | `rounded-xl` |
| `"16px"` | `rounded-2xl` |
| `"50%"` | `rounded-full` |

---

## 3. codeHint Handling

**RULE:** `codeHint` fields MUST be inserted verbatim, NO interpretation.

### 3.1 Animation codeHints

```json
// JSON
{
  "visual": {
    "type": "3d-planet-parallax",
    "codeHint": "gsap.to(planet, { x: 15, scrollTrigger: { scrub: true } })"
  }
}

// Component (✅ CORRECT)
React.useEffect(() => {
  if (data.visual?.codeHint) {
    eval(data.visual.codeHint);  // Insert directly
  }
}, [data.visual]);

// Component (❌ WRONG)
React.useEffect(() => {
  // AI interprets codeHint and rewrites it
  gsap.to('.planet', { /* ... */ });  // FORBIDDEN
}, []);
```

### 3.2 Library Detection

**RULE:** Auto-detect required libraries from codeHints, add CDN links.

```typescript
// Scan all sections
const needsGSAP = sections.some(s => 
  s.visual?.codeHint?.includes('gsap') || 
  s.animation?.codeHint?.includes('gsap')
);

const needsThree = sections.some(s => 
  s.visual?.codeHint?.includes('THREE') ||
  s.visual?.type === '3d-canvas-animation'
);

// Add to <head>
if (needsGSAP) {
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js" />
}

if (needsThree) {
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js" />
}
```

---

## 4. Image Handling

### 4.1 Functional Images (Use Exact Path)

**RULE:** Use exact JSON path if image is functional (3D texture, video thumbnail, diagram).

```json
// JSON
{ "visual": { "type": "3d-canvas-animation", "texture": "/images/container.png" }}

// Component
<img src="/images/container.png" alt="Container texture" />
```

### 4.2 Decorative Images (Use Placeholder)

**RULE:** Use `picsum.photos` for logos, portraits, illustrations.

```json
// JSON
{ "logo": "/logos/company.svg" }

// Component
<img src="https://picsum.photos/seed/company-logo/200/100" alt="Company logo" />
```

**Decision Tree:**
1. Is parent type `3d-*`, `video`, `interactive-*`? → Use exact path
2. Does image have `animation`, `transform`, `parallax` properties? → Use exact path
3. Otherwise (logos, portraits, cards) → Use picsum placeholder

---

## 5. Component Structure

### 5.1 TypeScript Types

**RULE:** Generate interface for EVERY section type.

```typescript
// types/sections.ts
export interface HeroSection extends SectionBase {
  type: "hero-section";
  heading: string;
  buttons: {
    primary: { text: string; href: string };
    secondary: { text: string; href: string };
  };
  visual?: {
    type: string;
    codeHint?: string;
  };
  bottomContent?: Array<{
    type: string;
    heading: string;
    description: string;
  }>;
  integrations?: Array<{ name: string; logo: string }>;
  style?: {
    background?: string;
    heading?: { fontSize?: string; color?: string };
    buttons?: { primary?: Record<string, any>; secondary?: Record<string, any> };
  };
}
```

### 5.2 Component File Structure

**RULE:** One component per section type, standardized structure.

```tsx
// components/HeroSection.tsx
import React from 'react';
import { HeroSection as HeroSectionType } from '@/types/sections';

export function HeroSection({ data }: { data: HeroSectionType }) {
  const { style } = data;
  
  // 1. Animation setup (if codeHint exists)
  React.useEffect(() => {
    if (data.visual?.codeHint) {
      eval(data.visual.codeHint);
    }
  }, [data.visual]);
  
  // 2. Render JSX with Tailwind
  return (
    <section 
      className="relative min-h-screen"
      style={{ backgroundColor: style?.background }}
    >
      {/* All elements from JSON */}
    </section>
  );
}
```

---

## 6. Responsive Design

### 6.1 Breakpoints

**RULE:** Use Tailwind's default breakpoints.

| Breakpoint | Tailwind Prefix | Screen Size |
|-----------|----------------|-------------|
| Mobile | (none) | < 768px |
| Tablet | `md:` | 768px - 1024px |
| Desktop | `lg:` | > 1024px |

### 6.2 Mobile-First Approach

**RULE:** Base classes for mobile, `md:` for tablet/desktop.

```tsx
// ✅ CORRECT (mobile-first)
<h1 className="text-4xl md:text-6xl lg:text-7xl">

// ❌ WRONG (desktop-first)
<h1 className="text-7xl md:text-4xl">
```

---

## 7. Accessibility

### 7.1 Semantic HTML

**RULE:** Use correct HTML5 semantic tags.

| Section Type | HTML Tag |
|-------------|----------|
| hero-section | `<section>` or `<header>` |
| feature-section | `<section>` |
| footer | `<footer>` |
| cta-section | `<section>` with `<aside>` |
| faq-section | `<section>` with `<details>` |

### 7.2 Alt Text

**RULE:** All images MUST have descriptive alt text.

```tsx
// ✅ CORRECT
<img src={data.logo} alt={`${data.name} logo`} />

// ❌ WRONG
<img src={data.logo} />  // Missing alt
<img src={data.logo} alt="logo" />  // Generic alt
```

---

## 8. Validation Checklist

**AI MUST output this after each component:**

```
✅ Validation Complete: HeroSection

JSON Mapping:
- ✅ All required fields rendered (heading, buttons, visual)
- ✅ All optional fields checked (bottomContent, integrations)
- ✅ No extra fields added

Style Application:
- ✅ fontSize range converted (60-72px → text-6xl md:text-7xl)
- ✅ Colors exact match (#FF6635 → text-[#FF6635])
- ✅ Spacing applied (padding, margin)
- ✅ Responsive breakpoints (mobile → desktop)

Animation:
- ✅ codeHint preserved (no interpretation)
- ✅ Required libraries detected (GSAP, Three.js)

Accessibility:
- ✅ Semantic HTML (<section>)
- ✅ Alt text for all images
- ✅ ARIA labels where needed

Type Safety:
- ✅ TypeScript interface matches JSON
- ✅ No type errors
```

---

## 9. Common Pitfalls (FORBIDDEN)

### ❌ DO NOT:
1. **Simplify ranges:** `"60-72px"` → `"72px"` (loses mobile sizing)
2. **Abstract colors:** `"#FF6635"` → `"orange"` (loses precision)
3. **Interpret codeHints:** Rewriting GSAP/Three.js code
4. **Skip optional fields:** Not checking `data.bottomContent?.length`
5. **Use generic placeholders:** `<div>Content goes here</div>`
6. **Combine multiple sections:** One component per section type
7. **Hard-code values:** Always use `data.*` props
8. **Ignore responsive:** Mobile-only or desktop-only styles

---

## 10. Version Compatibility

- **Framework:** React 18+ or Next.js 13+
- **TypeScript:** 5.0+
- **Tailwind CSS:** 3.3+
- **Animation Libraries:** GSAP 3.12+, Three.js r128+

---

## Summary

**Golden Rules:**
1. JSON is law → Render everything, add nothing
2. Ranges stay ranges → `60-72px` = responsive classes
3. Colors stay exact → Hex values in arbitrary Tailwind classes
4. codeHints stay verbatim → No interpretation, direct insertion
5. One component per section → Modular, testable, reusable
6. Mobile-first → Base classes for mobile, `md:` for desktop
7. Validate everything → Output checklist after each component

**When in doubt:** Check JSON first, ask user second, never guess.
