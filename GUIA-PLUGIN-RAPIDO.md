# Guía Rápida: Crear un Plugin WordPress desde Cero

> Para principiantes. Sin rodeos, solo lo que necesitas saber.

---

## ¿Qué es un Plugin?

Un plugin **agrega funcionalidad** a WordPress. A diferencia de un tema (que controla cómo se ve), el plugin controla **qué hace** el sitio. Si cambias de tema, el plugin sigue funcionando.

**Ejemplos:** formulario de contacto, SEO, tienda online (WooCommerce), custom post types, etc.

---

## 1. Archivo mínimo — Un plugin en 30 segundos

Crea una carpeta en `wp-content/plugins/mi-plugin/` con un solo archivo:

**mi-plugin.php**
```php
<?php
/*
 * Plugin Name: Mi Plugin
 * Description: Descripción corta de qué hace
 * Version: 1.0
 * Author: Tu Nombre
 */

// Seguridad: evita acceso directo al archivo
if (!defined('ABSPATH')) exit;

// Tu código va aquí
```

Eso es todo. Ve a **WP Admin > Plugins** y ya aparece para activarlo.

El comentario del header es lo único obligatorio. WordPress lo lee para mostrar el plugin en la lista.

---

## 2. ¿Dónde va mi código?

Todo tu código va **dentro del archivo principal** del plugin (o en archivos que incluyas desde ahí). Nunca fuera.

### Estructura simple:
```
wp-content/plugins/mi-plugin/
├── mi-plugin.php        ← Archivo principal (obligatorio)
```

### Estructura cuando crece:
```
wp-content/plugins/mi-plugin/
├── mi-plugin.php        ← Archivo principal
├── includes/
│   ├── cpt.php          ← Custom Post Types
│   ├── shortcodes.php   ← Shortcodes
│   └── admin.php        ← Páginas del admin
├── css/
│   └── estilos.css
├── js/
│   └── scripts.js
└── templates/
    └── formulario.php
```

Para incluir archivos:
```php
require plugin_dir_path(__FILE__) . 'includes/cpt.php';
require plugin_dir_path(__FILE__) . 'includes/shortcodes.php';
```

---

## 3. Hooks — Cómo funciona TODO en WordPress

WordPress funciona con **hooks** (ganchos). Son puntos donde WordPress te dice: "aquí puedes enganchar tu código".

Hay 2 tipos:

### Actions — "Haz algo en este momento"
```php
// Sintaxis: add_action('cuándo', 'tu_función');

function mi_plugin_hacer_algo() {
    // Tu código
}
add_action('init', 'mi_plugin_hacer_algo');
```

### Filters — "Modifica este dato antes de usarlo"
```php
// Sintaxis: add_filter('qué_dato', 'tu_función');

function mi_plugin_modificar_titulo($titulo) {
    return '★ ' . $titulo;  // Agrega una estrella a todos los títulos
}
add_filter('the_title', 'mi_plugin_modificar_titulo');
```

### Hooks más usados:

| Hook | Tipo | Cuándo se ejecuta |
|---|---|---|
| `init` | Action | Al arrancar WordPress (registrar CPT, taxonomías) |
| `wp_enqueue_scripts` | Action | Para cargar CSS/JS en el frontend |
| `admin_enqueue_scripts` | Action | Para cargar CSS/JS en el admin |
| `admin_menu` | Action | Para agregar páginas al menú del admin |
| `save_post` | Action | Cuando se guarda un post |
| `wp_footer` | Action | Antes de cerrar `</body>` |
| `the_content` | Filter | Modifica el contenido de un post antes de mostrarlo |
| `the_title` | Filter | Modifica el título antes de mostrarlo |

---

## 4. Custom Post Types (CPT) — Lo más común en plugins

Un CPT es un nuevo tipo de contenido. WordPress trae "Posts" y "Pages", pero puedes crear los tuyos: Productos, Recetas, Propiedades, etc.

