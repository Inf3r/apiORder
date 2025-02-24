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

HTTP es un protocolo cliente/servidor que opera a través de una "conexión" "confiable" (dependiendo de la versión utilizada, y si dicha versión se basa en una capa segura (como TLS1.3+)) de capa de sesión (número 5 del modelo OSI) o transporte (número 4 del modelo OSI). Un "cliente" HTTP es un programa que establece una conexión con un servidor con el fin de enviar una o más solicitudes HTTP. Un "servidor" HTTP es un programa que acepta conexiones para atender dichas solicitudes mediante el envío de respuestas.

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

El siguiente ejemplo ilustra un intercambio de mensajes HTTP/1.1 típico para una solicitud GET en la URI

```
http://www.example.com/hello.txt
```

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

Donde **rule** es una "no terminal" (un símbolo "si terminal" es un literal como por ejemplo: "+", o "function", o una clase como integer) que no distingue entre mayúsculas y minúsculas, **definition** consta de secuencias de símbolos que definen la regla, un **comment** para la documentación, y finaliza con un retorno de carro (CR) y un salto de línea (LF).

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

Sin embargo, hay que tener en cuenta que lo anterior no es el único medio para obtener una respuesta autorizada, ni implica que siempre sea necesaria una respuesta autorizada. Por ejemplo, el campo de encabezado ALTSVC permite que un servidor de origen identifique otros servicios que también tienen autoridad para ese origen. El acceso a recursos identificados "http" también puede proporcionarse mediante protocolos fuera del alcance de este documento.

>Orígenes HTTPS

El esquema "https" asocia la autoridad en función de la capacidad de un servidor para utilizar la clave privada correspondiente a un certificado que el cliente considera confiable para el servidor de origen identificado. El cliente generalmente se basa en una cadena de confianza, transmitida desde algún ancla de confianza preestablecida o configurada, para considerar que un certificado es confiable.

En HTTP/1.1 y versiones anteriores, un cliente solo atribuirá autoridad a un servidor cuando se comunique a través de una conexión segura y establecida con éxito específicamente con el host de ese URI de origen.

El establecimiento de la conexión y la verificación del certificado se utilizan como prueba de autoridad.

En HTTP/2 y HTTP/3, un cliente atribuirá autoridad a un servidor cuando se comuniquen a través de una conexión segura y establecida con éxito si el host del origen de la URI coincide con cualquiera de los hosts presentes en el certificado del servidor y el cliente cree que podría abrir una conexión a ese host para esa URI. 

En la práctica, un cliente realizará una consulta DNS para verificar que el host del origen contenga la misma dirección IP del servidor que la conexión establecida.

Esta restricción puede eliminarse si el servidor de origen envía un marco ORIGIN equivalente.

El host y el valor del puerto del destino de la solicitud se pasan dentro de cada solicitud HTTP, identificando el origen y distinguiéndolo de otros espacios de nombres que podrían estar controlados por el mismo servidor. 

Es responsabilidad del origen asegurar que todos los servicios provistos con control sobre la clave privada de su certificado sean igualmente responsables de administrar los espacios de nombres "https" correspondientes o al menos estén preparados para rechazar solicitudes que parezcan haber sido mal dirigidas.

Un servidor de origen podría no estar dispuesto a procesar solicitudes para ciertas URI de destino incluso cuando tiene la autoridad para hacerlo. Por ejemplo, cuando un host opera distintos servicios en diferentes puertos (por ejemplo, 443 y 8000), es necesario verificar la URI de destino en el servidor de origen (incluso después de que se haya asegurado la conexión) porque un atacante de red podría hacer que las conexiones para un puerto se reciban en algún otro puerto.

No verificar la URI de destino podría permitir que un atacante de este tipo reemplace una respuesta a una URI de destino, por ejemplo

```
https://example.com/foo
```

con una respuesta aparentemente autoritaria del otro puerto, por ejemplo

```
https://example.com:8000/foo
```

Tener en cuenta que el esquema "https" no se basa en TCP y el número de puerto conectado para asociar la autoridad, ya que ambos están fuera de la comunicación segura y, por lo tanto, no se puede confiar en ellos como definitivos.  Por lo tanto, la comunicación HTTP puede tener lugar a través de cualquier canal que haya sido protegido, incluidos los protocolos que no utilizan TCP.

Cuando se utiliza una URI "https" dentro de un contexto que solicita acceso al recurso indicado, un cliente PUEDE intentar acceder resolviendo el identificador de host a una dirección IP, estableciendo una conexión TCP a esa dirección en el puerto indicado, asegurando la conexión de extremo a extremo iniciando exitosamente TLS sobre TCP con protección de confidencialidad e integridad, y enviando a través de esa conexión un mensaje de solicitud HTTP que contenga un destino de solicitud que coincida con la URI de destino del cliente.

Si el servidor responde a dicha solicitud con un mensaje de respuesta HTTP no provisional, entonces esa respuesta se considera una respuesta autorizada a la solicitud del cliente. Sin embargo, tener en cuenta que lo anterior no es el único medio para obtener una respuesta autorizada, ni implica que una respuesta autorizada siempre sea necesaria.

>Verificación de certificado HTTPS

Para establecer una conexión segura para desreferenciar un URI, un cliente DEBE verificar que la identidad del servicio coincida de manera aceptable con el servidor de origen del URI. La verificación de certificados se utiliza para evitar la suplantación de identidad del servidor por parte de un atacante en la ruta o de un atacante que controle la resolución de nombres. Este proceso requiere que un cliente esté configurado con un conjunto de anclajes de confianza.

En general, un cliente DEBE verificar la identidad del servicio mediante el proceso de verificación. El cliente DEBE construir una identidad de referencia a partir del host del servicio: si el host es una dirección IP literal, la identidad de referencia es un ```IP-ID```; de lo contrario, el host es un nombre y la identidad de referencia es un ```DNS-ID```.

Los clientes NO DEBEN utilizar una identidad de referencia de tipo CN-ID, los clientes más antiguos pueden utilizar una identidad de referencia de tipo CN-ID.

Un cliente puede estar configurado especialmente para aceptar una forma alternativa de verificación de identidad del servidor. Por ejemplo, un cliente puede conectarse a un servidor cuya dirección y nombre de host son dinámicos, con la expectativa de que el servicio presente un certificado específico (o un certificado que coincida con alguna identidad de referencia definida externamente) en lugar de uno que coincida con el origen del URI de destino.

En casos especiales, puede ser apropiado que un cliente simplemente ignore la identidad del servidor, pero debe entenderse que esto deja una conexión abierta a un ataque activo.

