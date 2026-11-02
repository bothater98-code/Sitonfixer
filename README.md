# Sitonfixer — variante gratuita experimental

Based on MuffinEMU by Void: https://github.com/MuffinEMU/Muffin-EMU

Base: `64a872318ba615701609b5be443ab357154d30ff` (v11.2). Sitonfixer es una adaptación propia, no una versión oficial. Se conservan GPL-3.0, ATTRIBUTION.md, MPL-2.0 en el núcleo y las licencias de las dependencias.

## Diseño gratuito

Emulador Wii U y auxiliar JIT en la misma app. Reutiliza la VPN local ya instalada. No requiere PC ni USB durante el uso, ni abrir StikDebug. No integra una nueva extensión VPN ni pide Network Extension en la firma.

Al elegir un juego descubre y comprueba el puerto local, inicia el auxiliar y coordina la preparación de memoria TXM. Una conexión TCP o la marca de debugger no se presentan como prueba de que JIT esté completamente preparado. Tras un fallo no cambia silenciosamente al intérprete.

Conserva la tabla ISO de idioma de Paper Mario y la genera sin sobrescribir archivos si falta en el contenedor nuevo. No incluye juegos, partidas, claves ni emparejamientos del usuario.

La preparación por Internet está desactivada por defecto. Si falta la DDI montada, se detiene sin descargar ni pedir personalización a Apple. Esta preparación inicial puede habilitarse explícitamente en Ajustes.

## Pendiente de demostrar

Todavía no está compilado ni probado en el iPhone. Reutilizar una VPN local no demuestra que pueda reconectarse desde cero en modo avión. Tampoco se garantiza funcionamiento desconectado tras reiniciar iOS. El presupuesto máximo es 0 euros; no se contrata ni factura ningún servicio.

## Entorno

El paquete contiene bootstrap.py, la base upstream fijada por commit, el parche y los archivos modificados. El bootstrap crea una carpeta nueva y desactiva los workflows heredados de publicación. No toca un teléfono ni sube archivos.

En macOS con Xcode/SDK iOS 27 y Homebrew:

1. `bash sitonfixer/prepare-macos.sh`
2. `bash sitonfixer/build-macos.sh`

La salida es una IPA sin firma. Un instalador compatible debe firmarla con la cuenta gratuita y conservar el auxiliar y get-task-allow. Esa firma sigue pendiente de validación. El código no elimina los límites de aplicaciones y renovación de la cuenta gratuita.

La firma macOS opcional solo requiere dos perfiles de desarrollo ya disponibles y una identidad en el llavero; no pide Network Extension. No se incluyen certificados ni contraseñas.

GitHub Actions puede compilar con sus ejecutores estándar gratuitos en un repositorio público que contenga exclusivamente código. Primero se comprueban los componentes nuevos con el compilador iOS; después se compila la aplicación completa. No se usan ejecutores de pago ni trabajos en repositorios privados.
