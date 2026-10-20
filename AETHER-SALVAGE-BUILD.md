# Aether Salvage: cómo activar la compilación

1. Descargá el archivo `Aether-Salvage-limpio.zip` entregado en el chat.
2. En el repositorio **Santyxdddgt/Backrooms-APK**, subilo a la raíz con ese nombre exacto. No hace falta extraerlo ni crear otro repositorio.
3. GitHub Actions ejecutará `Build Aether Salvage APK (clean)`. También se puede iniciar manualmente desde la pestaña **Actions**.
4. Cuando termine, abrí la ejecución y descargá el artifact `Aether-Salvage-APK-N`; contiene `Aether-Salvage.apk`.

## Firma para actualizar la instalación anterior

El workflow busca estos nombres de secretos (también acepta algunos alias): `ANDROID_KEYSTORE_BASE64`, `ANDROID_KEYSTORE_PASSWORD`, `ANDROID_KEY_ALIAS`, `ANDROID_KEY_PASSWORD`. Si tu keystore ya está guardada con otros nombres, hay que añadirlos a las expresiones del workflow. No se puede reconstruir la clave privada a partir del APK.

Si no encuentra los secretos de firma, el workflow hará un APK de prueba firmado con una clave debug creada para esa ejecución. No servirá para actualizar la instalación firmada previamente con otra clave.

El workflow conserva el repo Backrooms existente: solo lee el ZIP de Aether Salvage, construye el proyecto en una carpeta temporal del runner y publica el APK como artifact.
