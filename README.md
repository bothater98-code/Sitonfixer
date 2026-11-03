# Sitonfixer

Prototipo de emulación Wii U basado en MuffinEMU/Cemu v11.2. Presupuesto máximo: 0 euros. El objetivo es unificar el uso en el iPhone sin PC, VPN ni servicios durante el juego; el objetivo completo no está terminado.

## Resultado confirmado

La revisión 8 se compiló desde fuentes, se instaló y el usuario confirmó 32 casos del backend ARM64 real después de reiniciar el iPhone, sin USB, Internet, VPN ni activar el JIT de iOS. Se comprobaron enteros, RAM big-endian, división con saltos, dobles y llamada al motor. No era aún el frontend PowerPC ni un juego.

## Trabajo actual: revisión 9

Añade «Probar frontend PowerPC de Cemu». Un programa sintético de nueve instrucciones pasa por el análisis de límites, el decodificador, las optimizaciones, la asignación de registros y el emisor ARM64 reales. Usa una ventana privada de instrucciones y RAM de prueba. Incluye suma, almacenamiento/carga big-endian, comparación y dos ramas, contador de ciclos y retorno mediante stubs firmados. El comprobador verifica 32 estados incluyendo suma cero y desbordamiento modular.

La emisión compartida conserva el cuerpo de generación de la revisión 8. La nueva salida de bytes usa un allocator RW, no finaliza memoria ejecutable y no publica funciones en las tablas de juegos. Se mantienen la prueba escalar y la del backend anterior.

Esta revisión requiere compilación y prueba física antes de declararla validada. No arranca títulos, no implementa publicación de módulos de producción, no almacena la identidad ni renueva perfiles. Véase sitonfixer/FRONTEND-PROBE.md.

## Compilación

Se necesitan macOS, Xcode/SDK iOS 27 y Homebrew. El workflow autorizado usa un ejecutor estándar gratuito en un repositorio público, con permisos de lectura.

```sh
export SITONFIXER_BUILD_CORE_FROM_SOURCE=1
bash sitonfixer/prepare-macos.sh
bash sitonfixer/build-macos.sh
```

Es necesario recompilar Cemu completo. La biblioteca oficial no contiene las funciones nuevas. El gate exige ambas funciones backend y ambas frontend. La salida es una IPA sin firma; el instalador usa la identidad gratuita existente. La variante de pruebas omite el helper JIT y conserva el contenedor autorizado.

Los certificados y perfiles no forman parte del código público. La identidad importada se conserva solo en memoria durante cada prueba. El firmador local no inicia sesión, no crea túneles ni renueva la aplicación. Las bibliotecas usan el identificador exacto del host y omiten entitlements; la dependencia apple-codesign-quick 0.1.0 se reconstruye mediante prepare-codesign.py con checksum y modificaciones fijados, con su licencia LGPL incluida. Se conservan las licencias originales de los demás componentes.

## Pendiente

Después del frontend controlado faltan integrar la publicación firmada de funciones y stubs de juegos, resolver propiedad/invalidez/apagado seguro de módulos, lotes y caché, medir títulos reales, ofrecer importación/almacenamiento local opcional de identidad y resolver la renovación. No se ha demostrado operación indefinida offline con perfiles gratuitos.
