# Pruebas Cortas y Parcial Integrador — Logística Internacional
Universidad de Panamá · Centro Regional Universitario de Veraguas

Aplicación web para crear pruebas cortas y parciales integradores, generar un
código de acceso único por prueba, aplicar el examen a los estudiantes con
corrección automática y retroalimentación inmediata, y consultar el informe
de calificaciones y tiempos (solo visible para el docente).

## Contenido del proyecto

```
index.html                  → la aplicación COMPLETA en un solo archivo
                               (interfaz, lógica y adaptador de almacenamiento)
firestore.rules             → reglas de seguridad sugeridas para Firestore
netlify.toml / vercel.json  → configuración mínima para desplegar como sitio estático
```

Todo vive en `index.html`. No hay carpetas ni archivos adicionales que mover:
puedes copiar ese único archivo a cualquier lugar y seguirá funcionando,
siempre que no borres ni edites las etiquetas `<script>` de configuración
que están cerca del inicio del archivo.

## Paso 1 — Crear la base de datos (Firebase, gratis)

La app necesita un lugar en la nube donde guardar las pruebas y las
respuestas, para que el docente y todos los estudiantes vean la misma
información. Se usa **Firebase Firestore**, con plan gratuito suficiente
para un curso.

1. Entra a https://console.firebase.google.com y crea un proyecto nuevo
   (puedes desactivar Google Analytics, no es necesario).
2. En el menú lateral, entra a **Firestore Database → Crear base de datos**.
   Elige modo **producción** y la región más cercana (por ejemplo
   `us-central` o `southamerica-east1`).
3. Ve a **Reglas** dentro de Firestore y pega el contenido del archivo
   `firestore.rules` de este proyecto. Publica los cambios.
4. En el menú lateral entra a **Configuración del proyecto** (ícono de
   engranaje) → pestaña **General** → sección "Tus apps" → botón `</>`
   (Web). Registra la app con cualquier nombre (ej. "pruebas-logistica").
5. Firebase te mostrará un bloque `firebaseConfig = {...}`. Abre
   `index.html` con un editor de texto (Notepad, VS Code, etc.), busca el
   bloque que dice:
   ```js
   window.FIREBASE_CONFIG = {
     apiKey: "TU_API_KEY",
     authDomain: "TU_PROYECTO.firebaseapp.com",
     ...
   };
   ```
   (está cerca de la línea 15, justo después de `<script src=".../firebase-firestore-compat.js">`)
   y reemplaza esos valores por los que te dio Firebase. Guarda el archivo.

Mientras no completes este paso, la app funciona en **modo local de
prueba** (verás una franja roja de aviso): los datos se guardan solo en
el navegador donde la abriste, así que sirve para revisar que la interfaz
funciona (crear una prueba, subir un documento, resolverla tú mismo), pero
no para aplicarla de verdad, porque cada estudiante vería su propio
almacenamiento vacío en su propio navegador/computadora.

## Paso 2 — Probar localmente antes de publicar

Puedes abrir `index.html` haciendo doble clic (se abre en tu navegador).
Funcionará en modo local de prueba (franja roja) hasta que completes el
Paso 1. Es la forma más rápida de revisar que todo se ve y se comporta
como esperas antes de subirlo a internet.

## Paso 3 — Subir el proyecto a GitHub

```bash
git init
git add .
git commit -m "Aplicación de pruebas - Logística Internacional"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/TU_REPOSITORIO.git
git push -u origin main
```

> Importante: `index.html` queda en el repositorio con tus llaves de
> Firebase dentro del bloque `FIREBASE_CONFIG`. Esto es normal para apps
> web de Firebase (las llaves de un proyecto web no son secretas por
> diseño), pero la protección real vive en las **reglas de Firestore**
> del paso 1. Si el repositorio es público y quieres más control de
> acceso, agrega Firebase Authentication más adelante.

## Paso 4 — Desplegar

