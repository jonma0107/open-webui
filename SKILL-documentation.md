# SKILL.md

## Plantilla y reglas para documentación en proyectos

### 1. Verificar Pandoc

Antes de documentar, asegúrate de que Pandoc esté instalado globalmente. Si no lo está, instálalo:

```bash
sudo apt-get install pandoc
```

### 2. Estructura inicial para toda documentación

Copia y adapta este bloque YAML al inicio de tu archivo .md:

---
title: "Título del Proyecto"
author: "Tu nombre o empresa"
date: "Año"
graphics: true
header-includes: |
  \usepackage{float}
  \usepackage{longtable}
  \usepackage{booktabs}
  \usepackage{array}
  \usepackage{makecell}
  \renewcommand{\arraystretch}{1.4}
  \let\origfigure\figure
  \let\endorigfigure\endfigure
  \renewenvironment{figure}[1][2] {
    \expandafter\origfigure\expandafter[H]
  } {
    \endorigfigure
  }

geometry: "top=3cm,bottom=3cm,left=4cm,right=2cm"
lang: "es" # Esto ayuda a que los índices y fechas salgan en español
toc: true
toc-title: "Índice de Contenidos"  
---

### 3. Colores para títulos y subtítulos

Usa la siguiente sintaxis para títulos y subtítulos en Markdown:

```markdown
### \textcolor{blue}{TITULO}
### \textcolor{blue}{subtitulo}
```

### 4. Generar documentación

- Escribe la documentación en formato .md siguiendo la estructura y estilos anteriores.

### 5. Convertir a PDF

Utiliza el siguiente comando para convertir tu archivo Markdown a PDF:

```bash
pandoc README.md -o README.pdf --pdf-engine=xelatex -V colorlinks=true -V linkcolor=blue -V urlcolor=blue -V toccolor=black --highlight-style=tango --toc --toc-depth=3 -V papersize=a3 -V fontsize=11pt -V mainfont="DejaVu Sans" -V monofont="DejaVu Sans Mono"
```

> Reemplaza README.md por el nombre de tu archivo .md.

---

## Ejemplo de inicio de documentación

---
title: "Migración de DAS a AWS Fargate + ECS"
author: "TicSocial S. A. S."
date: "2026"
graphics: true
header-includes: |
  \usepackage{float}
  \usepackage{longtable}
  \usepackage{booktabs}
  \usepackage{array}
  \usepackage{makecell}
  \renewcommand{\arraystretch}{1.4}
  \let\origfigure\figure
  \let\endorigfigure\endfigure
  \renewenvironment{figure}[1][2] {
    \expandafter\origfigure\expandafter[H]
  } {
    \endorigfigure
  }

geometry: "top=3cm,bottom=3cm,left=4cm,right=2cm"
lang: "es"
toc: true
toc-title: "Índice de Contenidos"  
---

### \textcolor{blue}{TITULO}

### \textcolor{blue}{subtitulo}

---

Este archivo SKILL.md puede copiarse y adaptarse en cualquier proyecto para estandarizar la documentación y su conversión a PDF.

---

## Reglas y buenas prácticas adicionales para la documentación

### 1. Sección de Preguntas Frecuentes (FAQ)
Incluye siempre una sección de “Preguntas Frecuentes” al final de la documentación. Esto ayuda a resolver dudas comunes y mejora la experiencia de los usuarios y desarrolladores.

### 2. Gráficos y diagramas
- Utiliza diagramas de flujo, secuencia o arquitectura para explicar procesos complejos.
- Puedes usar arte ASCII, herramientas como Mermaid, o imágenes externas.
- Ejemplo de diagrama de flujo (ASCII):

```
┌─────────────┐
│  INICIO     │
└─────┬───────┘
      ▼
  [Proceso]
      ▼
┌─────────────┐
│   FIN       │
└─────────────┘
```

- Ejemplo de diagrama de secuencia:

```
┌─────────┐   ┌─────────┐
│ Cliente │   │ Backend │
└────┬────┘   └────┬────┘
     │             │
     │ 1. Acción   │
     │────────────>│
     │             │
     │ 2. Respuesta│
     │<────────────│
```

### 3. Saltos de línea tras subtítulos
Después de cada subtítulo (### \textcolor{blue}{...}), deja siempre un salto de línea antes del contenido. Esto mejora la legibilidad y el formato al convertir a PDF.

### 4. Tablas para resúmenes y comparativas
- Usa tablas **solo cuando las columnas sean cortas** (máx. 2-3 palabras por celda).
- Ejemplo de tabla adecuada:

| Archivo                       | Descripción                        | Tests |
|-------------------------------|------------------------------------|-------|
| `test_category_permission.py`  | Tests del modelo CategoryPermission| 5     |
| `test_support_files_view.py`   | Tests CRUD de archivos             | 11    |

### 5. Evitar superposición de texto en tablas

Cuando una tabla tiene columnas con textos largos (nombres de funciones, descripciones extensas, etc.), el texto se **superpone** en el PDF generado. Para evitarlo:

**Opción 1 — Convertir a listas (recomendado para textos largos):**

En lugar de:

```markdown
| Test | Descripción |
|------|-------------|
| `test_file_upload_saves_url_in_form_data` | Archivos subidos generan URLs |
```

Usar:

```markdown
- **`test_file_upload_saves_url_in_form_data`**:
  Archivos subidos generan URLs.
```

**Opción 2 — Acortar contenido:**

Si se necesita tabla, acortar los textos para que cada celda no supere ~40 caracteres.

**Opción 3 — Los paquetes LaTeX del header:**

Los paquetes `longtable`, `booktabs`, `array`, `makecell` y `\arraystretch{1.4}` ya están incluidos en la plantilla YAML de esta skill. Estos mejoran el soporte de tablas, pero **no resuelven completamente** la superposición en columnas muy anchas. Siempre prefiere la Opción 1 cuando el contenido es extenso.

> **Regla general:** Si una tabla tiene más de 2 columnas con textos largos, conviértela a lista.

### 6. Sección de componentes principales
Incluye una sección que resuma los principales módulos, scripts o componentes del proyecto, con una breve descripción de cada uno.

### 7. Buenas prácticas para gráficos
- Usa gráficos para explicar flujos de permisos, procesos de negocio, o arquitectura.
- Si el flujo es complejo, acompaña el gráfico con una breve explicación textual.

### 8. Ejemplo de sección FAQ

### 	extcolor{blue}{Preguntas Frecuentes}

- **¿Cómo genero el PDF?**  
  Usa el comando Pandoc especificado en la plantilla.
- **¿Qué hago si falta un permiso?**  
  Revisa la sección de componentes y verifica la configuración en Django.

### 9. Enlaces, imágenes y videos

Puedes incluir enlaces a recursos externos, imágenes o videos para enriquecer la documentación.

- Para imágenes, usa la sintaxis Markdown:

  ![Descripción de la imagen](https://url.de/imagen.jpg)

  Ejemplo:
  ![Configuración en Render](https://i.ibb.co/DnmdpTd/render.jpg)

- Para enlaces a videos o recursos:

  [Ver video explicativo](https://www.youtube.com/watch?v=video_id)

- Si mencionas un video en la solicitud o documentación, incluye el link en la sección correspondiente.  