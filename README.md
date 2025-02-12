># api+OR+der

<p align="center">
<sub>Todo</sub> sobre <sup>HTTP</sup>./... desde la perspectiva de un buen barco, con algo de ron y un parche.
</p>

>RFC9110

[< Principal](https://github.com/Inf3r/apiORder/tree/main)
<br>
<br>
<br>
<br>
>Fuente [IETF.ORG:](https://datatracker.ietf.org/doc/html/rfc9110)

>Historia y evolución

HTTP ha sido el principal protocolo de transferencia de información para la World Wide Web desde su introducción en 1990. Comenzó como un mecanismo trivial para solicitudes de baja latencia, con un único método (GET) (protocolo de "una sola línea") para solicitar la transferencia de un supuesto documento de hipertexto identificado por una ruta de acceso determinada, dentro de un entorno aislado, es decir, la URL completa no se incluía porque el protocolo, el servidor y el puerto, no eran necesarios una vez que se conectaba al servidor. A medida que la Web fue creciendo, HTTP se amplió para incluir solicitudes y respuestas dentro de mensajes, transferir formatos de datos arbitrarios utilizando tipos de medios similares a MIME (especificación de correo electrónico) y enrutar solicitudes a través de intermediarios. Estos protocolos se definieron finalmente como HTTP/0.9 y HTTP/1.0.

HTTP/1.1 fue diseñado para refinar las características del protocolo, manteniendo la compatibilidad con la sintaxis de mensajería basada en texto existente, mejorando su interoperabilidad, escalabilidad y robustez en Internet. Esto incluía delimitadores de datos basados ​​en longitud para contenido fijo y dinámico (fragmentado), un marco consistente para la negociación de contenido, validadores opacos para solicitudes condicionales, controles de caché para una mejor consistencia de caché, solicitudes de rango para actualizaciones parciales y conexiones persistentes predeterminadas.

Una "consecuencia" de la flexibilidad que ofrece el protocolo HTTP, es que no puede definirse en términos de lo que ocurre detrás de la interfaz. En cambio, está limitado a definir la sintaxis de la comunicación, la intención recibida y el comportamiento esperado de los destinatarios. Si la comunicación se considera de forma aislada, las acciones exitosas deberían reflejarse en cambios correspondientes en la interfaz observable proporcionada por los servidores (comunicación genérica). Sin embargo, dado que varios clientes pueden actuar en paralelo y quizás con propósitos cruzados, no se le puede exigir al protocolo que dichos cambios sean observables más allá del alcance de una única respuesta.

HTTP/2 introdujo una capa de sesión multiplexada sobre los protocolos TLS y TCP existentes para intercambiar mensajes HTTP simultáneos con compresión de campos y envío al servidor eficientes. HTTP/3 proporciona mayor independencia para los mensajes simultáneos al utilizar QUIC como un transporte multiplexado seguro sobre UDP en lugar de TCP.

Las tres versiones principales de HTTP se basan en la semántica definida en este documento. No se han vuelto obsoletas entre sí porque cada una tiene ventajas y limitaciones específicas según el contexto de uso. Se espera que las implementaciones elijan la sintaxis de transporte y mensajería más apropiada para su contexto particular. 

Esta revisión de HTTP separa la definición de semántica (este documento) y el almacenamiento en caché de la sintaxis de mensajería HTTP/1.1 actual, para permitir que cada versión principal del protocolo progrese de forma independiente mientras hace referencia a la misma semántica central.

>Semántica básica

HTTP proporciona una interfaz uniforme para interactuar con un recurso, independientemente de su tipo, naturaleza o implementación, mediante el envío de mensajes que manipulan o transfieren representaciones.

Cada mensaje es una solicitud o una respuesta. Un cliente construye mensajes de solicitud que comunican sus intenciones y los envía a un servidor de origen identificado. Un servidor escucha las solicitudes, analiza cada mensaje recibido, interpreta la semántica del mensaje en relación con el recurso de destino identificado y responde a esa solicitud con uno o más mensajes de respuesta. El cliente examina las respuestas recibidas para ver si se cumplieron sus intenciones y determina qué hacer a continuación en función de los códigos de estado y el contenido recibido. 

La semántica HTTP incluye las intenciones definidas por cada método de solicitud, extensiones de esas semánticas que podrían describirse en los campos de encabezado de la solicitud, códigos de estado que describen la respuesta y otros datos de control y de recursos que podrían proporcionarse en los campos de respuesta.

La semántica también incluye metadatos de representación que describen cómo se pretende que el destinatario interprete el contenido, campos de encabezado de solicitud que podrían influir en la selección de contenido y los diversos algoritmos de selección a los que colectivamente se hace referencia como "negociación de contenido".

>Versión del protocolo

El número de versión de HTTP consta de dos dígitos decimales separados por un "." (punto o punto decimal). El primer dígito (versión principal) indica la sintaxis del mensaje, mientras que el segundo dígito (versión secundaria) indica la versión secundaria más alta dentro de esa versión principal con la que el remitente es compatible (capaz de comprender para futuras comunicaciones). 

Si bien la semántica básica de HTTP no cambia entre versiones de protocolo, su expresión "en la red" puede cambiar, y por lo tanto el número de versión de HTTP cambia cuando se realizan cambios incompatibles en el formato de la red. Además, HTTP permite realizar cambios incrementales compatibles con versiones anteriores en el protocolo sin cambiar su versión mediante el uso de puntos de extensión definidos. 

La versión del protocolo en su conjunto indica la conformidad del remitente con el conjunto de requisitos establecidos en las especificaciones correspondientes a esa versión. Por ejemplo, la versión "HTTP/1.1" se define mediante las especificaciones combinadas de este documento, "HTTP Caching" y "HTTP/1.1".

El número de versión principal de HTTP se incrementa cuando se introduce una sintaxis de mensaje incompatible. El número menor se incrementa cuando los cambios realizados al protocolo tienen el efecto de agregar algo a la semántica del mensaje o implicar capacidades adicionales del remitente. 

La versión menor anuncia las capacidades de comunicación del remitente incluso cuando el remitente solo usa un subconjunto compatible con versiones anteriores del protocolo, lo que permite al destinatario saber que se pueden usar funciones más avanzadas en respuesta (por parte de los servidores) o en futuras solicitudes (por parte de los clientes). 

Cuando una versión principal de HTTP no define ninguna versión secundaria, se implica la versión secundaria "0". El "0" se utiliza cuando se hace referencia a ese protocolo dentro de elementos que requieren un identificador de versión secundaria.

>Recursos

El destino de una solicitud HTTP se denomina "recurso". HTTP no limita la naturaleza de un recurso; simplemente define una interfaz que puede utilizarse para interactuar con los mismos. La mayoría de los recursos se identifican mediante un Identificador Uniforme de Recursos (URI).

Un objetivo de diseño de HTTP es separar la identificación de recursos de la semántica de la solicitud, lo que se hace posible al incorporar la semántica de la solicitud al método de solicitud, junto con algunos campos de encabezado que la modifican. Un recurso no puede tratar una solicitud de una manera incoherente con la semántica del método de la solicitud. Por ejemplo, aunque el URI de un recurso puede implicar una semántica que no es segura, un cliente puede esperar que el recurso evite acciones que no son seguras al procesar una solicitud con un método seguro.

>Representaciones

Una "representación" es información que pretende reflejar un estado pasado, actual o deseado de un recurso determinado, en un formato que se pueda comunicar fácilmente a través del protocolo. Una representación consiste en un conjunto de metadatos de representación y un flujo potencialmente ilimitado de datos de representación. 

El protocolo HTTP permite "ocultar información" detrás de su interfaz uniforme al definir la comunicación con respecto a una representación transferible del estado del recurso, en lugar de transferir el recurso en sí. Esto permite que el recurso identificado por una URI sea cualquier cosa, incluidas funciones temporales como "el clima actual en La Plata", al tiempo que proporciona potencialmente información que representa ese recurso en el momento en que se genera un mensaje. 

La interfaz uniforme es similar a una ventana a través de la cual se puede observar y actuar sobre una cosa únicamente mediante la comunicación de mensajes a un actor independiente del otro lado. Se necesita una abstracción compartida para representar ("tomar el lugar de") el estado actual o deseado de esa cosa en nuestras comunicaciones. Cuando una representación es hipertexto, puede proporcionar tanto una representación del estado del recurso como instrucciones de procesamiento que ayuden a guiar las interacciones futuras del receptor.

Un recurso de destino puede contar con, o ser capaz de generar, múltiples representaciones que están destinadas a reflejar el estado actual del recurso. Se utilizaría un algoritmo, generalmente basado en la negociación de contenido, para seleccionar una de esas representaciones como la más aplicable a una solicitud dada. Esta "representación seleccionada" proporciona los datos y metadatos para evaluar solicitudes condicionales y construir el contenido para las respuestas 200 (OK), 206 (Contenido parcial) y 304 (No modificado) al método GET.

>Conexiones, Clientes y Servidores

HTTP es un protocolo cliente/servidor que opera a través de una "conexión" "confiable" (dependiendo de la versión utilizada, y si dicha versión se basa en una capa segura (como TLS1.3+)) de capa de sesión o transporte. Un "cliente" HTTP es un programa que establece una conexión con un servidor con el fin de enviar una o más solicitudes HTTP. Un "servidor" HTTP es un programa que acepta conexiones para atender dichas solicitudes mediante el envío de respuestas.

Los términos cliente y servidor se refieren únicamente a las funciones que estos programas desempeñan para una conexión y un momento particular. El mismo programa puede actuar como cliente en algunas conexiones y como servidor en otras.

HTTP se define como un protocolo sin estado, lo que significa que la semántica de cada mensaje de solicitud se puede entender de forma aislada y que la relación entre las conexiones y los mensajes en ellas no tiene impacto en la interpretación de esos mensajes. Por ejemplo, un método de solicitud CONNECT, o una solicitud con el campo de encabezado UPDATE, puede ocurrir en cualquier momento, no solo en el primer mensaje de una conexión. Muchas implementaciones dependen del diseño sin estado de HTTP para reutilizar conexiones proxy o equilibrar dinámicamente la carga de solicitudes en múltiples servidores.

Como resultado, un servidor NO DEBE asumir que dos solicitudes en la misma conexión provienen del mismo agente de usuario, a menos que la conexión sea segura y específica de ese agente.

>Mensajes HTTP


