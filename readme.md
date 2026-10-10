# 🏡 Nexter — Luxury Real Estate Landing Page

> An advanced, modern web development project showcasing **CSS Grid architecture**, **Dart Sass/SCSS modularization**, and **responsive layout design** without reliance on heavy frameworks. Originally built as part of Jonas Schmedtmann's masterclass (_Advanced CSS and Sass_), this repository has been comprehensively audited, refactored, and annotated with in-depth study notes.

---

## 📋 Table of Contents

1. [Project Overview](#-project-overview)
2. [Project Architecture & Directory Structure](#-project-architecture--directory-structure)
3. [Deep-Dive Study Notes: Mastering CSS Grid](#-deep-dive-study-notes-mastering-css-grid)
   - [1. The 8-Column Layout System & Named Grid Lines](#1-the-8-column-layout-system--named-grid-lines)
   - [2. The Full-Bleed vs Centered Content "Breakout" Pattern](#2-the-full-bleed-vs-centered-content-breakout-pattern)
   - [3. Intrinsic Sizing & Track Functions (`minmax`, `fr`, `min-content`)](#3-intrinsic-sizing--track-functions-minmax-fr-min-content)
   - [4. Responsive Auto-Placement (`repeat(auto-fit, minmax(...))`)](#4-responsive-auto-placement-repeatauto-fit-minmax)
   - [5. Asymmetric Mosaic & Image Overlays](#5-asymmetric-mosaic--image-overlays)
   - [6. Grid Auto-Placement Algorithm & Reordering Trick](#6-grid-auto-placement-algorithm--reordering-trick)
4. [Sass / SCSS Architecture & Methodology](#-sass--scss-architecture--methodology)
   - [Modern Dart Sass Module System (`@use`)](#modern-dart-sass-module-system-use)
   - [BEM (Block Element Modifier) Conventions](#bem-block-element-modifier-conventions)
   - [Sass Placeholders (`%heading`) and Mixins](#sass-placeholders-heading-and-mixins)
5. [Responsive Design & Breakpoint Strategy](#-responsive-design--breakpoint-strategy)
6. [Build Pipeline & Tooling](#-build-pipeline--tooling)
7. [Code Quality Audit, Code Smells & Refactoring Log](#-code-quality-audit-code-smells--refactoring-log)
8. [Getting Started & Local Development](#-getting-started--local-development)
9. [Future Roadmap & Modern CSS Enhancements](#-future-roadmap--modern-css-enhancements)

---

## 🌟 Project Overview

**Nexter** is a high-end luxury real estate landing page engineered to push CSS Grid to its absolute limits. While modern web development often defaults to utility classes or heavy UI libraries, Nexter demonstrates how pure CSS Grid and modern Sass can handle complex, multi-dimensional, magazine-style layouts cleanly and efficiently.

### Key Highlights

- **100% Pure CSS Grid & Flexbox**: No external UI frameworks (Bootstrap, Tailwind, etc.).
- **Named Grid Lines**: Semantic and readable grid placement across both axes.
- **Nested Grid Architectures**: Every section acts as an independent sub-layout inside the primary page grid.
- **Fluid Layouts with Zero Javascript**: Responsive card grids, image mosaics, and coordinate overlays created declaratively.
- **Modern Sass (SCSS)**: Modular stylesheet architecture using modern `@use` rules.

---

## 📁 Project Architecture & Directory Structure

```text
Nexter/
├── css/
│   ├── style.css             # Minified, prefixed production stylesheet
│   ├── style.comp.css        # Raw compiled CSS (intermediate build artifact)
│   ├── style.prefix.css      # Autoprefixed CSS (intermediate build artifact)
│   └── style.css.map         # Source map for debugging
├── img/                      # Optimized image assets & SVG sprite
│   ├── favicon.png
│   ├── hero.jpeg             # Header background image
│   ├── back.jpg              # Story section background image
│   ├── house-1.jpeg .. 6     # Real estate property showcase imagery
│   ├── gal-1.jpeg .. 14      # Mosaic gallery image collection
│   ├── realtor-1.jpeg .. 3   # Top realtors headshots
│   ├── logo.png, logo-*.png  # Nexter brand and press partner logos
│   └── sprite.svg            # SVG vector icon sprite
├── sass/                     # Modular SCSS source code (7-1 pattern adaptation)
│   ├── main.scss             # Entry point aggregating all modules via @use
│   ├── _base.scss            # Variables, reset, typography tokens, container grid
│   ├── _typography.scss      # Heading presets, buttons, utility spacing classes
│   ├── _sidebar.scss         # Responsive navigation sidebar & hamburger button
│   ├── _header.scss          # Hero header section with layered gradient & press logos
│   ├── _realtors.scss        # Top realtors card with subgrid track definitions
│   ├── _features.scss        # 6-column feature benefits with 2D icon layout
│   ├── _story.scss           # Overlapping picture composition & customer story
│   ├── _homes.scss           # Property listing cards with micro-grids & SVG icons
│   ├── _gallery.scss         # 8x7 asymmetrical photography mosaic
│   └── _footer.scss          # Responsive link grid & copyright attribution
├── .gitignore                # Git exclusions (node_modules, build artifacts)
├── index.html                # Semantic HTML5 document
├── package.json              # NPM dependencies & build automation scripts
├── package-lock.json         # Pinned dependency lockfile
└── README.md                 # Project documentation & masterclass study notes
```

---

## 🧠 Deep-Dive Study Notes: Mastering CSS Grid

CSS Grid is a **two-dimensional** layout system (handling columns and rows simultaneously), fundamentally different from Flexbox (which is primarily **one-dimensional**). Nexter is designed as an architectural case study of how these two specifications complement one another.

```text
+---------------------------------------------------------------------------------------------------+
| [sidebar-start] | [full-start]   [center-start]                    [center-end]       [full-end]  |
|                 | <--------------------- 1140px Centered Track --------------------->             |
|   SIDEBAR       |  HEADER (cols 1..6)                   |  REALTORS (cols 7..8)                   |
|   (8rem /       |---------------------------------------------------------------------------------|
|    top bar      |  FEATURES (repeat(auto-fit, minmax(25rem, 1fr)))                                |
|    on mobile)   |---------------------------------------------------------------------------------|
|                 |  STORY PICTURES (cols 1..4)           |  STORY CONTENT (cols 5..8)              |
|                 |---------------------------------------------------------------------------------|
|                 |  HOMES (repeat(auto-fit, minmax(25rem, 1fr)))                                   |
|                 |---------------------------------------------------------------------------------|
|                 |  GALLERY (8 cols x 7 rows, 5vw height, full-bleed)                              |
|                 |---------------------------------------------------------------------------------|
|                 |  FOOTER (full-bleed, nav grid repeat(auto-fit, minmax(15rem, 1fr)))              |
+---------------------------------------------------------------------------------------------------+
```

---

### 1. The 8-Column Layout System & Named Grid Lines

Most legacy layout frameworks use 12 columns. Nexter uses an **8-column centered track system with side padding tracks** defined directly on `.container`:

```scss
.container {
  display: grid;
  grid-template-rows: 80vh min-content 40vw repeat(3, min-content);
  grid-template-columns:
    [sidebar-start] 8rem [sidebar-end full-start] minmax(6rem, 1fr)
    [center-start]
    repeat(8, [col-start] minmax(min-content, 14rem) [col-end]) [center-end]
    minmax(6rem, 1fr) [full-end];
}
```

#### Anatomical Breakdown of `grid-template-columns`:

1. `[sidebar-start] 8rem [sidebar-end]`:
   - A dedicated fixed `8rem` (80px) vertical track on the left edge for the navigation sidebar.
2. `[full-start] minmax(6rem, 1fr)`:
   - The left gutter track. It guarantees a minimum margin of `6rem` (60px) on medium screens, while stretching to `1fr` on wide viewports to automatically center the page content.
3. `[center-start] repeat(8, [col-start] minmax(min-content, 14rem) [col-end]) [center-end]`:
   - An 8-track central column group.
   - Each column is bounded between `min-content` (it will never compress smaller than its smallest word/content) and `14rem` (140px max width).
   - Maximum width of all 8 columns combined: `8 × 14rem = 112rem` (1120px) — creating a natural maximum reading container without needing a separate wrapper `div` or `max-width: 1140px; margin: 0 auto;`.
4. `minmax(6rem, 1fr) [full-end]`:
   - The symmetrical right gutter track.

---

### 2. The Full-Bleed vs Centered Content "Breakout" Pattern

In traditional CSS, breaking an element out to full screen width required closing the `.container` wrapper, opening a full-width wrapper, or using negative margins with `calc(50% - 50vw)`.

With Named Grid Lines, any child of `.container` chooses its width explicitly:

- **Full-Bleed Element** (spans entire viewport width except sidebar):
  ```scss
  .gallery,
  .footer {
    grid-column: full-start / full-end;
  }
  ```
- **Centered Content Element** (stays bounded in the 1120px reading area):
  ```scss
  .features,
  .homes {
    grid-column: center-start / center-end;
  }
  ```
- **Asymmetric Split Elements** (split across specific column boundaries):
  ```scss
  .header {
    grid-column: full-start / col-end 6; // Starts at viewport edge, ends at column 6
  }
  .realtors {
    grid-column: col-start 7 / full-end; // Starts at column 7, ends at viewport edge
  }
  .story__pictures {
    grid-column: full-start / col-end 4;
  }
  .story__content {
    grid-column: col-start 5 / full-end;
  }
  ```

---

### 3. Intrinsic Sizing & Track Functions (`minmax`, `fr`, `min-content`)

| Unit / Function        | How it Computes                                                                         | Usage in Nexter                                                                                   |
| :--------------------- | :-------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------ |
| `minmax(min, max)`     | Constrains a track between an lower and upper bound                                     | `minmax(6rem, 1fr)` keeps margins fluid while preventing collapse on smaller screens              |
| `fr` (Fractional Unit) | Distributes a share of the _free remaining space_ after all definite sizes are resolved | Divides remaining gutter space evenly (`1fr` left, `1fr` right)                                   |
| `min-content`          | Smallest size an element can take without overflowing (e.g. longest un-hyphenated word) | `grid-template-rows: ... min-content ...` lets sections size themselves strictly to their content |
| `max-content`          | Natural size with zero line wrapping                                                    | Used in `grid-template-columns: min-content max-content` in Realtors list                         |
| `vw` / `vh`            | Direct percentage of viewport width or height                                           | `80vh` for header height; `40vw` for the story section ratio; `5vw` for gallery rows              |

---

### 4. Responsive Auto-Placement (`repeat(auto-fit, minmax(...))`)

One of the most powerful modern CSS patterns is the responsive card grid that **re-flows dynamically without media queries**:

```scss
.features {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(25rem, 1fr));
  gap: 6rem;
}

.homes {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(25rem, 1fr));
  gap: 7rem;
}
```

#### `auto-fit` vs `auto-fill` Demystified:

- **`auto-fill`**: Fills the row with as many tracks as possible. If there is leftover space for empty tracks, it **preserves** those empty tracks at minimum size.
- **`auto-fit`**: Fills the row with as many tracks as fit, then **collapses** any empty tracks down to `0px` and stretches the occupied tracks via `1fr` to occupy the remaining row space.
- Result: On a 1200px screen, 3 columns render side-by-side. On an 800px screen, it flows to 2 columns. On a 400px screen, each card smoothly stretches to 1 column. Zero media queries required!

---

### 5. Asymmetric Mosaic & Image Overlays

#### The Story Image Overlap (Coordinate Grid Plane):

Rather than relying on fragile `position: absolute` with arbitrary pixel offsets, `.story__pictures` establishes its own internal 6x6 grid:

```scss
.story__pictures {
  display: grid;
  grid-template-rows: repeat(6, 1fr);
  grid-template-columns: repeat(6, 1fr);
  align-items: center;

  &__img--1 {
    grid-row: 2 / 6;
    grid-column: 2 / 6;
    box-shadow: 0 2rem 5rem rgba(0, 0, 0, 0.1);
  }

  &__img--2 {
    grid-row: 4 / 6;
    grid-column: 4 / 7;
    z-index: 20;
    width: 115%; // Pulls image slightly past track boundary for depth
    box-shadow: 0 2rem 5rem rgba(0, 0, 0, 0.2);
  }
}
```

Because both images exist inside the same 6x6 coordinate matrix, their overlap scales proportionally across viewports.

#### The 14-Image Gallery Mosaic:

The gallery uses an 8-column by 7-row layout with viewport-relative row heights (`5vw`):

```scss
.gallery {
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  grid-template-rows: repeat(7, 5vw);
  gap: 1.5rem;

  &__img {
    width: 100%;
    height: 100%;
    object-fit: cover; // Crucial: prevents aspect ratio distortion across disparate spans
    display: block;
  }
}
```

Every image tile specifies custom spans (e.g., `grid-row: 1 / span 2; grid-column: 1 / span 2;`). Combined with `object-fit: cover;`, images crop gracefully to their assigned bounding boxes.

---

### 6. Grid Auto-Placement Algorithm & Reordering Trick

In `index.html`, the markup order is:

```html
<div class="story__pictures">...</div>
<div class="story__content">...</div>
```

On screens $\le 800\text{px}$ (`$bp-medium`), the desired visual layout is for the textual content (`.story__content`) to appear **before** the images (`.story__pictures`).

Instead of duplicating markup with mobile/desktop classes, Nexter leverages CSS Grid's placement rules:

```scss
.story__content {
  @media only screen and (max-width: base.$bp-medium) {
    grid-column: 1 / -1;
    grid-row: 5 / 6; // Explicit placement!
  }
}

.story__pictures {
  @media only screen and (max-width: base.$bp-medium) {
    grid-column: 1 / -1;
    // No explicit grid-row declared!
  }
}
```

#### How the CSS Grid Auto-Placement Algorithm Works Here:

1. **Step 1 (Explicit Items Placed First)**: The browser places `.sidebar` (row 1) and `.story__content` (explicitly locked to row 5).
2. **Step 2 (Auto-Placement of Unlocked Items)**:
   - `.header` occupies Row 2.
   - `.realtors` occupies Row 3.
   - `.features` occupies Row 4.
   - The algorithm searches for an empty row for `.story__pictures`. Because Row 5 is already claimed across all columns by `.story__content`, the algorithm automatically pushes `.story__pictures` into **Row 6**!
3. **Result**: Visual reordering achieved purely through grid auto-placement, preserving semantic HTML.

---

## 🎨 Sass / SCSS Architecture & Methodology

### Modern Dart Sass Module System (`@use`)

This project uses modern Dart Sass `@use` syntax rather than deprecated `@import`:

```scss
// sass/main.scss
@use 'base';
@use 'typography';
@use 'sidebar';
@use 'header';
@use 'realtors';
@use 'features';
@use 'story';
@use 'homes';
@use 'gallery';
@use 'footer';
```

#### Why `@use` is Superior to `@import`:

- **Namespacing**: Variables and mixins are explicitly scoped (e.g., `base.$color-primary`), avoiding global namespace pollution.
- **Single Compilation**: Each file is compiled only once, preventing code bloat caused by redundant imports.
- **Explicit Dependencies**: Each partial declares exactly what it depends on at the top of the file.

---

### BEM (Block Element Modifier) Conventions

Styles follow the BEM naming methodology to minimize selector specificity and prevent style leakage:

```text
.block { }
.block__element { }
.block--modifier { }
.block__element--modifier { }
```

**Real Examples from the Codebase:**

- `.home` $\rightarrow$ Component Block.
- `.home__img`, `.home__name`, `.home__price` $\rightarrow$ Component Elements.
- `.heading-2--dark`, `.heading-4--light` $\rightarrow$ Element Modifiers.

---

### Sass Placeholders (`%heading`) and Mixins

Common typography properties are grouped into reusable placeholders:

```scss
%heading {
  font-family: base.$font-display;
  font-weight: 400;
}

.heading-1 {
  @extend %heading;
  font-size: 4.5rem;
  color: base.$color-grey-light-1;
  line-height: 1;
}
```

Using `%heading` with `@extend` compiles shared selectors into a single CSS rule block (`.heading-1, .heading-2, .heading-3, .heading-4 { font-family: ... }`), reducing compiled stylesheet weight.

---

## 📱 Responsive Design & Breakpoint Strategy

Media query breakpoints are defined with `em` units in `_base.scss` to guarantee that browser zoom levels and custom user font settings scale predictably:

$$\text{Value in em} = \frac{\text{Pixel Value}}{16\text{px}}$$

| Breakpoint Variable | Value in `em` | Pixel Equivalent | Purpose & Structural Transformation                                                                                             |
| :------------------ | :------------ | :--------------- | :------------------------------------------------------------------------------------------------------------------------------ |
| `$bp-largest`       | `75em`        | `1200px`         | Base font size drops from `62.5%` ($10\text{px}$) to `50%` ($8\text{px}$). All `rem` dimensions scale down uniformly.           |
| `$bp-large`         | `62.5em`      | `1000px`         | Sidebar moves from vertical column ($8\text{rem}$) to horizontal top bar (`grid-row: 1 / 2; grid-column: 1 / -1;`).             |
| `$bp-medium`        | `50em`        | `800px`          | Header & realtors stack vertically; story text reordered above story photos; container switches to dynamic 2-row explicit grid. |
| `$bp-small`         | `37.5em`      | `600px`          | Header padding compressed; realtors list adjusts to 2-column format; spacing refined for mobile screens.                        |

---

## ⚙️ Build Pipeline & Tooling

The build automation pipeline defined in `package.json` runs modern SCSS compilation, vendor prefixing, and minification:

```json
"scripts": {
  "watch:sass": "sass sass/main.scss css/style.css -w",
  "start": "npm-run-all --parallel watch:sass",
  "compile:sass": "sass sass/main.scss css/style.comp.css",
  "prefix:css": "postcss --use autoprefixer -b \"last 10 versions\" css/style.comp.css -o css/style.prefix.css",
  "compress:css": "sass css/style.prefix.css css/style.css --style compressed",
  "build:css": "npm-run-all compile:sass prefix:css compress:css"
}
```

### Script Execution Flow:

1. `npm run compile:sass`: Compiles SCSS partials via Dart Sass into an unminified `style.comp.css`.
2. `npm run prefix:css`: Runs PostCSS with Autoprefixer to apply browser vendor prefixes (`-webkit-`, `-moz-`).
3. `npm run compress:css`: Minifies the autoprefixed CSS into the final distribution bundle `style.css`.
4. `npm run build:css`: Executes the sequential compilation chain via `npm-run-all`.

---

## 🚀 Getting Started & Local Development

### Prerequisites

- [Node.js](https://nodejs.org/) (v16.0.0 or higher recommended)
- `npm` (v7.0.0 or higher)

### Installation

Clone the repository and install the developer dependencies:

```bash
git clone https://github.com/Mohamed-Y0/Nexter-SCSS-Grid
cd Nexter
npm install
```

### Available NPM Scripts

| Command                | Action                                                                                      |
| :--------------------- | :------------------------------------------------------------------------------------------ |
| `npm start`            | Launches Dart Sass watcher on `sass/main.scss` targeting `css/style.css`                    |
| `npm run build:css`    | Runs complete production pipeline (compile $\rightarrow$ autoprefix $\rightarrow$ compress) |
| `npm run compile:sass` | Compiles raw SCSS to `css/style.comp.css`                                                   |
| `npm run prefix:css`   | Adds vendor prefixes via PostCSS / Autoprefixer                                             |
| `npm run compress:css` | Minifies `css/style.prefix.css` to `css/style.css`                                          |

### Viewing the Project

Because this is a static HTML/CSS web application, you can view it directly by:

1. Opening `index.html` in any modern web browser.
2. Or running VS Code's **Live Server** extension on `index.html`.

---

## 🔮 Future Roadmap & Modern CSS Enhancements

For learners and developers seeking to modernize the project further:

1. **CSS Subgrid Adoption**:
   - Modern browsers now universally support CSS Subgrid. Property cards (`.home`) could declare `grid-template-rows: subgrid` to synchronize title heights, attribute rows, and buttons across cards in the same row.
2. **Container Queries (`@container`)**:
   - Instead of viewport-bound media queries (`@media`), card components can use container queries to rearrange their internal layout when placed into narrower or wider containers.
3. **Responsive Images (`<picture>` and Modern Formats)**:
   - Convert JPEG assets to modern next-gen formats (`.avif` and `.webp`) with responsive `srcset` resolutions to optimize Largest Contentful Paint (LCP).
4. **Dynamic Viewport Units**:
   - Replace `100vh` in mobile container calculations with `100dvh` (Dynamic Viewport Height) to account for shifting mobile browser address bars.
5. **Native CSS Nesting**:
   - Native CSS nesting is now supported across all major browsers, allowing progressive transition of standard nesting rules directly into vanilla CSS.

---

### 👨‍💻 Course & Project Credits

- **Design & Original Concept**: Jonas Schmedtmann (_Advanced CSS and Sass: Flexbox, Grid, Animations and More!_).
- **Audit, Refactoring & Study Notes**: Enhanced and documented for modern web standards.
