# Pruebas Cortas y Parcial Integrador — Logística Internacional
Universidad de Panamá · Centro Regional Universitario de Veraguas

Aplicación web para crear pruebas cortas y parciales integradores, generar un
código de acceso único por prueba, aplicar el examen a los estudiantes con
corrección automática y retroalimentación inmediata, y consultar el informe
de calificaciones y tiempos (solo visible para el docente).

> **Si nunca ha programado, no se preocupe.** Esta guía no requiere que
> entienda ni edite código. Solo debe hacer clic en botones y copiar/pegar
> unos valores que Google le va a mostrar. Tómese unos 10 minutos y
> quedará funcionando permanentemente.

## ⚠️ Por qué es indispensable hacer el Paso 1 (Firebase)

Ahora mismo, si su sitio muestra una **franja roja** que dice "Modo local
de prueba", significa que los datos de las pruebas se guardan únicamente en
la computadora/navegador de la persona que los creó. Si usted crea una
prueba en su computadora, **sus estudiantes NO la van a poder ver desde las
suyas**, aunque tengan el código correcto. El Paso 1 conecta la app a una
base de datos gratuita en la nube para que todos —usted y todos sus
estudiantes— vean exactamente la misma información sin importar desde qué
computadora o celular entren.

## Paso 1 — Crear la base de datos gratuita (Firebase)

1. Entre a **https://console.firebase.google.com** con su cuenta de Gmail.
2. Haga clic en **"Crear un proyecto"** (o "Add project"). Póngale el nombre
   que quiera (ej. "logistica-internacional"). Cuando le pregunte por
   Google Analytics, puede dejarlo desactivado y continuar.
3. Cuando el proyecto esté listo, en el menú de la izquierda busque
   **"Compilación" → "Firestore Database"**. Haga clic en **"Crear base de
   datos"**.
   - Elija **"Iniciar en modo de producción"**.
   - Elija la ubicación más cercana (por ejemplo, cualquiera que diga
     `us-central` o `southamerica`) y confirme.
4. Dentro de Firestore, vaya a la pestaña **"Reglas"** (arriba). Va a ver
   un recuadro de texto con código. Borre todo ese contenido y pegue en su
   lugar el contenido del archivo `firestore.rules` que viene en esta
   carpeta (ábralo con el Bloc de notas, copie todo, péguelo ahí). Haga
   clic en **"Publicar"**.
5. Ahora vuelva al menú de la izquierda, arriba de todo, y haga clic en el
   ícono de **engranaje ⚙ → "Configuración del proyecto"**.
6. Baje hasta la sección **"Tus apps"** y haga clic en el ícono `</>`
   (Web). Escriba un nombre para la app (ej. "pruebas-logistica") y haga
   clic en **"Registrar app"**. No necesita activar Firebase Hosting.
