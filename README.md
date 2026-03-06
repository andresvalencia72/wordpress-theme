# GymFitness - Guía Completa para Entrevista Técnica WordPress

> Este README usa el tema **GymFitness** como base práctica para explicar todos los conceptos clave que necesitas dominar en una entrevista técnica para desarrollador WordPress.

---

## Tabla de Contenidos

1. [¿Qué es un Tema de WordPress?](#1-qué-es-un-tema-de-wordpress)
2. [Estructura de Archivos de un Tema](#2-estructura-de-archivos-de-un-tema)
3. [El archivo style.css — Metadatos del Tema](#3-el-archivo-stylecss--metadatos-del-tema)
4. [Template Hierarchy (Jerarquía de Plantillas)](#4-template-hierarchy-jerarquía-de-plantillas)
5. [header.php y footer.php — Estructura Base](#5-headerphp-y-footerphp--estructura-base)
6. [El Loop de WordPress](#6-el-loop-de-wordpress)
7. [Template Parts (Partes Reutilizables)](#7-template-parts-partes-reutilizables)
8. [functions.php — El Cerebro del Tema](#8-functionsphp--el-cerebro-del-tema)
9. [Hooks: Actions y Filters](#9-hooks-actions-y-filters)
10. [Enqueue de Scripts y Estilos](#10-enqueue-de-scripts-y-estilos)
11. [Menús de Navegación](#11-menús-de-navegación)
12. [Custom Post Types (CPT)](#12-custom-post-types-cpt)
13. [WP_Query — Consultas Personalizadas](#13-wp_query--consultas-personalizadas)
14. [Advanced Custom Fields (ACF)](#14-advanced-custom-fields-acf)
15. [Widgets y Sidebars](#15-widgets-y-sidebars)
16. [Shortcodes](#16-shortcodes)
17. [Conditional Tags (Etiquetas Condicionales)](#17-conditional-tags-etiquetas-condicionales)
18. [Seguridad: Escape y Sanitización](#18-seguridad-escape-y-sanitización)
19. [Internacionalización (i18n)](#19-internacionalización-i18n)
20. [Imágenes Destacadas y Tamaños](#20-imágenes-destacadas-y-tamaños)
21. [Sistema de Comentarios](#21-sistema-de-comentarios)
22. [Paginación](#22-paginación)
23. [CSS: Mobile First, Grid, Flexbox y Custom Properties](#23-css-mobile-first-grid-flexbox-y-custom-properties)
24. [JavaScript en WordPress](#24-javascript-en-wordpress)
25. [Custom Page Templates (Plantillas de Página)](#25-custom-page-templates-plantillas-de-página)
26. [Preguntas Frecuentes de Entrevista](#26-preguntas-frecuentes-de-entrevista)

---

## 1. ¿Qué es un Tema de WordPress?

Un tema controla **toda la presentación visual** de un sitio WordPress. Es una carpeta dentro de `wp-content/themes/` que contiene archivos PHP, CSS y JS que WordPress usa para renderizar las páginas.

**Pregunta de entrevista:** *¿Cuál es la diferencia entre un tema y un plugin?*
- **Tema** → controla cómo se **ve** el sitio (presentación)
- **Plugin** → agrega **funcionalidad** al sitio (lógica de negocio)
- Regla general: si al cambiar de tema algo se pierde, probablemente debería ser un plugin

---

## 2. Estructura de Archivos de un Tema

```
gymfitness/
├── style.css                        ← Metadatos del tema + todos los estilos
├── functions.php                    ← Configuración, hooks, shortcodes
├── header.php                       ← HTML <head>, menú de navegación
├── footer.php                       ← Pie de página, cierre de HTML
├── index.php                        ← Plantilla de respaldo (obligatorio)
├── front-page.php                   ← Página de inicio
├── home.php                         ← Listado del blog
├── single.php                       ← Post individual
├── page.php                         ← Página genérica
├── category.php                     ← Archivo de categoría
├── author.php                       ← Archivo de autor
├── single-gymfitness_clases.php     ← Post individual de CPT "clases"
├── page-galeria.php                 ← Plantilla de página personalizada
├── page-listado-clases.php          ← Plantilla de página personalizada
├── page-contenido-centrado.php      ← Plantilla de página personalizada
├── sidebar.php                      ← Sidebar principal
├── sidebar-clases.php               ← Sidebar de clases
├── template-parts/                  ← Fragmentos reutilizables
│   ├── blog.php                     ← Card de post (listados)
│   ├── post.php                     ← Contenido de post completo
│   ├── clase.php                    ← Contenido de clase
│   └── pagina.php                   ← Contenido de página genérica
├── includes/
│   ├── queries.php                  ← Funciones con WP_Query personalizadas
│   └── widgets.php                  ← Widget personalizado
├── js/
│   ├── scripts.js                   ← JS principal (Swiper, animaciones, menú)
│   └── lightbox.min.js              ← Librería lightbox
├── css/
│   └── lightbox.min.css
└── img/                             ← Imágenes del tema
```

**Los 2 archivos mínimos obligatorios** para que WordPress reconozca un tema son:
1. `style.css` (con el comentario de metadatos)
2. `index.php` (plantilla de respaldo)

---

## 3. El archivo style.css — Metadatos del Tema

El archivo `style.css` **debe** comenzar con un comentario especial que WordPress lee para identificar el tema:

```css
/*
    Theme Name: Gym Fitness
    Theme URI:
    Author: Juan Pablo De la torre Valdez
    Author URI: http://twitter.com/codigoconjuan
    Description: Theme Diseñado para el Gimnasio GymFitness
    Version: 1.0
    License: GNU General Public License v2 or later
    License URI: http://www.gnu.org/licenses/gpl-2.0.html
    Tags: Gym, gimnasio, flexbox, css grid, mobile first
    Text Domain: gymfitness
*/
```

**Pregunta de entrevista:** *¿Qué es el Text Domain?*
- Es el identificador único para las funciones de traducción del tema
- Se usa en funciones como `__('texto', 'gymfitness')` y `esc_html__('texto', 'gymfitness')`

---

## 4. Template Hierarchy (Jerarquía de Plantillas)

Este es **el concepto más importante** en desarrollo de temas WordPress. WordPress decide qué archivo PHP usar para cada URL siguiendo un orden de prioridad.

### Flujo de selección de plantilla:

```
¿Es la página de inicio (front page)?
  → front-page.php  ✅ (existe en este tema)
  → home.php
  → index.php

¿Es el blog (listado de posts)?
  → home.php  ✅
  → index.php

¿Es un post individual?
  → single-{post_type}.php  ✅ (single-gymfitness_clases.php para clases)
  → single.php  ✅ (para posts normales)
  → singular.php
  → index.php

¿Es una página?
  → {template-personalizada}.php  ✅ (page-galeria.php, etc.)
  → page-{slug}.php
  → page-{id}.php
  → page.php  ✅
  → singular.php
  → index.php

¿Es un archivo de categoría?
  → category-{slug}.php
  → category-{id}.php
  → category.php  ✅
  → archive.php
  → index.php

¿Es un archivo de autor?
  → author-{slug}.php
  → author-{id}.php
  → author.php  ✅
  → archive.php
  → index.php

Nada coincide:
  → index.php  ✅ (respaldo final, SIEMPRE se usa si no hay otro)
```

### Tabla del tema GymFitness:

| URL / Contexto | Archivo que usa WordPress |
|---|---|
| Página de inicio estática | `front-page.php` |
| Listado del blog | `home.php` |
| Post individual del blog | `single.php` |
| Clase individual (CPT) | `single-gymfitness_clases.php` |
| Página genérica | `page.php` |
| Página con slug "galeria" | `page-galeria.php` (Template Name) |
| Página con slug "listado-clases" | `page-listado-clases.php` (Template Name) |
| Categoría | `category.php` |
| Autor | `author.php` |
| Cualquier otra cosa | `index.php` |

**Pregunta de entrevista:** *¿Qué diferencia hay entre `front-page.php` y `home.php`?*
- `front-page.php` → se usa cuando hay una **página estática** configurada como página de inicio (Settings > Reading)
- `home.php` → se usa para la **página del blog** (listado de posts)
- Si no existe `front-page.php`, WordPress usa `home.php` para la página de inicio

---

## 5. header.php y footer.php — Estructura Base

### header.php
Contiene todo lo que va **antes** del contenido: doctype, `<head>`, barra de navegación.

```php
<!DOCTYPE html>
<html <?php language_attributes(); ?>>  <!-- Atributo de idioma dinámico -->
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <?php wp_head(); ?>  <!-- ¡OBLIGATORIO! Inyecta CSS, JS, meta tags de WP y plugins -->
</head>
<body <?php body_class(); ?>>  <!-- Agrega clases CSS automáticas como "home", "single", etc. -->
```

### footer.php
Contiene todo lo que va **después** del contenido: pie de página y cierre de HTML.

```php
    <footer class="footer contenedor">
        <!-- Contenido del footer -->
    </footer>
    <?php wp_footer(); ?>  <!-- ¡OBLIGATORIO! Inyecta JS de WP y plugins al final -->
</body>
</html>
```

### Cómo se usan en otras plantillas:
```php
<?php get_header(); ?>    <!-- Incluye header.php -->

    <main>
        <!-- Contenido de la página -->
    </main>

<?php get_footer(); ?>    <!-- Incluye footer.php -->
```

**Pregunta de entrevista:** *¿Qué pasa si olvidas `wp_head()` o `wp_footer()`?*
- Los estilos y scripts registrados con `wp_enqueue_*` **no se cargarán**
- Los plugins no podrán inyectar su código
- La barra de administración no aparecerá
- El SEO y scripts de analytics se romperán

### Funciones clave:
| Función | Qué hace |
|---|---|
| `language_attributes()` | Imprime `lang="es-ES"` (según config de WP) |
| `body_class()` | Agrega clases CSS dinámicas al body (`home`, `single`, `page-id-5`, etc.) |
| `wp_head()` | Hook que inyecta CSS/JS/meta en el `<head>` |
| `wp_footer()` | Hook que inyecta JS antes de `</body>` |
| `site_url('/')` | URL raíz del sitio |
| `get_template_directory_uri()` | URL de la carpeta del tema (para referenciar archivos) |
| `get_bloginfo('name')` | Nombre del sitio configurado en WP |

---

## 6. El Loop de WordPress

El Loop es **el mecanismo central** para mostrar contenido. Es un bucle `while` que recorre los posts disponibles para la página actual.

### Loop básico (usado en `home.php`):
```php
<?php while( have_posts() ) { the_post(); ?>
    <h2><?php the_title(); ?></h2>
    <?php the_content(); ?>
<?php } ?>
```

### ¿Cómo funciona paso a paso?
1. `have_posts()` → comprueba si hay más posts por mostrar (devuelve `true` o `false`)
2. `the_post()` → prepara el siguiente post y hace disponibles las Template Tags
3. Dentro del loop, usas **Template Tags** para mostrar datos del post actual

### Template Tags más usadas (dentro del Loop):
| Función | Qué muestra |
|---|---|
| `the_title()` | Título del post |
| `the_content()` | Contenido completo del post |
| `the_excerpt()` | Extracto/resumen del post |
| `the_permalink()` | URL permanente del post |
| `the_post_thumbnail()` | Imagen destacada |
| `the_time('d/m/Y')` | Fecha de publicación |
| `the_category()` | Lista de categorías |
| `the_author()` | Nombre del autor |
| `get_the_author_meta('ID')` | ID del autor (para generar enlaces) |

### Ejemplo real del tema (`template-parts/blog.php`):
```php
<li class="card">
    <?php the_category(); ?>              <!-- Muestra las categorías -->
    <?php the_post_thumbnail(); ?>        <!-- Muestra la imagen destacada -->
    <div class="contenido">
        <a href="<?php the_permalink(); ?>">
            <h3><?php the_title(); ?></h3>
        </a>
        <p class="meta">
            <span>Por: </span>
            <a href="<?php echo get_author_posts_url( get_the_author_meta('ID')); ?>">
                <?php echo get_the_author_meta('display_name'); ?>
            </a>
        </p>
        <p class="meta">
            <span>Fecha: </span>
            <?php the_time( get_option('date_format') ); ?>
        </p>
    </div>
</li>
```

**Pregunta de entrevista:** *¿Cuál es la diferencia entre `the_title()` y `get_the_title()`?*
- `the_title()` → **imprime** (echo) el título directamente
- `get_the_title()` → **retorna** el título como string (para asignarlo a una variable)
- Esto aplica para casi todas las Template Tags: `the_*` imprime, `get_the_*` retorna

---

## 7. Template Parts (Partes Reutilizables)

`get_template_part()` permite dividir templates en fragmentos reutilizables. Evita duplicar código.

### Sintaxis:
```php
get_template_part('template-parts/blog');    // Carga template-parts/blog.php
get_template_part('template-parts/post');    // Carga template-parts/post.php
get_template_part('template-parts/clase');   // Carga template-parts/clase.php
```

### ¿Dónde se reutilizan en este tema?

| Template Part | Usado en |
|---|---|
| `template-parts/blog.php` | `home.php`, `category.php`, `author.php`, `front-page.php` |
| `template-parts/post.php` | `single.php` |
| `template-parts/clase.php` | `single-gymfitness_clases.php` |
| `template-parts/pagina.php` | `page.php`, `page-listado-clases.php` |

### Ejemplo práctico:
En `home.php` se itera y se reutiliza la misma card:
```php
<ul class="listado-grid">
    <?php while( have_posts() ) {
        the_post();
        get_template_part('template-parts/blog');  // Reutiliza el mismo card
    } ?>
</ul>
```

---

## 8. functions.php — El Cerebro del Tema

`functions.php` se ejecuta automáticamente cuando el tema está activo. Aquí se configura todo: soporte del tema, scripts, menús, widgets, shortcodes.

### Estructura del functions.php de GymFitness:
```php
<?php
// 1. Incluir archivos externos
require get_template_directory() . '/includes/widgets.php';
require get_template_directory() . '/includes/queries.php';

// 2. Configuración del tema
function gymfitness_setup() {
    add_theme_support('post-thumbnails');  // Habilitar imágenes destacadas
    add_theme_support('title-tag');        // <title> dinámico para SEO
}
add_action('after_setup_theme', 'gymfitness_setup');

// 3. Registrar menús de navegación
function gymfitness_menus() {
    register_nav_menus( array(
        'menu-principal' => __('Menu Principal', 'gymfitness')
    ));
}
add_action('init', 'gymfitness_menus');

// 4. Encolar scripts y estilos (ver sección 10)
// 5. Registrar zonas de widgets (ver sección 15)
// 6. Crear shortcodes (ver sección 16)
// 7. CSS dinámico para el hero (ver sección 14)
```

### Funciones clave usadas:
| Función | Propósito |
|---|---|
| `get_template_directory()` | Ruta absoluta del tema en el servidor (para `require`) |
| `get_template_directory_uri()` | URL del tema (para enlaces a assets) |
| `get_stylesheet_uri()` | URL del `style.css` del tema |
| `add_theme_support()` | Activa features de WordPress |
| `add_action()` | Conecta funciones a hooks de WP |

**Pregunta de entrevista:** *¿Cuál es la diferencia entre `get_template_directory()` y `get_template_directory_uri()`?*
- `get_template_directory()` → ruta del **sistema de archivos** (`/var/www/html/wp-content/themes/gymfitness`) — para incluir archivos PHP con `require`
- `get_template_directory_uri()` → **URL** (`https://sitio.com/wp-content/themes/gymfitness`) — para referenciar archivos en el HTML (imágenes, CSS, JS)

---

## 9. Hooks: Actions y Filters

Los hooks son **el sistema de extensibilidad** de WordPress. Permiten ejecutar tu código en momentos específicos del ciclo de vida de WordPress.

### Actions (Acciones)
Ejecutan una función en un punto específico. **No retornan nada**, solo ejecutan.

```php
// Sintaxis: add_action( 'nombre_del_hook', 'tu_funcion', prioridad, num_args );

add_action('after_setup_theme', 'gymfitness_setup');        // Al inicializar tema
add_action('init', 'gymfitness_menus');                      // Al iniciar WordPress
add_action('wp_enqueue_scripts', 'gymfitness_scripts_styles'); // Al cargar scripts
add_action('widgets_init', 'gymfitness_widgets');            // Al inicializar widgets
```

### Hooks usados en este tema:

| Hook | Cuándo se ejecuta | Qué hace aquí |
|---|---|---|
| `after_setup_theme` | Después de cargar el tema | Habilita thumbnails y title-tag |
| `init` | Al iniciar WordPress | Registra menús y CSS dinámico del hero |
| `wp_enqueue_scripts` | Al preparar scripts/estilos | Carga CSS y JS |
| `widgets_init` | Al inicializar widgets | Registra sidebars y widget personalizado |

### Filters (Filtros)
Modifican un valor antes de que se muestre o guarde. **Siempre retornan** el dato modificado.

```php
// Ejemplo conceptual (no presente en este tema pero importante para entrevista):
function modificar_titulo($title) {
    return 'Prefijo - ' . $title;
}
add_filter('the_title', 'modificar_titulo');
```

**Pregunta de entrevista:** *¿Diferencia entre Action y Filter?*
- **Action** → ejecuta código en un momento dado. No devuelve nada. Ejemplo: agregar un script
- **Filter** → modifica un valor y lo **devuelve**. Ejemplo: cambiar el título antes de mostrarlo

---

## 10. Enqueue de Scripts y Estilos

**NUNCA** debes poner `<link>` o `<script>` directamente en el HTML. WordPress tiene un sistema para manejar dependencias correctamente.

### Sistema de enqueue:
```php
function gymfitness_scripts_styles() {
    // ========= CSS =========
    // wp_enqueue_style( $handle, $src, $deps, $ver, $media )

    // CSS externo (CDN)
    wp_enqueue_style('normalize',
        'https://necolas.github.io/normalize.css/8.0.1/normalize.css',
        array(),        // Sin dependencias
        '8.0.1'         // Versión (para cache busting)
    );

    // CSS condicional — solo en la página de galería
    if(is_page('galeria')) {
        wp_enqueue_style('lightboxcss',
            get_template_directory_uri() . '/css/lightbox.min.css',
            array(), '2.11.3'
        );
    }

    // CSS condicional — solo en el front page
    if(is_front_page()) {
        wp_enqueue_style('swiper-css',
            'https://cdn.jsdelivr.net/npm/swiper@8/swiper-bundle.min.css',
            array(), '8.4.5'
        );
    }

    // CSS principal del tema (depende de normalize)
    wp_enqueue_style('style',
        get_stylesheet_uri(),   // Apunta a style.css automáticamente
        array('normalize'),     // Se carga DESPUÉS de normalize
        '1.0.0'
    );

    // ========= JS =========
    // wp_enqueue_script( $handle, $src, $deps, $ver, $in_footer )

    // JS condicional
    if(is_page('galeria')) {
        wp_enqueue_script('lightboxjs',
            get_template_directory_uri() . '/js/lightbox.min.js',
            array('jquery'),    // Depende de jQuery
            '2.11.3',
            true               // Cargar en el footer (antes de </body>)
        );
    }

    if(is_front_page()) {
        wp_enqueue_script('swiper-js',
            'https://cdn.jsdelivr.net/npm/swiper@8/swiper-bundle.min.js',
            array(), '8.4.5', true
        );
        wp_enqueue_script('anime',
            'https://cdnjs.cloudflare.com/ajax/libs/animejs/2.0.2/anime.min.js',
            array(), '2.0.2', true
        );
    }

    // JS principal (siempre se carga)
    wp_enqueue_script('scripts',
        get_template_directory_uri() . '/js/scripts.js',
        array(), '1.0.0', true
    );
}
add_action('wp_enqueue_scripts', 'gymfitness_scripts_styles');
```

### Parámetros de `wp_enqueue_style()`:
| Parámetro | Tipo | Descripción |
|---|---|---|
| `$handle` | string | Identificador único del recurso |
| `$src` | string | URL del archivo CSS |
| `$deps` | array | Handles de los que depende (se cargan primero) |
| `$ver` | string | Versión (se agrega como `?ver=1.0.0` para cache busting) |
| `$media` | string | Media query CSS (default: `'all'`) |

### Parámetros de `wp_enqueue_script()`:
| Parámetro | Tipo | Descripción |
|---|---|---|
| `$handle` | string | Identificador único |
| `$src` | string | URL del archivo JS |
| `$deps` | array | Dependencias (ejemplo: `array('jquery')`) |
| `$ver` | string | Versión |
| `$in_footer` | bool | `true` = cargar antes de `</body>` (recomendado) |

**Pregunta de entrevista:** *¿Por qué usar `wp_enqueue_*` en vez de poner tags directamente?*
1. **Evita duplicados** — WordPress no carga el mismo handle dos veces
2. **Maneja dependencias** — garantiza el orden correcto de carga
3. **Permite condicionales** — solo carga donde se necesita
4. **Plugins y child themes** pueden desregistrar/reemplazar scripts
5. **Combinación y minificación** — plugins de optimización pueden procesarlos

---

## 11. Menús de Navegación

### Paso 1: Registrar la ubicación del menú
```php
function gymfitness_menus() {
    register_nav_menus( array(
        'menu-principal' => __('Menu Principal', 'gymfitness')
        // Puedes registrar múltiples ubicaciones:
        // 'menu-footer' => __('Menu Footer', 'gymfitness')
    ));
}
add_action('init', 'gymfitness_menus');
```

### Paso 2: Mostrar el menú en una plantilla
```php
<?php
    $args = array(
        'theme_location'  => 'menu-principal',   // Coincide con el slug registrado
        'container'       => 'nav',               // Elemento HTML contenedor
        'container_class' => 'menu-principal'     // Clase CSS del contenedor
    );
    wp_nav_menu($args);
?>
```

### Paso 3: Asignar el menú en WP Admin
**Apariencia > Menús** → Crear menú → Asignar a "Menu Principal"

### Parámetros útiles de `wp_nav_menu()`:
| Parámetro | Descripción |
|---|---|
| `theme_location` | Ubicación registrada |
| `container` | Tag HTML contenedor (`nav`, `div`, `false`) |
| `container_class` | Clase CSS del contenedor |
| `menu_class` | Clase CSS del `<ul>` |
| `depth` | Niveles de profundidad (0 = todos, 1 = solo nivel principal) |
| `fallback_cb` | Función callback si no hay menú asignado |

WordPress genera automáticamente clases CSS como:
- `.current_page_item` → página actual
- `.menu-item-has-children` → tiene submenús
- `.current-menu-ancestor` → ancestro de la página actual

---

## 12. Custom Post Types (CPT)

Los CPT extienden WordPress más allá de posts y páginas. Permiten crear tipos de contenido personalizados.

### CPTs usados en GymFitness:

| CPT | Slug | Para qué |
|---|---|---|
| Clases | `gymfitness_clases` | Clases del gimnasio (yoga, crossfit, etc.) |
| Instructores | `instructores` | Perfiles de instructores |
| Testimoniales | `testimoniales` | Opiniones de clientes |

### Cómo registrar un CPT (ejemplo para entrevista):
```php
function registrar_cpt_clases() {
    $args = array(
        'labels' => array(
            'name'          => 'Clases',
            'singular_name' => 'Clase',
            'add_new'       => 'Agregar Clase',
            'add_new_item'  => 'Agregar Nueva Clase',
            'edit_item'     => 'Editar Clase',
        ),
        'public'       => true,          // Visible en el frontend y admin
        'has_archive'  => true,          // Tiene página de archivo (/clases/)
        'menu_icon'    => 'dashicons-welcome-learn-more',
        'supports'     => array('title', 'editor', 'thumbnail'),
        'rewrite'      => array('slug' => 'clases'),
    );
    register_post_type('gymfitness_clases', $args);
}
add_action('init', 'registrar_cpt_clases');
```

### Plantillas para CPT:
WordPress busca automáticamente:
- `single-gymfitness_clases.php` → para un post individual del CPT
- `archive-gymfitness_clases.php` → para el listado/archivo del CPT

**Pregunta de entrevista:** *¿Dónde deberían registrarse los CPT, en el tema o en un plugin?*
- **En un plugin**, porque los CPT son **datos/funcionalidad**, no presentación
- Si los registras en el tema y el usuario cambia de tema, pierde acceso a su contenido
- En este proyecto están registrados externamente (posiblemente con un plugin como CPT UI)

---

## 13. WP_Query — Consultas Personalizadas

`WP_Query` permite hacer consultas personalizadas a la base de datos, independientes del Loop principal.

### Ejemplo del tema (`includes/queries.php`):
```php
function gymfitness_lista_clases($cantidad = -1) {
    $args = array(
        'post_type'      => 'gymfitness_clases',  // Solo del CPT "clases"
        'posts_per_page' => $cantidad               // -1 = todos
    );

    $clases = new WP_Query($args);

    while($clases->have_posts()) {
        $clases->the_post();
        // Aquí el Loop funciona igual: the_title(), the_content(), etc.
        ?>
        <h3><?php the_title(); ?></h3>
        <?php
    }

    wp_reset_postdata();  // ¡IMPORTANTE! Restaura la query global
}
```

### Parámetros más comunes de WP_Query:
```php
$args = array(
    'post_type'      => 'post',             // Tipo: post, page, CPT
    'posts_per_page' => 10,                 // Cantidad (-1 = todos)
    'orderby'        => 'date',             // Ordenar por: date, title, rand, meta_value
    'order'          => 'DESC',             // DESC o ASC
    'category_name'  => 'noticias',         // Filtrar por categoría
    'tag'            => 'destacado',        // Filtrar por tag
    'meta_key'       => 'precio',           // Filtrar por custom field
    'meta_value'     => '100',
    'tax_query'      => array(...),         // Filtrar por taxonomía
    'meta_query'     => array(...),         // Filtrar por múltiples meta fields
    's'              => 'búsqueda',         // Búsqueda por texto
    'paged'          => get_query_var('paged'), // Para paginación
);
```

**Pregunta de entrevista:** *¿Por qué es obligatorio llamar `wp_reset_postdata()` después de un WP_Query personalizado?*
- `the_post()` modifica la variable global `$post`
- Si no reseteas, las funciones del Loop (como `the_title()`) fuera de tu query personalizada seguirán mostrando datos del último post de tu query en vez del contenido correcto
- `wp_reset_postdata()` restaura `$post` a su valor original

**Pregunta de entrevista:** *¿Diferencia entre `wp_reset_postdata()` y `wp_reset_query()`?*
- `wp_reset_postdata()` → restaura `$post` global. Úsalo después de `new WP_Query()`
- `wp_reset_query()` → restaura la query global completa. Úsalo después de `query_posts()` (que ya NO debes usar)

---

## 14. Advanced Custom Fields (ACF)

ACF permite crear campos personalizados sin escribir código en la base de datos. Es el plugin más popular para agregar campos a posts, páginas y CPT.

### Funciones principales:

| Función | Descripción |
|---|---|
| `the_field('nombre')` | **Imprime** el valor del campo |
| `get_field('nombre')` | **Retorna** el valor del campo |
| `get_field('nombre', $post_id)` | Obtiene campo de un post específico |

### Campos ACF en GymFitness:

#### Página de inicio (Front Page):
```php
// Texto simple
the_field('encabezado_bienvenida');    // Imprime: "Bienvenido a GymFitness"
the_field('texto_bienvenida');          // Imprime: párrafo descriptivo
the_field('hero_heading');              // Imprime: título del hero
the_field('hero_texto');                // Imprime: subtítulo del hero

// Campo de imagen (retorna array o ID)
$id_imagen = get_field('hero_imagen', $front_id);
$imagen = wp_get_attachment_image_src($id_imagen, 'full')[0];  // URL de la imagen

// Grupo de campos (retorna array asociativo)
$area1 = get_field('area_1');
$imagen = $area1['imagen']['sizes']['medium_large'];  // URL del tamaño específico
$texto = $area1['texto'];                               // Texto del área
```

#### Custom Post Type (Clases):
```php
// Dentro del Loop de una clase
$hora_inicio = get_field('hora_inicio');  // "09:00"
$hora_fin = get_field('hora_fin');         // "10:30"
the_field('dias_clase');                    // "Lunes, Miércoles, Viernes"
```

#### Instructores (Array/Checkbox):
```php
$especialidades = get_field('especialidad');  // Array: ["Yoga", "Pilates"]
foreach($especialidades as $esp) {
    echo '<span class="etiqueta">' . esc_html($esp) . '</span>';
}
```

### CSS Dinámico con ACF:
El tema inyecta CSS inline para usar la imagen del hero como background dinámico:

```php
function gymfitness_hero_imagen() {
    $front_id = get_option('page_on_front');         // ID de la página de inicio
    $id_imagen = get_field('hero_imagen', $front_id);
    $imagen = wp_get_attachment_image_src($id_imagen, 'full')[0];

    // Registrar un style handle vacío para inyectar CSS
    wp_register_style('custom', false);
    wp_enqueue_style('custom');

    $css = "
        body.home .header {
            background-image: linear-gradient(rgb(0 0 0 / .75), rgb(0 0 0 / .75)), url($imagen);
        }
    ";

    wp_add_inline_style('custom', $css);  // Inyecta CSS inline vinculado al handle
}
add_action('init', 'gymfitness_hero_imagen');
```

**Pregunta de entrevista:** *¿Cuándo usas `the_field()` vs `get_field()`?*
- `the_field()` → cuando quieres **imprimir directamente** en el HTML
- `get_field()` → cuando necesitas **manipular** el dato antes de mostrarlo (guardarlo en variable, comparar, iterar, etc.)

---

## 15. Widgets y Sidebars

### Paso 1: Registrar áreas de widgets (sidebars)
```php
function gymfitness_widgets() {
    register_sidebar( array(
        'name'          => 'Sidebar 1',                              // Nombre visible en admin
        'id'            => 'sidebar_1',                              // Identificador único
        'before_widget' => '<div class="widget">',                   // HTML antes del widget
        'after_widget'  => '</div>',                                 // HTML después del widget
        'before_title'  => '<h3 class="text-center text-primary">', // HTML antes del título
        'after_title'   => '</h3>'                                   // HTML después del título
    ));

    register_sidebar( array(
        'name' => 'Sidebar 2',
        'id'   => 'sidebar_2',
        // ... mismos parámetros
    ));
}
add_action('widgets_init', 'gymfitness_widgets');
```

### Paso 2: Mostrar el sidebar en una plantilla
```php
<!-- sidebar.php -->
<aside>
    <?php dynamic_sidebar('sidebar_1'); ?>  <!-- Muestra todos los widgets del sidebar_1 -->
</aside>
```

Para cargar un sidebar específico:
```php
<?php get_sidebar(); ?>          // Carga sidebar.php
<?php get_sidebar('clases'); ?>  // Carga sidebar-clases.php
```

### Paso 3: Crear un Widget personalizado
La clase `GymFitness_Clases_Widget` extiende `WP_Widget` y tiene 3 métodos obligatorios:

```php
class GymFitness_Clases_Widget extends WP_Widget {

    // Constructor: define ID, nombre y descripción
    function __construct() {
        parent::__construct(
            'gymfitness_widget',                                    // ID del widget
            esc_html__('GymFitness Clases', 'gymfitness'),         // Nombre visible
            array('description' => esc_html__('Agrega las Clases como Widget', 'gymfitness'))
        );
    }

    // widget(): Qué se muestra en el FRONTEND
    public function widget($args, $instance) {
        $args = array(
            'post_type'      => 'gymfitness_clases',
            'posts_per_page' => $instance['cantidad']     // Valor del campo del admin
        );
        $clases = new WP_Query($args);
        while($clases->have_posts()) {
            $clases->the_post();
            // Renderiza cada clase con thumbnail, título y horario
        }
        wp_reset_postdata();
    }

    // form(): Qué se muestra en el ADMIN (formulario de configuración)
    public function form($instance) {
        $cantidad = !empty($instance['cantidad']) ? $instance['cantidad'] : '';
        ?>
        <p>
            <label for="<?php echo esc_attr($this->get_field_id('cantidad')); ?>">
                ¿Cuántas clases deseas mostrar?
            </label>
            <input class="widefat"
                id="<?php echo esc_attr($this->get_field_id('cantidad')); ?>"
                name="<?php echo esc_attr($this->get_field_name('cantidad')); ?>"
                type="number"
                value="<?php echo esc_attr($cantidad); ?>"
            />
        </p>
        <?php
    }

    // update(): Cómo se GUARDAN los datos del formulario
    public function update($new_instance, $old_instance) {
        $instance = array();
        $instance['cantidad'] = sanitize_text_field($new_instance['cantidad']);
        return $instance;
    }
}

// Registrar el widget
function gymfitness_registrar_widget() {
    register_widget('GymFitness_Clases_Widget');
}
add_action('widgets_init', 'gymfitness_registrar_widget');
```

**Pregunta de entrevista:** *¿Cuáles son los 3 métodos obligatorios de un Widget?*
1. `widget()` → renderiza el HTML en el frontend
2. `form()` → muestra el formulario de configuración en el admin
3. `update()` → sanitiza y guarda los datos del formulario

---

## 16. Shortcodes

Un shortcode es una "etiqueta" que puedes escribir dentro del editor de WordPress y se reemplaza por contenido dinámico.

### Crear un shortcode:
```php
function gymfitness_ubicacion_shortcode() {
    ?>
    <div class="mapa">
        <?php
            if(is_page('contacto')) {
                the_field('ubicacion');   // Campo ACF con mapa
            }
        ?>
    </div>

    <h2 class="text-center text-primary">Formulario de Contacto</h2>
    <?php
    // Ejecutar otro shortcode dentro del tuyo
    echo do_shortcode('[contact-form-7 id="102" title="Formulario de contacto 1"]');
}
add_shortcode('gymfitness_ubicacion', 'gymfitness_ubicacion_shortcode');
```

### Cómo se usa:
En el editor de WordPress escribes: `[gymfitness_ubicacion]`
WordPress lo reemplaza por el HTML que genera la función.

### Shortcode con parámetros (ejemplo para entrevista):
```php
function mi_shortcode($atts) {
    $atts = shortcode_atts( array(
        'color'  => 'rojo',      // Valor por defecto
        'tamaño' => 'grande',
    ), $atts );

    return '<div class="' . esc_attr($atts['color']) . '">' . esc_html($atts['tamaño']) . '</div>';
}
add_shortcode('mi_elemento', 'mi_shortcode');
// Uso: [mi_elemento color="azul" tamaño="pequeño"]
```

**Pregunta de entrevista:** *¿Diferencia entre `do_shortcode()` y simplemente escribir el shortcode?*
- En el editor de WP, escribes `[shortcode]` y WordPress lo procesa automáticamente
- En el código PHP, debes usar `echo do_shortcode('[shortcode]')` para que se procese

---

## 17. Conditional Tags (Etiquetas Condicionales)

Las Conditional Tags permiten ejecutar código solo en contextos específicos.

### Usadas en este tema:
```php
is_front_page()     // ¿Es la página de inicio?
is_page('galeria')  // ¿Es la página con slug "galeria"?
is_page('contacto') // ¿Es la página con slug "contacto"?
```

### Lista de Conditional Tags más importantes:
| Función | Devuelve `true` cuando... |
|---|---|
| `is_front_page()` | Es la página de inicio |
| `is_home()` | Es la página del blog |
| `is_single()` | Es un post individual |
| `is_page()` | Es una página |
| `is_page('contacto')` | Es la página con slug "contacto" |
| `is_page(42)` | Es la página con ID 42 |
| `is_category()` | Es un archivo de categoría |
| `is_archive()` | Es cualquier archivo (categoría, fecha, autor, tag) |
| `is_author()` | Es un archivo de autor |
| `is_search()` | Es la página de resultados de búsqueda |
| `is_404()` | Es la página de error 404 |
| `is_singular()` | Es un single post o page |
| `is_singular('gymfitness_clases')` | Es un single de un CPT específico |
| `has_post_thumbnail()` | El post tiene imagen destacada |
| `is_active_sidebar('sidebar_1')` | El sidebar tiene widgets |

### Ejemplo práctico — Carga condicional de assets:
```php
// Solo carga Swiper en la página de inicio
if(is_front_page()) {
    wp_enqueue_style('swiper-css', '...');
    wp_enqueue_script('swiper-js', '...');
}

// Solo carga Lightbox en la galería
if(is_page('galeria')) {
    wp_enqueue_style('lightboxcss', '...');
    wp_enqueue_script('lightboxjs', '...');
}
```

---

## 18. Seguridad: Escape y Sanitización

### Regla de oro:
> **Escapa todo lo que se muestra (output), sanitiza todo lo que se guarda (input).**

### Funciones de Escape (Output):
Usadas en este tema:

```php
// Escapa atributos HTML (src, alt, href, id, class, etc.)
echo esc_attr($imagen);
// Salida: convierte caracteres especiales para que sean seguros en atributos

// Escapa contenido HTML (texto visible)
echo esc_html($texto);
// Salida: convierte <, >, &, ", ' en entidades HTML

// Escapa URLs
echo esc_url( get_permalink( get_page_by_title('Nuestras Clases') ) );
// Salida: URL sanitizada y segura

// Escapa para traducciones
esc_html__('Texto', 'gymfitness');   // Retorna traducción escapada
esc_attr_e('Texto');                  // Imprime traducción escapada para atributo
```

### Funciones de Sanitización (Input):
Usadas en el widget:
```php
$instance['cantidad'] = sanitize_text_field($new_instance['cantidad']);
```

### Tabla de funciones de seguridad:
| Función | Uso | Cuándo |
|---|---|---|
| `esc_html()` | Escapar texto para HTML | Al mostrar texto libre en el HTML |
| `esc_attr()` | Escapar atributos HTML | En `src=""`, `alt=""`, `class=""`, etc. |
| `esc_url()` | Escapar URLs | En `href=""`, `src=""` de imágenes |
| `esc_js()` | Escapar para JavaScript | Texto dentro de JS inline |
| `wp_kses()` | Permitir solo ciertos tags HTML | Contenido enriquecido controlado |
| `sanitize_text_field()` | Limpiar texto plano | Al guardar datos de formularios |
| `sanitize_email()` | Validar formato email | Al guardar emails |
| `absint()` | Convertir a entero positivo | IDs, cantidades |

**Pregunta de entrevista:** *¿Qué es XSS y cómo lo previene WordPress?*
- **XSS (Cross-Site Scripting)** = un atacante inyecta JavaScript malicioso en tu sitio
- Se previene **escapando** toda la salida con `esc_html()`, `esc_attr()`, etc.
- Si alguien guarda `<script>alert('hack')</script>` como su nombre, `esc_html()` lo convierte en texto inofensivo

---

## 19. Internacionalización (i18n)

Permite que el tema sea traducible a otros idiomas.

### Funciones usadas en este tema:
```php
__('Menu Principal', 'gymfitness')        // Retorna texto traducible
esc_html__('GymFitness Clases', 'gymfitness')  // Retorna texto escapado y traducible
esc_attr_e('¿Cuántas clases?')            // Imprime texto escapado y traducible
```

### Tabla de funciones de traducción:
| Función | Acción | Escape |
|---|---|---|
| `__('texto', 'dominio')` | **Retorna** | No |
| `_e('texto', 'dominio')` | **Imprime** | No |
| `esc_html__('texto', 'dominio')` | **Retorna** | HTML |
| `esc_html_e('texto', 'dominio')` | **Imprime** | HTML |
| `esc_attr__('texto', 'dominio')` | **Retorna** | Atributo |
| `esc_attr_e('texto', 'dominio')` | **Imprime** | Atributo |

El segundo parámetro siempre es el **Text Domain** del tema (`gymfitness`).

---

## 20. Imágenes Destacadas y Tamaños

### Habilitar soporte:
```php
add_theme_support('post-thumbnails');  // En functions.php con after_setup_theme
```

### Mostrar la imagen destacada:
```php
// Imprime el tag <img> completo
the_post_thumbnail();                  // Tamaño por defecto
the_post_thumbnail('thumbnail');       // 150x150
the_post_thumbnail('medium');          // 300x300
the_post_thumbnail('medium_large');    // 768px ancho
the_post_thumbnail('large');           // 1024x1024
the_post_thumbnail('full');            // Tamaño original

// Con atributos HTML adicionales
the_post_thumbnail('full', array('class' => 'imagen-destacada'));

// Verificar si tiene imagen antes de mostrar
if(has_post_thumbnail()) {
    the_post_thumbnail('full');
}
```

### Obtener la URL de una imagen (sin el tag `<img>`):
```php
// Desde un ID de attachment
$url = wp_get_attachment_image_src($id_imagen, 'full')[0];

// Desde la imagen destacada del post actual
$url = get_the_post_thumbnail_url(get_the_ID(), 'large');
```

### Registrar tamaños personalizados:
```php
add_image_size('blog-card', 600, 400, true);  // nombre, ancho, alto, crop
// Uso: the_post_thumbnail('blog-card');
```

---

## 21. Sistema de Comentarios

### Mostrar formulario y listar comentarios (`single.php`):
```php
<div class="comentarios">
    <?php comment_form(); ?>  <!-- Formulario para escribir comentario -->

    <h3>Comentarios</h3>
    <ul class="lista-comentarios">
        <?php
            // Obtener comentarios aprobados del post actual
            $comentarios = get_comments( array(
                'post_id' => $post->ID,
                'status'  => 'approve'
            ));

            // Listar comentarios con formato
            wp_list_comments( array(
                'per_page'          => 10,
                'reverse_top_level' => false
            ), $comentarios);
        ?>
    </ul>
</div>
```

### Funciones clave:
| Función | Propósito |
|---|---|
| `comment_form()` | Genera el formulario completo de comentarios |
| `get_comments()` | Obtiene comentarios filtrados |
| `wp_list_comments()` | Genera HTML de la lista de comentarios (soporta hilos) |
| `comments_template()` | Carga el archivo `comments.php` (alternativa más flexible) |

---

## 22. Paginación

### Paginación nativa de WordPress:
```php
<?php the_posts_pagination(); ?>
```

WordPress genera automáticamente la paginación basándose en la configuración de **Settings > Reading > "Blog pages show at most X posts"**.

### Paginación personalizada:
```php
the_posts_pagination( array(
    'mid_size'  => 2,                    // Links a cada lado de la actual
    'prev_text' => '&laquo; Anterior',
    'next_text' => 'Siguiente &raquo;',
));
```

### Con WP_Query personalizado:
```php
$paged = get_query_var('paged') ? get_query_var('paged') : 1;
$args = array(
    'post_type'      => 'post',
    'posts_per_page' => 10,
    'paged'          => $paged
);
$query = new WP_Query($args);
```

---

## 23. CSS: Mobile First, Grid, Flexbox y Custom Properties

### Custom Properties (Variables CSS):
```css
:root {
    --fuente-principal: 'Raleway', sans-serif;
    --fuente-headings:  'Staatliches', cursive;
    --primario:         #ff5b00;
    --gris-oscuro:      #2f2e2e;
    --gris-claro:       #EBEBEB;
    --blanco:           #FFF;
    --negro:            #000;
}

/* Uso */
body { font-family: var(--fuente-principal); }
h1   { font-family: var(--fuente-headings); }
.boton-primario { background-color: var(--primario); }
```

### Truco del `font-size: 62.5%`:
```css
html {
    font-size: 62.5%;  /* 62.5% de 16px = 10px */
    /* Ahora 1rem = 10px, lo que facilita los cálculos:
       1.6rem = 16px, 2rem = 20px, 2.4rem = 24px */
}
```

### Mobile First:
Estilos base = móvil, luego se agregan estilos para pantallas más grandes con `min-width`:

```css
/* BASE: Móvil (una columna) */
.areas {
    display: grid;
    grid-template-columns: 1fr;
}

/* Tablet: 2 columnas */
@media (min-width: 480px) {
    .areas {
        grid-template-columns: repeat(2, 1fr);
    }
}

/* Desktop: 4 columnas */
@media (min-width: 768px) {
    .areas {
        grid-template-columns: repeat(4, 1fr);
    }
}
```

### Breakpoints del tema:
| Breakpoint | Dispositivo |
|---|---|
| < 480px | Móvil (base) |
| 480px | Móvil grande |
| 768px | Tablet |
| 992px | Desktop |

### CSS Grid — Layouts principales:
```css
/* Grid de 2 columnas para cards */
.listado-grid {
    display: grid;
    gap: 2rem;
    grid-template-columns: repeat(2, 1fr);
}

/* Layout con sidebar (2/3 + 1/3) */
.con-sidebar {
    display: grid;
    grid-template-columns: 2fr 1fr;
    gap: 4rem;
}

/* Galería con items de diferente tamaño */
.galeria-imagenes {
    grid-template-columns: repeat(4, 1fr);
}
.galeria-imagenes li:nth-child(4) {
    grid-column: 4 / 5;
    grid-row: 1 / 3;        /* Ocupa 2 filas */
}
```

### Flexbox — Navegación y componentes:
```css
/* Barra de navegación */
.barra-navegacion {
    display: flex;
    flex-direction: column;
    align-items: center;
}
@media (min-width: 768px) {
    .barra-navegacion {
        flex-direction: row;
        justify-content: space-between;
    }
}
```

### Función CSS `min()` para contenedores responsivos:
```css
.contenedor {
    width: min(95%, 120rem);  /* Nunca más de 1200px, 95% en pantallas chicas */
    margin: 0 auto;
}
```

### Animación CSS del menú de navegación:
```css
/* Hover en los links con pseudo-elementos animados */
.menu-principal a::before,
.menu-principal a::after {
    content: '';
    position: absolute;
    width: 100%;
    height: 50%;
    background-color: var(--primario);
    transform: scaleX(0);            /* Oculto */
    transition: transform .6s;
}
.menu-principal a::before { transform-origin: left; }
.menu-principal a::after  { transform-origin: right; }

.menu-principal a:hover::before,
.menu-principal a:hover::after {
    transform: scaleX(1);            /* Se expande al hacer hover */
}
```

**Pregunta de entrevista:** *¿Cuál es la diferencia entre Mobile First y Desktop First?*
- **Mobile First** → estilos base para móvil, `@media (min-width)` para agregar estilos en pantallas grandes
- **Desktop First** → estilos base para desktop, `@media (max-width)` para quitar estilos en pantallas chicas
- Mobile First es la práctica recomendada porque es más fácil agregar que quitar

---

## 24. JavaScript en WordPress

### Cómo WordPress maneja JS:
1. Se registra/encola con `wp_enqueue_script()` en `functions.php`
2. Se carga antes de `</body>` si el último parámetro es `true`
3. jQuery viene incluido con WordPress (no hace falta cargarlo manualmente)

### Vanilla JS del tema (`scripts.js`):
```javascript
// Todo se inicia cuando el DOM está listo
document.addEventListener('DOMContentLoaded', function() {

    // 1. Inicializar Swiper (solo existe en front page)
    if(document.querySelector('.swiper')) {
        new Swiper('.swiper', {
            loop: true,
            autoplay: { delay: 3000 }
        });
    }

    // 2. Animación de texto con anime.js
    var textWrapper = document.querySelector('.ml2');
    if(textWrapper) {
        textWrapper.innerHTML = textWrapper.textContent.replace(
            /\S/g, "<span class='letter'>$&</span>"
        );
        anime.timeline({loop: true})
            .add({
                targets: '.ml2 .letter',
                scale: [4, 1],
                opacity: [0, 1],
                duration: 950,
                delay: (el, i) => 70 * i
            })
            .add({
                targets: '.ml2',
                opacity: 0,
                duration: 1000,
                delay: 1000
            });
    }

    // 3. Toggle del menú hamburguesa
    const hamburger = document.querySelector('.hamburguer-menu svg');
    hamburger.addEventListener('click', function() {
        document.querySelector('.contenedor-menu').classList.toggle('mostrar');
    });
});

// 4. Navegación sticky con scroll
window.onscroll = function() {
    const barraNav = document.querySelector('.barra-navegacion');
    if(window.scrollY > 300) {
        barraNav.classList.add('fixed-top');
    } else {
        barraNav.classList.remove('fixed-top');
    }
};
```

### Pasar datos de PHP a JS:
```php
// En functions.php (no presente en este tema, pero importante para entrevista)
wp_localize_script('scripts', 'gymfitness_params', array(
    'ajax_url'  => admin_url('admin-ajax.php'),
    'nonce'     => wp_create_nonce('mi_nonce'),
    'site_url'  => site_url()
));
// En JS: gymfitness_params.ajax_url
```

---

## 25. Custom Page Templates (Plantillas de Página)

Permiten crear diseños únicos para páginas específicas. Se activan con un **comentario especial** al inicio del archivo.

### Declarar una plantilla personalizada:
```php
<?php
/*
 * Template Name: Galería
 */
get_header();
?>
    <!-- Contenido personalizado de la galería -->
<?php get_footer(); ?>
```

### Plantillas del tema:

| Archivo | Template Name | Funcionalidad |
|---|---|---|
| `page-galeria.php` | Galería | Grid de imágenes con lightbox |
| `page-listado-clases.php` | Listado de Clases | Lista todas las clases del CPT |
| `page-contenido-centrado.php` | Contenido Centrado (No Sidebars) | Contenido sin sidebars |

### Ejemplo completo — Galería con WordPress Gallery + Lightbox:
```php
<?php
/*
 * Template Name: Galería
 */
get_header();
?>
    <main class="contenedor seccion">
        <?php while( have_posts() ): the_post();
            the_title('<h1 class="text-center text-primary">', '</h1>');

            // Obtener la galería nativa de WordPress
            $galeria = get_post_gallery( get_the_ID(), false );
            $galeria_imagenes_ID = explode(",", $galeria['ids']);
        ?>
            <ul class="galeria-imagenes">
                <?php foreach($galeria_imagenes_ID as $id) {
                    $imagen_grande = wp_get_attachment_image_src($id, 'large')[0];
                    $imagen_full = wp_get_attachment_image_src($id, 'full')[0];
                ?>
                    <li>
                        <a data-lightbox="galeria" href="<?php echo $imagen_full; ?>">
                            <img src="<?php echo $imagen_grande; ?>" alt="imagen galeria"/>
                        </a>
                    </li>
                <?php } ?>
            </ul>
        <?php endwhile; ?>
    </main>
<?php get_footer(); ?>
```

### ¿Cómo se asigna?
En el editor de WP, al crear/editar una página, en el panel lateral aparece **"Atributos de Página" > "Plantilla"**, donde seleccionas "Galería", "Listado de Clases", etc.

---

## 26. Preguntas Frecuentes de Entrevista

### Conceptos Generales

**P: ¿Cuáles son los archivos mínimos para un tema de WordPress?**
R: `style.css` (con el header de metadatos) e `index.php`.

**P: ¿Qué es la Template Hierarchy?**
R: Es el sistema que WordPress usa para decidir qué archivo PHP renderizar según la URL actual. Busca desde el más específico (ej: `single-producto.php`) hasta el más general (`index.php`).

**P: ¿Diferencia entre tema padre y tema hijo (child theme)?**
R: Un child theme hereda del padre y permite personalizarlo sin modificar sus archivos originales. Se crea una carpeta con `style.css` que declara `Template: nombre-tema-padre` y un `functions.php` que puede agregar o sobrescribir funcionalidades.

**P: ¿Qué es el Loop?**
R: Es el mecanismo `while(have_posts()) { the_post(); ... }` que itera sobre los posts disponibles para la página actual y hace disponibles las Template Tags.

**P: ¿Qué diferencia hay entre `get_template_part()`, `include` y `require`?**
R: `get_template_part()` es la función WordPress que busca primero en el child theme y luego en el parent theme. `include`/`require` son funciones PHP que cargan archivos directamente sin esa búsqueda.

---

### Hooks y Funciones

**P: ¿Qué es un hook?**
R: Un punto de extensión donde puedes "enganchar" tu código. Los **actions** ejecutan código en un momento dado, los **filters** modifican datos antes de mostrarlos/guardarlos.

**P: Nombra 5 hooks importantes.**
R: `init`, `wp_enqueue_scripts`, `after_setup_theme`, `widgets_init`, `the_content` (filter), `save_post`, `wp_head`, `wp_footer`.

**P: ¿Qué es la prioridad en `add_action()`?**
R: El tercer parámetro (default: 10) determina el orden de ejecución. Números menores = se ejecuta primero.

---

### Seguridad

**P: ¿Cómo prevenir XSS en WordPress?**
R: Escapando toda la salida: `esc_html()` para texto, `esc_attr()` para atributos, `esc_url()` para URLs.

**P: ¿Qué es un nonce?**
R: Un token de seguridad que verifica que una acción viene de tu sitio y no de un tercero. Se crea con `wp_create_nonce()` y se verifica con `wp_verify_nonce()`.

**P: ¿Cómo prevenir SQL injection?**
R: Usando `$wpdb->prepare()` para queries directas, o mejor aún, usando `WP_Query` que ya maneja esto internamente.

---

### Performance

**P: ¿Cómo optimizar un tema WordPress?**
R:
1. Carga condicional de scripts con `is_page()`, `is_front_page()`, etc.
2. Usar `$in_footer = true` en `wp_enqueue_script()` para no bloquear el render
3. Especificar versiones en enqueue para cache busting
4. Usar tamaños de imagen adecuados (`thumbnail`, `medium`, `large` en vez de `full`)
5. Usar `wp_reset_postdata()` siempre después de WP_Query personalizado

**P: ¿Por qué cargar JS en el footer es mejor?**
R: Porque no bloquea la renderización del HTML. El navegador puede mostrar contenido visual mientras descarga y ejecuta el JS.

---

### Base de Datos y Datos

**P: ¿Dónde guarda WordPress los custom fields?**
R: En la tabla `wp_postmeta`, con columnas: `meta_id`, `post_id`, `meta_key`, `meta_value`.

**P: ¿Qué diferencia hay entre `get_option()` y `get_post_meta()`?**
R: `get_option()` lee datos globales del sitio (tabla `wp_options`). `get_post_meta()` lee datos asociados a un post específico (tabla `wp_postmeta`).

**P: ¿Cuándo usar transients?**
R: Para cachear datos costosos (llamadas a APIs externas, queries pesados). Se guardan con tiempo de expiración: `set_transient('key', $data, HOUR_IN_SECONDS)`.

---

### Sobre este Proyecto Específico

**P: ¿Qué plugins requiere este tema?**
R: **ACF** (Advanced Custom Fields) para campos personalizados y **Contact Form 7** para formularios.

**P: ¿Qué librerías JS usa y cuándo se cargan?**
R:
- **Swiper 8.4.5** → solo en `front-page.php` (slider de testimoniales)
- **anime.js 2.0.2** → solo en `front-page.php` (animación del hero)
- **Lightbox 2.11.3** → solo en la página "galería" (visor de imágenes)
- **normalize.css** → en todas las páginas (reset CSS)

**P: ¿Cómo maneja las imágenes dinámicas del hero?**
R: Usa `wp_add_inline_style()` para inyectar CSS dinámico que establece la imagen ACF como `background-image` del header, con un overlay de gradiente oscuro.

---

## Resumen Visual: Flujo de una Página en WordPress

```
1. Usuario visita URL
        ↓
2. WordPress determina el contexto (front-page, single, category, etc.)
        ↓
3. Template Hierarchy selecciona el archivo PHP correcto
        ↓
4. El archivo PHP llama a get_header() → carga header.php
        ↓
5. wp_head() en header.php inyecta CSS/JS registrados con wp_enqueue_*
        ↓
6. El Loop (have_posts/the_post) obtiene los datos de la BD
        ↓
7. Template Tags (the_title, the_content, get_field) renderizan el contenido
        ↓
8. get_template_part() carga fragmentos reutilizables
        ↓
9. get_sidebar() carga widgets de la zona registrada
        ↓
10. get_footer() carga footer.php → wp_footer() inyecta JS
        ↓
11. HTML completo se envía al navegador
```

---

**Tip final:** Practica explicando cada concepto con ejemplos de este proyecto. En una entrevista, poder decir "en mi proyecto GymFitness implementé X usando Y" es mucho más poderoso que solo dar definiciones teóricas.
