---
title: "Configuración de Acceso Externo para Ollama en Linux"
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

Por defecto, Ollama en Linux está configurado para escuchar únicamente peticiones locales (`127.0.0.1`). Cuando se utiliza Open WebUI (u otras aplicaciones) dentro de un contenedor Docker, estas no pueden comunicarse con Ollama a menos que se configure para aceptar conexiones de otras interfaces de red.

Este documento detalla los pasos necesarios para permitir que Ollama sea accesible desde contenedores Docker u otros dispositivos en la red.

### \textcolor{blue}{Paso 1: Prueba Temporal de Configuración}

Si deseas probar la configuración de manera inmediata en la sesión actual de tu terminal, puedes ejecutar:

```bash
export OLLAMA_HOST=0.0.0.0
```

*Nota: Esta configuración se perderá al cerrar la terminal o reiniciar el sistema.*

### \textcolor{blue}{Paso 2: Configuración Permanente (Systemd)}

Para que Ollama siempre acepte conexiones externas, es necesario modificar el servicio de `systemd`. Sigue estos pasos:

1. **Editar el servicio:**
   Ejecuta el siguiente comando para abrir el editor de configuración del servicio Ollama:
   ```bash
   sudo systemctl edit ollama.service
   ```

2. **Agregar la variable de entorno:**
   En el archivo que se abre, pega las siguientes líneas dentro de la sección `[Service]`:
   ```ini
   [Service]
   Environment="OLLAMA_HOST=0.0.0.0"
   ```

3. **Aplicar los cambios:**
   Guarda el archivo, cierra el editor y ejecuta los siguientes comandos para recargar la configuración y reiniciar el servicio:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart ollama
   ```

### \textcolor{blue}{Paso 3: Verificación}

Una vez reiniciado, puedes verificar que Ollama está escuchando en todas las interfaces ejecutando:

```bash
netstat -tulpn | grep 11434
```

Deberías ver una salida que indique que el puerto `11434` está escuchando en `0.0.0.0`.

### \textcolor{blue}{Preguntas Frecuentes (FAQ)}

- **¿Por qué no funciona con localhost o 127.0.0.1?**  
  Dentro de un contenedor Docker, `localhost` se refiere al propio contenedor, no a tu PC. Al configurar `0.0.0.0`, Ollama permite que Docker se conecte a través de la IP del host.

- **¿Es seguro poner 0.0.0.0?**  
  Si estás en una red pública, esto permite que cualquiera en tu red acceda a tu API de Ollama. Se recomienda usar un firewall para restringir el acceso si es necesario.

- **¿Cómo revierto los cambios?**  
  Simplemente borra las líneas añadidas con `sudo systemctl edit ollama.service` y repite el proceso de recarga y reinicio.
