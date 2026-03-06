# Guía Rápida: Configurar el Panel de WordPress para que todo funcione

> Tu tema y plugins pueden estar perfectos, pero si no configuras bien el panel de WordPress, no se ve nada. Esta guía te dice exactamente qué hacer.

---

## 1. Activar el Tema

**Apariencia > Temas** → Buscar "Gym Fitness" → Click en **Activar**

Si no aparece, la carpeta del tema no está en `wp-content/themes/`.

---

## 2. Configurar la Página de Inicio

Por defecto WordPress muestra los **últimos posts** como página de inicio. Para usar `front-page.php` necesitas cambiarlo.

**Ajustes > Lectura**

```
Tu portada muestra:
  ○ Tus últimas entradas
  ● Una página estática (seleccionar abajo)

    Portada: [Inicio]          ← La página que será tu home
    Página de entradas: [Blog]  ← La página que listará los posts
```

**Importante:** Primero debes crear esas páginas en **Páginas > Añadir nueva**:
1. Crear una página llamada "Inicio"
2. Crear una página llamada "Blog"
3. Luego asignarlas en Ajustes > Lectura

Sin esto, `front-page.php` **nunca se ejecuta**.

---

## 3. Crear las Páginas Necesarias

**Páginas > Añadir nueva** — Crea cada una de estas:

| Página | Plantilla a asignar | Para qué |
|---|---|---|
| Inicio | — (usa `front-page.php` automáticamente) | Página de inicio |
| Blog | — (usa `home.php` automáticamente) | Listado de posts |
| Nuestras Clases | Listado de Clases | Muestra todas las clases |
| Galería | Galería | Grid de imágenes con lightbox |
| Contacto | Contenido Centrado (No Sidebars) | Formulario + mapa |

### ¿Cómo asignar la plantilla?

Al editar una página, en el panel lateral derecho:

```
Página
  └── Resumen
        └── Plantilla: [Plantilla por defecto ▾]
                        ↓ Click aquí y selecciona
                        - Galería
                        - Listado de Clases
                        - Contenido Centrado (No Sidebars)
```

Si usas el **editor clásico**, está en el panel lateral como "Atributos de página > Plantilla".

---

## 4. Configurar el Menú de Navegación

**Apariencia > Menús**

### Paso 1: Crear el menú
```
Nombre del menú: [Menu Principal]  → Click "Crear menú"
```

### Paso 2: Agregar páginas al menú

En el panel izquierdo, marca las páginas que quieres y click **"Añadir al menú"**:
- ☑ Inicio
- ☑ Nuestras Clases
- ☑ Galería
- ☑ Blog
- ☑ Contacto

### Paso 3: Ordenar

Arrastra los elementos para ordenarlos. Si quieres submenús, arrastra un elemento **hacia la derecha** debajo de otro.

### Paso 4: Asignar ubicación

Abajo del todo:
```
Ajustes del menú
  Ubicación del tema:
    ☑ Menu Principal     ← MARCA ESTA CASILLA
```

Click **"Guardar menú"**.

**Si no asignas la ubicación**, el menú NO aparece en el header ni footer aunque lo hayas creado.

---

## 5. Instalar Plugins Obligatorios

**Plugins > Añadir nuevo** → Buscar e instalar:

### Advanced Custom Fields (ACF)
1. Buscar "Advanced Custom Fields"
2. Instalar → Activar
3. Aparece un nuevo menú: **ACF** (o "Campos personalizados")

### Contact Form 7
1. Buscar "Contact Form 7"
2. Instalar → Activar
3. Aparece un nuevo menú: **Contacto**

---

## 6. Crear los Custom Post Types

Este tema usa 3 CPT que **no están registrados en el tema**. Necesitas un plugin para crearlos.

### Opción fácil: Plugin "Custom Post Type UI"

**Plugins > Añadir nuevo** → Buscar "Custom Post Type UI" → Instalar → Activar

Aparece el menú **CPT UI**. Crear cada uno:

#### CPT: Clases
```
Slug del tipo de entrada: gymfitness_clases
Nombre (plural):          Clases
Nombre (singular):        Clase
```
En la sección "Soporta", marcar: ☑ Título, ☑ Editor, ☑ Imagen destacada

#### CPT: Instructores
```
Slug del tipo de entrada: instructores
Nombre (plural):          Instructores
Nombre (singular):        Instructor
```
Soporta: ☑ Título, ☑ Editor, ☑ Imagen destacada

#### CPT: Testimoniales
```
Slug del tipo de entrada: testimoniales
Nombre (plural):          Testimoniales
Nombre (singular):        Testimonial
```
Soporta: ☑ Título, ☑ Editor, ☑ Imagen destacada

