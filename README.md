# Monitoreo (SPA)

Sistema de monitoreo en una sola página (HTML/JS) con cámara, zonas, detección (TFJS), mapa (Leaflet) y conexión P2P (WebRTC) entre un equipo Principal y múltiples Nodos (móvil/tablet/PC). Interfaz en español.

## Estructura
- `monitoreo/index.html`: Aplicación completa (UI, lógica, P2P, detección, mapas, gráficos).
- `monitoreo/vercel.json`: Cabeceras de seguridad para despliegue en Vercel.

## Características principales
- Cámara local con selección frontal/posterior y cambio en vivo (Cambiar).
- Zonas de interés, detección en tiempo real (coco-ssd) y métricas con gráficos.
- Mapa con marcadores del Principal y Nodos (GPS opcional).
- Conexión P2P por WebRTC: canal de datos, audio/video y ubicación.
- Códigos de seguridad: Código de Emparejamiento (A123B) y Código de Proyecto (A123B).
- Configuración ICE (STUN/TURN) en UI y panel de diagnóstico de WebRTC.
- En modo Nodo, la interfaz muestra solo movimiento por diseño (detección desactivada por defecto).

## Requisitos
- Navegador moderno con HTTPS o `localhost` (getUserMedia/WebRTC requieren origen seguro).
- Permisos de Cámara/Micrófono/Ubicación según lo que habilite en la UI.

## Inicio rápido (local)
El proyecto es estático; solo necesita un servidor local para HTTPS/localhost.

Opciones (use una):
1) Extensión “Live Server” de VS Code (recomendado). Abra la carpeta `monitoreo/` y sirva `index.html`.
2) Node.js
   - Instale Node y ejecute en PowerShell:
     ```powershell
     npx serve -l 5173 monitoreo
     ```
   - Abra: http://localhost:5173/
3) Python 3
   - En PowerShell dentro de la carpeta `monitoreo/`:
     ```powershell
     python -m http.server 5173
     ```
   - Abra: http://localhost:5173/

Nota: Para cámaras en móviles, prefiera HTTPS (un dominio público o túnel) o `localhost` en el mismo dispositivo.

## Roles y flujo P2P (Principal ↔ Nodo)
1) Abra `index.html` en ambos dispositivos.
2) Seleccione el Rol (Principal o Nodo) en cada uno.
3) Defina el Código de Proyecto (A123B) igual en todos los equipos de ese proyecto.
4) Defina/comparta el Código de Emparejamiento (A123B) entre el Principal y cada Nodo.
5) Inicie la oferta desde un lado, copie la Oferta y péguela en el otro (Aceptar Oferta), copie la Respuesta y regrésela (Aplicar Respuesta).
6) En “Medios P2P”, active lo que quiera compartir (video/audio/geo) en el Nodo.
7) En el Principal, en la pestaña Cámara, elija “Fuente” para ver la cámara de un Nodo conectado.

Consejos:
- Use el panel “Diagnóstico” para revisar estados: signaling/ice/connection, candidatos ICE.
- Si la conexión no completa (ICE=failed), configure un TURN.

## Configuración de cámaras
- Local y P2P permiten elegir cámara frontal/posterior.
- El botón “Cambiar” invierte rápidamente la cámara.
- Si el modo `facingMode` falla, el sistema intenta elegir por `deviceId`.

## Zonas, detección y movimiento
- Dibuje zonas en la UI. La detección usa TensorFlow.js (coco-ssd) y se muestra con etiquetas en español.
- En Rol Nodo: se ocultan listas/gráficos de detección y solo se muestra movimiento (detección OFF por defecto).
- En Principal puede mantener la detección activa si desea analítica local.

## Mapa y GPS
- Active “Compartir geolocalización” para enviar posición vía canal de datos.
- El mapa muestra al Principal y a los Nodos con sus últimas ubicaciones.

## ICE (STUN/TURN)
- En “Conexiones” configure servidores ICE según su red.
- Ejemplo STUN: `stun:stun.l.google.com:19302`.
- Para NAT simétrico/CGNAT, agregue un TURN con usuario/clave de su proveedor (por ejemplo, un servicio TURN administrado).

## Seguridad y privacidad
- Códigos de Emparejamiento y de Proyecto se validan en la señalización (manual, por copiar y pegar). Deben coincidir.
- E2E con WebRTC; la app es 100% estática (no hay servidor propio de señalización).
- `vercel.json` incluye cabeceras (HSTS, COOP/COEP, Permissions-Policy). Revise y ajuste dominios si usa TURN propio.
- La configuración local (códigos, zonas, preferencia de cámara) se guarda en `localStorage` del navegador.

## Despliegue en Vercel
- Nuevo proyecto → Framework: “Other”.
- Root Directory: `monitoreo`
- Build Command: vacío (no hay build)
- Output Directory: `.`
- Producción: Vercel sirve con HTTPS. `vercel.json` agrega cabeceras de seguridad.

## Solución de problemas
- Permisos: verifique cámara/mic/ubicación en el navegador.
- Diagnóstico WebRTC: vea estados y conteos de candidatos. Si ICE no completa, use TURN.
- Códigos: asegúrese que Emparejamiento y Proyecto coincidan en ambos lados.
- Cámara negra en móvil: cambie frontal/posterior o reinicie permisos del sitio.
- Lento en dispositivos modestos: desactive detección o baje la resolución/velocidad.

## Limitaciones actuales
- Señalización manual (copiar/pegar). Para automatizar, necesitaría un servidor de señalización (no incluido).
- Detección basada en coco-ssd (CPU); el rendimiento depende del dispositivo.

## Licencias y créditos
- TensorFlow.js, coco-ssd, Chart.js, Leaflet, QRCode, JSZip: sirven desde CDNs, con sus licencias respectivas.
- Este repositorio contiene solo la integración y la UI (propósito demostrativo/educativo).