### Opción A: Netlify
1. Entra a https://app.netlify.com → "Add new site" → "Import an existing project".
2. Conecta tu cuenta de GitHub y selecciona el repositorio.
3. Deja el "Build command" vacío y "Publish directory" en `.` (raíz).
4. Deploy. Netlify te da una URL tipo `https://tu-sitio.netlify.app`.

### Opción B: Vercel
1. Entra a https://vercel.com/new e importa el repositorio de GitHub.
2. Framework Preset: "Other". No requiere build command.
3. Deploy. Vercel te da una URL tipo `https://tu-sitio.vercel.app`.

### Opción C: GitHub Pages
1. En el repositorio: Settings → Pages → Source: rama `main`, carpeta `/ (root)`.
2. Guarda. GitHub te da una URL tipo `https://tu-usuario.github.io/tu-repo/`.

No se necesita ningún proceso de compilación (build): es un sitio estático
de un solo archivo.

## Uso de la aplicación

- **Docente:** entra al sitio → "Panel del docente" → código de acceso por
  defecto `CRUV-2026` (cámbialo editando la constante `PIN_DOCENTE` dentro
  de `index.html` antes de desplegar). Ahí subes el documento de preguntas
  por módulo (.docx o .txt, formato explicado dentro de la propia app),
  generas el código de acceso único de la prueba y consultas el informe de
  calificaciones y tiempos.
- **Estudiante:** entra al sitio → "Acceso del estudiante" → ingresa el
  código de la prueba que le compartió el docente, o abre directamente el
  enlace `tu-sitio.com/#codigo=EL-CODIGO` que la app genera automáticamente
  (ya viene con el código cargado).

## Solución de problemas

**Veo un mensaje rojo grande: "No se pudo guardar información en este navegador".**
Significa que el navegador está bloqueando el almacenamiento local para este
archivo. Es muy común al abrir el archivo con doble clic (`file://`) en
computadoras con Chrome de perfil empresarial/gestionado. La propia pantalla
de error te da tres soluciones (de más simple a más robusta); la más
confiable es configurar Firebase (Paso 1) y probar la app ya publicada.

**Subo un documento .docx/.txt y no pasa nada, o me sale un mensaje de error
al procesarlo.**
Desde la actualización más reciente, la app te muestra el motivo exacto
(documento vacío, formato no reconocido, o el lector de .docx bloqueado por
tu red). Si el mensaje menciona que no se pudo cargar el "componente para
leer .docx", tu red o un bloqueador de anuncios está impidiendo que se
descargue la librería externa que lee Word — como alternativa, guarda el
documento como texto plano (.txt) con el mismo formato y súbelo así.

**Genero el código de la prueba pero el estudiante recibe "código no
encontrado".**
Antes de este cambio, esto podía pasar en modo local si el guardado fallaba
silenciosamente. Ahora, si el guardado falla, verás una alerta explicativa
en el momento de generar la prueba (no se te mostrará un código si no se
guardó). Si el guardado sí fue exitoso pero el estudiante no encuentra el
código, confirma que ambos estén usando exactamente el mismo sitio/archivo
publicado (no cada uno una copia distinta en su propia computadora) — por
eso Firebase es indispensable para uso real con estudiantes.

## Notas importantes

- Cambia el PIN docente (`PIN_DOCENTE` en `index.html`) antes de compartir
  el sitio, para que los estudiantes no puedan entrar al panel del docente.
- Si sales de la pantalla de examen (cambias de pestaña o app) mientras la
  prueba está en curso, se anula automáticamente con nota 0, solo para ese
  estudiante — no afecta a los demás.
- Cada estudiante tiene un único intento por prueba, identificado por su
  cédula.
- Las reglas de `firestore.rules` son básicas (lectura/escritura abierta a
  quien tenga el enlace del sitio). Es el mismo nivel de protección de una
  hoja de cálculo compartida por enlace: adecuado para un curso, no para
  datos sensibles a gran escala.
- Si ves la franja roja "Modo local de prueba" después de publicar el
  sitio, significa que el Paso 1 no se completó correctamente: revisa que
  copiaste bien las llaves de Firebase dentro de `FIREBASE_CONFIG`.

