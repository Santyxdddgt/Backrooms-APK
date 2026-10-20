# BACKROOMS — APK Android

Versión Android del juego **BACKROOMS** (Nivel 0 — registro de cámara corporal).
El `index.html` original corre intacto dentro de un WebView nativo a pantalla
completa, en modo inmersivo, con acceso a internet para el CDN (three.js,
fuentes, modelos 3D y sonidos).

## Requisitos

| Herramienta | Versión |
|---|---|
| Android Studio | Ladybug (2024.2.1) o más nuevo |
| JDK | 17+ (incluido en Android Studio) |
| Android SDK | API 35 (lo descarga Android Studio solo) |
| Dispositivo/emulador | Android 7.0 (API 24) o superior |

No necesita claves API, no usa Firebase, no usa Compose: compila en segundos.

## Cómo compilar el APK

1. Descomprimí este zip.
2. En Android Studio: **File → Open…** y elegí la carpeta `BACKROOMS`.
3. Esperá la sincronización de Gradle (la primera vez descarga dependencias,
   ~1-2 min).
4. Conectá tu teléfono con **Depuración USB** activada y apretá **Run ▶**,
   o bien generá el APK:
   - **APK debug**: `Build → Build APK(s)` → `app/build/outputs/apk/debug/app-debug.apk`
   - **APK release**: `Build → Generate Signed App Bundle / APK → APK`,
     elegí *release* (ya viene firmado con la keystore de debug, ver abajo).
5. Pasá el `.apk` al teléfono (o descargalo directo) e instalalo. Si Android
   pregunta, habilitá *"instalar apps de fuentes desconocidas"* para tu
   navegador/administrador de archivos.

> También podés compilar por consola desde la raíz del proyecto:
> `./gradlew assembleDebug` (Linux/macOS) o `gradlew.bat assembleDebug` (Windows).

## Cómo actualizar el juego

El juego es un único archivo:

```
app/src/main/assets/index.html
```

Reemplazalo por una versión más nueva del juego y recompilá. Nada más.
El ícono, el nombre y la configuración del WebView quedan como están.

## Qué hace la app (detalle técnico)

- **WebView a pantalla completa** con modo inmersivo permanente (sin barra de
  estado ni de navegación; se re-ocultan al recuperar el foco).
- **WebViewAssetLoader**: el juego se sirve desde
  `https://appassets.androidplatform.net/assets/index.html`. Esto le da un
  **origen seguro** (equivalente a https), imprescindible para que funcionen
  los módulos ES con *importmap* (three.js 0.170 desde jsdelivr), `localStorage`
  (progreso y stats del juego), WebAudio, WebGL y WebSockets. Cargar con
  `file://` rompería varias de estas cosas.
- **Permisos**: `INTERNET` y `ACCESS_NETWORK_STATE` — el juego descarga three.js,
  las fuentes de Google, los modelos `.glb` de las entidades y los `.mp3` de los
  jumpscare desde CDN, y el multijugador se conecta a servidores WebSocket.
- **Orientación libre** (vertical/horizontal sin lock): el propio juego muestra
  el aviso de girar el teléfono y tiene su botón de pantalla completa. Rotar no
  reinicia la partida (`configChanges` en el manifest).
- **Teclado**: al escribir el nombre de sala multijugador o la URL del servidor,
  el contenido se reencoge para que los campos no queden tapados.
- **Doble ATRÁS para salir** (aviso con toast), pantalla siempre encendida y
  recreación automática del WebView si el renderizador WebGL llegara a morir.
- Guardados en `localStorage` del WebView: persisten entre sesiones.

## Multijugador (opcional)

El juego trae pestaña **MODO MULTIJUGADOR**. Necesita un servidor WebSocket
(`server.js` del proyecto original) desplegado en Render/Railway/Fly.io/VPS;
desde la app se pega su dirección `wss://…` en el campo correspondiente y se
presiona CONECTAR. Sin servidor, el resto del juego funciona 100% offline tras
la primera carga de CDN.

## Firma

- Por defecto el **debug** usa la keystore de debug autogenerada.
- El **release** está configurado para firmar también con la debug keystore
  (`signingConfig = signingConfigs.getByName("debug")` en
  `app/build.gradle.kts`), así obtenés un APK instalable sin configurar nada.
- Para publicar en Play Store:
  ```bash
  keytool -genkey -v -keystore my-upload-key.jks -keyalg RSA -keysize 2048 \
          -validity 10000 -alias upload
  ```
  y reemplazá esa línea en `app/build.gradle.kts` por un `signingConfigs`
  propio con tu keystore y contraseñas.

## Estructura

```
BACKROOMS/
├── settings.gradle.kts        # módulos y repositorios
├── build.gradle.kts           # plugins raíz (AGP 8.7.3, Kotlin 2.0.21)
├── gradle.properties
├── gradlew / gradlew.bat      # wrapper Gradle 8.11.1 (incluye el .jar)
└── app/
    ├── build.gradle.kts       # minSdk 24 · target 35 · solo core-ktx + webkit
    └── src/main/
        ├── AndroidManifest.xml
        ├── assets/index.html  # ◀ EL JUEGO (reemplazable)
        ├── java/com/backrooms/game/MainActivity.kt
        └── res/               # ícono pasillo Nivel 0 (adaptivo + legacy), theme negro
```
