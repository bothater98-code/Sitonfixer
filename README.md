# Sitonfixer — variante gratuita experimental

Based on MuffinEMU by Void: https://github.com/MuffinEMU/Muffin-EMU

Base: `64a872318ba615701609b5be443ab357154d30ff` (v11.2). Sitonfixer es una adaptación propia, no una versión oficial. Se conservan GPL-3.0, ATTRIBUTION.md, MPL-2.0 en el núcleo y las licencias de las dependencias.

## Objetivo y límite actual

El objetivo solicitado es una sola aplicación que reúna emulación, JIT, túnel local y renovación de firma, con coste total 0 €, sin PC durante el uso. El prototipo actual NO cumple aún ese objetivo: integra emulación y preparación JIT, pero depende de LocalDevVPN y de un firmador externo. No incluye un sustituto de SideStore.

La extensión de túnel requiere un perfil autorizado para Network Extension. El proyecto gratuito no fabrica ese permiso ni altera la firma de LocalDevVPN. Integrar fuentes de SideStore tampoco elimina los permisos del túnel ni la caducidad de los perfiles gratuitos. La renovación de estos perfiles exige comunicarse con Apple; no se promete mantenimiento indefinido totalmente desconectado.

## Diseño gratuito

Emulador Wii U y auxiliar JIT en la misma app. Reutiliza la VPN local ya instalada. No requiere PC ni USB durante el uso, ni abrir StikDebug. No integra una nueva extensión VPN ni pide Network Extension en la firma.

Al elegir un juego descubre y comprueba el puerto local, inicia el auxiliar y coordina la preparación de memoria TXM. Una conexión TCP o la marca de debugger no se presentan como prueba de que JIT esté completamente preparado. Tras un fallo no cambia silenciosamente al intérprete.

Conserva la tabla ISO de idioma de Paper Mario y la genera sin sobrescribir archivos si falta en el contenedor nuevo. No incluye juegos, partidas, claves ni emparejamientos del usuario.

La preparación por Internet está desactivada por defecto. Si falta la DDI montada, se detiene sin descargar ni pedir personalización a Apple. Esta preparación inicial puede habilitarse explícitamente en Ajustes.

## Pendiente de demostrar

Las revisiones anteriores han pasado la compilación completa de Xcode. La prueba escalar se ha ejecutado hasta la carga del bloque, donde iOS rechazó la firma; no se ha demostrado ejecución ARM64 ni emulación Wii U por esta vía. Reutilizar una VPN local tampoco demuestra que pueda reconectarse desde cero en modo avión. No se garantiza funcionamiento desconectado tras reiniciar iOS. El presupuesto máximo es 0 euros; no se contrata ni factura ningún servicio.

## Entorno

El paquete contiene bootstrap.py, la base upstream fijada por commit, el parche y los archivos modificados. El bootstrap crea una carpeta nueva y desactiva los workflows heredados de publicación. No toca un teléfono ni sube archivos.

En macOS con Xcode/SDK iOS 27 y Homebrew:

1. `bash sitonfixer/prepare-macos.sh`
2. `bash sitonfixer/build-macos.sh`

La salida es una IPA sin firma. Un instalador compatible debe firmarla con la cuenta gratuita y conservar el auxiliar y get-task-allow. Esa firma sigue pendiente de validación. El código no elimina los límites de aplicaciones y renovación de la cuenta gratuita.

La firma macOS opcional solo requiere dos perfiles de desarrollo ya disponibles y una identidad en el llavero; no pide Network Extension. No se incluyen certificados ni contraseñas.

GitHub Actions puede compilar con sus ejecutores estándar gratuitos en un repositorio público que contenga exclusivamente código. Primero se comprueban los componentes nuevos con el compilador iOS; después se compila la aplicación completa. No se usan ejecutores de pago ni trabajos en repositorios privados.

La compilación rápida reutiliza Cemu.framework sin modificar de la IPA oficial v11.2, comprobando el SHA-256 del archivo completo. Compila la app y el auxiliar modificados. Para reconstruir también el núcleo desde su fuente puede usarse SITONFIXER_BUILD_CORE_FROM_SOURCE=1; no se afirma haber recompilado ese núcleo cuando se reutiliza.

## Investigación alternativa sin JIT

Esta revisión añade una prueba de traducción previa PowerPC → ARM64 y firma local, accesible en Ajustes > JIT. Usa únicamente código sintético propio y un certificado importado localmente. No es un motor Wii U AOT completo y no elimina todavía LocalDevVPN de la ruta habitual. No renueva perfiles. Consulta sitonfixer/AOT-RESEARCH.md para alcance y pruebas. Ningún certificado, contraseña, juego ni partida se publica.

## Corrección de firma de bibliotecas (revisión 6)

La dependencia LGPL-2.1-or-later apple-codesign-quick 0.1.0 se reconstruye desde su crate público con SHA-256 fijado y las modificaciones completas de prepare-codesign.py. La firma de bibliotecas omite entitlements; la del ejecutable principal los conserva. Las 19 pruebas del firmador pasan en Windows. Falta validar esta revisión en el iPhone. El diagnóstico de carga ahora conserva un motivo acotado y oculta rutas de contenedores e identificadores. No se publican registros ni archivos del teléfono. La licencia LGPL-2.1 se incluye en sitonfixer/LGPL-2.1.txt; fuente original: https://github.com/Dadoum/apple-crates y https://crates.io/crates/apple-codesign-quick/0.1.0.
