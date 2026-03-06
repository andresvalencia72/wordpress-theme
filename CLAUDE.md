# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GymFitness is a custom WordPress theme for a gym/fitness business. It is a server-rendered PHP theme — no build tools, no package manager, no test framework, no linting. All content is in Spanish.

**Author:** Juan Pablo De la torre Valdez (@codigoconjuan)

## Development Setup

This is a WordPress theme with no build step. To run locally:
1. Place this folder in `wp-content/themes/gymfitness/` of a WordPress installation
2. Activate the theme in WP Admin > Appearance > Themes
3. Install required plugins: **Advanced Custom Fields (ACF)** and **Contact Form 7**

## Architecture

### Template Hierarchy

WordPress selects PHP templates by URL context:

- `front-page.php` — Homepage (hero, areas, classes, instructors, testimonials, blog)
- `home.php` — Blog listing
- `single.php` — Single blog post
- `single-gymfitness_clases.php` — Single class (custom post type) with sidebar
- `page-listado-clases.php`, `page-galeria.php`, `page-contenido-centrado.php` — Custom page templates (declared via `/* Template Name: ... */`)
- `category.php`, `author.php` — Archive pages
- `header.php` / `footer.php` — Shared layout wrappers
- `template-parts/` — Reusable partials (blog card, post, clase, pagina)

### Custom Post Types

Three CPTs are used throughout (registered externally, not in theme code):
- `gymfitness_clases` — Gym classes (schedule, hours, days)
- `instructores` — Gym instructors
- `testimoniales` — Client testimonials

### Data Layer

All dynamic content uses **ACF** (`get_field()` / `the_field()`). Key ACF field groups:
- **Front page:** `hero_imagen`, `hero_heading`, `hero_texto`, `encabezado_bienvenida`, `texto_bienvenida`, `area_1`–`area_4` (each with `imagen` + `texto`)
- **Classes:** `hora_inicio`, `hora_fin`, `dias_clase`
- **Instructors:** `especialidad` (array)
- **Contact page:** `ubicacion` (map)

Custom queries live in `includes/queries.php`: `gymfitness_lista_clases($n)`, `gymfitness_instructores()`, `gymfitness_testimoniales()` — all use `WP_Query`.

### Key Files

- `functions.php` — Theme setup, script/style enqueueing (conditional per page), nav menus, widget areas, shortcodes
- `includes/widgets.php` — `GymFitness_Clases_Widget` (sidebar widget showing class list)
- `includes/queries.php` — WP_Query helper functions for CPTs
- `style.css` — All theme CSS in one file (CSS custom properties, Grid, Flexbox, mobile-first responsive)
- `js/scripts.js` — Vanilla JS: Swiper carousel, anime.js hero animation, hamburger menu, sticky nav

### CSS Custom Properties

```css
--fuente-principal: 'Raleway', sans-serif;
--fuente-headings:  'Staatliches', cursive;
--primario:         #ff5b00;
--gris-oscuro:      #2f2e2e;
--gris-claro:       #EBEBEB;
```

### Third-Party Libraries (CDN, no npm)

- **normalize.css 8.0.1** — Always loaded
- **Swiper 8.4.5** — Front page only (testimonials slider)
- **anime.js 2.0.2** — Front page only (hero text animation)
- **Lightbox 2.11.3** — Gallery page only
- **jQuery** — Bundled with WordPress

### Shortcode

`[gymfitness_ubicacion]` — Renders ACF location map + Contact Form 7 form (defined in `functions.php`).
