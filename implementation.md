# Workspace Architecture & Customization Guide

This guide provides a comprehensive overview of the portfolio's code architecture. It explains how the interactive elements, layout structures, and themes are implemented so you can easily modify them in the future.

---

## 1. Project Directory Structure

```text
maniradhakrishnan-dev.github.io/
├── index.html                  # Main webpage, DOM structure, and canvas engine
├── assets/
│   ├── css/
│   │   └── styles.css          # Layout variables, button effects, and themes
│   └── img/
│       └── mypic_with_specs.png # Profile photo asset
└── contents/
    ├── bio.md                  # Markdown text file for the profile summary
    └── profile.json            # Structured details (basics, experiences, education, blogs)
```

---

## 2. Interactive Canvas Background (index.html)

The interactive network background is powered by an HTML5 `<canvas>` and a vanilla JavaScript class called `NeuralNetworkBackground`.

### Class Properties & Configs
* **`count` (Density)**: The number of active particles. In `init()`, this is split by theme:
  * **Dark Mode**: `65` particles (denser, matches high-contrast glowing lines).
  * **Light Mode**: `35` particles (sparser, prevents the light page from looking cluttered).
* **`radius` (Particle Size)**:
  * **Dark Mode**: `Math.random() * 1.5 + 1` (smaller, sharper pixels).
  * **Light Mode**: `Math.random() * 1.8 + 2.0` (larger, softer nodes).

```javascript
// Controls particle properties during initialization
init() {
    const isDark = document.documentElement.getAttribute('data-theme') === 'dark' || 
                   (!document.documentElement.getAttribute('data-theme') && window.matchMedia('(prefers-color-scheme: dark)').matches);
    const count = isDark ? 65 : 35;
    for (let i = 0; i < count; i++) {
        this.particles.push({
            x: Math.random() * this.canvas.width,
            y: Math.random() * this.canvas.height,
            vx: (Math.random() - 0.5) * 0.55,
            vy: (Math.random() - 0.5) * 0.55,
            radius: isDark ? (Math.random() * 1.5 + 1) : (Math.random() * 1.8 + 2.0)
        });
    }
}
```

### Color Configuration (in `animate()`)
Theme-based colors are checked dynamically on each frame to support live switching:

| Property | Dark Mode Color | Light Mode Color | Customization Purpose |
| :--- | :--- | :--- | :--- |
| `particleColor` | `rgba(88, 166, 255, 0.35)` | `rgba(99, 102, 241, 0.30)` | Color of the floating dots |
| `lineColor` | `rgba(88, 166, 255, 0.05)` | `rgba(99, 102, 241, 0.18)` | Color of connections within `140px` |
| `pulseColor` | `rgba(88, 166, 255, 0.8)` | `rgba(99, 102, 241, 0.70)` | Color of data packets moving along lines |
| Mouse line color | `rgba(88, 166, 255, 0.12)` | `rgba(99, 102, 241, 0.15)` | Color of lines connecting to the cursor |

---

## 3. Layout Styles & Theme Settings (styles.css)

All styling is managed through CSS custom variables defined inside `:root` selector rules.

### CSS Custom Variables
To change the main colors of either theme, locate the variables in `assets/css/styles.css`:

```css
/* Light Theme Variables */
[data-theme="light"] {
    --bg: #f8fafc;        /* Slate off-white background */
    --text: #334155;      /* Dark gray body text */
    --headings: #0f172a;  /* Dark slate headings */
    --link: #1d4ed8;      /* Primary royal blue accent */
    --border: #e2e8f0;    /* Card and button boundaries */
}

/* Dark Theme Variables */
[data-theme="dark"] {
    --bg: #0d1117;        /* Deep charcoal background */
    --text: #c9d1d9;      /* Bright gray body text */
    --headings: #ffffff;  /* White headings */
    --link: #58a6ff;      /* Bright sky blue accent */
    --border: #30363d;    /* Charcoal border outline */
}
```

### Profile Photo Shape & Dimensions (`.profile-img`)
Located around line 160. To adjust the size of the circular picture, edit the `width` and `height` properties together:

```css
.profile-img {
    width: 225px;            /* Current size (increased by 25%) */
    height: 225px;
    border-radius: 50%;      /* Creates the circular frame */
    object-fit: cover;       /* Keeps correct aspect ratio */
}
```

### Social Link Capsules
To prevent browsers from applying default purple colors after clicking a link, we explicitly style `:visited`, `:active`, and `:focus` states:

```css
/* Default Unhovered State */
.social-links a,
.social-links a:visited {
    color: var(--link);
    border: 1px solid rgba(29, 78, 216, 0.25);
    background: rgba(29, 78, 216, 0.03);
}

/* Hover/Click Active States */
.social-links a:hover,
.social-links a:focus,
.social-links a:active {
    border-color: var(--link) !important;
    color: #ffffff !important;         /* Contrast text color on hover */
    background: var(--link) !important; /* Fills button with color */
    transform: translateY(-2px);       /* Smoothly slides up */
}
```

### Theme Button Micro-Animations
The sun and moon icons transition dynamically on click using scale and rotational properties instead of disappearing instantly:

```css
.theme-btn span {
    position: absolute;
    transition: transform 0.5s cubic-bezier(0.34, 1.56, 0.64, 1), opacity 0.4s ease;
}

/* Rotate and scale hidden icons out of bounds */
.theme-btn .sun {
    opacity: 0;
    transform: rotate(90deg) scale(0.5);
}
[data-theme="dark"] .theme-btn .sun {
    opacity: 1;
    transform: rotate(0) scale(1);
}
```

---

## 4. Editing Website Content (profile.json)

The portfolio loads dynamically by fetching `contents/profile.json`. You can modify details here without writing HTML:

* **Name/Tagline**: Modify `"name"` or `"tagline"` in the `"basics"` object.
* **Experiences**: Add objects containing `"role"`, `"company"`, and `"period"` inside the `"experience"` array.
* **Education**: Add objects containing `"degree"`, `"school"`, `"period"`, and `"meta"` inside the `"education"` array.
* **Articles/Projects**: Add new items inside the `"items"` array of `"content_sections"`. Provide the path to your content markdown file in `"path"`.