7. Google le va a mostrar un recuadro de código que empieza así:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "logistica-internacional-xxxx.firebaseapp.com",
     projectId: "logistica-internacional-xxxx",
     ...
   };
   ```
   **Copie ese bloque completo** (los valores entre comillas son los que
   necesita).
8. Abra el archivo `index.html` de esta carpeta con el Bloc de notas (clic
   derecho sobre el archivo → "Abrir con" → "Bloc de notas", o con
   Notepad++/VS Code si tiene). Presione **Ctrl+F** y busque el texto
   `TU_API_KEY`. Va a encontrar un bloque así, cerca del inicio del
   archivo:
   ```js
   window.FIREBASE_CONFIG = {
     apiKey: "TU_API_KEY",
     authDomain: "TU_PROYECTO.firebaseapp.com",
     projectId: "TU_PROYECTO",
     storageBucket: "TU_PROYECTO.appspot.com",
     messagingSenderId: "000000000000",
     appId: "1:000000000000:web:xxxxxxxxxxxxxxxxxxxxxx"
   };
   ```
   Reemplace cada valor por el que le dio Google en el paso anterior
   (`apiKey` por `apiKey`, `authDomain` por `authDomain`, etc.). Guarde el
   archivo (Ctrl+S), sin cambiar el nombre ni la extensión `.html`.

Con esto, la franja roja debería desaparecer la próxima vez que abra la
app, y los datos ya se compartirán entre todos.

## Paso 2 — Subir el proyecto a GitHub

Si ya tiene el repositorio conectado a Netlify (como parece ser su caso),
simplemente reemplace el archivo `index.html` de su repositorio por esta
nueva versión (la puede subir directo desde la página de GitHub, sin usar
la terminal: entre a su repositorio → haga clic sobre `index.html` → ícono
de lápiz "Edit" → borre todo → pegue el contenido del nuevo archivo →
"Commit changes"). Netlify va a volver a publicar el sitio automáticamente
en 1-2 minutos.

Si está empezando de cero:
```bash
git init
git add .
git commit -m "Aplicación de pruebas - Logística Internacional"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/TU_REPOSITORIO.git
git push -u origin main
```

## Paso 3 — Asegurarse de que el sitio sea PÚBLICO en Netlify

Esto es indispensable y es distinto de Firebase: si su sitio de Netlify
muestra el mensaje **"This project is private. Only project members can
view this site"**, sus estudiantes no podrán abrir el enlace aunque todo
lo demás esté bien configurado. Para arreglarlo:

1. Entre a **https://app.netlify.com** y abra su sitio.
2. Vaya a **"Site configuration"** (o "Site settings") en el menú.
3. Busque la sección **"Visitor access"** (Acceso de visitantes).
4. Si ve algo como "Netlify visitor access" o restricción por contraseña o
   por equipo, cámbielo a **"Public"** (público) y guarde.

Ese mensaje que vio ("This project is private…") normalmente solo lo ve
usted por estar conectado a su cuenta de Netlify — pero es buena práctica
confirmar en "Visitor access" que el sitio esté en público antes de
compartir el enlace con sus estudiantes.

## Cambiar la clave de acceso del docente (sin tocar código)

Ya no es necesario editar el archivo para esto. Una vez dentro del Panel
del Docente, verá un botón **"⚙ Cambiar clave de acceso"**. Ahí escribe su
clave actual (por defecto `CRUV-2026`, a menos que ya la haya cambiado
antes) y la nueva clave que quiera usar. Se guarda igual que las pruebas,
así que también depende de tener Firebase configurado (Paso 1) para que el
cambio se mantenga de forma permanente y funcione igual desde cualquier
computadora.

## Uso de la aplicación

- **Docente:** entra al sitio → "Panel del docente" → escribe la clave de
  acceso (`CRUV-2026` si nunca la ha cambiado). Ahí sube el documento de
  preguntas por módulo (.docx o .txt, formato explicado dentro de la propia
  app), genera el código de acceso único de la prueba, y en "📊 Ver
  calificaciones" consulta el informe de notas y tiempos de cada
  estudiante para esa prueba.
- **Estudiante:** entra al sitio → "Acceso del estudiante" → ingresa el
  código de la prueba que le compartió el docente, o abre directamente el
  enlace `tu-sitio.netlify.app/#codigo=EL-CODIGO` que la app genera
  automáticamente (ya viene con el código cargado).

## Solución de problemas

**Veo la franja roja "Modo local de prueba" en el sitio ya publicado.**
El Paso 1 (Firebase) no quedó bien configurado. Revise que copió las
llaves correctas dentro de `FIREBASE_CONFIG` en `index.html`, que guardó el
archivo, y que subió esa versión actualizada a GitHub (Netlify solo publica
lo que está en el repositorio).

**Mis estudiantes no pueden abrir el enlace del sitio.**
Revise el Paso 3 (Visitor access debe estar en "Public" en Netlify).

**Genero el código de la prueba pero el estudiante recibe "código no
encontrado".**
Casi siempre es porque el Paso 1 no está completo (cada quien tiene su
propia copia local de los datos). Confirme que la franja roja ya no
aparece para nadie, ni para usted ni para el estudiante.

**Subo un documento .docx/.txt y no pasa nada, o me sale un mensaje de
error al procesarlo.**
La app le muestra el motivo exacto (documento vacío, formato no reconocido,
o el lector de .docx bloqueado por su red). Si el mensaje menciona que no
se pudo cargar el "componente para leer .docx", como alternativa guarde el
documento como texto plano (.txt) con el mismo formato y súbalo así.

## Notas importantes

- Cambie la clave del docente (ver sección arriba) antes de compartir el
  sitio, para que los estudiantes no puedan entrar al panel del docente.
- Si un estudiante sale de la pantalla de examen (cambia de pestaña o app)
  mientras la prueba está en curso, se anula automáticamente con nota 0,
  solo para ese estudiante — no afecta a los demás.
- Cada estudiante tiene un único intento por prueba, identificado por su
  cédula.
- Las reglas de `firestore.rules` son básicas (lectura/escritura abierta a
  quien tenga el enlace del sitio). Es el mismo nivel de protección de una
  hoja de cálculo compartida por enlace: adecuado para un curso, no para
  datos sensibles a gran escala.
