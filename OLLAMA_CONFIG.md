---
title: "Configuración de Conexión entre Open WebUI y Ollama en Linux"
author: "Jonathan Meza"
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

### \textcolor{blue}{Introducción}

Por defecto, Ollama en Linux está configurado para escuchar únicamente peticiones locales (`127.0.0.1`). Cuando se ejecuta Open WebUI a través de Docker, este se encuentra en una red aislada y no puede comunicarse directamente con Ollama en el host de manera predeterminada.

El **estándar recomendado** para solucionar esto en Linux es configurar el contenedor de Docker para que comparta la red del host, evitando tener que modificar la configuración interna de Ollama o exponerlo a toda la red local de forma innecesaria.

### \textcolor{blue}{Método Estándar: Usar \texttt{network\_mode: host}}

La forma más segura y recomendada de conectar Open WebUI con un Ollama instalado de forma local en Linux es modificar el archivo `docker-compose.yml` utilizando la propiedad `network_mode: host`.

1. Abre tu archivo `docker-compose.yml`.
2. Asegúrate de incluir la línea `network_mode: host` y de apuntar la variable de entorno `OLLAMA_BASE_URL` al localhost.

**Ejemplo de configuración:**
```yaml
version: "3.9"

services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    restart: always
    network_mode: host
    environment:
      - OLLAMA_BASE_URL=http://127.0.0.1:11434
      - PORT=3000
    volumes:
      - open-webui:/app/backend/data

volumes:
  open-webui:
```

Con esta configuración, Open WebUI puede conectarse a Ollama directamente en `127.0.0.1` ya que el contenedor comparte la misma interfaz de red que tu computadora.

### \textcolor{blue}{Método Alternativo: Modificar Ollama (Systemd)}

*Nota: Este método es opcional y solo necesario si deseas que Ollama sea accesible desde **otros dispositivos en tu red local** o si, por alguna restricción técnica, no puedes utilizar `network_mode: host` en tu contenedor.*

Para que Ollama acepte conexiones de cualquier interfaz de red:

1. **Editar el servicio:**
   Ejecuta el siguiente comando en la terminal:
   ```bash
   sudo systemctl edit ollama.service
   ```

2. **Agregar la variable de entorno:**
   En el editor, pega las siguientes líneas:
   ```ini
   [Service]
   Environment="OLLAMA_HOST=0.0.0.0"
   ```

3. **Aplicar cambios:**
   Guarda el archivo y reinicia el servicio:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart ollama
   ```

### \textcolor{blue}{Preguntas Frecuentes (FAQ)}

- **¿Por qué `network_mode: host` es el estándar en Linux?**  
  Porque es más seguro y eficiente. Mantiene la comunicación confinada a tu propia máquina (`127.0.0.1`) sin tener que exponer el puerto `11434` de Ollama a todos los dispositivos que estén conectados a tu router/red Wi-Fi.

- **¿Es seguro usar `network_mode: host`?**  
  Sí, en entornos locales de Linux. El contenedor asume la red del host, lo cual es ideal para servicios de inferencia como Ollama que se ejecutan a nivel local.