```php
function mi_plugin_registrar_cpt() {
    register_post_type('producto', array(
        'labels' => array(
            'name'          => 'Productos',
            'singular_name' => 'Producto',
            'add_new'       => 'Agregar Producto',
            'add_new_item'  => 'Agregar Nuevo Producto',
            'edit_item'     => 'Editar Producto',
            'not_found'     => 'No se encontraron productos',
        ),
        'public'       => true,       // Visible en frontend y admin
        'has_archive'  => true,       // Tiene página de listado (/productos/)
        'menu_icon'    => 'dashicons-cart',  // Ícono en el menú admin
        'supports'     => array('title', 'editor', 'thumbnail'),
        'rewrite'      => array('slug' => 'productos'),
    ));
}
add_action('init', 'mi_plugin_registrar_cpt');
```

Después de activar, aparece "Productos" en el menú del admin.

### Opciones de `supports`:
| Valor | Qué habilita |
|---|---|
| `title` | Campo de título |
| `editor` | Editor de contenido |
| `thumbnail` | Imagen destacada |
| `excerpt` | Extracto/resumen |
| `comments` | Comentarios |
| `author` | Selector de autor |
| `custom-fields` | Campos personalizados nativos |

### Iconos para `menu_icon`:
Usa cualquiera de: https://developer.wordpress.org/resource/dashicons/

Ejemplos: `dashicons-cart`, `dashicons-admin-users`, `dashicons-calendar`, `dashicons-heart`, `dashicons-star-filled`

---

## 5. Taxonomías — Categorías personalizadas para tu CPT

Las taxonomías son como las "Categorías" o "Tags" pero para tus CPT.

```php
function mi_plugin_registrar_taxonomia() {
    register_taxonomy('tipo_producto', 'producto', array(
        'labels' => array(
            'name'          => 'Tipos de Producto',
            'singular_name' => 'Tipo de Producto',
            'add_new_item'  => 'Agregar Tipo de Producto',
        ),
        'public'       => true,
        'hierarchical' => true,   // true = como Categorías (con padres/hijos)
                                  // false = como Tags (planas)
        'rewrite'      => array('slug' => 'tipo'),
    ));
}
add_action('init', 'mi_plugin_registrar_taxonomia');
```

---

## 6. Shortcodes — Insertar tu plugin en cualquier página

Un shortcode es una "etiqueta" que el usuario escribe en el editor y WordPress reemplaza por HTML.

### Shortcode simple:
```php
function mi_plugin_saludo() {
    return '<div class="saludo"><h2>¡Hola desde mi plugin!</h2></div>';
}
add_shortcode('saludo', 'mi_plugin_saludo');

// Uso en el editor: [saludo]
```

### Shortcode con parámetros:
```php
function mi_plugin_boton($atts) {
    $atts = shortcode_atts(array(
        'texto' => 'Click aquí',       // Valor por defecto
        'url'   => '#',
        'color' => 'blue',
    ), $atts);

    return '<a href="' . esc_url($atts['url']) . '"
               style="background:' . esc_attr($atts['color']) . '"
               class="mi-boton">' . esc_html($atts['texto']) . '</a>';
}
add_shortcode('boton', 'mi_plugin_boton');

// Uso: [boton texto="Comprar" url="/tienda" color="red"]
```

### Shortcode que lista tu CPT:
```php
function mi_plugin_listar_productos($atts) {
    $atts = shortcode_atts(array(
        'cantidad' => 6,
    ), $atts);

    $query = new WP_Query(array(
        'post_type'      => 'producto',
        'posts_per_page' => $atts['cantidad'],
    ));

    $html = '<div class="productos-grid">';
    while ($query->have_posts()) {
        $query->the_post();
        $html .= '<div class="producto">';
        $html .= '<h3>' . get_the_title() . '</h3>';
        $html .= get_the_post_thumbnail(get_the_ID(), 'medium');
        $html .= '</div>';
    }
    $html .= '</div>';
    wp_reset_postdata();

    return $html;  // Los shortcodes SIEMPRE retornan, nunca imprimen (echo)
}
add_shortcode('productos', 'mi_plugin_listar_productos');

// Uso: [productos cantidad="4"]
```

**Regla importante:** los shortcodes **siempre usan `return`**, nunca `echo`.

---

## 7. Cargar CSS y JS desde tu plugin