Después de crear cada CPT aparecen en el menú lateral del admin.

---

## 7. Crear los Campos ACF

**ACF > Grupos de campos > Añadir nuevo**

### Grupo 1: Campos del Hero (Página de Inicio)

**Título del grupo:** "Campos Hero"

Crear estos campos:

| Nombre del campo | Tipo de campo | Instrucciones |
|---|---|---|
| `hero_imagen` | Imagen (retorna ID) | Imagen de fondo del hero |
| `hero_heading` | Texto | Título principal del hero |
| `hero_texto` | Área de texto | Subtítulo del hero |
| `encabezado_bienvenida` | Texto | Encabezado sección bienvenida |
| `texto_bienvenida` | Área de texto | Texto de bienvenida |
| `area_1` | Grupo | — |
| → `imagen` | Imagen (retorna array) | Imagen del área 1 |
| → `texto` | Texto | Texto del área 1 |
| `area_2` | Grupo | (igual que area_1) |
| `area_3` | Grupo | (igual que area_1) |
| `area_4` | Grupo | (igual que area_1) |

**Regla de ubicación:**
```
Mostrar este grupo si:
  Tipo de entrada  es igual a  Página
  Y
  Página          es igual a  Inicio
```

### Grupo 2: Campos de Clases

**Título del grupo:** "Datos de la Clase"

| Nombre del campo | Tipo de campo |
|---|---|
| `hora_inicio` | Texto |
| `hora_fin` | Texto |
| `dias_clase` | Texto |

**Regla de ubicación:**
```
Tipo de entrada  es igual a  gymfitness_clases
```

### Grupo 3: Campos de Instructores

**Título del grupo:** "Datos del Instructor"

| Nombre del campo | Tipo de campo |
|---|---|
| `especialidad` | Checkbox (opciones: Yoga, CrossFit, Spinning, etc.) |

**Regla de ubicación:**
```
Tipo de entrada  es igual a  instructores
```

### Grupo 4: Campo de Contacto

**Título del grupo:** "Ubicación"

| Nombre del campo | Tipo de campo |
|---|---|
| `ubicacion` | Área de texto / oEmbed |

**Regla de ubicación:**
```
Página  es igual a  Contacto
```

---

## 8. Crear Contenido

Ahora sí, crear el contenido real:

### Posts del Blog
**Entradas > Añadir nueva**
- Poner título
- Escribir contenido
- Asignar **imagen destacada** (panel lateral derecho)
- Asignar **categoría**
- Publicar

### Clases
**Clases > Añadir nueva**
- Título: "Yoga Matutino"
- Contenido: descripción de la clase
- Imagen destacada: foto de la clase
- Llenar campos ACF: hora inicio, hora fin, días
- Publicar

### Instructores
**Instructores > Añadir nuevo**
- Título: nombre del instructor
- Contenido: biografía
- Imagen destacada: foto del instructor
- Llenar campo ACF: especialidades (marcar checkboxes)
- Publicar

### Testimoniales
**Testimoniales > Añadir nuevo**
- Título: nombre del cliente
- Contenido: el testimonio/quote
- Imagen destacada: foto del cliente
- Publicar

---

## 9. Configurar los Widgets (Sidebars)

**Apariencia > Widgets**

Verás dos zonas:

### Sidebar 1 (aparece en blog y posts)
Arrastra widgets como:
- Búsqueda
- Categorías
- Entradas recientes

### Sidebar 2 (aparece en las clases individuales)
Arrastra el widget personalizado:
- **GymFitness Clases** → Configurar "cantidad" (ej: 5)

---

## 10. Configurar la Galería

La página de galería usa la **galería nativa de WordPress**.

1. Editar la página "Galería" (que ya tiene la plantilla "Galería" asignada)
2. En el editor, click **"Añadir multimedia"** (o insertar bloque Galería)
3. Subir o seleccionar las imágenes
4. Click **"Crear galería"**
5. Insertar en la página
6. Publicar

El tema lee los IDs de las imágenes de esa galería y las muestra con Lightbox.

---

## 11. Configurar Contact Form 7

**Contacto > Formularios de contacto**

Ya viene uno creado por defecto. Puedes editarlo o dejarlo así.

Copia el shortcode que aparece arriba:
```
[contact-form-7 id="102" title="Formulario de contacto 1"]
```

En este tema **no necesitas pegarlo manualmente** porque el shortcode `[gymfitness_ubicacion]` ya lo incluye dentro del código. Solo asegúrate que el ID coincida (en `functions.php` está hardcodeado como `id="102"`).

