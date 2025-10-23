### Funcionalidades que debe tener

**Entrada de datos:**
El usuario accede a un formulario web donde completa: tema (ej: "Dragones"), protagonista (ej: "Sofia"), cantidad mínima de personajes (ej: 3), y tono (ej: "aventurero"). También puede agregar preferencias adicionales en texto. Todos estos campos tienen validación en el navegador antes de enviar. El máximo es 5000 caracteres en cualquier campo de texto.

**Generación de historias:**
El sistema envía estos parámetros a OpenAI GPT-4 Turbo para que genere la historia. El prompt debe dejar bien claro que la historia debe tener estructura en 3 actos (inicio, conflicto, desenlace) y tener alrededor de 500 palabras. La generación no puede tomar más de 30 segundos. Una vez generada, la historia se guarda en la base de datos.

**Validación automática:**
El sistema verifica que la historia cumpla tres cosas: primero, que tenga entre 450 y 550 palabras (es decir, aproximadamente 500); segundo, que tenga estructura clara con al menos 2 párrafos separados, un conflicto evidente y un desenlace; tercero, que aparezcan mínimo 3 personajes distintos nombrados. Si todo está bien, la historia se acepta. Si falla algo, retorna un error específico diciendo qué no cumplió.

**Reintentos automáticos:**
Si la validación falla, el sistema reintenenta hasta 3 veces más. En cada reintento, ajusta el prompt basándose en los errores anteriores. Por ejemplo, si falló la longitud, el nuevo prompt dirá específicamente "exactamente entre 450 y 550 palabras". Si falló la estructura, aclarará mejor qué es inicio, conflicto y desenlace. Si falló el número de personajes, listará cuántos hacen falta. Cada intento queda registrado con la hora, el prompt, la respuesta y el resultado de validación. Después de 3 intentos fallidos, le muestra un error al usuario.

**Ver el proceso completo:**
El usuario puede ver un panel lateral que muestra cada intento: "Intento 1 - Rechazado por longitud insuficiente (350 palabras, necesitaba 450-550). Intento 2 - Aceptado exitosamente." Esto da total transparencia sobre qué pasó.

**Descargar y compartir:**
El usuario puede copiar el texto, descargarlo como archivo de texto plano, o regenerar la historia si quiere intentar de nuevo.

**Historial de historias:**
Solo usuarios autenticados pueden generar. Cada usuario ve sus propias historias en un dashboard con un historial de las últimas 50 que generó, con información de cuándo fueron creadas, cuántas palabras tienen, cuántos personajes y cuántos intentos tomó.

### Requisitos técnicos

**Velocidad:**
El formulario debe responder en menos de 100 milisegundos. La generación con OpenAI no puede tardar más de 30 segundos. La validación debe completarse en menos de 5 segundos. El dashboard debe cargar en menos de 2 segundos. En total, de principio a fin, todo debe tomar menos de 45 segundos en promedio.

**Disponibilidad:**
El sistema tiene que estar disponible el 99% del tiempo en horario comercial (8 a 22 horas UTC). Si OpenAI falla, muestra un error claro al usuario. Si hay timeout, reintenenta automáticamente una vez después de esperar 2 segundos.

**Seguridad:**
Todo viaja por HTTPS. Las claves de API de OpenAI nunca están en el código ni en el navegador, solo en variables de entorno del servidor. Los formularios tienen protección CSRF. Hay límite de 10 generaciones por usuario por hora. El servidor valida todos los inputs, no confía solo en validación del navegador. No guardamos datos sensibles más que lo mínimo necesario.

**Escalabilidad:**
La arquitectura está diseñada para que si mañana queremos dividir el backend en microservicios, no necesitemos cambiar todo desde cero. La base de datos puede soportar más de 10000 usuarios. Los costos estimados de OpenAI son entre 50 y 200 dólares por mes (dependiendo de si generamos 100 o 500 historias diarias).

**Código de calidad:**
Todo el código es TypeScript con modo estricto. Los servicios tienen tests unitarios. El flujo completo tiene tests de integración. Las funciones públicas tienen comentarios JSDoc. No usamos librerías innecesarias.

**Visibilidad:**
Los errores se registran en Sentry para que podamos monitorearlos. Rastreamos cuántas generaciones hay, cuántas resultan exitosas, cuánto tiempo toman en promedio. Todas las trazas de cada usuario se guardan en base de datos para poder auditar qué sucedió.


Principios de Arquitectura
Separación de Responsabilidades:
Cada servicio hace UNA cosa. No mezclar lógica de IA con persistencia. No mezclar validación con orquestación.

Diseño Testeable:
Inyectar dependencias. Evitar singletons globales. Permitir mocks fáciles para testing sin API keys reales.

Seguridad por Defecto:
API keys nunca en código. Inputs siempre validados. Usuarios siempre autenticados. Rate limiting desde el inicio.

Progresión Vertical:
Implementar en profundidad por cada sprint. Sprint 1: todo sobre setup. Sprint 2: todo sobre generación. No tocar UI hasta que backend esté sólido.