Si el certificado no es válido para el origen de la URI de destino, un agente de usuario DEBE obtener confirmación del usuario antes de continuar, o finalizar la conexión con un error de certificado incorrecto. Los clientes automatizados DEBEN registrar el error en un registro de auditoría adecuado (si está disponible) y DEBEN finalizar la conexión (con un error de certificado incorrecto). Los clientes automatizados PUEDEN proporcionar una configuración que deshabilite esta verificación, pero DEBEN proporcionar una configuración que la habilite.

>Identidad de referencia de IP-ID

Un servidor que se identifica mediante una dirección IP literal en el campo "host" de un URI "https" tiene una identidad de referencia de tipo IP-ID. Una dirección IP versión 4 utiliza la regla ABNF "IPv4address" y una dirección IP versión 6 utiliza la producción "IP-literal" con la opción "IPv6address". Una identidad de referencia de tipo IP-ID contiene los bytes decodificados de la dirección IP.

Una dirección IP versión 4 tiene 4 octetos y una dirección IP versión 6 tiene 16 octetos. El uso de IP-ID no está definido para ninguna otra versión IP. La opción iPAddress en la extensión subjectAltName del certificado no incluye explícitamente la versión IP y, por lo tanto, depende de la longitud de la dirección para distinguir las versiones.

Una identidad de referencia de tipo ```IP-ID coincide si la dirección es idéntica a un valor iPAddress de la extensión subjectAltName del certificado```.

>Campos

HTTP utiliza "campos" para proporcionar datos en forma de pares nombre/valor extensibles con un espacio de nombres de clave registrado. Los campos se envían y reciben dentro de las secciones de encabezado y final de los mensajes.

>Nombres de campos

Un nombre de campo etiqueta el valor de campo correspondiente como si tuviera la semántica definida por ese nombre. Por ejemplo, el campo de encabezado de fecha que contiene la marca de tiempo de origen del mensaje en el que aparece.

```nombre-campo = token```
  
Los nombres de campo no distinguen entre mayúsculas y minúsculas y deben registrarse en el "Registro de nombres de campo del Protocolo de transferencia de hipertexto (HTTP)".

La interpretación de un campo no cambia entre versiones menores de la misma versión principal de HTTP, aunque el comportamiento predeterminado de un destinatario en ausencia de dicho campo puede cambiar. A menos que se especifique lo contrario, los campos se definen para todas las versiones de HTTP. En particular, los campos Host y Connection deben ser reconocidos por todas las implementaciones de HTTP, independientemente de si anuncian o no su conformidad con HTTP/1.1.

Se pueden introducir nuevos campos sin cambiar la versión del protocolo si su semántica definida permite que los destinatarios que no los reconocen puedan ignorarlos de forma segura.

Un proxy DEBE reenviar campos de encabezado no reconocidos a menos que el nombre del campo esté incluido en el campo de encabezado de conexión, o que el proxy esté configurado específicamente para bloquear o transformar de otro modo dichos campos. Otros destinatarios DEBEN ignorar los campos de encabezado y de final no reconocidos. El cumplimiento de estos requisitos permite ampliar la funcionalidad de HTTP sin actualizar ni eliminar intermediarios implementados.

>Líneas de campo y valor de campo combinado

Las secciones de campo se componen de cualquier número de "líneas de campo", cada una con un "nombre de campo" que lo identifica, y un "valor de línea de campo" que transmite datos para esa instancia implementada.

Cuando un nombre de campo solo está presente una vez en una sección, el "valor de campo" combinado para ese campo consiste en el valor de la línea de campo correspondiente. Cuando un nombre de campo se repite dentro de una sección, su valor de campo combinado consiste en la lista de valores de línea de campo correspondientes dentro de esa sección, concatenados en orden, con cada valor de línea de campo separado por una coma.

Por ejemplo, esta sección:

```
Ejemplo de campo: Foo, Bar
Ejemplo de campo: Baz
```

contiene dos líneas de campo, ambas con el nombre de campo "Ejemplo de campo". La primera línea de campo tiene un valor de línea de campo de "Foo, Bar", mientras que el valor de la segunda línea de campo es "Baz". El valor del campo final para "Ejemplo de campo" es la lista "Foo, Bar, Baz".

>Orden de campo

Un destinatario PUEDE combinar múltiples líneas de campo dentro de una sección de campo que tienen el mismo nombre de campo en una línea de campo, sin cambiar la semántica del mensaje, agregando cada valor de línea de campo posterior al valor de línea de campo inicial en orden, separados por una coma (",") y espacios en blanco opcionales (OWS). Para mantener la coherencia, utilzar la coma SP.

Por lo tanto, el orden en que se reciben las líneas de campo con el mismo nombre es importante para la interpretación del valor del campo; un proxy NO DEBE cambiar el orden de estos valores de línea de campo al reenviar un mensaje.

