># api+OR+der

<p align="center">
<sub>Todo</sub> sobre <sup>HTTP</sup>./
</p>

>Principal

<br>
<br>
<br>
<br>

>Fuente [WIKIPEDIA.ORG:](https://es.wikipedia.org/wiki/Request_for_Comments)

Las RFC (Request For Comments) son una serie de publicaciones del grupo de trabajo de ingeniería de Internet, (IETF) (el consorcio de colaboración técnica más importante de la red), que describen diversos aspectos del funcionamiento de Internet y otras redes de computadoras, como protocolos, procedimientos, mejoras, etc, junto con comentarios e ideas sobre estos. Cada RFC constituye un monográfico o memorando histórico, que ingenieros y/o expertos en la materia hicieron llegar al IETF, para que este sea valorado por el resto de la comunidad.

>Fuente [IBM.COM:](https://www.ibm.com/docs/es/cics-ts/6.x?topic=concepts-http-protocol)

Las RFC que moldean el protocolo HTTP, establecen las acciones técnicas que un cliente y un servidor deben realizar para lograr intercambiar peticiones y respuestas de forma adecuada en cada versión que lo componen. Un cliente realiza solicitudes HTTP a un servidor con el objetivo de acceder a recursos alojados en el mismo. Una respuesta HTTP la realiza (normalmente) un servidor a un cliente. El objetivo de la respuesta es proporcionar al cliente el recurso que solicitó, o, en su defecto, informarle que se llevó a cabo la acción requerida. Todas estas acciones se describen como "requisitos". Se dice que un cliente o servidor que cumple con los requisitos de su versión del protocolo HTTP es "conforme" con la especificación HTTP.

>Fuente [IETF.ORG:](https://datatracker.ietf.org/doc/html/rfc9110)

El Protocolo de transferencia de hipertexto (HTTP) es una familia de protocolos de solicitud/respuesta, a nivel de aplicación y sin estado, que comparten una interfaz genérica, una semántica extensible y mensajes autodescriptivos para permitir una interacción flexible con sistemas de información de hipertexto basados ​​en red.

HTTP oculta los detalles de cómo se implementa un servicio al presentar una interfaz uniforme a los clientes, que es independiente de los tipos de recursos proporcionados. De la misma manera, los servidores no necesitan conocer el propósito de cada cliente: una solicitud se puede considerar de forma aislada en lugar de asociarla con un tipo específico de cliente o una secuencia predeterminada de pasos de la aplicación. Esto permite que las implementaciones de propósito general se utilicen de manera efectiva en muchos contextos diferentes, reduce la complejidad de la interacción, y permite una evolución independiente a lo largo del tiempo.

HTTP también está diseñado para usarse como protocolo de intermediación, en donde los servidores proxy y las pasarelas pueden traducir sistemas de información que no sean HTTP a una interfaz más genérica.

HTTP ha sido el principal protocolo de transferencia de información para la World Wide Web desde su introducción en 1990. Comenzó como un mecanismo trivial para solicitudes de baja latencia, con un único método (GET) para solicitar la transferencia de un supuesto documento de hipertexto identificado por una ruta determinada. La primer versión no tenía número específico, por lo que se la diferenció de las demas (de la segunda mejora llamada HTTP/1.0) con 0.9, uno por debajo. 

En los viejos tiempos, los problemas de interoperabilidad eran habituales. En un esfuerzo por resolverlos, en noviembre de 1996, se publicó un documento informativo que describía las prácticas comunes, conocido como RFC 1945, en el que se definía el HTTP/1.0.

Mientras tanto, se estaba llevando a cabo una estandarización adecuada, que se produjo en paralelo a las diversas implementaciones de HTTP/1.0. La primera versión estandarizada de HTTP, HTTP/1.1, se publicó a principios de 1997, sólo unos meses después de HTTP/1.0.

HTTP/1.1 aclaró ambigüedades e introdujo numerosas mejoras, por ejemplo, la posibilidad de reutilizar la conexión (a diferencia de HTTP/0.9 y /1.0, que cerraban la conexión al terminar cada mensaje (al recibir la respuesta)), lo cual generaba ahorro de tiempo, de latencia y recursos informáticos.

Tampoco era necesario abrir la conexión varias veces para mostrar los recursos integrados en el documento original solicitado (HTTP/0.9 y /1.0 debían abrir una conexión para la solicitud de un documento con un archivo X, para luego tener que abrir una segunda conexión y obtener ese archivo X).

También se agregó la segmentación de mensajes, lo que permitió enviar una segunda solicitud antes de que se transmitiera por completo la respuesta a la primera, reduciendo así la latencia de la comunicación e incrementando la interoperabilidad.

Además, se admitieron respuestas fragmentadas, se introdujeron mecanismos de control de caché adicionales, se introdujo la negociación de contenido (que incluye idioma, codificación y tipo) generando que un cliente y un servidor pudieran acordar qué contenido intercambiar. También, gracias a la adición del encabezado Host, se agregó la capacidad de alojar diferentes dominios desde la misma dirección IP, permitiendo la ubicación y expansión de servidores.

La extensibilidad de HTTP facilitó la creación de nuevos encabezados y métodos. Aunque el protocolo HTTP/1.1 se perfeccionó en dos revisiones, fue extremadamente estable durante más de 15 años. HTTP/1.1 se actualizó nuevamente en 2022 con RFC 9110. No solo se actualizó HTTP/1.1, sino que se revisó todo HTTP y ahora se divide en los siguientes 5 documentos principales: 
  
>Semántica HTTP:

[1 ( RFC 9110 )](https://github.com/Inf3r/apiORder/tree/RFC9110)

>Almacenamiento en Caché:

[2 ( RFC 9111 )](https://www.rfc-editor.org/rfc/rfc9111)

>el cual se aplica a todas las versiones de HTTP...

>HTTP/1.1:

[3 ( RFC 9112 )](https://www.rfc-editor.org/rfc/rfc9112)

>HTTP/2:

[4 ( RFC 9113 )](https://www.rfc-editor.org/rfc/rfc9113)

>y HTTP/3:

[5 ( RFC 9114 )](https://www.rfc-editor.org/rfc/rfc9114)