```php
function mi_plugin_scripts() {
    // CSS
    wp_enqueue_style(
        'mi-plugin-css',
        plugin_dir_url(__FILE__) . 'css/estilos.css',
        array(),
        '1.0'
    );

    // JS
    wp_enqueue_script(
        'mi-plugin-js',
        plugin_dir_url(__FILE__) . 'js/scripts.js',
        array(),     // Dependencias, ej: array('jquery')
        '1.0',
        true         // Cargar en el footer
    );
}
add_action('wp_enqueue_scripts', 'mi_plugin_scripts');
```

### Diferencia clave tema vs plugin:
| En un tema | En un plugin |
|---|---|
| `get_template_directory_uri()` | `plugin_dir_url(__FILE__)` |
| `get_template_directory()` | `plugin_dir_path(__FILE__)` |
| `get_stylesheet_uri()` | No existe, usa `plugin_dir_url` |

---

## 8. Página de Ajustes en el Admin

### Agregar una página al menú del admin:
```php
function mi_plugin_menu_admin() {
    add_menu_page(
        'Mi Plugin',              // Título de la página
        'Mi Plugin',              // Texto del menú
        'manage_options',         // Permiso requerido (solo admins)
        'mi-plugin',              // Slug de la URL
        'mi_plugin_pagina_admin', // Función que renderiza la página
        'dashicons-admin-generic', // Ícono
        100                        // Posición en el menú
    );
}
add_action('admin_menu', 'mi_plugin_menu_admin');

function mi_plugin_pagina_admin() {
    ?>
    <div class="wrap">
        <h1>Configuración de Mi Plugin</h1>
        <form method="post" action="options.php">
            <?php
            settings_fields('mi_plugin_opciones');
            do_settings_sections('mi-plugin');
            submit_button();
            ?>
        </form>
    </div>
    <?php
}
```

### Registrar opciones (Settings API):
```php
function mi_plugin_registrar_opciones() {
    // Registrar la opción en la BD
    register_setting('mi_plugin_opciones', 'mi_plugin_color');
    register_setting('mi_plugin_opciones', 'mi_plugin_cantidad');

    // Crear una sección
    add_settings_section(
        'mi_plugin_seccion',
        'Opciones Generales',
        null,
        'mi-plugin'
    );

    // Agregar campos a la sección
    add_settings_field(
        'color',
        'Color principal',
        'mi_plugin_campo_color',
        'mi-plugin',
        'mi_plugin_seccion'
    );
}
add_action('admin_init', 'mi_plugin_registrar_opciones');

function mi_plugin_campo_color() {
    $valor = get_option('mi_plugin_color', '#ff0000');
    echo '<input type="color" name="mi_plugin_color" value="' . esc_attr($valor) . '">';
}
```

### Leer las opciones en tu código:
```php
$color = get_option('mi_plugin_color', '#ff0000');    // Segundo param = default
$cantidad = get_option('mi_plugin_cantidad', 10);
```

---

## 9. Meta Boxes — Campos personalizados en el editor

Un meta box agrega campos extra cuando editas un post/página/CPT.

```php
// Agregar el meta box
function mi_plugin_agregar_metabox() {
    add_meta_box(
        'precio_producto',          // ID único
        'Precio del Producto',      // Título visible
        'mi_plugin_metabox_html',   // Función que renderiza el HTML
        'producto',                 // En qué post type aparece
        'side'                      // Posición: 'normal', 'side', 'advanced'
    );
}
add_action('add_meta_boxes', 'mi_plugin_agregar_metabox');

// HTML del meta box
function mi_plugin_metabox_html($post) {
    $precio = get_post_meta($post->ID, '_precio', true);
    wp_nonce_field('mi_plugin_nonce_action', 'mi_plugin_nonce');
    ?>
    <label for="precio">Precio ($):</label>
    <input type="number" id="precio" name="precio"
           value="<?php echo esc_attr($precio); ?>" style="width:100%">
    <?php
}

// Guardar el dato cuando se guarda el post
function mi_plugin_guardar_metabox($post_id) {
    // Verificar nonce (seguridad)
    if (!isset($_POST['mi_plugin_nonce'])) return;
    if (!wp_verify_nonce($_POST['mi_plugin_nonce'], 'mi_plugin_nonce_action')) return;

    // No guardar en autoguardado
    if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) return;

    // Guardar el campo
    if (isset($_POST['precio'])) {
        update_post_meta($post_id, '_precio', sanitize_text_field($_POST['precio']));
    }
}
add_action('save_post', 'mi_plugin_guardar_metabox');
```

