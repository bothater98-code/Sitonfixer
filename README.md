# Sitonfixer

Prototipo de emulación Wii U basado en MuffinEMU/Cemu v11.2. Presupuesto máximo: 0 euros. El objetivo es unificar el uso en el iPhone sin PC, VPN ni servicios durante el juego; el objetivo completo no está terminado.

## Resultado confirmado

La revisión 8 se compiló desde fuentes, se instaló y el usuario confirmó 32 casos del backend ARM64 real después de reiniciar el iPhone, sin USB, Internet, VPN ni activar el JIT de iOS. Se comprobaron enteros, RAM big-endian, división con saltos, dobles y llamada al motor. No era aún el frontend PowerPC ni un juego.

## Frontend confirmado: revisión 9

Añade «Probar frontend PowerPC de Cemu». Un programa sintético de nueve instrucciones pasa por el análisis de límites, el decodificador, las optimizaciones, la asignación de registros y el emisor ARM64 reales. Usa una ventana privada de instrucciones y RAM de prueba. Incluye suma, almacenamiento/carga big-endian, comparación y dos ramas, contador de ciclos y retorno mediante stubs firmados. El comprobador verifica 32 estados incluyendo suma cero y desbordamiento modular.

La emisión compartida conserva el cuerpo de generación de la revisión 8. La nueva salida de bytes usa un allocator RW, no finaliza memoria ejecutable y no publica funciones en las tablas de juegos. Se mantienen la prueba escalar y la del backend anterior.

La revisión 9 se compiló e instaló y el usuario confirmó los 32 casos tras el procedimiento de reinicio completo sin USB, red ni VPN. Es evidencia comunicada por el usuario, sin captura USB simultánea. No arranca títulos, no implementa publicación de módulos de producción, no almacena la identidad ni renueva perfiles. Véase sitonfixer/FRONTEND-PROBE.md.

## Módulo mayor confirmado: revisión 10

Añade «Probar módulo firmado de 64 KiB». El linker de la plataforma genera una plantilla con 64 KiB de texto alineado a 16 KiB, conservando la estructura Mach-O que él mismo calcula. El compositor copia sin modificar el programa nativo del frontend validado en la revisión 9 en el offset 32 KiB. Dos funciones nativas adicionales realizan llamadas entre páginas, una de ellas anidada y hacia atrás. Se comprueban 32 estados por cada una de las tres entradas (96 en total).

No son tres programas PowerPC diferentes ni un gestor de módulos para juegos. Es una prueba de tamaño, firma/carga de más páginas, offsets y llamadas entre funciones. Los tests previos ejecutan las instrucciones del compositor Swift real en un emulador ARM64 independiente y comprueban argumentos, pila, registros preservados y memoria. La revisión 10 se compiló e instaló y el usuario confirmó los 96 casos siguiendo el procedimiento de reinicio sin USB, red ni VPN; sin captura USB simultánea.

## Cargador confirmado: revisión 11

Añade «Probar carga y cierre de módulos», utilizando `SignedCodeImage`, `SignedCodeModule` y `ModuleLifetime`. Cada imagen recibe UUID e install_name propios antes de firmar. El cargador presta las entradas dentro de llamadas acotadas, impide nuevos préstamos al pedir el cierre y libera su referencia al cargador tras terminar las llamadas activas. La creación del directorio es exclusiva; una colisión no borra los archivos de otro módulo.

La nueva prueba abre dos imágenes con marcadores distintos y el mismo programa PPC9, comprueba 192 casos y verifica cierre diferido, rechazo de llamadas nuevas y conservación de la otra biblioteca. El preflight utiliza además bibliotecas ARM64 reales de macOS, firma ad hoc sin claves del usuario, el cargador nativo y pruebas concurrentes del contrato de vida. La revisión 11 se compiló e instaló y el usuario confirmó los 192 casos después del reinicio sin USB, red ni VPN; sin captura USB simultánea. No publica funciones en el dispatcher de juegos ni gestiona todavía sus hilos o renovación. Véase sitonfixer/MODULE-LIFETIME.md.

## Entrada y salida confirmadas: revisión 12

Añade «Probar entrada y salida de Cemu». El emisor compartido produce bytes inertes de los stubs reales de entrada/salida, con CPU, tabla y RAM suministradas como argumentos explícitos. La prueba los firma junto con el programa PPC9 y utiliza el cargador validado de la revisión 11. Se comprueban ambas salidas y 192 casos entre dos módulos. Se compiló e instaló y el usuario confirmó esos 192 casos tras el reinicio sin USB, red ni VPN, sin observador USB simultáneo. No cambia todavía la inicialización de juegos ni activa un modo firmado de producción. Véase sitonfixer/INTERFACE-PROBE.md.

## Despacho confirmado: revisión 13

Añade «Probar despacho entre funciones PowerPC». Dos funciones distintas se compilan por separado y se cargan en dos imágenes firmadas. Una llamada PowerPC A → B retorna a una entrada de continuación de A mediante una tabla privada. La prueba retira B, comprueba la salida controlada, la repone y reanuda. Son 192 comprobaciones sobre 64 estados iniciales con ambas salidas firmadas. No ejecuta aún un intérprete de respaldo ni modifica las tablas de juegos. Se compiló e instaló y el usuario confirmó las 192 comprobaciones tras reinicio sin USB, red ni VPN; sin observador USB simultáneo. Véase sitonfixer/DISPATCH-PROBE.md.

## Trabajo actual: revisión 14

Añade «Probar inicialización real de Cemu», sin certificado: llama a la inicialización real del puente y núcleo, comprueba ocho condiciones sobre finalización, base de memoria, dos zonas iniciales, selección del intérprete, proceso limpio y ausencia de título. Reserva el proceso hasta cerrarlo y excluye lanzamientos y otras pruebas. No ejecuta aún instrucciones de juego ni valida toda la RAM. Compilación y prueba física pendientes. Véase sitonfixer/INITIALIZATION-PROBE.md.

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
