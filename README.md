# Sitonfixer

Aplicación experimental basada en MuffinEMU v11.2 para investigar emulación independiente en el iPhone, con presupuesto máximo de 0 euros.

## Estado comprobado

La revisión 7 ha ejecutado un pequeño bloque escalar PowerPC → ARM64 generado y firmado en el propio iPhone. El usuario confirmó la ejecución tras cerrar y abrir la app, sin USB, Internet, VPN ni depurador. La confianza inicial de esa instalación requirió conexión. Esto no demuestra juegos Wii U ni renovación autónoma.

## Siguiente hito: backend real de Cemu

Esta revisión añade «Probar backend real de Cemu» en Ajustes → JIT → Prueba experimental sin JIT. Utiliza las rutinas ARM64 existentes del motor para emitir un bloque IML de prueba, con registros, RAM big-endian, división con saltos, coma flotante y una llamada a un auxiliar del motor. Comprueba 32 casos y conserva registros no modificados y bytes de RAM como testigos. No usa juegos ni cambia la ruta de ejecución habitual.

El código se emite en memoria solamente escribible, se copia a una biblioteca Mach-O, se firma localmente y se carga. No se activa el JIT de iOS. La prueba rechaza procesos depurados y no puede publicar memoria anónima ejecutable. Esta integración está pendiente de compilación completa y validación física; no es aún el recompilador conectado a juegos. Véase sitonfixer/CORE-BACKEND-PROBE.md.

El certificado importado y su contraseña permanecen solo en memoria durante la operación. No se envían ni guardan. Ningún certificado, perfil, emparejamiento, juego, partida o registro privado pertenece al paquete público.

## Compilación gratuita

El bootstrap reconstruye la fuente pública fijada por commit y aplica el parche revisado. Desactiva los workflows heredados de publicación. En un ejecutor macOS con Xcode/SDK iOS 27 y Homebrew:

```sh
export SITONFIXER_BUILD_CORE_FROM_SOURCE=1
bash sitonfixer/prepare-macos.sh
bash sitonfixer/build-macos.sh
```

Esta revisión recompila Cemu.framework completo: reutilizar la biblioteca oficial ya no es suficiente. La compilación comprueba que ambos símbolos nuevos estén exportados. GitHub Actions se limita a ejecutores estándar de un repositorio público, sin servicios de pago. La IPA resultante queda sin firma.

## Límites pendientes

Faltan integrar la publicación de bloques en la ejecución de títulos, su invalidación y duración, comprobar compatibilidad y rendimiento, guardar opcionalmente la identidad en el llavero local y resolver la renovación. La firma gratuita y la verificación de iOS conservan sus límites; este código no demuestra funcionamiento indefinido sin contacto con Apple.

El firmador usa apple-codesign-quick 0.1.0 con modificaciones reproducibles de prepare-codesign.py y licencia LGPL-2.1-or-later incluida. Las bibliotecas omiten entitlements y usan el identificador exacto del host. Se conservan las licencias y atribuciones de las fuentes originales.