### Leer el campo en el frontend:
```php
$precio = get_post_meta(get_the_ID(), '_precio', true);
echo '$' . esc_html($precio);
```

---

## 10. AJAX — Hacer peticiones sin recargar la página

### Paso 1: Pasar datos de PHP a JS
```php
function mi_plugin_scripts() {
    wp_enqueue_script('mi-plugin-js', plugin_dir_url(__FILE__) . 'js/scripts.js', array(), '1.0', true);

    // Pasar variables de PHP a JavaScript
    wp_localize_script('mi-plugin-js', 'miPlugin', array(
        'ajax_url' => admin_url('admin-ajax.php'),
        'nonce'    => wp_create_nonce('mi_plugin_nonce'),
    ));
}
add_action('wp_enqueue_scripts', 'mi_plugin_scripts');
```

### Paso 2: Crear el handler PHP
```php
function mi_plugin_ajax_handler() {
    // Verificar seguridad
    check_ajax_referer('mi_plugin_nonce', 'nonce');

    $nombre = sanitize_text_field($_POST['nombre']);

    // Hacer algo (consultar BD, procesar datos, etc.)
    $resultado = '¡Hola, ' . $nombre . '!';

    wp_send_json_success(array('mensaje' => $resultado));
    // o en caso de error: wp_send_json_error('Algo salió mal');
}
add_action('wp_ajax_mi_accion', 'mi_plugin_ajax_handler');        // Usuarios logueados
add_action('wp_ajax_nopriv_mi_accion', 'mi_plugin_ajax_handler'); // Visitantes
```

### Paso 3: Hacer la petición desde JS
```javascript
// js/scripts.js
document.getElementById('mi-boton').addEventListener('click', function() {
    const formData = new FormData();
    formData.append('action', 'mi_accion');           // Coincide con wp_ajax_MI_ACCION
    formData.append('nonce', miPlugin.nonce);          // Variable de wp_localize_script
    formData.append('nombre', 'Andrés');

    fetch(miPlugin.ajax_url, {
        method: 'POST',
        body: formData
    })
    .then(response => response.json())
    .then(data => {
        if (data.success) {
            alert(data.data.mensaje);  // "¡Hola, Andrés!"
        }
    });
});
```

---

## 11. Activación y Desactivación

Código que se ejecuta **una sola vez** al activar/desactivar el plugin:

```php
// Al activar — crear tablas, opciones por defecto, etc.
function mi_plugin_activar() {
    // Registrar CPT (necesario antes de flush)
    mi_plugin_registrar_cpt();

    // Regenerar URLs bonitas
    flush_rewrite_rules();

    // Crear opciones por defecto
    add_option('mi_plugin_color', '#ff0000');
}
register_activation_hook(__FILE__, 'mi_plugin_activar');

// Al desactivar — limpiar
function mi_plugin_desactivar() {
    flush_rewrite_rules();
}
register_deactivation_hook(__FILE__, 'mi_plugin_desactivar');
```

---

## 12. Seguridad — Lo mínimo que debes saber

### Siempre escapa la salida:
```php
echo esc_html($texto);         // Texto visible
echo esc_attr($valor);         // Dentro de atributos HTML
echo esc_url($url);            // URLs
```

### Siempre sanitiza la entrada:
```php
$limpio = sanitize_text_field($_POST['campo']);
$email  = sanitize_email($_POST['email']);
$numero = absint($_POST['cantidad']);          // Entero positivo
```

### Siempre usa nonces en formularios:
```php
// En el formulario (PHP):
wp_nonce_field('mi_accion', 'mi_nonce');

// Al procesar (PHP):
if (!wp_verify_nonce($_POST['mi_nonce'], 'mi_accion')) {
    die('Acción no autorizada');
}
```

### Evita acceso directo:
```php
// Primera línea después del header del plugin:
if (!defined('ABSPATH')) exit;
```

---

## Plugin Completo de Ejemplo

Juntando todo, así se ve un plugin funcional:

