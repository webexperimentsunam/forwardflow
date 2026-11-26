# Forward Flow — migración de guardado FF-1.0-eventos

## Instalación
1. Conserva una copia de tu index.html original. No elimines los datos anteriores ni IndexedDB de sesiones pendientes.
2. Sustituye únicamente index.html en el repositorio forwardflow y añade control-expbuilder-forwardflow.html en la raíz. Conserva img/ y los demás archivos. No es necesario subir las imágenes de nuevo.
3. El control es una copia byte por byte del último index.html de ExpBuilder, con el identificador nuevo. No publiques el control sobre index.html mientras haces pruebas de la tarea.
4. Mantén IndexedDB activado en ExpBuilder. Batch y timeout provienen de la configuración del servidor. Los dos HTML tienen almacenes locales separados; usa una ventana/perfil limpio para cada prueba nueva, sin borrar sesiones pendientes.

## Pruebas recomendadas
- Control: https://webexperimentsunam.github.io/forwardflow/control-expbuilder-forwardflow.html
- Tarea completa: https://webexperimentsunam.github.io/forwardflow/
- Probar primero el rechazo del consentimiento: debe aparecer el final original, sin enviar respuestas experimentales ni solicitar una exportación. El servidor puede conservar metadatos de la sesión creada.
- Probar aceptación y tarea completa: revisar Network > apiData (createSession, activateConnection, appendEvents, completeSession, sessionStatus si se necesita). La confirmación final debe mostrar completion.confirmed=true y export.confirmed=true. Comparar cantidad de eventos y hash, y abrir el CSV. La ejecución anterior mostró 110 registros; es una referencia, no un conteo fijo para todas las rutas.
- En consola, ForwardFlowDiagnostics.snapshot() muestra la identidad de sesión, batch y timeout efectivos y otros datos operativos. No cambia la sesión.
- Si aparece Storage credentials require attention, conserva la pestaña y utiliza Reintentar guardado después de que se restablezca la autorización del backend.

## Alcance y limitaciones
- Nuevo ID: 1c62c85f-f48b-4f80-ac27-a3342897e639. ID antiguo: ca2580f8-5532-47ec-b3e2-1b3214219e1b. Se usa la configuración del nuevo HTML, no se incorporan tokens OAuth al código. El servidor controla su renovación.
- Protocolo de sesiones v3 y eventos v1, confirmaciones por hash SHA-256; límite de 100 eventos por solicitud y división por tamaño según el HTML de referencia.
- Se conserva literalmente el bloque experimental (estímulos, bucles, consentimiento, rutas, interfaz y ajustes móviles). Se mantiene DynamicPlugin 1.0.7 original y jsPsych 8.2.2; se sustituye el favicon faltante por el embebido del nuevo HTML. Se conservan los finales en español y se trata el fallo de exportación como recuperable.
- Antes de aceptar el consentimiento se mantienen las filas en memoria, sin persistir ni transmitir respuestas. Al aceptar se incorporan en orden a IndexedDB; al rechazar se descartan. No se solicita completeSession para rechazo.
- No se migran automáticamente las sesiones antiguas. No se implementó reanudación exacta tras recargar a mitad de los bucles (la navegación antigua no contiene un cursor suficiente para reconstruir la secuencia). Si una sesión activa tiene eventos respaldados y no llegó a finalización, se bloquea el reinicio para no duplicar ensayos; se conservan los datos. La finalización pendiente y el reintento de exportación sí tienen rutas de recuperación. Validar estos casos en el servidor antes del reclutamiento.
- No se agregó un modo de prueba corto para no cambiar la tarea.
- Verificación: sintaxis Node; construcción de 17 nodos raíz en una simulación; pruebas de 110 eventos sintéticos con batches 0, 25 y 150, hash y secuencias, reintento tras error de credenciales y rechazo sin envío con batch 1. Se simularon jsPsych, DOM, Firebase, IndexedDB y servidor; no se comprobó la interacción visual real ni exportación a Drive. Chromium del entorno bloqueó la navegación.
