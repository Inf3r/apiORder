># api+OR+der

<p align="center">
<sub>Todo</sub> sobre <sup>HTTP</sup>./
</p>

>RFC9110

[< Principal](https://github.com/Inf3r/apiORder/tree/main)
<br>
<br>
<br>
<br>
>Fuente [IETF.ORG:](https://datatracker.ietf.org/doc/html/rfc9110)

>Historia y evolución

En los comienzos del desarrollo de esta tecnología, que Tim Berners-Lee creó, HTTP existía con el nombre de "The Mesh", dentro de un entorno de laboratorio informático aislado. Luego, fue renombrado a "HTTP" donde datos como la URL completa no se incluía, ya que el protocolo, la dirección y el puerto no eran necesarios una vez que se conectaba al único servidor que funcionaba en ese entonces como pruebas. **(Ultra resumiendo)** A medida que la Web fue creciendo, HTTP se amplió para incluir solicitudes y respuestas dentro de mensajes, transferir formatos de datos arbitrarios utilizando tipos de medios similares a MIME (especificación de correo electrónico) y enrutar solicitudes a través de intermediarios. Estos protocolos se definieron finalmente como HTTP/0.9 y HTTP/1.0.

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

Si bien la semántica básica de HTTP no cambia entre versiones de protocolo, su expresión "en la red" puede variar, y por lo tanto, el número de versión de HTTP cambia cuando se realizan cambios incompatibles en el formato de la red. Además, HTTP permite realizar cambios incrementales compatibles con versiones anteriores en el protocolo sin cambiar su versión mediante el uso de puntos de extensión definidos. 

La versión del protocolo en su conjunto indica la conformidad del remitente con el conjunto de requisitos establecidos en las especificaciones correspondientes a esa versión. Por ejemplo, la versión "HTTP/1.1" se define mediante las especificaciones combinadas de este documento, "Caché HTTP" y "HTTP/1.1".

El número de versión principal de HTTP se incrementa cuando se introduce una sintaxis de mensaje incompatible. El número menor se incrementa cuando los cambios realizados al protocolo tienen el efecto de agregar algo a la semántica del mensaje o implicar capacidades adicionales del remitente. 

La versión menor anuncia las capacidades de comunicación del remitente incluso cuando el remitente solo usa un subconjunto compatible con versiones anteriores del protocolo, lo que permite al destinatario saber que se pueden usar funciones más avanzadas en respuesta (por parte de los servidores) o en futuras solicitudes (por parte de los clientes). 

Cuando una versión principal de HTTP no define ninguna versión secundaria, se implica la versión secundaria "0". El "0" se utiliza cuando se hace referencia a ese protocolo dentro de elementos que requieren un identificador de versión secundaria.

>Recursos

El destino de una solicitud HTTP se denomina "recurso". HTTP no limita la naturaleza de un recurso; simplemente define una interfaz que puede utilizarse para interactuar con los mismos. La mayoría de los recursos se identifican mediante un Identificador Uniforme de Recursos (URI).

Un objetivo de diseño de HTTP es separar la identificación de recursos de la semántica de la solicitud, lo que se hace posible al incorporar la semántica de la solicitud al método de solicitud, junto con algunos campos de encabezado que la modifican. Un recurso no puede tratar una solicitud de una manera incoherente con la semántica del método de la solicitud. Por ejemplo, aunque el URI de un recurso puede implicar una semántica que no es segura, un cliente puede esperar que el recurso evite acciones que no son seguras al procesar una solicitud con un método seguro.

>Representaciones

Una "representación" es información que pretende reflejar un estado pasado, actual o deseado de un recurso determinado, en un formato que se pueda comunicar fácilmente a través del protocolo. Una representación consiste en un conjunto de metadatos de representación y un flujo potencialmente ilimitado de datos de representación. 

El protocolo HTTP permite "ocultar información" detrás de su interfaz uniforme al definir la comunicación con respecto a una representación transferible del estado del recurso, en lugar de transferir el recurso en sí. Esto permite que el recurso identificado por una URI sea cualquier cosa, incluidas funciones temporales como "el clima actual en La Plata", al tiempo que proporciona potencialmente información que representa ese recurso en el momento en que se genera un mensaje. 

La interfaz uniforme es similar a una ventana a través de la cual se puede observar y actuar sobre una cosa, únicamente mediante la comunicación de mensajes a un actor independiente del otro lado. Se necesita una abstracción compartida para representar ("tomar el lugar de") el estado actual o deseado de esa cosa en nuestras comunicaciones. Cuando una representación es hipertexto, puede proporcionar tanto una representación del estado del recurso como instrucciones de procesamiento que ayuden a guiar las interacciones futuras del receptor.

Un recurso de destino puede contar con, o ser capaz de generar, múltiples representaciones que están destinadas a reflejar el estado actual del recurso. Se utilizaría un algoritmo, generalmente basado en la negociación de contenido, para seleccionar una de esas representaciones como la más aplicable a una solicitud dada. Esta "representación seleccionada" proporciona los datos y metadatos para evaluar solicitudes condicionales y construir el contenido para las respuestas 200 (OK), 206 (Contenido parcial) y 304 (No modificado) al método GET.

>Conexiones, Clientes y Servidores

HTTP es un protocolo cliente/servidor que opera a través de una "conexión" "confiable" (dependiendo de la versión utilizada, y si dicha versión se basa en una capa segura (como TLS1.3+)) de capa de sesión o transporte. Un "cliente" HTTP es un programa que establece una conexión con un servidor con el fin de enviar una o más solicitudes HTTP. Un "servidor" HTTP es un programa que acepta conexiones para atender dichas solicitudes mediante el envío de respuestas.

Los términos cliente y servidor se refieren únicamente a las funciones que estos programas desempeñan para una conexión y un momento particular. El mismo programa puede actuar como cliente en algunas conexiones, y como servidor en otras.

HTTP se define como un protocolo sin estado, lo que significa que la semántica de cada mensaje de solicitud se puede entender de forma aislada, y que la relación entre las conexiones y los mensajes en ellas no tiene impacto en la interpretación de esos mensajes. Por ejemplo, un método de solicitud CONNECT, o una solicitud con el campo de encabezado UPDATE, puede ocurrir en cualquier momento, no solo en el primer mensaje de la comunicación.

Muchas implementaciones dependen del diseño sin estado de HTTP para reutilizar conexiones proxy o equilibrar dinámicamente la carga de solicitudes en múltiples servidores. Como resultado, un servidor NO DEBE asumir que dos solicitudes en la misma conexión provienen del mismo agente de usuario, a menos que la conexión sea segura y específica de ese agente.

>Mensajes

Los términos "remitente" y "destinatario" se refieren a cualquier implementación que envía o recibe un mensaje determinado, respectivamente. Un cliente envía solicitudes a un servidor en forma de mensaje de "solicitud" con un método y un destino de solicitud. La solicitud también puede contener campos de encabezado para modificadores de solicitud, información del cliente y metadatos de representación, contenido destinado a ser procesado de acuerdo al contexto, y campos finales para comunicar la información recopilada durante el envío.

Un servidor responde a la solicitud de un cliente enviando uno o más mensajes de "respuesta", cada uno de los cuales incluye un código de estado. La respuesta también puede contener campos de encabezado para información del servidor, metadatos de recursos y metadatos de representación, contenido que se interpretará de acuerdo con el código de estado y campos finales para comunicar la información recopilada durante el envío del contenido.

>Agentes de usuario

El término "agente de usuario" se refiere a cualquiera de los diversos programas cliente que inician una solicitud. La forma más familiar de agente de usuario es el navegador web de propósito general, pero eso es sólo un pequeño porcentaje de las implementaciones. Otros agentes de usuario comunes incluyen arañas (robots que atraviesan redes), herramientas de línea de comandos, pantallas publicitarias, electrodomésticos, básculas, bombillas, scripts de actualización de firmware, aplicaciones móviles y dispositivos de comunicación en una multitud de formas y tamaños.

Ser un agente de usuario no implica que haya un usuario humano interactuando directamente con el agente de software en el momento de una solicitud. En muchos casos, se instala o configura un agente de usuario para ejecutarse en segundo plano y guardar sus resultados para una inspección posterior (o guardar solo un subconjunto de los resultados que puedan ser interesantes o erróneos). Las arañas, por ejemplo, normalmente reciben un URI de inicio y se configuran para seguir cierto comportamiento mientras rastrean la Web como un gráfico de hipertexto.

Muchos agentes de usuario no pueden, o eligen no hacerlo, hacer sugerencias interactivas a sus usuarios o proporcionar advertencias adecuadas por motivos de seguridad o privacidad. En los pocos casos en los que esta especificación requiere informar de errores al usuario, es aceptable que dichos informes solo sean observables en una consola de errores o en un archivo de registro. Del mismo modo, los requisitos de que el usuario confirme una acción automatizada antes de continuar pueden cumplirse mediante opciones de configuración avanzadas, opciones de tiempo de ejecución o simplemente evitando la acción insegura. La confirmación no implica ninguna interfaz de usuario específica ni la interrupción del procesamiento normal si el usuario ya ha hecho esa elección.

>Servidor de origen

El término "servidor de origen" se refiere a un programa que puede generar respuestas autorizadas para un recurso de destino determinado. La forma más familiar de un servidor de origen son los grandes sitios web públicos. Sin embargo, al igual que los agentes de usuario se equiparan con los navegadores, es fácil caer en el error de pensar que todos los servidores de origen son iguales.

Los servidores de origen comunes también incluyen unidades de automatización del hogar, componentes de red configurables, máquinas de oficina, robots autónomos, fuentes de noticias, cámaras de tráfico, selectores de anuncios en tiempo real y plataformas de vídeo bajo demanda.

La mayor parte de la comunicación HTTP consiste en una solicitud de recuperación (GET) para una representación de algún recurso identificado por un URI. En el caso más simple, esto podría lograrse mediante una única conexión bidireccional (<===>) entre el agente de usuario (UA) y el servidor de origen (O).

         Solicitud   >
    (UA) ======================================= (O)
                                   <   Respuesta

>Intermediarios

HTTP permite el uso de intermediarios para satisfacer solicitudes a través de una cadena de conexiones. Existen tres formas comunes de "intermediario" HTTP: proxy, puerta de enlace y túnel. En algunos casos, un único intermediario puede llegar a actuar como servidor de origen, como proxy, como puerta de enlace o túnel, cambiando su comportamiento en función de la naturaleza de cada solicitud en un contexto determinado.

         >             >             >             >
    (UA) =========== A =========== B =========== C =========== (O)
               <             <             <             <

La figura anterior muestra tres intermediarios (A, B y C) entre el agente de usuario (UA) y el servidor de origen (O). En este ejemplo, un mensaje de solicitud o respuesta que recorre toda la cadena, pasará por cuatro conexiones independientes.

En el ejemplo, algunas opciones de comunicación HTTP pueden aplicarse solo a la conexión con el vecino más cercano (que no sea un túnel), a los puntos finales de la cadena, o a todas las conexiones a lo largo de la comunicación.

Aunque el diagrama es lineal, cada participante puede estar involucrado en múltiples comunicaciones simultáneas. Por ejemplo, B puede estar recibiendo solicitudes de muchos clientes distintos de A y/o reenviando solicitudes a servidores distintos de C, al mismo tiempo que pueda estar procesando la solicitud de A. Del mismo modo, las solicitudes posteriores pueden enviarse a través de una ruta de conexiones diferentes, a menudo en función de la configuración dinámica para el equilibrio de carga que se presente en ese momento.

Los términos "ascendente" y "descendente" se utilizan para describir los requisitos direccionales en relación con el flujo de mensajes: todos los mensajes fluyen desde el flujo ascendente al descendente. Los términos "entrante" y "saliente" se utilizan para describir los requisitos direccionales en relación con la ruta de la solicitud: entrante significa "hacia el servidor de origen (O)", mientras que saliente significa "hacia el agente de usuario (UA)".

Un "proxy" es un agente de reenvío de mensajes que el cliente elige, generalmente a través de reglas de configuración locales, para recibir solicitudes de algún tipo de URI absoluta e intentar satisfacer esas solicitudes mediante la traducción a través de la interfaz HTTP.

Algunas traducciones son mínimas, como las solicitudes de proxy para URI "http", mientras que otras solicitudes pueden requerir la traducción hacia y desde protocolos de nivel de aplicación completamente diferentes. Los proxies se utilizan a menudo para agrupar las solicitudes HTTP de una organización a través de un intermediario común para fines de servicios de seguridad, servicios de anotación, o almacenamiento en caché compartido.

Algunos proxies están diseñados para aplicar transformaciones a mensajes o contenido seleccionados mientras se reenvían.

Un "gateway" (también conocido como "proxy inverso") es un intermediario que actúa como un servidor de origen para la conexión saliente, pero traduce las solicitudes recibidas y las reenvía a otro servidor o servidores. Los gateways se utilizan a menudo para encapsular servicios de información heredados o no confiables, para mejorar el rendimiento del servidor a través del almacenamiento en caché "acelerador", y para permitir la partición o el equilibrio de carga de los servicios HTTP en varias máquinas.

Todos los requisitos HTTP aplicables a un servidor de origen, también se aplican a la comunicación saliente de una puerta de enlace. Una puerta de enlace se comunica con servidores entrantes utilizando cualquier protocolo que desee, incluidas las extensiones privadas de HTTP que están fuera del alcance de esta especificación. Sin embargo, una puerta de enlace HTTP a HTTP que desee interoperar con servidores HTTP de terceros debe cumplir con los requisitos del agente de usuario en la conexión entrante de la puerta de enlace.

Un "túnel" actúa como un relé ciego entre dos conexiones sin cambiar los mensajes. Una vez activo, un túnel no se considera parte de la comunicación HTTP, aunque el túnel podría haber sido iniciado por una.

Un túnel deja de existir cuando ambos extremos de la conexión retransmitida se cierran. Los túneles se utilizan para extender una conexión virtual a través de un intermediario, como cuando se utiliza Transport Layer Security (TLS 1.3+) para establecer una comunicación confidencial a través de un proxy de firewall compartido.

Las categorías anteriores de intermediarios solo consideran a aquellos que actúan como participantes en la comunicación HTTP. También hay intermediarios que pueden actuar en capas inferiores de la pila de protocolos de red, filtrando o redirigiendo el tráfico sin el conocimiento o permiso de los remitentes de los mensajes. Los intermediarios de red son indistinguibles (a nivel de protocolo) de un atacante en ruta, y a menudo introducen fallas de seguridad o problemas de interoperabilidad debido a la violación por error de la semántica HTTP.

Por ejemplo, un "proxy de interceptación" (también conocido comúnmente como "proxy transparente") se diferencia de un proxy HTTP porque no es elegido por el cliente. En cambio, un proxy de interceptación filtra o redirige los paquetes salientes del puerto TCP 80 (y ocasionalmente el tráfico de otros puertos comunes). Los proxies de interceptación se encuentran comúnmente en puntos de acceso de redes públicas, como un medio para hacer cumplir la suscripción de cuentas antes de permitir el uso de servicios de Internet no locales, y dentro de los firewalls corporativos para hacer cumplir las políticas de uso de la red.

>Cachés

Un "caché" es un almacén local de mensajes de respuesta anteriores y el subsistema que controla el almacenamiento, la recuperación y la eliminación de dichos mensajes. 

Un caché almacena respuestas para reducir el tiempo de latencia y el consumo de ancho de banda de red en futuras solicitudes equivalentes. Cualquier cliente o servidor puede emplear un caché, aunque no se puede utilizar un caché mientras actúa como túnel.

El efecto de un caché es que la cadena de solicitud/respuesta se acorta si uno de los participantes a lo largo de la cadena tiene una respuesta almacenada en caché aplicable a esa solicitud.

A continuación, se ilustra la cadena resultante si B tiene una copia almacenada en caché de una respuesta anterior de (O) (a través de C) para una solicitud que no ha sido almacenada en caché por (UA) o A.

            >             >
       (UA) =========== A =========== B - - - - - - C - - - - - - (O)
                  <             <

Una respuesta es "almacenable en caché" si se permite que una caché almacene una copia del mensaje de respuesta para usarla en la respuesta a solicitudes posteriores. Incluso cuando una respuesta es almacenable en caché, puede haber restricciones adicionales impuestas por el cliente o por el servidor de origen sobre cuándo se puede usar esa respuesta almacenada en caché para una solicitud en particular. 

Existe una amplia variedad de arquitecturas y configuraciones de cachés implementadas en la World Wide Web y dentro de grandes organizaciones. Estas incluyen jerarquías nacionales de cachés proxy para ahorrar ancho de banda y reducir la latencia, redes de distribución de contenido que usan el almacenamiento en caché de puerta de enlace para optimizar la distribución regional y global de sitios populares, sistemas colaborativos que transmiten o multidifunden entradas de caché, archivos de entradas de caché precargadas para usar en entornos fuera de línea o de alta latencia, etc.

>Ejemplo de intercambio de mensajes

El siguiente ejemplo ilustra un intercambio de mensajes HTTP/1.1 típico para una solicitud GET en la URI "http://www.example.com/hello.txt"

Solicitud de cliente:

```
GET /hello.txt HTTP/1.1
User-Agent: curl/7.64.1
Host: www.example.com
Accept-Language: en, mi, ar
```

Respuesta de servidor:

```
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
Vary: Accept-Encoding
Content-Type: text/plain

Hello World! My content includes a trailing CRLF.
```

>Referencias URI

Las referencias URI se utilizan para dirigir solicitudes, indicar redirecciones y definir relaciones.

Las definiciones de "URI-reference", "absolute-URI", "relative-part", "authority", "port", "host", "path-abempty", "segment" y "query" se adoptan de la sintaxis genérica de URI.

Se define una regla de "ruta absoluta" para los elementos de protocolo que pueden contener un componente de ruta no vacío. Se define una regla de "URI parcial" para los elementos de protocolo que pueden contener una URI relativa pero no un componente de fragmento.

```
URI-reference = URI-reference
absolute-URI  = absolute-URI
relative-part = relative-part
authority     = authority
uri-host      = host
port          = port
path-abempty  = path-abempty
segment       = segment
query         = query

absolute-path = 1*( "/" segment )
partial-URI   = relative-part [ "?" query ]
```

Cada elemento de protocolo en HTTP, que permite una referencia URI, indicará en su producción **(*)ABNF** si el elemento permite cualquier forma de referencia (URI-reference), solo una URI en forma absoluta (absolute-URI), solo la ruta y los componentes de consulta opcionales (partial-URI), o alguna combinación de lo anterior. A menos que se indique lo contrario, las referencias URI se analizan en relación con la URI de destino.

Se recomienda que todos los remitentes y destinatarios admitan, como mínimo, URI con longitudes de 8000 octetos en elementos de protocolo. Tener en cuenta que esto implica que algunas estructuras y representaciones on-wire (por ejemplo, la línea de solicitud en HTTP/1.1) serán necesariamente más grandes en algunos casos. 

**(*)ABNF:**

En informática, la "Forma Backus-Naur Aumentada" (ABNF) es un metalenguaje basado en la "Forma Backus–Naur" (BNF) pero que consta de su propia sintaxis y reglas de derivación. El principio motriz de la ABNF es describir un sistema formal de un lenguaje que se utilizará como protocolo de comunicaciones bidireccional.

Una especificación ABNF es un conjunto de reglas de derivación, escritas como:

```
rule = definition; comment CR
LF
```

Donde **rule** es una no-terminal que no distingue entre mayúsculas y minúsculas, **definition** consta de secuencias de símbolos que definen la regla, un **comment** para la documentación, y finaliza con un retorno de carro (CR) y un salto de línea (LF).

Los nombres de **rule** no distinguen entre mayúsculas y minúsculas. Los nombres de las reglas constan de una letra seguida de letras, números y guiones. 

Los corchetes angulares (<, >) no son necesarios alrededor de los nombres de las reglas (como en BNF). Sin embargo, se pueden usar para delimitar un nombre de regla cuando se usan en prosa para discernir un nombre de regla.

Los terminales se especifican mediante uno o más caracteres numéricos.

Los caracteres numéricos se pueden especificar con el signo de porcentaje %, seguido de la base (b = binario, d = decimal y x = hexadecimal), seguido del valor o la concatenación de valores (indicada por .).

Por ejemplo, un retorno de carro (CR) se especifica mediante %d13 en decimal o %x0D en hexadecimal. 

Un retorno de carro seguido de un avance de línea (CRLF, \r\n) se puede especificar con una concatenación decimal como **%d13.10**.

**(*)CONCLUSIÓN:**

\n representa LF, un salto de línea, o el número 10 en decimal, y canónicamente un salto de línea significa simplemente "moverse hacia abajo una fila" en terminales y teletipos.

\r representa CR, un retorno de carro, o el número 13 en decimal. En sistemas Windows, Unix y la mayoría de terminales, un CR mueve el cursor al principio de la línea (aunque este no es el caso de las máquinas de 8 bits, ya que la mayoría de ellas avanzan a la siguiente línea con un CR).

Algunos procesos, como la consola de texto, pueden agregar una CR automáticamente cuando se envía un LF. Sin embargo, dado que la CR simplemente se mueve al comienzo de la línea, no habría problema en enviar la CR dos veces.

Por otro lado, los cuadros de diálogo, cuadros de texto y otros elementos de visualización, requieren tanto CR como LF para iniciar correctamente una nueva línea.

Dado que realmente no hay ninguna desventaja en enviar ambos, y ambos son necesarios en algunas situaciones y contextos, la política más simple es usar los dos.

En el caso del texto literal, se especifica mediante el uso de una cadena entre comillas (""). Estas cadenas no distinguen entre mayúsculas y minúsculas, y el conjunto de caracteres utilizado es (US-)ASCII. Por lo tanto, la cadena "abc" coincidirá con “abc”, “Abc”, “aBc”, “abC”, “ABc”, “AbC”, “aBC” y “ABC”. 

El RFC 7405 agregó una sintaxis para cadenas que distinguen entre mayúsculas y minúsculas: **%s**"aBc", por lo que en este caso, solo coincidirá con "aBc". 

Antes de esa implementación, una cadena que distinguía entre mayúsculas y minúsculas solo se podía especificar enumerando los caracteres individuales: por ejemplo para que coincida con “aBc”, la definición sería %d97.66.99.

Una cadena también se puede especificar explícitamente como no distinguible entre mayúsculas y minúsculas con el prefijo **%i**.

El espacio en blanco se utiliza para separar elementos de una definición; para que el espacio se reconozca como delimitador, debe incluirse explícitamente. La referencia explícita para un solo carácter de espacio en blanco es WSP (espacio en blanco lineal), y LWSP es para cero o más caracteres de espacio en blanco con saltos de línea permitidos. Se necesita al menos un carácter de espacio en blanco para formar un delimitador entre dos campos.

Las definiciones están alineadas a la izquierda. Cuando se requieren varias líneas (para facilitar la lectura), las líneas de continuación se sangran con espacios en blanco.

**; COMENTARIO:**

Un punto y coma (;) inicia un comentario que continúa hasta el final de la línea.

**CONCATENACIÓN:**

```Rule1 Rule2```

Una regla se puede definir enumerando una secuencia de nombres de reglas.

Para hacer coincidir la cadena “aba”, se pueden utilizar las siguientes reglas:

```
fu     = %x61	; a
bar    = %x62	; b
mumble = fu bar fu
```

**CONCATENACIÓN ALTERNATIVA:**

```Rule1 / Rule2```

Una regla puede definirse mediante una lista de reglas alternativas separadas por una barra (/).

Para aceptar la regla fu o la regla bar, se podría construir la siguiente regla:

fubar = fu / bar

Se pueden agregar alternativas adicionales a una regla mediante el uso de =/ entre el nombre de la regla y la definición.

**ALTERNATIVAS INCREMENTALES:**

```Rule1 =/ Rule2```

Se pueden agregar alternativas adicionales a una regla mediante el uso de =/ entre el nombre de la regla y la definición.

La regla

```
ruleset = alt1 / alt2
ruleset =/ alt3
ruleset =/ alt4 / alt5
```

es equivalente a

```
ruleset = alt1 / alt2 / alt3 / alt4 / alt5
```

**RANGO DE VALORES:**

```%c##-##```

Se puede especificar un rango de valores numéricos mediante el uso de un guión (-).

La regla

```
OCTAL = %x30-37
```

es equivalente a

```
OCTAL = "0" / "1" / "2" / "3" / "4" / "5" / "6" / "7"
```

**GRUPO DE SECUENCIA:**

```(Rule1 Rule2)```

Los elementos se pueden colocar entre paréntesis para agrupar reglas en una definición.

Para hacer coincidir "a b d" o "a c d", se podría construir la siguiente regla:

```
group = a (b / c) d
```

Para hacer coincidir “a b” o “c d”, se podrían construir las siguientes reglas:

```
group = a b / c d
group = (a b) / (c d)
```

**REPETICIÓN VARIABLE:**

```n*nRule```

Para indicar la repetición de un elemento, se utiliza la forma ```<a>*<b>element```. El ```<a>``` opcional proporciona el número mínimo de elementos que se incluirán (con el valor predeterminado de 0). El ```<b>``` opcional proporciona el número máximo de elementos que se incluirán (con el valor predeterminado de infinito).

Utilizar ```*element``` para cero o más elementos, ```*1element``` para cero o un elemento, ```1*element``` para uno o más elementos y ```2*3element``` para dos o tres elementos, expresiones regulares ```e*, e?, e+``` y ```e{2,3}```.

**REPETICIÓN ESPECÍFICA:**

```nRule```

Para indicar un número explícito de elementos, se utiliza la forma ```<a>element``` y es equivalente a ```<a>*<a>element```.

Utilizar ```2DIGIT``` para obtener dos dígitos numéricos y ```3DIGIT``` para obtener tres dígitos numéricos.

**REGLAS BÁSICAS ABNF:**

```
REGLA	DEFINICIÓN FORMAL	                        SIGNIFICADO

ALPHA	%x41–%x5A / %x61–%x7A	                        Letras ASCII mayúsculas y minúsculas (A–Z, a–z)
DIGIT	%x30–39	                                        Dígitos decimales (0–9)
HEXDIG	DIGIT / "A" / "B" / "C" / "D" / "E" / "F"	Dígitos hexadecimales (0–9, A–F, a–f)
DQUOTE	%x22	                                        Comilla doble
SP	%x20	                                        Espacio
HTAB	%x09	                                        Tab horizontal
WSP	SP / HTAB	                                Espacio y Tab horizontal
LWSP	*(WSP / CRLF WSP)	                        Espacio en blanco lineal (más allá de la nueva línea)
VCHAR	%x21–%x7E	                                Caracteres visibles (imprimibles)
CHAR	%x01–%x7F	                                Cualquier carácter ASCII, excepto NUL
OCTET	%x00–%xFF	                                8 bits de datos
CTL	%x00–%x1F / %x7F	                        Controles
CR	%x0D	                                        Retorno de carro
LF	%x0A	                                        Avance de línea
CRLF	CR LF	                                        Nueva línea estándar de Internet
BIT	"0" / "1"	                                Dígito binario
```

**SECUENCIA OPCIONAL:**

```[Rule]```

Para indicar un elemento opcional, las siguientes construcciones son equivalentes:

```
[fubar snafu]
*1(fubar snafu)
0*1(fubar snafu)
```

**PRIORIDAD DEL OPERADOR:**

Los siguientes operadores tienen la prioridad dada desde el enlace más apretado hasta el enlace más flojo:

```
Strings, names formation
Comment
Value range
Repetition
Grouping, optional
Concatenation
Alternative
```

**EJEMPLO DE CÓDIGO POSTAL:**

Un ejemplo de dirección postal (de EE. UU.) del Formulario Aumentado Backus-Naur (ABNF) se puede especificar de la siguiente manera:

```
postal-address   = name-part street zip-part

name-part        = *(personal-part SP) last-name [SP suffix] CRLF
name-part        =/ personal-part CRLF

personal-part    = first-name / (initial ".")
first-name       = *ALPHA
initial          = ALPHA
last-name        = *ALPHA
suffix           = ("Jr." / "Sr." / 1*("I" / "V" / "X"))

street           = [apt SP] house-num SP street-name CRLF
apt              = 1*4DIGIT
house-num        = 1*8(DIGIT / ALPHA)
street-name      = 1*VCHAR

zip-part         = town-name "," SP state 1*2SP zip-code CRLF
town-name        = 1*(ALPHA / SP)
state            = 2ALPHA
zip-code         = 5DIGIT ["-" 4DIGIT]
```

>Esquemas de URI HTTP

La presencia de un URI "http" o "https" no implica que siempre haya un servidor HTTP en el origen identificado escuchando conexiones. Cualquiera puede crear un URI, exista o no un servidor, y si ese servidor asigna o no ese identificador a un recurso. La naturaleza delegada de los nombres registrados y las direcciones IP crea un espacio de nombres federado, esté presente o no un servidor HTTP.

El esquema de URI "http" se define por la presente para acuñar identificadores dentro del espacio de nombres jerárquico gobernado por un posible servidor de origen HTTP, que escucha conexiones TCP en un puerto determinado.

```
http-URI = "http" "://" authority path-abempty [ "?" query ]
```

El servidor de origen para un URI "http" se identifica mediante el componente de autoridad, que incluye un identificador de host y un número de puerto opcional. Si el subcomponente del puerto está vacío o no se proporciona, el puerto TCP 80 (el puerto reservado para los servicios WWW NO SEGUROS) es el predeterminado. El origen determina quién tiene derecho a responder con autoridad a las solicitudes dirigidas al recurso identificado.

Un remitente NO DEBE generar un URI "http" con un identificador de host vacío. Un destinatario que procese dicha referencia de URI DEBE rechazarla por considerarla no válida. El componente de ruta jerárquica y el componente de consulta opcional identifican el recurso de destino dentro del espacio de nombres de ese servidor de origen.

>Esquemas de URI HTTPs

El esquema de URI "https" se define por la presente para acuñar identificadores dentro del espacio de nombres jerárquico gobernado por un servidor de origen potencial, que escucha conexiones TCP en un puerto determinado y es capaz de establecer una conexión TLS (TLS 1.3+) que ha sido protegida para la comunicación HTTP. En este contexto, "seguro" significa específicamente que el servidor ha sido autenticado como si actuara en nombre de la autoridad identificada y que toda comunicación HTTP con ese servidor tiene una protección de confidencialidad e integridad que es aceptable tanto para el cliente como para el servidor.

```
https-URI = "https" "://" authority path-abempty [ "?" query ]
```

El servidor de origen de un URI "https" se identifica mediante el componente de autoridad, que incluye un identificador de host y un número de puerto opcional. Si el subcomponente del puerto está vacío o no se proporciona, el puerto TCP 443 (el puerto reservado para HTTP sobre TLS) es el predeterminado. El origen determina quién tiene derecho a responder con autoridad a las solicitudes dirigidas al recurso identificado.

Un remitente NO DEBE generar un URI "https" con un identificador de host vacío. Un destinatario que procese dicha referencia de URI DEBE rechazarla por considerarla no válida. El componente de ruta jerárquica y el componente de consulta opcional identifican el recurso de destino dentro del espacio de nombres de ese servidor de origen.

Un cliente DEBE asegurarse de que sus solicitudes HTTP para un recurso "https" estén seguras, antes de ser comunicadas, y que solo acepte respuestas seguras a esas solicitudes. Tener en cuenta que la definición de qué mecanismos criptográficos son aceptables para el cliente y el servidor suele negociarse y puede cambiar con el tiempo.

Los recursos disponibles a través del esquema "https" no tienen identidad compartida con el esquema "http". Son orígenes distintos con espacios de nombres separados y de capas diferentes. Sin embargo, las extensiones de HTTP que se definen como aplicables a todos los orígenes con el mismo host, como el protocolo de cookies, permiten que la información establecida por un servicio afecte la comunicación con otros servicios dentro de un grupo coincidente de dominios de host. Estas extensiones deben diseñarse con mucho cuidado para evitar que la información obtenida de una conexión segura (HTTPs) se intercambie inadvertidamente dentro de un contexto no seguro (HTTP).

>HTTPS, Normalización y comparación

Los URI con un esquema "http" o "https" se normalizan y comparan de acuerdo con los métodos definidos, utilizando los valores predeterminados descritos anteriormente para cada esquema.

HTTP no requiere el uso de un método específico para determinar la equivalencia. Por ejemplo, una clave de caché podría compararse como una cadena simple, después de una normalización basada en sintaxis o después de una normalización basada en esquemas.

La normalización basada en esquemas de los URI "http" y "https" implican las siguientes reglas adicionales:

* Si el puerto es igual al puerto predeterminado de un esquema, la forma normal es omitir el subcomponente del puerto.
* Cuando no se utiliza como destino de una solicitud de OPTIONS, un componente de ruta vacío equivale a una ruta absoluta de "/", por lo que la forma normal es proporcionar una ruta de "/" en su lugar.
* El esquema y el host no distinguen entre mayúsculas y minúsculas y normalmente se proporcionan en minúsculas; todos los demás componentes se comparan distinguiendo entre mayúsculas y minúsculas.
* Los caracteres distintos de los del conjunto "reservado" son equivalentes a sus octetos codificados en porcentaje: la forma normal es no codificarlos.

Por ejemplo, los tres URI siguientes son equivalentes:

```
http://example.com:80/~smith/home.html
http://EXAMPLE.com/%7Esmith/home.html
http://EXAMPLE.com:/%7esmith/home.html
```

Se puede suponer que dos URI HTTP que son equivalentes después de la normalización (usando cualquier método) identifican el mismo recurso, y cualquier componente HTTP PUEDE realizar la normalización. Como resultado, los recursos distintos NO DEBEN identificarse mediante URI HTTP que sean equivalentes después de la normalización.

>Desuso de información de usuario en URI HTTPS

La sintaxis genérica de URI para autoridad también incluye un subcomponente de información de usuario para incluir información de autenticación de usuario en el URI. En ese subcomponente, el uso del formato "usuario:contraseña" está obsoleto.

Algunas implementaciones utilizan el componente userinfo para la configuración interna de la información de autenticación, como dentro de las opciones de invocación de comandos, archivos de configuración o listas de marcadores, aunque dicho uso pueda exponer un identificador de usuario o una contraseña.

Un remitente NO DEBE generar el subcomponente información de usuario (y su delimitador "@") cuando se genera una referencia de URI "http" o "https" dentro de un mensaje como URI de destino o valor de campo.

Antes de hacer uso de una referencia URI "http" o "https" recibida de una fuente que no es de confianza, el destinatario DEBE analizar la información del usuario y tratar su presencia como un error. Es probable que esta técnica sea utilizada para ocultar la autoridad con el fin de realizar ataques de phishing.

>Referencias HTTPS con identificadores de fragmentos

Los identificadores de fragmentos permiten la identificación indirecta de un recurso secundario, independiente del esquema URI. Algunos elementos del protocolo que hacen referencia a un URI permiten la inclusión de un fragmento, mientras que otros no. Se distinguen por el uso de la regla ABNF para elementos donde se permite la fragmentación; de lo contrario, se utiliza una regla específica que excluye fragmentos.

Nota: El componente identificador de fragmento no forma parte de la definición de esquema para un esquema URI, por lo tanto no aparece en las definiciones ABNF para los esquemas URI "http" y "https" anteriores.

>Acceso autorizado

El acceso autorizado se refiere a eliminar la referencia a un identificador determinado, con el fin de acceder al recurso identificado, de una manera que el cliente considere autorizada (controlada por el propietario del recurso). El proceso para determinar si se concede el acceso está definido por el esquema de URI y, a menudo, utiliza datos dentro de los componentes de URI, como el componente de autoridad cuando se utiliza la sintaxis genérica. Sin embargo, el acceso autorizado no se limita al mecanismo identificado.

>Origen de la URI

El "origen" de un URI determinado es el triple de esquema, host y puerto después de normalizar el esquema y el host a minúsculas y normalizar el puerto para eliminar los ceros iniciales. Si se omite el puerto del URI, se utiliza el puerto predeterminado para ese esquema. Por ejemplo, la URI

```
https://Example.Com/happy.js
```

tendría el origen

```
{ "https", "example.com", "443" }
```

que también puede describirse como el prefijo URI normalizado con el puerto siempre presente:

```
https://example.com:443
```

Cada origen define su propio espacio de nombres y controla cómo se asignan los identificadores dentro de ese espacio de nombres a los recursos. A su vez, la forma en que el origen responde a solicitudes válidas, de manera consistente a lo largo del tiempo, determina la semántica que los usuarios asociarán con un URI, y la utilidad de esa semántica es lo que en última instancia transforma estos mecanismos en un recurso al que los usuarios pueden hacer referencia y acceder en el futuro.

Dos orígenes son distintos si difieren en esquema, host o puerto. Incluso cuando se puede verificar que la misma entidad controla dos orígenes distintos, los dos espacios de nombres bajo esos orígenes son distintos a menos que un servidor autorizado para ese origen tenga un alias explícito.

>Orígenes HTTP

Aunque HTTP es independiente del protocolo de transporte, el esquema "http" es específico para asociar autoridad con quien controla el servidor de origen que escucha las conexiones TCP en el puerto indicado de cualquier host identificado dentro del componente de autoridad. Este es un sentido de autoridad muy débil porque depende tanto de los mecanismos de resolución de nombres específicos del cliente como de la comunicación que podría no estar protegida de un atacante en la ruta. Sin embargo, es un mínimo suficiente para vincular identificadores "http" a un servidor de origen para una resolución consistente dentro de un entorno confiable.

Si el identificador de host se proporciona como una dirección IP, el servidor de origen es el que escucha (si lo hay) en el puerto TCP indicado en esa dirección IP. Si el host es un nombre registrado, el nombre registrado es un identificador indirecto que se utiliza con un servicio de resolución de nombres, como DNS, para encontrar una dirección para un servidor de origen apropiado.

Cuando se utiliza un URI "http" dentro de un contexto que solicita acceso al recurso indicado, un cliente PUEDE intentar acceder resolviendo el identificador de host en una dirección IP, estableciendo una conexión TCP a esa dirección en el puerto indicado y enviando a través de esa conexión un mensaje de solicitud HTTP que contiene un destino de solicitud que coincide con el URI de destino del cliente.

Si el servidor responde a dicha solicitud con un mensaje de respuesta HTTP no provisional, entonces esa respuesta se considera una respuesta autorizada a la solicitud del cliente.

Sin embargo, tenga en cuenta que lo anterior no es el único medio para obtener una respuesta autorizada, ni implica que siempre sea necesaria una respuesta autorizada. Por ejemplo, el campo de encabezado ALTSVC permite que un servidor de origen identifique otros servicios que también tienen autoridad para ese origen. El acceso a recursos identificados "http" también puede proporcionarse mediante protocolos fuera del alcance de este documento.

>Orígenes HTTPS



