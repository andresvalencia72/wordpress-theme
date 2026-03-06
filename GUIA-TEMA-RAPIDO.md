# Guía Rápida: Crear un Tema WordPress desde Cero

## 1. Archivos mínimos obligatorios

Crea una carpeta en `wp-content/themes/mi-tema/` con estos 2 archivos:

**style.css** — WordPress lee este comentario para registrar el tema:
```css
/*
    Theme Name: Mi Tema
    Version: 1.0
    Text Domain: mi-tema
*/
```

**index.php** — Plantilla de respaldo:
```php
<?php get_header(); ?>
    <?php while(have_posts()) { the_post(); ?>
        <h2><?php the_title(); ?></h2>
        <?php the_content(); ?>
    <?php } ?>
<?php get_footer(); ?>
```

Con eso ya tienes un tema activable.

---

## 2. Agregar header y footer

**header.php**
```php
<!DOCTYPE html>
<html <?php language_attributes(); ?>>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <?php wp_head(); ?>
</head>
<body <?php body_class(); ?>>
    <header>
        <!-- Tu navegación -->
    </header>
```

**footer.php**
```php
    <footer>
        <!-- Tu pie de página -->
    </footer>
    <?php wp_footer(); ?>
</body>
</html>
```

---

## 3. functions.php — Activar todo

```php
<?php
// Habilitar features
function mi_tema_setup() {
    add_theme_support('post-thumbnails');
    add_theme_support('title-tag');
}
add_action('after_setup_theme', 'mi_tema_setup');

// Cargar CSS y JS
function mi_tema_scripts() {
    wp_enqueue_style('style', get_stylesheet_uri(), array(), '1.0');
    wp_enqueue_script('scripts', get_template_directory_uri() . '/js/scripts.js', array(), '1.0', true);
}
add_action('wp_enqueue_scripts', 'mi_tema_scripts');

// Registrar menú
function mi_tema_menus() {
    register_nav_menus(array('menu-principal' => 'Menu Principal'));
}
add_action('init', 'mi_tema_menus');
```

---

## 4. Template Hierarchy — Lo esencial

Crea solo los archivos que necesites:

| Quieres mostrar... | Crea el archivo |
|---|---|
| Página de inicio | `front-page.php` |
| Blog (listado) | `home.php` |
| Post individual | `single.php` |
| Página | `page.php` |
| Categoría | `category.php` |
| CPT individual | `single-{cpt}.php` |

Todos siguen el mismo patrón:
```php
<?php get_header(); ?>
    <!-- Tu contenido + Loop -->
<?php get_footer(); ?>
```

---

## 5. El Loop — Siempre igual

```php
<?php while(have_posts()) { the_post(); ?>
    <?php the_title(); ?>        <!-- Título -->
    <?php the_content(); ?>      <!-- Contenido -->
    <?php the_post_thumbnail(); ?> <!-- Imagen -->
    <?php the_permalink(); ?>    <!-- URL -->
    <?php the_time('d/m/Y'); ?>  <!-- Fecha -->
<?php } ?>
```

---

## 6. WP_Query — Traer contenido personalizado

```php
<?php
$query = new WP_Query(array(
    'post_type'      => 'post',
    'posts_per_page' => 4
));
while($query->have_posts()) {
    $query->the_post();
    the_title();
}
wp_reset_postdata(); // SIEMPRE al final
?>
```

---

## 7. Orden de trabajo recomendado

```
1. Crear carpeta del tema con style.css + index.php
2. Activar el tema en WP Admin
3. Crear header.php + footer.php
4. Crear functions.php (scripts, menús, thumbnails)
5. Crear las plantillas que necesites (front-page, single, page, etc.)
6. Extraer partes repetidas a template-parts/
7. Agregar sidebar si necesitas
```

---

## Cheatsheet de funciones

| Hacer esto | Usa esto |
|---|---|
| URL del tema | `get_template_directory_uri()` |
| Ruta del tema (PHP) | `get_template_directory()` |
| Incluir header/footer | `get_header()` / `get_footer()` |
| Incluir partial | `get_template_part('template-parts/card')` |
| Incluir sidebar | `get_sidebar()` |
| Mostrar menú | `wp_nav_menu(array('theme_location' => 'menu-principal'))` |
| URL del sitio | `site_url('/')` |
| Nombre del sitio | `get_bloginfo('name')` |
| Escapar texto | `esc_html($var)` |
| Escapar URL | `esc_url($var)` |
| Escapar atributo | `esc_attr($var)` |

Eso es todo lo que necesitas para arrancar. El resto se va agregando según lo necesites.