```php
<?php
/*
 * Plugin Name: Productos Simple
 * Description: Agrega un CPT de Productos con precio
 * Version: 1.0
 * Author: Tu Nombre
 */

if (!defined('ABSPATH')) exit;

// ===== CPT =====
function productos_registrar_cpt() {
    register_post_type('producto', array(
        'labels' => array(
            'name' => 'Productos',
            'singular_name' => 'Producto',
            'add_new_item' => 'Agregar Producto',
        ),
        'public'      => true,
        'has_archive' => true,
        'menu_icon'   => 'dashicons-cart',
        'supports'    => array('title', 'editor', 'thumbnail'),
    ));
}
add_action('init', 'productos_registrar_cpt');

// ===== META BOX (campo de precio) =====
function productos_metabox() {
    add_meta_box('precio_box', 'Precio', 'productos_metabox_html', 'producto', 'side');
}
add_action('add_meta_boxes', 'productos_metabox');

function productos_metabox_html($post) {
    $precio = get_post_meta($post->ID, '_precio', true);
    wp_nonce_field('productos_nonce_action', 'productos_nonce');
    echo '<input type="number" name="precio" value="' . esc_attr($precio) . '" style="width:100%" placeholder="0.00" step="0.01">';
}

function productos_guardar($post_id) {
    if (!isset($_POST['productos_nonce'])) return;
    if (!wp_verify_nonce($_POST['productos_nonce'], 'productos_nonce_action')) return;
    if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) return;
    if (isset($_POST['precio'])) {
        update_post_meta($post_id, '_precio', sanitize_text_field($_POST['precio']));
    }
}
add_action('save_post', 'productos_guardar');

// ===== SHORTCODE =====
function productos_shortcode($atts) {
    $atts = shortcode_atts(array('cantidad' => 6), $atts);
    $query = new WP_Query(array(
        'post_type' => 'producto',
        'posts_per_page' => $atts['cantidad'],
    ));
    $html = '<div class="productos-grid">';
    while ($query->have_posts()) {
        $query->the_post();
        $precio = get_post_meta(get_the_ID(), '_precio', true);
        $html .= '<div class="producto-card">';
        $html .= get_the_post_thumbnail(get_the_ID(), 'medium');
        $html .= '<h3>' . get_the_title() . '</h3>';
        $html .= '<p class="precio">$' . esc_html($precio) . '</p>';
        $html .= '</div>';
    }
    $html .= '</div>';
    wp_reset_postdata();
    return $html;
}
add_shortcode('productos', 'productos_shortcode');

// ===== CSS =====
function productos_estilos() {
    wp_enqueue_style('productos-css', plugin_dir_url(__FILE__) . 'css/estilos.css', array(), '1.0');
}
add_action('wp_enqueue_scripts', 'productos_estilos');

// ===== ACTIVACIÓN =====
function productos_activar() {
    productos_registrar_cpt();
    flush_rewrite_rules();
}
register_activation_hook(__FILE__, 'productos_activar');
```

---

## Cheatsheet — Tema vs Plugin

| Concepto | En un Tema | En un Plugin |
|---|---|---|
| Ruta de archivos | `get_template_directory()` | `plugin_dir_path(__FILE__)` |
| URL de archivos | `get_template_directory_uri()` | `plugin_dir_url(__FILE__)` |
| Incluir archivo | `require get_template_directory() . '/archivo.php'` | `require plugin_dir_path(__FILE__) . 'archivo.php'` |
| Se ubica en | `wp-content/themes/` | `wp-content/plugins/` |
| Se activa en | Apariencia > Temas | Plugins > Plugins |
| ¿Qué controla? | Presentación visual | Funcionalidad |
| ¿Sobrevive cambio de tema? | No | Sí |

---

## Orden de trabajo para crear un plugin

```
1. Crear carpeta en wp-content/plugins/mi-plugin/
2. Crear mi-plugin.php con el header comment
3. Activar en WP Admin > Plugins
4. Registrar CPT si necesitas (add_action init)
5. Agregar meta boxes si necesitas campos extra
6. Crear shortcode para mostrar en el frontend
7. Cargar CSS/JS con wp_enqueue_scripts
8. Agregar página de ajustes si necesitas (admin_menu)
9. Agregar hooks de activación/desactivación
```