Esto significa que, aparte de la conocida excepción que se indica a continuación, un remitente NO DEBE generar múltiples líneas de campo con el mismo nombre en un mensaje (ya sea en los encabezados o en los finales) ni agregar una línea de campo cuando ya existe una línea de campo con el mismo nombre en el mensaje, a menos que la definición de ese campo permita que se recombinen múltiples valores de líneas de campo como una lista separada por comas (es decir, al menos una alternativa de la definición del campo permite una lista separada por comas lista, como una regla ABNF de #(valores).

Nota: En la práctica, el campo de encabezado "Set-Cookie" suele aparecer en un mensaje de respuesta en varias líneas de campo y no utiliza la sintaxis de lista, lo que viola los requisitos anteriores en varias líneas de campo con el mismo nombre de campo. Dado que no se puede combinar en un único valor de campo, los destinatarios deben manejar "Set-Cookie" como un caso especial al procesar los campos.

El orden en el que se reciben en una sección las líneas de campo con diferentes nombres de campo no es significativo. Sin embargo, es una buena práctica enviar primero los campos de encabezado que contienen datos de control adicionales, como Host en solicitudes y Fecha en respuestas, para que las implementaciones puedan decidir cuándo no manejar un mensaje lo antes posible.

Un servidor NO DEBE aplicar una solicitud al recurso de destino hasta que reciba la sección completa del encabezado de la solicitud, ya que las líneas posteriores del campo del encabezado pueden incluir condicionales, credenciales de autenticación o campos de encabezado duplicados deliberadamente engañosos que podrían afectar el procesamiento de la solicitud.

>Límites de campo

HTTP no impone un límite predefinido a la longitud de cada línea de campo, valor de campo o a la longitud de un encabezado o sección final en su conjunto. En la práctica se encuentran varias limitaciones ad hoc en longitudes individuales, a menudo dependiendo de la semántica del campo específico.

Un servidor que recibe una línea de campo de encabezado de solicitud, un valor de campo o un conjunto de campos más grande de lo que desea procesar DEBE responder con un código de estado 4xx (Error de cliente) apropiado. Ignorar dichos campos de encabezado aumentaría la vulnerabilidad del servidor para solicitar ataques de contrabando.

Un cliente PUEDE descartar o truncar líneas de campo recibidas que sean más grandes de lo que el cliente desea procesar si la semántica del campo es tal que los valores eliminados se pueden ignorar de manera segura sin cambiar el marco del mensaje o la semántica de la respuesta.

>Valores de campo

Los valores de los campos HTTP constan de una secuencia de caracteres en un formato definido por la gramática del campo. La gramática de cada campo generalmente se define usando ABNF.

```
field-value    = *field-content
field-content  = field-vchar
                 [ 1*( SP / HTAB / field-vchar ) field-vchar ]
field-vchar    = VCHAR / obs-text
obs-text       = %x80-FF
```

Un valor de campo no incluye espacios en blanco iniciales o finales. Cuando una versión específica de HTTP permite que dichos espacios en blanco aparezcan en un mensaje, una implementación de análisis de campos DEBE excluir dichos espacios en blanco antes de evaluar el valor del campo.

Los valores de campo generalmente están restringidos al rango de caracteres US-ASCII. Los campos que necesiten una mayor variedad de caracteres pueden utilizar una codificación. Históricamente, HTTP permitía contenido de campo con texto en el juego de caracteres ISO-8859-1, y admitía otros juegos de caracteres solo mediante el uso de la codificación. Las especificaciones para campos recién definidos DEBEN limitar sus valores a octetos US-ASCII visibles (VCHAR), SP y HTAB. Un destinatario DEBE tratar otros octetos permitidos en el contenido del campo (es decir, texto obs) como datos opacos.

Los valores de campo que contienen caracteres CR, LF o NUL no son válidos y son peligrosos debido a las distintas formas en que las implementaciones pueden analizar e interpretar esos caracteres; un destinatario de CR, LF o NUL dentro de un valor de campo DEBE rechazar el mensaje o reemplazar cada uno de esos caracteres con SP antes de continuar procesando o reenviando ese mensaje. Los valores de campo que contienen otros caracteres CTL tampoco son válidos; sin embargo, los destinatarios PUEDEN conservar dichos caracteres en aras de la solidez cuando aparecen dentro de un contexto seguro (por ejemplo, una cadena entrecomillada específica de la aplicación que no será procesada por ningún analizador HTTP posterior).

Los campos que solo anticipan un único miembro como valor de campo se denominan "campos únicos".

Los campos que permiten varios miembros como valor de campo se denominan "campos basados ​​en listas". La extensión del operador de lista se utiliza como notación común para definir valores de campo que pueden contener varios miembros.

Debido a que las comas (",") se utilizan como delimitador entre miembros, deben tratarse con cuidado si se permiten como datos dentro de un miembro. Esto es válido tanto para los campos basados ​​en listas como para los campos singleton, ya que un campo singleton puede enviarse por error con varios miembros y la detección de dichos errores mejora la interoperabilidad. Los campos que esperan contener una coma dentro de un miembro, como dentro de un elemento de fecha HTTP o de referencia URI, deben definirse con delimitadores alrededor de ese elemento para distinguir cualquier coma dentro de esos datos de posibles separadores de lista.

Por ejemplo, una fecha textual y un URI (cualquiera de los cuales podría contener una coma) podrían incluirse de forma segura en valores de campo basados ​​en listas como estos:

```
Example-URIs: "http://example.com/a.html,foo",
              "http://without-a-comma.example.com/"
Example-Dates: "Sat, 04 May 1996", "Wed, 14 Sep 2005"
```

Tenga en cuenta que los delimitadores de comillas dobles casi siempre se utilizan con la producción de cadenas entre comillas; el uso de una sintaxis diferente entre comillas dobles probablemente provocará una confusión innecesaria.

Muchos campos (como el tipo de contenido) utilizan una sintaxis común para los parámetros que permite la sintaxis tanto sin comillas (token) como entre comillas (cadena entre comillas) para un valor de parámetro. El uso de una sintaxis común permite a los destinatarios reutilizar los componentes del analizador existentes. Al permitir ambas formas, el significado del valor de un parámetro debe ser el mismo ya sea que se reciba como un token o como una cadena entre comillas.

Nota: Para definir la sintaxis del valor del campo, esta especificación utiliza una regla ABNF nombrada después del nombre del campo para definir la gramática permitida para el valor de ese campo (después de que dicho valor se haya extraído de la sintaxis de mensajería subyacente y se hayan combinado varias instancias en una lista).

>Reglas comunes para definir valores de campo

>Listas (extensión #rule ABNF)

Se utiliza una extensión #rule de las reglas ABNF para mejorar la legibilidad en las definiciones de algunos valores de campo basados ​​en listas.

Se define una construcción "#", similar a "*", para definir listas de elementos delimitadas por comas. La forma completa es "<n>#<m>elemento" que indica al menos ```<n>``` y como máximo ```<m>``` elementos, cada uno separado por una sola coma (",") y un espacio en blanco opcional (OWS).

>Requisitos del remitente

En cualquier producción que utilice la construcción de lista, un remitente NO DEBE generar elementos de lista vacíos. En otras palabras, un remitente debe generar listas que satisfagan la siguiente sintaxis:

```
 1#element => element *( OWS "," OWS element )
```
y:
```
#element => [ 1#element ]
```
y por ```n >= 1``` y ```m > 1```:
```
<n>#<m>element => element <n-1>*<m-1>( OWS "," OWS element )
```

>Requisitos del receptor

Los elementos vacíos no contribuyen al recuento de elementos presentes. Un destinatario DEBE analizar e ignorar una cantidad razonable de elementos de lista vacía: suficiente para manejar errores comunes de los remitentes al fusionar valores, pero no tanto como para poder usarlos como un mecanismo de denegación de servicio. En otras palabras, un destinatario DEBE aceptar listas que cumplan con la siguiente sintaxis:

```
#element => [ element ] *( OWS "," OWS [ element ] )
```

Tenga en cuenta que debido a la posible presencia de elementos de lista vacíos, el RFC 5234 ABNF no puede imponer la cardinalidad de los elementos de la lista y, en consecuencia, todos los casos se asignan como si no se hubiera especificado ninguna cardinalidad.

Por ejemplo, dadas estas producciones de ABNF:

```
example-list      = 1#example-list-elmt
example-list-elmt = token
```

Entonces los siguientes son valores válidos para la lista de ejemplos (sin incluir las comillas dobles, que están presentes solo para la delimitación):

```
"foo,bar"
"foo ,bar,"
"foo , ,bar,charlie"
```

Por el contrario, los siguientes valores no serían válidos, ya que la producción de la lista de ejemplo requiere al menos un elemento no vacío:

```
""
","
",   ,"
```

>Tokens

Los tokens son identificadores textuales cortos que no incluyen espacios en blanco ni delimitadores.

```
token          = 1*tchar

tchar          = "!" / "#" / "$" / "%" / "&" / "'" / "*"
               / "+" / "-" / "." / "^" / "_" / "`" / "|" / "~"
               / DIGIT / ALPHA
               ; any VCHAR, except delimiters
```

Muchos valores de campos HTTP se definen utilizando componentes de sintaxis comunes, separados por espacios en blanco o caracteres delimitadores específicos. Los delimitadores se eligen del conjunto de caracteres visuales US-ASCII no permitidos en un token (DQUOTE y "(),/:;<=>?@[\]{}").

>Espacio en blanco

Esta especificación utiliza tres reglas para indicar el uso de espacios en blanco lineales: OWS (espacio en blanco opcional), RWS (espacio en blanco requerido) y BWS (espacio en blanco "incorrecto").

La regla OWS se utiliza cuando pueden aparecer cero o más octetos de espacios en blanco lineales. Para elementos de protocolo donde se prefiere el espacio en blanco opcional para mejorar la legibilidad, un remitente DEBE generar el espacio en blanco opcional como un único SP; de lo contrario, un remitente NO DEBE generar espacios en blanco opcionales, excepto cuando sea necesario para sobrescribir elementos de protocolo no válidos o no deseados durante el filtrado de mensajes local.

La regla RWS se utiliza cuando se requiere al menos un octeto de espacio en blanco lineal para separar los tokens de campo. Un remitente DEBE generar RWS como un único SP.

OWS y RWS tienen la misma semántica que un único SP. Cualquier contenido que se sepa que está definido como OWS o RWS PUEDE reemplazarse con un único SP antes de interpretarlo o reenviar el mensaje.

La regla BWS se utiliza cuando la gramática permite espacios en blanco opcionales sólo por razones históricas. Un remitente NO DEBE generar BWS en los mensajes. Un destinatario DEBE analizar esos espacios en blanco incorrectos y eliminarlos antes de interpretar el elemento del protocolo.

BWS no tiene semántica. Cualquier contenido que se sepa que está definido como BWS PUEDE eliminarse antes de interpretarlo o reenviar el mensaje.

```
OWS            = *( SP / HTAB )
               ; optional whitespace
RWS            = 1*( SP / HTAB )
               ; required whitespace
BWS            = OWS
               ; "bad" whitespace
```

>Cadenas citadas

Una cadena de texto se analiza como un valor único si se cita entre comillas dobles.

```
quoted-string  = DQUOTE *( qdtext / quoted-pair ) DQUOTE
qdtext         = HTAB / SP / %x21 / %x23-5B / %x5D-7E / obs-text
```

El octeto de barra invertida ```\``` se puede utilizar como mecanismo de citación de un solo octeto dentro de construcciones de cadena entre comillas y comentarios. Los destinatarios que procesan el valor de una cadena entre comillas DEBEN manejar un par entre comillas como si fuera reemplazado por el octeto que sigue a la barra invertida.

```
quoted-pair    = "\" ( HTAB / SP / VCHAR / obs-text )
```

Un remitente NO DEBE generar un par entre comillas en una cadena entre comillas, excepto cuando sea necesario citar DQUOTE y octetos de barra invertida que aparecen dentro de esa cadena. Un remitente NO DEBE generar un par entre comillas en un comentario, excepto cuando sea necesario citar paréntesis ```["("``` y ```")"]``` y octetos de barra invertida que aparecen dentro de ese comentario.

>Comentarios

Los comentarios se pueden incluir en algunos campos HTTP rodeando el texto del comentario entre paréntesis. Los comentarios solo se permiten en campos que contienen "comentario" como parte de la definición del valor del campo.

```
comment        = "(" *( ctext / quoted-pair / comment ) ")"
ctext          = HTAB / SP / %x21-27 / %x2A-5B / %x5D-7E / obs-text
```

>Parámetros

Los parámetros son instancias de pares nombre/valor; a menudo se utilizan en valores de campo como sintaxis común para agregar información auxiliar a un elemento. Cada parámetro suele estar delimitado por un punto y coma inmediatamente anterior.

```
parameters      = *( OWS ";" OWS [ parameter ] )
parameter       = parameter-name "=" parameter-value
parameter-name  = token
parameter-value = ( token / quoted-string )
```

Los nombres de los parámetros no distinguen entre mayúsculas y minúsculas. Los valores de los parámetros pueden distinguir entre mayúsculas y minúsculas o no, según la semántica del nombre del parámetro.

Un valor de parámetro que coincida con la producción del token se puede transmitir como un token o dentro de una cadena entrecomillada. Los valores entrecomillados y no entrecomillados son equivalentes.

Nota: Los parámetros no permiten espacios en blanco (ni siquiera espacios en blanco "incorrectos") alrededor del carácter "=".

>Formatos de fecha/hora

Antes de 1995, los servidores utilizaban tres formatos diferentes para comunicar marcas de tiempo. Por compatibilidad con implementaciones antiguas, las tres se definen acá. El formato preferido es un subconjunto de longitud fija y de zona única de la especificación de fecha y hora utilizada por el formato de mensajes de Internet.

```
HTTP-date    = IMF-fixdate / obs-date
```

Un ejemplo del formato preferido es:

```
Sun, 06 Nov 1994 08:49:37 GMT    ; IMF-fixdate
```

Ejemplos de los dos formatos obsoletos son:

```
Sunday, 06-Nov-94 08:49:37 GMT   ; obsolete RFC 850 format
Sun Nov  6 08:49:37 1994         ; ANSI C's asctime() format
```

Un destinatario que analiza un valor de marca de tiempo en un campo HTTP DEBE aceptar los tres formatos de fecha HTTP. Cuando un remitente genera un campo que contiene una o más marcas de tiempo definidas como fecha HTTP, el remitente DEBE generar esas marcas de tiempo en el formato de fecha fija del FMI.

Un valor de fecha HTTP representa la hora como una instancia de hora universal coordinada (UTC). Los dos primeros formatos indican UTC mediante la abreviatura de tres letras de la hora media de Greenwich, "GMT", un predecesor del nombre UTC; Se supone que los valores en formato asctime están en UTC.

Un "reloj" es una implementación capaz de proporcionar una aproximación razonable del instante actual en UTC. Una implementación de reloj debería utilizar NTP, o algún protocolo similar, para sincronizarse con UTC.

Formato preferido:

```
  IMF-fixdate  = day-name "," SP date1 SP time-of-day SP GMT
  ; fixed length/zone/capitalization subset of the format
  ; see Section 3.3 of [RFC5322]

  day-name     = %s"Mon" / %s"Tue" / %s"Wed"
               / %s"Thu" / %s"Fri" / %s"Sat" / %s"Sun"

  date1        = day SP month SP year
               ; e.g., 02 Jun 1982

  day          = 2DIGIT
  month        = %s"Jan" / %s"Feb" / %s"Mar" / %s"Apr"
               / %s"May" / %s"Jun" / %s"Jul" / %s"Aug"
               / %s"Sep" / %s"Oct" / %s"Nov" / %s"Dec"
  year         = 4DIGIT

  GMT          = %s"GMT"

  time-of-day  = hour ":" minute ":" second
               ; 00:00:00 - 23:59:60 (leap second)

  hour         = 2DIGIT
  minute       = 2DIGIT
  second       = 2DIGIT
```

Formatos obsoletos:

```
  obs-date     = rfc850-date / asctime-date
  rfc850-date  = day-name-l "," SP date2 SP time-of-day SP GMT
  date2        = day "-" month "-" 2DIGIT
               ; e.g., 02-Jun-82

  day-name-l   = %s"Monday" / %s"Tuesday" / %s"Wednesday"
               / %s"Thursday" / %s"Friday" / %s"Saturday"
               / %s"Sunday"
  asctime-date = day-name SP date3 SP time-of-day SP year
  date3        = month SP ( 2DIGIT / ( SP 1DIGIT ))
               ; e.g., Jun  2
```

La fecha HTTP distingue entre mayúsculas y minúsculas. Un remitente NO DEBE generar espacios en blanco adicionales en una fecha HTTP más allá de lo incluido específicamente como SP en la gramática. La semántica de nombre del día, mes, año y hora del día es la misma que la definida para las construcciones del formato de mensaje de Internet con el nombre correspondiente.

Los destinatarios de un valor de marca de tiempo en formato de fecha rfc850, que utiliza un año de dos dígitos, DEBEN interpretar una marca de tiempo que parece tener más de 50 años en el futuro como representativa del año más reciente del pasado que tuvo los mismos dos últimos dígitos.

Se recomienda a los destinatarios de los valores de las marcas de tiempo que sean sólidos al analizar las marcas de tiempo, a menos que la definición del campo lo restrinja. Por ejemplo, en ocasiones los mensajes se reenvían a través de HTTP desde una fuente que no es HTTP y que podría generar cualquiera de las especificaciones de fecha y hora definidas por el formato de mensajes de Internet.

Nota: Los requisitos HTTP para formatos de marca de tiempo se aplican solo a su uso dentro del flujo de protocolo. No es necesario que las implementaciones utilicen estos formatos para la presentación del usuario, el registro de solicitudes, etc.

>Abstracción de mensajes

Cada versión principal de HTTP define su propia sintaxis para comunicar mensajes. Esta sección define un tipo de datos abstracto para los mensajes HTTP basado en una generalización de las características de los mensajes, la estructura común y la capacidad para transmitir semántica. Esta abstracción se utiliza para definir requisitos para los remitentes y destinatarios que son independientes de la versión de HTTP, de modo que un mensaje de una versión pueda transmitirse a través de otras versiones sin cambiar su significado.

Un "mensaje" consta de lo siguiente:

* datos de control para describir y enrutar el mensaje,
* una tabla de búsqueda de encabezados de pares nombre/valor para ampliar esos datos de control y transmitir información adicional sobre el remitente, el mensaje, el contenido o el contexto,
* un flujo de contenido potencialmente ilimitado, y
* una tabla de búsqueda de trailers de pares nombre/valor para comunicar información obtenida al enviar el contenido.

Primero se envían los datos de control y de encuadre, seguidos de una sección de encabezado que contiene los campos de la tabla de encabezados. Cuando un mensaje incluye contenido, este se envía después de la sección de encabezado, posiblemente seguido de una sección de avance que puede contener los campos de la tabla de avances.

Se espera que los mensajes se procesen como un flujo continuo, en el que el propósito de ese flujo y su procesamiento continuo se revelan mientras se leen. Por lo tanto, los datos de control describen lo que el destinatario necesita saber de inmediato, los campos de encabezado describen lo que se debe saber antes de recibir el contenido, el contenido (cuando está presente) presumiblemente contiene lo que el destinatario desea o necesita para cumplir con la semántica del mensaje y los campos de avance proporcionan metadatos opcionales que se desconocían antes de enviar el contenido.

Los mensajes deben ser "autodescriptivos": todo lo que un destinatario necesita saber sobre el mensaje se puede determinar observando el mensaje en sí, después de decodificar o reconstruir partes que se han comprimido o eliminado en tránsito, sin necesidad de comprender el estado actual de la aplicación del remitente (establecido a través de mensajes anteriores). Sin embargo, un cliente DEBE conservar el conocimiento de la solicitud al analizar, interpretar o almacenar en caché una respuesta correspondiente. Por ejemplo, las respuestas al método HEAD se ven exactamente como el comienzo de una respuesta a GET, pero no se pueden analizar de la misma manera.

Tenga en cuenta que esta abstracción de mensajes es una generalización en muchas versiones de HTTP, incluidas funciones que podrían no encontrarse en algunas versiones. Por ejemplo, los tráileres se introdujeron dentro de la codificación de transferencia fragmentada de HTTP/1.1 como una sección de tráiler después del contenido. Una función equivalente está presente en HTTP/2 y HTTP/3 dentro del bloque de encabezado que finaliza cada transmisión.

>Enmarcación y completitud

El encuadre de los mensajes indica cómo comienza y termina cada mensaje, de modo que cada uno de ellos pueda distinguirse de otros mensajes o del ruido en la misma conexión. Cada versión principal de HTTP define su propio mecanismo de encuadre.

HTTP/0.9 y las primeras implementaciones de HTTP/1.0 utilizaban el cierre de la conexión subyacente para finalizar una respuesta. Para lograr compatibilidad con versiones anteriores, este encuadre implícito también está permitido en HTTP/1.1. Sin embargo, el encuadre implícito puede fallar al distinguir una respuesta incompleta si la conexión se cierra antes de tiempo. Por ese motivo, casi todas las implementaciones modernas utilizan encuadre explícito en forma de secuencias de datos de mensajes delimitadas por longitud.

Un mensaje se considera "completo" cuando todos los octetos indicados por su encuadre están disponibles. Tenga en cuenta que, cuando no se utiliza un encuadre explícito, un mensaje de respuesta que finaliza con el cierre de la conexión subyacente se considera completo aunque pueda no distinguirse de una respuesta incompleta, a menos que un error a nivel de transporte indique que no está completo.

>Datos de control

Los mensajes comienzan con datos de control que describen su propósito principal. Los datos de control del mensaje de solicitud incluyen un método de solicitud, un objetivo de solicitud y una versión de protocolo. Los datos de control del mensaje de respuesta incluyen un código de estado, una frase de motivo opcional y una versión de protocolo.

En HTTP/1.1 y versiones anteriores, los datos de control se envían como la primera línea de un mensaje. En HTTP/2 y HTTP/3, los datos de control se envían como campos de pseudoencabezado con un prefijo de nombre reservado (por ejemplo, ":authority").

Cada mensaje HTTP tiene una versión de protocolo. Según la versión que se utilice, puede identificarse dentro del mensaje de forma explícita o inferirse a partir de la conexión a través de la cual se recibe el mensaje. Los destinatarios utilizan esa información de versión para determinar las limitaciones o el potencial de una comunicación posterior con ese remitente.

Cuando un intermediario reenvía un mensaje, la versión del protocolo se actualiza para reflejar la versión utilizada por ese intermediario. El campo de encabezado Via se utiliza para comunicar información del protocolo ascendente dentro de un mensaje reenviado.

Un cliente DEBE enviar una versión de solicitud igual a la versión más alta con la que el cliente es compatible y cuya versión principal no sea superior a la versión más alta compatible con el servidor, si se conoce. Un cliente NO DEBE enviar una versión con la que no es compatible.

Un cliente PUEDE enviar una versión de solicitud inferior si se sabe que el servidor implementa incorrectamente la especificación HTTP, pero solo después de que el cliente haya intentado al menos una solicitud normal y haya determinado a partir del código de estado de respuesta o los campos de encabezado que el servidor maneja incorrectamente versiones de solicitud superiores.

Un servidor DEBE enviar una versión de respuesta igual a la versión más alta con la que el servidor es compatible y que tiene una versión principal menor o igual a la que recibió en la solicitud. Un servidor NO DEBE enviar una versión con la que no es compatible. Un servidor puede enviar una respuesta 505 (Versión HTTP no compatible) si desea, por cualquier motivo, rechazar el servicio de la versión principal del protocolo del cliente.

Un destinatario que recibe un mensaje con un número de versión principal que implementa y un número de versión secundaria superior al que implementa DEBERÍA procesar el mensaje como si estuviera en la versión secundaria más alta dentro de esa versión principal con la que el destinatario es compatible. Un destinatario puede suponer que un mensaje con una versión secundaria superior, cuando se envía a un destinatario que aún no ha indicado su compatibilidad con esa versión superior, es lo suficientemente compatible con versiones anteriores como para que cualquier implementación de la misma versión principal lo procese de manera segura.

>Campos de encabezado

Los campos que se envían o reciben antes del contenido se denominan "campos de encabezado" (o simplemente "encabezados", coloquialmente).

La "sección de encabezado" de un mensaje consta de una secuencia de líneas de campos de encabezado. Cada campo de encabezado puede modificar o ampliar la semántica del mensaje, describir al remitente, definir el contenido o proporcionar contexto adicional.

Nota: Nos referimos a los campos nombrados específicamente como "campos de encabezado" cuando solo se permite enviarlos en la sección de encabezado.

>Contenido

Los mensajes HTTP, a menudo transfieren una representación completa o parcial como el "contenido" del mensaje: un flujo de octetos enviados después de la sección de encabezado, tal como lo delinea el marco del mensaje.

Esta definición abstracta de contenido refleja los datos después de que se han extraído del marco del mensaje. Por ejemplo, un cuerpo de mensaje HTTP/1.1 podría consistir en un flujo de datos codificados con la codificación de transferencia fragmentada (una secuencia de fragmentos de datos, un fragmento de longitud cero y una sección final), mientras que el contenido de ese mismo mensaje incluye solo el flujo de datos después de que se ha decodificado la codificación de transferencia; no incluye las longitudes de los fragmentos, la sintaxis de marco fragmentado ni los campos finales.

Nota: Algunos nombres de campos tienen el prefijo ```Content-```. Esta es una convención informal; mientras que algunos de estos campos se refieren al contenido del mensaje, como se define anteriormente, otros tienen como alcance la representación seleccionada.

>Semántica del contenido

El propósito del contenido de una solicitud se define mediante la semántica del método. Por ejemplo, una representación en el contenido de una solicitud PUT representa el estado deseado del recurso de destino después de que la solicitud se aplica con éxito, mientras que una representación en el contenido de una solicitud POST representa la información que debe procesar el recurso de destino.

En una respuesta, el propósito del contenido se define por el método de solicitud, el código de estado de la respuesta y los campos de respuesta que describen ese contenido. Por ejemplo, el contenido de una respuesta 200 (OK) a GET representa el estado actual del recurso de destino, como se observa en el momento de la fecha de origen del mensaje, mientras que el contenido del mismo código de estado en una respuesta a POST podría representar el resultado del procesamiento o el nuevo estado del recurso de destino después de aplicar el procesamiento.

El contenido de una respuesta 206 (Contenido parcial) a GET contiene una sola parte de la representación seleccionada o un cuerpo de mensaje de varias partes que contiene varias partes de esa representación.

Los mensajes de respuesta con un código de estado de error generalmente contienen contenido que representa la condición del error, de modo que el contenido describe el estado del error y los pasos sugeridos para resolverlo.

Las respuestas al método de solicitud HEAD nunca incluyen contenido; los campos de encabezado de respuesta asociados indican solo cuáles habrían sido sus valores si el método de solicitud hubiera sido GET.

Las respuestas 2xx (exitosas) a un método de solicitud CONNECT cambian la conexión al modo túnel en lugar de tener contenido.

Todas las respuestas 1xx (Informativo), 204 (Sin contenido) y 304 (No modificado) no incluyen contenido.

Todas las demás respuestas incluyen contenido, aunque ese contenido puede tener una extensión cero.

>Identificar contenido

Cuando se transfiere una representación completa o parcial como contenido de un mensaje, a menudo es deseable que el remitente proporcione, o que el destinatario determine, un identificador para un recurso correspondiente a esa representación específica. Por ejemplo, un cliente que realiza una solicitud GET en un recurso para "el informe meteorológico actual" podría querer un identificador específico del contenido devuelto (por ejemplo, "informe meteorológico de Laguna Beach en 20210720T1711"). Esto puede resultar útil para compartir o marcar contenido de recursos que se espera que tengan representaciones cambiantes con el tiempo.

Para un mensaje de solicitud:

* Si la solicitud tiene un campo de encabezado ```Content-Location```, entonces el remitente afirma que el contenido es una representación del recurso identificado por el valor del campo ```Content-Location```. Sin embargo, no se puede confiar en dicha afirmación a menos que pueda verificarse por otros medios (no definidos en esta especificación). La información aún podría ser útil para los enlaces del historial de revisiones.
De lo contrario, HTTP no identifica el contenido, pero es posible que se proporcione un identificador más específico dentro del propio contenido.

Para un mensaje de respuesta, se aplican las siguientes reglas en orden hasta que se encuentra una coincidencia:

* Si el método de solicitud es HEAD o el código de estado de respuesta es 204 (Sin contenido) o 304 (No modificado), no hay contenido en la respuesta.
* Si el método de solicitud es GET y el código de estado de respuesta es 200 (OK), el contenido es una representación del recurso de destino.
* Si el método de solicitud es GET y el código de estado de respuesta es 203 (Información no autorizada), el contenido es una representación potencialmente modificada o mejorada del recurso de destino proporcionado por un intermediario.
* Si el método de solicitud es GET y el código de estado de respuesta es 206 (Contenido parcial), el contenido es una o más partes de una representación del recurso de destino.
* Si la respuesta tiene un campo de encabezado ```Content-Location``` y el valor de su campo es una referencia al mismo URI que el URI de destino, el contenido es una representación del recurso de destino.
* Si la respuesta tiene un campo de encabezado ```Content-Location``` y el valor de su campo es una referencia a un URI diferente del URI de destino, entonces el remitente afirma que el contenido es una representación del recurso identificado por el valor del campo ```Content-Location```. Sin embargo, no se puede confiar en dicha afirmación a menos que pueda verificarse por otros medios (no definidos en esta especificación).
De lo contrario, HTTP no identifica el contenido, pero es posible que se proporcione un identificador más específico dentro del propio contenido.

>Campos de trailers

Los campos que se encuentran dentro de una "sección de trailer" se denominan "campos de trailers" (o simplemente "trailers", coloquialmente). Los campos de trailers pueden resultar útiles para proporcionar comprobaciones de integridad de mensajes, firmas digitales, métricas de entrega o información de estado de posprocesamiento. Estos, deben procesarse y almacenarse por separado de los campos de la sección del encabezado para evitar contradecir la semántica del mensaje conocida en el momento en que se completó la sección del encabezado. La presencia o ausencia de ciertos campos de encabezado podría afectar las decisiones tomadas para el enrutamiento o procesamiento del mensaje en su conjunto antes de recibir los avances; esas elecciones no pueden deshacerse con el descubrimiento posterior de campos de trailers.

>Limitaciones en el uso de remolques

Una sección de trailer solo es posible cuando la admite la versión de HTTP en uso y está habilitada por un mecanismo de encuadre explícito. Por ejemplo, la codificación de transferencia fragmentada en HTTP/1.1 permite enviar una sección de avance después del contenido.

Muchos campos no se pueden procesar fuera de la sección del encabezado porque su evaluación es necesaria antes de recibir el contenido, como aquellos que describen el encuadre del mensaje, el enrutamiento, la autenticación, los modificadores de solicitudes, los controles de respuesta o el formato del contenido. 

Un remitente NO DEBE generar un campo de trailer a menos que sepa que la definición del nombre del campo de encabezado correspondiente permite que el campo se envíe en trailers.

Los campos de trailers pueden resultar difíciles de procesar para los intermediarios que reenvían mensajes de una versión de protocolo a otra. Si el mensaje completo se puede almacenar en tránsito, algunos intermediarios podrían fusionar los campos de trailers en la sección del encabezado (según corresponda) antes de reenviarlo. Sin embargo, en la mayoría de los casos, los trailers simplemente se desechan. Un destinatario NO DEBE fusionar un campo de trailer en una sección de encabezado a menos que comprenda la definición del campo de encabezado correspondiente y esa definición permita y defina explícitamente cómo los valores del campo de trailer se pueden fusionar de forma segura.

La presencia de la palabra clave "trailers" en el campo de encabezado TE de una solicitud, indica que el cliente está dispuesto a aceptar campos de trailer, en su nombre y en el de cualquier cliente descendente. Para solicitudes de un intermediario, esto implica que todos los clientes posteriores están dispuestos a aceptar campos finales en la respuesta reenviada. Tenga en cuenta que la presencia de "trailers" no significa que los clientes procesarán ningún campo de avance particular en la respuesta; sólo que ninguno de los clientes dejará caer la(s) sección(es) del/los trailer/s.

Debido a la posibilidad de que los campos de trailer se descarten en tránsito, un servidor NO DEBE generar campos de trailer que considere necesarios para que los reciba el agente de usuario.

>Procesamiento de campos de trailer

El campo de encabezado "Tráiler" se puede enviar para indicar los campos que probablemente se enviarán en la sección de trailer, lo que permite a los destinatarios prepararse para su recepción antes de procesar el contenido. Por ejemplo, esto podría ser útil si un nombre de campo indica que se debe calcular una suma de verificación dinámica a medida que se recibe el contenido y luego verificarse inmediatamente al recibir el valor del campo.

Al igual que los campos de encabezado, los campos de trailer con el mismo nombre se procesan en el orden en que se reciben; varias líneas de campo de cola con el mismo nombre tienen la semántica equivalente a agregar los valores múltiples como una lista de miembros. Los campos de trailer que podrían generarse más de una vez durante un mensaje DEBEN definirse como un campo basado en lista, incluso si cada valor de miembro solo se procesa una vez por línea de campo recibida.

Al final de un mensaje, un destinatario PUEDE tratar el conjunto de campos de trailer recibidos como una estructura de datos de pares de nombre/valor, similar (pero separada) de los campos de encabezado. Se pueden definir expectativas de procesamiento adicionales, si las hay, dentro de la especificación de campo para un campo destinado a uso en trailers.

>Metadatos del mensaje

Los campos que describen el mensaje en sí, como cuándo y cómo se generó el mensaje, pueden aparecer tanto en solicitudes como en respuestas.

>Fecha

El campo de encabezado ```Fecha``` representa la fecha y hora en que se originó el mensaje y tiene la misma semántica que el campo Fecha de origen (orig-date). El valor del campo es una fecha HTTP.

```
Date = HTTP-date
```

Un ejemplo es

```
Date: Tue, 15 Nov 1994 08:12:31 GMT
```

Un remitente que genera un campo de encabezado de Fecha DEBE generar el valor de su campo como la mejor aproximación disponible de la fecha y hora de generación del mensaje. En teoría, la fecha debería representar el momento justo antes de generar el contenido del mensaje. En la práctica, un remitente puede generar el valor de la fecha en cualquier momento durante el origen del mensaje.

Un servidor de origen con un reloj DEBE generar un campo de encabezado de Fecha en todas las respuestas 2xx (Exitosa), 3xx (Redirección) y 4xx (Error del cliente), y PUEDE generar un campo de encabezado de Fecha en las respuestas 1xx (Informativa) y 5xx (Error del servidor).

Un servidor de origen sin reloj NO DEBE generar un campo de encabezado de Fecha.

Un destinatario con un reloj que recibe un mensaje de respuesta sin un campo de encabezado de Fecha DEBE registrar la hora en que se recibió y agregar un campo de encabezado de Fecha correspondiente a la sección de encabezado del mensaje si se almacena en caché o se reenvía en sentido descendente.

Un destinatario con un reloj que recibe una respuesta con un valor de campo de encabezado de fecha no válido PUEDE reemplazar ese valor con la hora en que se recibió la respuesta.

Un agente de usuario PUEDE enviar un campo de encabezado de Fecha en una solicitud, aunque generalmente no lo hará a menos que crea que transmite información útil al servidor. Por ejemplo, las aplicaciones personalizadas de HTTP pueden transmitir una fecha si se espera que el servidor ajuste su interpretación de la solicitud del usuario en función de las diferencias entre los relojes del agente de usuario y del servidor.

>Trailer

El campo de encabezado "Trailer" proporciona una lista de nombres de campos que el remitente prevé enviar como campos de tráiler dentro de ese mensaje. Esto permite que el destinatario se prepare para recibir los metadatos indicados antes de comenzar a procesar el contenido.

```
Trailer = #field-name
```

Por ejemplo, un remitente puede indicar que se calculará una firma a medida que se transmite el contenido, y proporcionar la firma final como un campo de tráiler. Esto permite que un destinatario realice la misma verificación sobre la marcha mientras recibe el contenido.¶

Un remitente que pretende generar uno o más campos de tráiler en un mensaje DEBERÍA generar un campo de encabezado de tráiler en la sección de encabezado de ese mensaje para indicar qué campos podrían estar presentes en los tráileres.

Si un intermediario descarta la sección del tráiler en tránsito, el campo Trailer podría proporcionar una pista de qué metadatos se perdieron, aunque no hay garantía de que un remitente de Trailer siempre continúe enviando los campos nombrados.

>Enrutamiento de mensajes HTTP

El enrutamiento de mensajes de solicitud HTTP lo determina cada cliente en función del recurso de destino, la configuración del proxy del cliente y el establecimiento o la reutilización de una conexión entrante. El enrutamiento de respuesta correspondiente sigue la misma cadena de conexión hasta el cliente.

>Determinación del recurso de destino

Aunque el protocolo HTTP se utiliza en una amplia variedad de aplicaciones, la mayoría de los clientes dependen del mismo mecanismo de identificación de recursos y técnicas de configuración que los navegadores web de propósito general. Incluso cuando las opciones de comunicación están codificadas en la configuración de un cliente, podemos pensar en su efecto combinado como una referencia URI.

Una referencia URI se resuelve a su forma absoluta para obtener el "URI de destino". El URI de destino excluye el componente de fragmento de la referencia, si lo hay, ya que los identificadores de fragmentos están reservados para el procesamiento del lado del cliente.

Para realizar una acción en un "recurso de destino", el cliente envía un mensaje de solicitud que contiene suficientes componentes de su URI de destino analizado para permitir que los destinatarios identifiquen ese mismo recurso.  Por razones históricas, los componentes de la URI de destino analizados, denominados colectivamente "destino de la solicitud", se envían dentro de los datos de control del mensaje y el campo de encabezado del host.

Hay dos casos inusuales en los que los componentes del destino de la solicitud están en un formato específico del método:

* Para CONNECT el destino de la solicitud es el nombre del host y el número de puerto del destino del túnel, separados por dos puntos.

* Para OPTIONS el destino de la solicitud puede ser un solo asterisco ("*").

Consultar las definiciones de los métodos respectivos para obtener más detalles. Estos formularios NO DEBEN usarse con otros métodos.

Al recibir la solicitud de un cliente, un servidor reconstruye la URI de destino a partir de los componentes recibidos de acuerdo con su configuración local y el contexto de conexión entrante. Esta reconstrucción es específica para cada versión principal del protocolo. Por ejemplo, la Sección 3.3 de [HTTP/1.1], define cómo un servidor determina la URI de destino de una solicitud HTTP/1.1.

Nota: Las especificaciones anteriores definieron la URI de destino recompuesta como un concepto distinto, la "URI de solicitud efectiva".

>Host y :authority

El campo de encabezado "Host" en una solicitud proporciona la información del host y del puerto de la URI de destino, lo que permite al servidor de origen distinguir entre recursos mientras atiende solicitudes para múltiples nombres de host.

En HTTP/2 y HTTP/3, el campo de encabezado Host es, en algunos casos, reemplazado por el campo de pseudoencabezado ":authority" de los datos de control de una solicitud.

```
Host = uri-host [ ":" port ]
```

La información de autoridad de la URI de destino es fundamental para gestionar una solicitud. Un agente de usuario DEBE generar un campo de encabezado Host en una solicitud a menos que envíe esa información como un campo de pseudoencabezado ":authority".

Un agente de usuario que envía Host DEBE enviarlo como el primer campo en la sección de encabezado de una solicitud.

Por ejemplo, una solicitud GET al servidor de origen para 

```
http://www.example.org/pub/WWW/ 
```

comenzaría con:

```
GET /pub/WWW/ HTTP/1.1
Host: www.example.org
```

Dado que la información del host y del puerto actúan como un mecanismo de enrutamiento a nivel de aplicación, es un objetivo frecuente para el malware que busca envenenar un caché compartido o redirigir una solicitud a un servidor no deseado. Un proxy de interceptación es particularmente vulnerable si se basa en la información del host y del puerto para redirigir solicitudes a servidores internos, o para usarla como clave de caché en un caché compartido, sin verificar primero que la conexión interceptada esté dirigida a una dirección IP válida para ese host.

>Enrutamiento de solicitudes entrantes

Una vez que se determinan la URI de destino y su origen, un cliente decide si es necesaria una solicitud de red para lograr la semántica deseada y, de ser así, a dónde debe dirigirse esa solicitud.

>A un caché

Si el cliente tiene un caché y este puede satisfacer la solicitud, entonces la solicitud generalmente se dirige ahí primero.

>A un proxy

Si un caché no satisface la solicitud, un cliente típico comprobará su configuración para determinar si se debe utilizar un proxy para satisfacer la solicitud. La configuración del proxy depende de la implementación, pero a menudo se basa en la coincidencia de prefijos de URI, la coincidencia de autoridad selectiva o ambas, y el proxy en sí mismo suele identificarse mediante un URI "http" o "https".

Si se aplica un proxy "http" o "https", el cliente se conecta de forma entrante estableciendo (o reutilizando) una conexión con ese proxy y luego enviándole un mensaje de solicitud HTTP que contiene un destino de solicitud que coincide con el URI de destino del cliente.

>Al origen

Si no se aplica ningún proxy, un cliente típico invocará una rutina de controlador (específica para el esquema de la URI de destino) para obtener acceso al recurso identificado. La forma en que esto se logra depende del esquema de la URI de destino y se define por su especificación asociada.

La sección 4.3.2 define cómo obtener acceso a un recurso "http" estableciendo (o reutilizando) una conexión entrante al servidor de origen identificado y luego enviándole un mensaje de solicitud HTTP que contenga un destino de solicitud que coincida con la URI de destino del cliente.

La sección 4.3.3 define cómo obtener acceso a un recurso "https" estableciendo (o reutilizando) una conexión segura entrante a un servidor de origen que tenga autoridad para el origen identificado y luego enviándole un mensaje de solicitud HTTP que contenga un destino de solicitud que coincida con la URI de destino del cliente.