Si tu formulario tiene otro ID, edita `functions.php` línea 86 y cambia el número.

---

## 12. Configurar Permalinks (URLs bonitas)

**Ajustes > Enlaces permanentes**

Seleccionar:
```
● Nombre de la entrada     https://tusitio.com/ejemplo-de-entrada/
```

Click **"Guardar cambios"**.

**Esto es crítico.** Sin esto:
- Las URLs de los CPT no funcionan (da error 404)
- Las URLs se ven feas: `?p=123`

Si después de crear CPTs ves error 404 al visitar una clase o instructor, vuelve aquí y dale **"Guardar cambios"** otra vez (esto regenera las reglas de rewrite).

---

## 13. Imágenes Destacadas — No las olvides

Casi todos los templates usan `the_post_thumbnail()`. Si creas un post/clase/instructor **sin imagen destacada**, aparece un espacio vacío.

Al editar cualquier entrada, en el panel lateral derecho:
```
Entrada / Página
  └── Imagen destacada
        └── [Establecer imagen destacada]   ← Click aquí
```

Sube o selecciona una imagen → **Establecer imagen destacada**.

**Tamaños recomendados:**
- Hero: mínimo 1920x1080px
- Cards de blog/clases: mínimo 800x600px
- Instructores: mínimo 600x800px (vertical)
- Testimoniales thumbnail: 150x150px

---

## 14. Ajustes Generales Importantes

### Ajustes > Generales
```
Título del sitio: GymFitness     ← Aparece en el copyright del footer
Descripción corta: Tu gimnasio   ← SEO
```

### Ajustes > Lectura
```
Las páginas del blog muestran como máximo: 10 entradas
```

### Ajustes > Comentarios
Si no quieres comentarios en páginas:
```
☐ Permitir que se publiquen comentarios en los artículos nuevos
```

### Ajustes > Medios
WordPress genera automáticamente estos tamaños al subir imágenes:
```
Miniatura:     150 x 150   (thumbnail)
Medio:         300 x 300   (medium)
Grande:        1024 x 1024 (large)
Medio grande:  768 x 0     (medium_large) — usado en las áreas del front
```

---

## Checklist Final — ¿Todo funciona?

```
☐ Tema activado
☐ Plugins instalados: ACF, Contact Form 7, Custom Post Type UI
☐ Página de inicio configurada en Ajustes > Lectura
☐ Menú creado Y asignado a "Menu Principal"
☐ Permalinks en "Nombre de la entrada"
☐ CPTs creados: gymfitness_clases, instructores, testimoniales
☐ Campos ACF creados y asignados al tipo de contenido correcto
☐ Página "Inicio" con campos ACF llenos (hero, bienvenida, áreas)
☐ Al menos 4 Clases creadas (con imagen, horario, días)
☐ Al menos 2 Instructores creados (con imagen y especialidades)
☐ Al menos 2 Testimoniales creados (con imagen y texto)
☐ Al menos 4 Posts del blog creados (con imagen y categoría)
☐ Página "Galería" con plantilla "Galería" y galería de imágenes insertada
☐ Página "Contacto" con campo ACF "ubicación" lleno
☐ Widgets configurados en ambos sidebars
☐ Formulario de contacto con el ID correcto en functions.php
```

Si marcas todo, tu sitio debería verse completo.

---

## Errores Comunes y Solución

| Problema | Causa | Solución |
|---|---|---|
| Página de inicio muestra el blog | No configuraste página estática | Ajustes > Lectura > Página estática |
| Menú no aparece | No asignaste ubicación | Apariencia > Menús > marcar "Menu Principal" |
| Clase/Instructor da error 404 | Permalinks sin regenerar | Ajustes > Enlaces permanentes > Guardar |
| Imagen no aparece en card | No pusiste imagen destacada | Editar entrada > panel lateral > Imagen destacada |
| Campos ACF no aparecen al editar | Regla de ubicación mal puesta | ACF > editar grupo > revisar regla "Mostrar si..." |
| Hero sin imagen de fondo | Campo `hero_imagen` vacío | Editar página Inicio > llenar campo hero_imagen |
| Galería vacía | No insertaste galería en la página | Editar página Galería > insertar bloque Galería |
| Formulario no aparece en Contacto | ID del formulario no coincide | Comparar ID en Contact Form 7 vs functions.php línea 86 |
| Widget de clases no aparece | No arrastraste el widget al sidebar | Apariencia > Widgets > arrastrar "GymFitness Clases" a Sidebar 2 |
| Sidebar vacío | Sin widgets asignados | Apariencia > Widgets > agregar widgets |
