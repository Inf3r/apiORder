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

En los comienzos del desarrollo de esta tecnología, que Tim Berners-Lee creó, HTTP existía con el nombre de "The Mesh", dentro de un entorno de laboratorio informático aislado. Luego, fue renombrado a "HTTP" donde datos como la URL completa no se incluía, ya que el protocolo, la dirección y el puerto no eran necesarios una vez que se conectaba al único servidor que funcionaba como pruebas en ese entonces. A medida que la Web fue creciendo, HTTP se amplió para incluir solicitudes y respuestas dentro de mensajes, transferir formatos de datos arbitrarios utilizando tipos de medios similares a MIME (especificación de correo electrónico) y enrutar solicitudes a través de intermediarios. Estos protocolos se definieron finalmente como HTTP/0.9 y HTTP/1.0.

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

>Rechazar solicitudes mal dirigidas

Una vez que un servidor recibe una solicitud y la analiza lo suficiente para determinar su URI de destino, el servidor decide si procesar la solicitud por sí mismo, reenviarla a otro servidor, redirigir al cliente a un recurso diferente, responder con un error o interrumpir la conexión. Esta decisión puede verse influenciada por cualquier aspecto de la solicitud o del contexto de conexión, pero está dirigida específicamente a si el servidor ha sido configurado para procesar solicitudes para esa URI de destino y si el contexto de conexión es apropiado para esa solicitud.

Por ejemplo, una solicitud podría haber sido mal dirigida, deliberada o accidentalmente, de modo que la información dentro de un campo de encabezado de Host recibido difiere del host o puerto de la conexión. Si la conexión proviene de una puerta de enlace de confianza, dicha inconsistencia podría esperarse; de lo contrario, podría indicar un intento de eludir los filtros de seguridad, engañar al servidor para que entregue contenido no público o envenenar una caché.

A menos que la conexión provenga de una puerta de enlace de confianza, un servidor de origen DEBE rechazar una solicitud si no se cumple algún requisito específico del esquema para la URI de destino. En particular, una solicitud de un recurso "https" DEBE ser rechazada a menos que se haya recibido a través de una conexión que se haya asegurado mediante un certificado válido para el origen de esa URI de destino.

El código de estado 421 (Solicitud mal dirigida) en una respuesta, indica que el servidor de origen ha rechazado la solicitud porque parece haber sido mal dirigida.

>Correlación de respuestas

Se puede utilizar una conexión para varios intercambios de solicitudes y respuestas. El mecanismo utilizado para correlacionar los mensajes de solicitud y respuesta dependen de la versión; algunas versiones de HTTP utilizan un orden implícito de los mensajes, mientras que otras utilizan un identificador explícito.

Todas las respuestas, independientemente del código de estado (incluidas las respuestas provisionales), se pueden enviar en cualquier momento después de recibir una solicitud, incluso si la solicitud aún no se ha completado. Una respuesta puede completarse antes de que se complete su solicitud correspondientes. Asimismo, no se espera que los clientes esperen una cantidad específica de tiempo para recibir una respuesta.  Los clientes (incluidos los intermediarios) pueden abandonar una solicitud si no reciben una respuesta en un plazo de tiempo razonable.

Un cliente que recibe una respuesta mientras todavía está enviando la solicitud asociada DEBERÍA continuar enviando esa solicitud a menos que reciba una indicación explícita.

>Reenvío de mensajes

Los intermediarios pueden cumplir una variedad de funciones en el procesamiento de solicitudes y respuestas HTTP. Algunos intermediarios se utilizan para mejorar el rendimiento o la disponibilidad. Otros se utilizan para el control de acceso o para filtrar contenido. Dado que un flujo HTTP tiene características similares a una arquitectura de canalización y filtro, no existen límites inherentes en cuanto a la medida en que un intermediario puede mejorar (o interferir) con cualquier dirección del flujo.

Se espera que los intermediarios reenvíen mensajes incluso cuando no se reconocen elementos del protocolo (por ejemplo, nuevos métodos, códigos de estado o nombres de campo), ya que eso preserva la extensibilidad para los destinatarios posteriores.

Un intermediario que no actúe como un túnel DEBE implementar el campo de encabezado Connection, y excluir del reenvío los campos que solo están destinados a la conexión entrante.

Un intermediario NO DEBE reenviarse un mensaje a sí mismo a menos que esté protegido de un bucle de solicitud infinito.  En general, un intermediario debe reconocer sus propios nombres de servidor, incluidos los alias, las variaciones locales o las direcciones IP literales, y responder a dichas solicitudes directamente.

Un mensaje HTTP se puede analizar como un flujo para su procesamiento incremental o reenvío en sentido descendente. Sin embargo, los remitentes y los destinatarios no pueden confiar en la entrega incremental de mensajes parciales, ya que algunas implementaciones almacenarán en búfer o retrasarán el reenvío de mensajes por el bien de la eficiencia de la red, los controles de seguridad o las transformaciones de contenido.

>Conexión

El campo de encabezado ```Connection``` permite al remitente enumerar las opciones de control deseadas para la conexión actual.

```
Connection        = #connection-option 
connection-option = token
```

Las opciones de conexión no distinguen entre mayúsculas y minúsculas.

Cuando se utiliza un campo distinto de Conexión para proporcionar información de control para o sobre la conexión actual, el remitente DEBE incluir el nombre del campo correspondiente dentro del campo de encabezado de Conexión.  Tener en cuenta que algunas versiones de HTTP prohíben el uso de campos para dicha información y, por lo tanto, no permiten el campo Connection.

Los intermediarios DEBEN analizar un campo de encabezado Connection recibido antes de que se reenvíe un mensaje y, para cada opción de conexión en este campo, eliminar cualquier campo de encabezado o tráiler del mensaje con el mismo nombre que la opción de conexión y, luego, eliminar el campo de encabezado Connection en sí (o reemplazarlo con las opciones de control propias del intermediario para el mensaje reenviado).

Por lo tanto, el campo de encabezado Connection proporciona una forma declarativa de distinguir los campos que solo están destinados al destinatario inmediato ("salto a salto") de aquellos campos que están destinados a todos los destinatarios en la cadena ("de extremo a extremo"), lo que permite que el mensaje sea autodescriptivo y que se puedan implementar futuras extensiones específicas de la conexión sin temor a que sean reenviadas ciegamente por intermediarios más antiguos.

Además, los intermediarios DEBEN eliminar o reemplazar los campos que se sabe que requieren eliminación antes del reenvío, ya sea que aparezcan o no, como una opción de conexión después de aplicar la semántica de esos campos. Esto incluye, entre otras cosas:

```
* Proxy-Connection
* Keep-Alive
* TE 
* Transfer-Encoding
* Upgrade
```

Un remitente NO DEBE enviar una opción de conexión correspondiente a un campo que está destinado a todos los destinatarios del contenido. Por ejemplo, Cache-Control nunca es apropiado como opción de conexión.

Las opciones de conexión no siempre corresponden a un campo presente en el mensaje, ya que un campo específico de conexión podría no ser necesario si no hay parámetros asociados con una opción de conexión. Por el contrario, un campo específico de conexión recibido sin una opción de conexión correspondiente, generalmente indica que el campo ha sido reenviado incorrectamente por un intermediario y debe ser ignorado por el destinatario.

Al definir una nueva opción de conexión que no corresponde a un campo, los autores de especificaciones deben reservar el nombre del campo correspondiente de todos modos para evitar colisiones posteriores. Dichos nombres de campo reservados se registran en el "Registro de nombres de campo del Protocolo de transferencia de hipertexto (HTTP)".

>Max-Forwards

El campo de encabezado "Max-Forwards" proporciona un mecanismo con los métodos de solicitud TRACE y OPTIONS para limitar la cantidad de veces que los servidores proxy reenvían la solicitud. Esto puede resultar útil cuando el cliente intenta rastrear una solicitud que parece fallar o entrar en un bucle a mitad de la cadena.

```
Max-Forwards = 1*DIGIT
```

El valor de Max-Forwards es un entero decimal que indica la cantidad restante de veces que se puede reenviar este mensaje de solicitud.

Cada intermediario que recibe una solicitud TRACE u OPTIONS que contiene un campo de encabezado Max-Forwards DEBE verificar y actualizar su valor antes de reenviar la solicitud. Si el valor recibido es cero (0), el intermediario NO DEBE reenviar la solicitud; en su lugar, el intermediario DEBE responder como el destinatario final. Si el valor de Max-Forwards recibido es mayor que cero, el intermediario DEBE generar un campo Max-Forwards actualizado en el mensaje reenviado con un valor de campo que sea el menor de 

```
a) el valor recibido disminuido en uno (1) 
o 
b) el valor máximo admitido por el destinatario para Max-Forwards.
```

Un destinatario PUEDE ignorar un campo de encabezado Max-Forwards recibido con cualquier otro método de solicitud.

>Via

El campo de encabezado "Via" indica la presencia de protocolos y destinatarios intermedios entre el agente de usuario y el servidor (en las solicitudes) o entre el servidor de origen y el cliente (en las respuestas), de forma similar al campo de encabezado "Recibido" en el correo electrónico. Via se puede utilizar para realizar un seguimiento de los reenvíos de mensajes, evitar bucles de solicitudes e identificar las capacidades de protocolo de los remitentes a lo largo de la cadena de solicitud/respuesta.

```
Via               = #( received-protocol RWS received-by [ RWS comment ] )
received-protocol = [ protocol-name "/" ] protocol-version
received-by       = pseudonym [ ":" port ]
pseudonym         = token
```

Cada miembro del valor del campo Via representa un proxy o una puerta de enlace que ha reenviado el mensaje. Cada intermediario agrega su propia información sobre cómo se recibió el mensaje, de modo que el resultado final se ordena de acuerdo con la secuencia de destinatarios que lo reenvían.

Un proxy DEBE enviar un campo de encabezado Via apropiado, como se describe a continuación, en cada mensaje que reenvía. Una puerta de enlace HTTP a HTTP DEBE enviar un campo de encabezado Via apropiado en cada mensaje de solicitud entrante y PUEDE enviar un campo de encabezado Via en los mensajes de respuesta reenviados.

Para cada intermediario, el protocolo recibido indica el protocolo y la versión del protocolo utilizados por el remitente ascendente del mensaje. Por lo tanto, el valor del campo Via registra las capacidades del protocolo anunciadas de la cadena de solicitud/respuesta de modo que permanezcan visibles para los destinatarios descendentes; esto puede ser útil para determinar qué características incompatibles con versiones anteriores podrían ser seguras para usar en respuesta o dentro de una solicitud posterior. Para abreviar, se omite el nombre del protocolo cuando el protocolo recibido es HTTP.

La parte "recibido por" normalmente es el host y el número de puerto opcional de un servidor o cliente destinatario que posteriormente reenvió el mensaje. Sin embargo, si se considera que el host real es información confidencial, el remitente PUEDE reemplazarlo con un seudónimo. Si no se proporciona un puerto, el destinatario PUEDE interpretarlo como que se recibió en el puerto predeterminado, si lo hubiera, para el protocolo recibido.

El remitente PUEDE generar comentarios para identificar el software de cada destinatario, de manera análoga a los campos de encabezado User-Agent y Server. Sin embargo, los comentarios en Via son opcionales y el destinatario PUEDE eliminarlos antes de reenviar el mensaje.

Por ejemplo, un mensaje de solicitud podría enviarse desde un agente de usuario HTTP/1.0 a un proxy interno cuyo nombre en código es "fred", que utiliza HTTP/1.1 para reenviar la solicitud a un proxy público en ```p.example.net```, que completa la solicitud reenviándola al servidor de origen en ```www.example.com```. La solicitud recibida por ```www.example.com``` tendría entonces el siguiente campo de encabezado Via:

```
Via: 1.0 fred, 1.1 p.example.net
```

Un intermediario utilizado como portal a través de un cortafuegos de red NO DEBE reenviar los nombres y puertos de los hosts dentro de la región del cortafuegos a menos que esté habilitado explícitamente para hacerlo. Si no está habilitado, dicho intermediario DEBE reemplazar cada host recibido de cualquier host detrás del cortafuegos por un seudónimo apropiado para ese host.

Un intermediario PUEDE combinar una subsecuencia ordenada de miembros de la lista de campos de encabezado Via en un solo miembro si las entradas tienen valores de protocolo recibido idénticos. Por ejemplo,

```
Via: 1.0 ricky, 1.1 ethel, 1.1 fred, 1.0 lucy
```

Podría colapsarse a

```
Via: 1.0 ricky, 1.1 mertz, 1.0 lucy
```

Un remitente NO DEBE combinar varios miembros de la lista a menos que todos estén bajo el mismo control organizacional y los hosts ya hayan sido reemplazados por seudónimos. Un remitente NO DEBE combinar miembros que tengan diferentes valores de protocolo recibido.

>Transformaciones de mensajes

Algunos intermediarios incluyen funciones para transformar los mensajes y su contenido. Un proxy puede, por ejemplo, convertir entre formatos de imagen para ahorrar espacio en la memoria caché o reducir la cantidad de tráfico en un enlace lento. Sin embargo, pueden surgir problemas operativos cuando estas transformaciones se aplican a contenido destinado a aplicaciones críticas, como imágenes médicas o análisis de datos científicos, en particular cuando se utilizan comprobaciones de integridad o firmas digitales para garantizar que el contenido recibido sea idéntico al original.

Un proxy HTTP a HTTP se denomina "proxy transformador" si está diseñado o configurado para modificar mensajes de una manera semánticamente significativa (es decir, modificaciones, más allá de las requeridas por el procesamiento HTTP normal, que cambian el mensaje de una manera que sería significativa para el remitente original o potencialmente significativa para los destinatarios posteriores). Por ejemplo, un proxy transformador podría actuar como un servidor de anotaciones compartido (modificando las respuestas para incluir referencias a una base de datos de anotaciones local), un filtro de malware, un transcodificador de formato o un filtro de privacidad. Se presume que dichas transformaciones son deseadas por cualquier cliente (u organización cliente) que elija el proxy.

Si un proxy recibe una URI de destino con un nombre de host que no es un nombre de dominio completo, PUEDE agregar su propio dominio al nombre de host que recibió al reenviar la solicitud. Un proxy NO DEBE cambiar el nombre de host si la URI de destino contiene un nombre de dominio completo.

Un proxy NO DEBE modificar las partes "absolute-path" y "query" de la URI de destino recibida al reenviarla al siguiente servidor entrante, excepto según lo requiera ese protocolo de reenvío. Por ejemplo, un proxy que reenvía una solicitud a un servidor de origen a través de HTTP/1.1 reemplazará una ruta vacía con "/" o "*", según el método de solicitud.

Un proxy NO DEBE transformar el contenido de un mensaje de respuesta que contenga una directiva de caché de no transformación. Tenga en cuenta que esto no se aplica a las transformaciones de mensajes que no cambian el contenido, como la adición o eliminación de codificaciones de transferencia (Sección 7 de [HTTP/1.1]).

Un proxy PUEDE transformar el contenido de un mensaje que no contenga una directiva de caché de no transformación ```(no-transform)```. Un proxy que transforma el contenido de una respuesta 200 (OK) puede informar a los destinatarios posteriores que se ha aplicado una transformación cambiando el código de estado de respuesta a 203 (Información no autorizada).

Un proxy NO DEBE modificar los campos de encabezado que proporcionan información sobre los puntos finales de la cadena de comunicación, el estado del recurso o la representación seleccionada (que no sea el contenido) a menos que la definición del campo permita específicamente dicha modificación o la modificación se considere necesaria para la privacidad o la seguridad.

>Upgrade

El campo de encabezado "Upgrade" tiene como objetivo proporcionar un mecanismo simple para realizar la transición de HTTP/1.1 a algún otro protocolo en la misma conexión.

Un cliente PUEDE enviar una lista de nombres de protocolos en el campo de encabezado Upgrade de una solicitud para invitar al servidor a cambiar a uno o más de los protocolos nombrados, en orden de preferencia descendente, antes de enviar la respuesta final. Un servidor PUEDE ignorar un campo de encabezado Upgrade recibido si desea continuar utilizando el protocolo actual en esa conexión. Upgrade no se puede utilizar para insistir en un cambio de protocolo.

```
Upgrade          = #protocol

protocol         = protocol-name ["/" protocol-version]
protocol-name    = token
protocol-version = token
```

Aunque los nombres de protocolo se registran con un orden de preferencia entre mayúsculas y minúsculas, los destinatarios DEBEN utilizar una comparación que no distinga entre mayúsculas y minúsculas al hacer coincidir cada nombre de protocolo con los protocolos admitidos.

Un servidor que envía una respuesta 101 (Cambio de protocolos) DEBE enviar un campo de encabezado de actualización para indicar los nuevos protocolos a los que se está cambiando la conexión; si se están cambiando varias capas de protocolo, el remitente DEBE enumerar los protocolos en orden ascendente de capa. Un servidor NO DEBE cambiar a un protocolo que no haya sido indicado por el cliente en el campo de encabezado de actualización de la solicitud correspondiente. Un servidor PUEDE optar por ignorar el orden de preferencia indicado por el cliente y seleccionar los nuevos protocolos en función de otros factores, como la naturaleza de la solicitud o la carga actual en el servidor.

Un servidor que envía una respuesta 426 (Actualización requerida) DEBE enviar un campo de encabezado de Actualización para indicar los protocolos aceptables, en orden de preferencia descendente.

Un servidor PUEDE enviar un campo de encabezado de Actualización en cualquier otra respuesta para anunciar que implementa soporte para la actualización a los protocolos enumerados, en orden de preferencia descendente, cuando sea apropiado para una solicitud futura.

El siguiente es un ejemplo hipotético enviado por un cliente:

```
GET /hello HTTP/1.1
Host: www.example.com
Connection: upgrade
Upgrade: websocket, IRC/6.9, RTA/x11
```

Las capacidades y la naturaleza de la comunicación a nivel de aplicación después del cambio de protocolo dependen completamente del nuevo protocolo o protocolos elegidos. Sin embargo, inmediatamente después de enviar la respuesta 101 (cambio de protocolos), se espera que el servidor continúe respondiendo a la solicitud original como si hubiera recibido su equivalente dentro del nuevo protocolo (es decir, el servidor aún tiene una solicitud pendiente que satisfacer después de que se haya cambiado el protocolo, y se espera que lo haga sin requerir que se repita la solicitud).

Por ejemplo, si se recibe el campo de encabezado Upgrade en una solicitud GET y el servidor decide cambiar de protocolo, primero responde con un mensaje 101 (cambio de protocolos) en HTTP/1.1 y luego inmediatamente después con el equivalente del nuevo protocolo de una respuesta a un GET en el recurso de destino. Esto permite que una conexión se actualice a protocolos con la misma semántica que HTTP sin el costo de latencia de un viaje de ida y vuelta adicional. Un servidor NO DEBE cambiar de protocolo a menos que la semántica del mensaje recibido pueda ser respetada por el nuevo protocolo; Una solicitud OPTIONS puede ser aceptada por cualquier protocolo.

A continuación se presenta un ejemplo de respuesta a la solicitud hipotética anterior:

```
HTTP/1.1 101 Switching Protocols
Connection: upgrade
Upgrade: websocket

[... data stream switches to websocket with an appropriate response
(as defined by new protocol) to the "GET /hello" request ...]
```

El remitente de una actualización DEBE enviar también una opción de conexión "Actualizar" en el campo de encabezado de Conexión para informar a los intermediarios que no deben reenviar este campo. Un servidor que recibe un campo de encabezado de Actualización en una solicitud HTTP/1.0 DEBE ignorar ese campo de Actualización.

Un cliente no puede comenzar a utilizar un protocolo actualizado en la conexión hasta que haya enviado completamente el mensaje de solicitud (es decir, el cliente no puede cambiar el protocolo que está enviando en medio de un mensaje). Si un servidor recibe tanto un campo de encabezado de Actualización como uno de Expect con la expectativa "100-continue", el servidor DEBE enviar una respuesta 100 (Continuar) antes de enviar una respuesta 101 (Cambiar protocolos).

El campo de encabezado Upgrade sólo se aplica a la conmutación de protocolos sobre la conexión existente; no se puede utilizar para cambiar el protocolo de conexión subyacente (transporte) ni para cambiar la comunicación existente a una conexión diferente. Para esos fines, es más apropiado utilizar una respuesta 3xx (Redirección).

>Datos de representación y metadatos

**DATOS DE REPRESENTACIÓN:**

Los datos de representación asociados a un mensaje HTTP se proporcionan como contenido del mensaje o se hace referencia a ellos mediante la semántica del mensaje y la URI de destino. Los datos de representación tienen un formato y una codificación definidos por los campos de encabezado de metadatos de representación.

El tipo de datos de los datos de representación se determina mediante los campos de encabezado Content-Type y Content-Encoding. Estos definen un modelo de codificación ordenado de dos capas:

```
representation-data := Content-Encoding( Content-Type( data ) )
```

**METADATOS DE REPRESENTACIÓN:**

Los campos de encabezado de representación proporcionan metadatos sobre la representación. Cuando un mensaje incluye contenido, los campos de encabezado de representación describen cómo interpretar esos datos. En una respuesta a una solicitud HEAD, los campos de encabezado de representación describen los datos de representación que se habrían incluido en el contenido si la misma solicitud hubiera sido GET.

**Content-Type:**

El campo de encabezado "Content-Type" indica el tipo de medio de la representación asociada: la representación incluida en el contenido del mensaje o la representación seleccionada, según lo determine la semántica del mensaje. El tipo de medio indicado define tanto el formato de los datos como la forma en que el destinatario pretende procesarlos, dentro del alcance de la semántica del mensaje recibido, después de que se decodifiquen las codificaciones de contenido indicadas por Content-Encoding.

```
Content-Type = media-type
```

Un ejemplo del campo de tipo de medio es

```
Content-Type: text/html; charset=ISO-8859-4
```

Un remitente que genera un mensaje que contiene contenido DEBERÍA generar un campo de encabezado Content-Type en ese mensaje a menos que el tipo de medio previsto de la representación adjunta sea desconocido para el remitente. Si no hay un campo de encabezado Content-Type, el destinatario PUEDE asumir un tipo de medio de "application/octet-stream" o examinar los datos para determinar su tipo.

En la práctica, los propietarios de recursos no siempre configuran correctamente su servidor de origen para proporcionar el tipo de contenido correcto para una representación dada. Algunos agentes de usuario examinan el contenido y, en ciertos casos, anulan el tipo recibido. Este "sniffing MIME" corre el riesgo de sacar conclusiones incorrectas sobre los datos, lo que podría exponer al usuario a riesgos de seguridad adicionales (por ejemplo, "escalada de privilegios"). Además, los distintos tipos de medios a menudo comparten un formato de datos común, que difiere solo en cómo se pretende procesar los datos, lo que es imposible de distinguir inspeccionando solo los datos. Cuando se implementa el sniffing, se recomienda a los implementadores que proporcionen un medio para que el usuario lo deshabilite.

Aunque Content-Type se define como un campo singleton, a veces se genera incorrectamente varias veces, lo que da como resultado un valor de campo combinado que parece una lista. Los destinatarios a menudo intentan manejar este error utilizando el último miembro sintácticamente válido de la lista, lo que genera posibles problemas de interoperabilidad y seguridad si las diferentes implementaciones tienen diferentes comportamientos de manejo de errores.

>Tipo de medio

HTTP utiliza tipos de medios en los campos de encabezado Content-Type y Accept para proporcionar una negociación de tipos y tipificación de datos abierta y extensible. Los tipos de medios definen tanto un formato de datos como varios modelos de procesamiento: cómo procesar esos datos de acuerdo con el contexto del mensaje.

```
media-type = type "/" subtype parameters
type       = token
subtype    = token
```

Los tokens de tipo y subtipo no distinguen entre mayúsculas y minúsculas.

El tipo/subtipo PUEDE ir seguido de parámetros delimitados por punto y coma en forma de pares nombre/valor. La presencia o ausencia de un parámetro puede ser significativa para el procesamiento de un tipo de medio, dependiendo de su definición dentro del registro de tipo de medio. Los valores de los parámetros pueden o no distinguir entre mayúsculas y minúsculas, dependiendo de la semántica del nombre del parámetro.

Por ejemplo, los siguientes tipos de medios son equivalentes a la hora de describir datos de texto HTML codificados en el esquema de codificación de caracteres UTF-8, pero se prefiere el primero por coherencia (el valor del parámetro "charset" se define como insensible a mayúsculas y minúsculas)

```
text/html;charset=utf-8
Text/HTML;Charset="utf-8"
text/html; charset="utf-8"
text/html;charset=UTF-8
```

>Juego de carácteres

HTTP utiliza nombres de "conjunto de caracteres" para indicar o negociar el esquema de codificación de caracteres de una representación textual. En los campos definidos por este documento, los nombres de conjuntos de caracteres aparecen en los parámetros (Content-Type) o, para Accept-Encoding, en forma de un token simple. En ambos casos, los nombres de conjuntos de caracteres se comparan sin distinguir entre mayúsculas y minúsculas.

Nota: En teoría, los nombres de conjuntos de caracteres se definen mediante la regla ABNF "mime-charset".  Esa regla permite dos caracteres que no están incluidos en "token" ("{" y "}"), pero ningún nombre de conjunto de caracteres registrado al momento de escribir este artículo incluye llaves.

>Tipos multiparte

MIME proporciona una serie de tipos "multiparte": encapsulamientos de una o más representaciones dentro de un único cuerpo de mensaje. Todos los tipos multiparte comparten una sintaxis común, e incluyen un parámetro de límite como parte del valor del tipo de medio. El cuerpo del mensaje es en sí mismo un elemento de protocolo; un remitente DEBE generar solo CRLF para representar saltos de línea entre las partes del cuerpo.

El enmarcado de mensajes HTTP no utiliza el límite multiparte como un indicador de la longitud del cuerpo del mensaje, aunque podría ser utilizado por implementaciones que generan o procesan el contenido. Por ejemplo, el tipo "multipart/form-data" se utiliza a menudo para llevar datos de formulario en una solicitud, y el tipo "multipart/byteranges" está definido por esta especificación para su uso en algunas respuestas 206 (Contenido parcial).

>Content-Encoding

El campo de encabezado "Content-Encoding" indica qué codificaciones de contenido se han aplicado a la representación, más allá de las inherentes al tipo de medio, y por lo tanto qué mecanismos de decodificación se deben aplicar para obtener datos en el tipo de medio al que hace referencia el campo de encabezado Content-Type. Content-Encoding se utiliza principalmente para permitir que los datos de una representación se compriman sin perder la identidad de su tipo de medio subyacente.

```
Content-Encoding = #content-coding
```

Un ejemplo de su uso es

```
Content-Encoding: gzip
```

Si se han aplicado una o más codificaciones a una representación, el remitente que aplicó las codificaciones DEBE generar un campo de encabezado Content-Encoding que enumere las codificaciones de contenido en el orden en que se aplicaron. Tenga en cuenta que la codificación denominada "identity" está reservada para su función especial en Accept-Encoding y, por lo tanto, NO DEBE incluirse.

Se puede proporcionar información adicional sobre los parámetros de codificación mediante otros campos de encabezado no definidos por esta especificación.

A diferencia de la codificación de transferencia, las codificaciones que se enumeran en la codificación de contenido son una característica de la representación; la representación se define en términos de la forma codificada, y todos los demás metadatos sobre la representación se refieren a la forma codificada a menos que se indique lo contrario en la definición de metadatos. Normalmente, la representación solo se decodifica justo antes de la renderización o del uso análogo.

Si el tipo de medio incluye una codificación inherente, como un formato de datos que siempre está comprimido, entonces esa codificación no se volvería a indicar en Content-Encoding incluso si resulta ser el mismo algoritmo que una de las codificaciones de contenido. Dicha codificación de contenido solo se incluiría si, por alguna extraña razón, se aplica una segunda vez para formar la representación. Del mismo modo, un servidor de origen puede optar por publicar los mismos datos como múltiples representaciones que difieren solo en si la codificación se define como parte de Content-Type o Content-Encoding, ya que algunos agentes de usuario se comportarán de manera diferente en su manejo de cada respuesta (por ejemplo, abrirán un cuadro de diálogo "Guardar como ..." en lugar de la descompresión y la representación automáticas del contenido).

Un servidor de origen PUEDE responder con un código de estado 415 (Tipo de medio no compatible) si una representación en el mensaje de solicitud tiene una codificación de contenido que no es aceptable.

>Content Codings

Los valores de codificación de contenido indican una transformación de codificación que se ha aplicado o se puede aplicar a una representación. Las codificaciones de contenido se utilizan principalmente para permitir que una representación se comprima o transforme de otro modo de manera útil sin perder la identidad de su tipo de medio subyacente y sin pérdida de información. Con frecuencia, la representación se almacena en forma codificada, se transmite directamente y solo la decodifica el destinatario final.

```
content-coding   = token
```

Todos los códigos de contenido no distinguen entre mayúsculas y minúsculas y deben registrarse en el "Registro de codificación de contenido HTTP". Los valores de codificación de contenido se utilizan en los campos de encabezado ```Accept-Encoding``` y ```Content-Encoding```.

>Compress Coding

La codificación "compress" es una codificación Lempel-Ziv-Welch (LZW) adaptativa [Welch] que se genera comúnmente con el programa de compresión de archivos UNIX "compress". Un destinatario DEBERÍA considerar que "x-compress" es equivalente a "compress".

>Deflate Coding

La codificación "deflate" es un formato de datos "zlib" que contiene un flujo de datos comprimidos "deflate" que utiliza una combinación del algoritmo de compresión Lempel-Ziv (LZ77) y la codificación Huffman.

Nota: Algunas implementaciones no conformes envían los datos comprimidos "deflate" sin el contenedor zlib.

>Gzip Coding

La codificación "gzip" es una codificación LZ77 con una comprobación de redundancia cíclica (CRC) de 32 bits que suele generar el programa de compresión de archivos gzip. El destinatario DEBERÍA considerar que "x-gzip" es equivalente a "gzip".

>Content-Language

El campo de encabezado "Content-Language" describe el idioma o los idiomas naturales de la audiencia a la que se dirige la representación. Tenga en cuenta que esto podría no ser equivalente a todos los idiomas utilizados en la representación.

```
Content-Language = #language-tag
```

El objetivo principal de Content-Language es permitir que un usuario identifique y diferencie las representaciones según su idioma preferido. Por lo tanto, si el contenido está destinado únicamente a un público alfabetizado en español, el campo apropiado es

```
Content-Language: es
```

Si no se especifica ningún idioma de contenido, el valor predeterminado es que el contenido está destinado a todos los idiomas. Esto puede significar que el remitente no lo considera específico para ningún idioma natural o que no sabe a qué idioma está destinado.

PUEDEN incluirse varios idiomas para el contenido destinado a varios públicos. Por ejemplo, una versión del "Tratado de Waitangi", presentada simultáneamente en las versiones originales en maorí e inglés, requeriría

```
Content-Language: mi, en
```

Sin embargo, el hecho de que una representación contenga varios idiomas no significa que esté destinada a distintos públicos lingüísticos. Un ejemplo sería un manual de iniciación lingüística, como "Una primera lección de latín", que claramente está destinado a un público que sepa leer y escribir en inglés. En este caso, Content-Language solo incluiría "en".

Content-Language PUEDE aplicarse a cualquier tipo de medio, no se limita a los documentos textuales.

>Language Tags

Una etiqueta de idioma, identifica un idioma natural hablado, escrito o transmitido de otro modo por seres humanos para la comunicación de información a otros seres humanos. Los lenguajes informáticos están explícitamente excluidos.

HTTP utiliza etiquetas de idioma dentro de los campos de encabezado Accept-Language y Content-Language. Accept-Language utiliza la producción de rango de idioma más amplio, mientras que Content-Language utiliza la producción de etiquetas de idioma definida a continuación.

```
language-tag = <Language-Tag>
```

Una etiqueta de idioma es una secuencia de una o más subetiquetas que no distinguen entre mayúsculas y minúsculas, cada una separada por un guion ("-", %x2D). En la mayoría de los casos, una etiqueta de idioma consta de una subetiqueta de idioma principal que identifica una amplia familia de idiomas relacionados (por ejemplo, "en" = inglés), a la que le sigue opcionalmente una serie de subetiquetas que refinan o limitan el alcance de ese idioma (por ejemplo, "en-CA" = la variedad de inglés que se comunica en Canadá). No se permiten espacios en blanco dentro de una etiqueta de idioma. Algunos ejemplos de etiquetas son

```
fr, en-US, es-419, az-Arab, x-pig-latin, man-Nkoo-GN
```

>Content-Length

El campo de encabezado "Content-Length" indica la longitud de los datos de la representación asociada como un número entero decimal no negativo de octetos. Al transferir una representación como contenido, Content-Length se refiere específicamente a la cantidad de datos incluidos para que se puedan utilizar para delimitar el encuadre. En otros casos, Content-Length indica la longitud actual de la representación seleccionada, que los destinatarios pueden utilizar para estimar el tiempo de transferencia o para comparar con representaciones almacenadas previamente.

```
Content-Length = 1*DIGIT
```

Un ejemplo es

```
Content-Length: 2600
```

Un agente de usuario DEBERÍA enviar Content-Length en una solicitud cuando el método define un significado para el contenido incluido y no está enviando Transfer-Encoding. Por ejemplo, un agente de usuario normalmente envía Content-Length en una solicitud POST incluso cuando el valor es 0 (lo que indica que el contenido está vacío). Un agente de usuario NO DEBERÍA enviar un campo de encabezado Content-Length cuando el mensaje de solicitud no contiene contenido y la semántica del método no anticipa dichos datos.

Un servidor PUEDE enviar un campo de encabezado Content-Length en una respuesta a una solicitud HEAD; un servidor NO DEBE enviar Content-Length en una respuesta de este tipo a menos que el valor de su campo sea igual al número decimal de octetos que se habrían enviado en el contenido de una respuesta si la misma solicitud hubiera utilizado el método GET.

Un servidor PUEDE enviar un campo de encabezado Content-Length en una respuesta 304 (No modificado) a una solicitud GET condicional; un servidor NO DEBE enviar Content-Length en dicha respuesta a menos que el valor de su campo sea igual al número decimal de octetos que se habrían enviado en el contenido de una respuesta 200 (OK) a la misma solicitud.

Un servidor NO DEBE enviar un campo de encabezado Content-Length en ninguna respuesta con un código de estado de 1xx (Informativo) o 204 (Sin contenido). Un servidor NO DEBE enviar un campo de encabezado Content-Length en ninguna respuesta 2xx (Exitosa) a una solicitud CONNECT.

Aparte de los casos definidos anteriormente, en ausencia de Transfer-Encoding, un servidor de origen DEBE enviar un campo de encabezado Content-Length cuando se conoce el tamaño del contenido antes de enviar la sección de encabezado completa. Esto permitirá a los destinatarios posteriores medir el progreso de la transferencia, saber cuándo se completa un mensaje recibido y potencialmente reutilizar la conexión para solicitudes adicionales.

Cualquier valor del campo Content-Length mayor o igual a cero es válido. Dado que no existe un límite predefinido para la longitud del contenido, un destinatario DEBE anticipar posibles números decimales grandes y evitar errores de análisis debido a desbordamientos de conversión de números enteros o pérdida de precisión debido a la conversión de números enteros.

Debido a que Content-Length se utiliza para la delimitación de mensajes en HTTP/1.1, su valor de campo puede afectar la forma en que los destinatarios posteriores analizan el mensaje incluso cuando la conexión inmediata no utiliza HTTP/1.1. Si el mensaje es reenviado por un intermediario posterior, un valor del campo Content-Length que no sea coherente con el encuadre del mensaje recibido puede provocar una falla de seguridad debido al contrabando de solicitudes o la división de respuestas.

Como resultado, un remitente NO DEBE reenviar un mensaje con un valor del campo de encabezado Content-Length que se sabe que es incorrecto.

De manera similar, un remitente NO DEBE reenviar un mensaje con un valor de campo de encabezado Content-Length que no coincida con el ABNF anterior, con una excepción: un destinatario de un valor de campo de encabezado Content-Length que consiste en el mismo valor decimal repetido como una lista separada por comas (por ejemplo, "Content-Length: 42, 42") PUEDE rechazar el mensaje como inválido o reemplazar ese valor de campo inválido con una sola instancia del valor decimal, ya que esto probablemente indica que un procesador de mensajes ascendente generó o combinó un duplicado.

>Content-Location

El campo de encabezado "Content-Location" hace referencia a un URI que se puede utilizar como identificador de un recurso específico correspondiente a la representación en el contenido de este mensaje. En otras palabras, si se realiza una solicitud GET en este URI en el momento de la generación de este mensaje, la respuesta 200 (OK) contendría la misma representación que se incluye como contenido en este mensaje.

```
Content-Location = absolute-URI / partial-URI
```

El valor del campo es un URI absoluto o un URI parcial. En el último caso, el URI al que se hace referencia es relativo al URI de destino.

El valor Content-Location no reemplaza al URI de destino. Son metadatos de representación. Tiene la misma sintaxis y semántica que el campo de encabezado del mismo nombre definido para las partes del cuerpo MIME. Sin embargo, su aparición en un mensaje HTTP tiene algunas implicaciones especiales para los destinatarios HTTP.

Si Content-Location se incluye en un mensaje de respuesta 2xx (correcto) y su valor hace referencia (después de la conversión a la forma absoluta) a un URI que es el mismo que el URI de destino, entonces el destinatario PUEDE considerar que el contenido es una representación actual de ese recurso en el momento indicado por la fecha de origen del mensaje. En el caso de una solicitud GET (Sección 9.3.1) o HEAD (Sección 9.3.2), esto es lo mismo que la semántica predeterminada cuando el servidor no proporciona una ubicación de contenido. En el caso de una solicitud que cambia el estado, como PUT (Sección 9.3.4) o POST (Sección 9.3.3), implica que la respuesta del servidor contiene la nueva representación de ese recurso, lo que la distingue de las representaciones que solo podrían informar sobre la acción (por ejemplo, "¡Funcionó!"). Esto permite que las aplicaciones de creación actualicen sus copias locales sin la necesidad de una solicitud GET posterior.

Si se incluye Content-Location en un mensaje de respuesta 2xx (correcto) y el valor de su campo hace referencia a un URI que difiere del URI de destino, el servidor de origen afirma que el URI es un identificador de un recurso diferente correspondiente a la representación incluida. Solo se puede confiar en esta afirmación si ambos identificadores comparten el mismo propietario del recurso, que no se puede determinar mediante programación a través de HTTP.

* En el caso de una respuesta a una solicitud GET o HEAD, esto es una indicación de que el URI de destino hace referencia a un recurso que está sujeto a la negociación de contenido y el valor del campo Content-Location es un identificador más específico para la representación seleccionada.
* En el caso de una respuesta 201 (Created) a un método de cambio de estado, un valor del campo Content-Location que sea idéntico al valor del campo Location indica que este contenido es una representación actual del recurso recién creado.
* De lo contrario, un Content-Location de este tipo indica que este contenido es una representación que informa sobre el estado de la acción solicitada y que el mismo informe está disponible (para un acceso futuro con GET) en el URI indicado. Por ejemplo, una transacción de compra realizada a través de una solicitud POST podría incluir un documento de recibo como contenido de la respuesta 200 (OK); el valor del campo Content-Location proporciona un identificador para recuperar una copia de ese mismo recibo en el futuro.

Un agente de usuario que envía Content-Location en un mensaje de solicitud está indicando que su valor se refiere al lugar donde el agente de usuario obtuvo originalmente el contenido de la representación adjunta (antes de cualquier modificación realizada por ese agente de usuario). En otras palabras, el agente de usuario está proporcionando un vínculo de retorno a la fuente de la representación original.

Un servidor de origen que recibe un campo Content-Location en un mensaje de solicitud DEBE tratar la información como un contexto de solicitud transitorio en lugar de como metadatos que se guardarán textualmente como parte de la representación. Un servidor de origen PUEDE usar ese contexto para guiar el procesamiento de la solicitud o para guardarla para otros usos, como dentro de los vínculos de origen o los metadatos de control de versiones. Sin embargo, un servidor de origen NO DEBE usar dicha información de contexto para alterar la semántica de la solicitud.

Por ejemplo, si un cliente realiza una solicitud PUT sobre un recurso negociado y el servidor de origen acepta esa solicitud PUT (sin redirección), se espera que el nuevo estado de ese recurso sea coherente con la representación suministrada en esa solicitud PUT; la ubicación del contenido no se puede utilizar como una forma de identificador de selección de contenido inverso para actualizar solo una de las representaciones negociadas. Si el agente de usuario hubiera deseado esta última semántica, habría aplicado la solicitud PUT directamente al URI de ubicación del contenido.

>Campos de validación

Los metadatos de recursos se denominan "validadores" si se pueden utilizar dentro de una condición previa para realizar una solicitud condicional. Los campos de validación transmiten un validador actual para la representación seleccionada.

En las respuestas a solicitudes seguras, los campos de validación describen la representación seleccionada elegida por el servidor de origen al procesar la respuesta. Tenga en cuenta que, según la semántica del método y del código de estado, la representación seleccionada para una respuesta determinada no es necesariamente la misma que la representación incluida como contenido de la respuesta.

En una respuesta exitosa a una solicitud de cambio de estado, los campos de validación describen la nueva representación que ha reemplazado a la representación seleccionada previamente como resultado del procesamiento de la solicitud.

Por ejemplo, un campo ETag en una respuesta 201 (Created) comunica la etiqueta de entidad de la representación del recurso recién creado, de modo que la etiqueta de entidad se pueda utilizar como un validador en solicitudes condicionales posteriores para evitar el problema de "actualización perdida".

Esta especificación define dos formas de metadatos que se utilizan comúnmente para observar el estado del recurso y comprobar las condiciones previas: fechas de modificación y etiquetas de entidad opacas. Se han definido metadatos adicionales que reflejan el estado del recurso mediante varias extensiones de HTTP, como Web Distributed Authoring and Versioning [WEBDAV], que están fuera del alcance de esta especificación.

>Débil vs Fuerte

Los validadores se presentan en dos tipos: fuertes o débiles. Los validadores débiles son fáciles de generar, pero son mucho menos útiles para las comparaciones. Los validadores fuertes son ideales para las comparaciones, pero pueden ser muy difíciles (y en ocasiones imposibles) de generar de manera eficiente. En lugar de imponer que todas las formas de recursos se adhieran a la misma fuerza del validador, HTTP expone el tipo de validador en uso e impone restricciones sobre cuándo se pueden usar validadores débiles como condiciones previas.

Un "validador fuerte" son metadatos de representación que cambian de valor cada vez que se produce un cambio en los datos de representación que serían observables en el contenido de una respuesta 200 (OK) a GET.

Un validador fuerte puede cambiar por razones distintas a un cambio en los datos de representación, como cuando se cambia una parte semánticamente significativa de los metadatos de representación (por ejemplo, Content-Type), pero lo mejor para el servidor de origen es cambiar el valor solo cuando sea necesario para invalidar las respuestas almacenadas en cachés remotos y herramientas de creación.

Las entradas de caché pueden persistir durante períodos arbitrarios, independientemente de los tiempos de expiración. Por lo tanto, una caché puede intentar validar una entrada utilizando un validador que obtuvo en un pasado lejano. Un validador fuerte es único en todas las versiones de todas las representaciones asociadas con un recurso en particular a lo largo del tiempo. Sin embargo, no hay implicación de unicidad entre representaciones de diferentes recursos (es decir, el mismo validador fuerte puede estar en uso para representaciones de múltiples recursos al mismo tiempo y no implica que esas representaciones sean equivalentes).

En la práctica, se utilizan diversos validadores potentes. Los mejores se basan en un estricto control de revisión, en el que cada cambio en una representación siempre da como resultado la asignación de un nombre de nodo y un identificador de revisión únicos antes de que la representación se haga accesible a GET. Una función hash resistente a colisiones aplicada a los datos de la representación también es suficiente si los datos están disponibles antes de que se envíen los campos del encabezado de respuesta y no es necesario volver a calcular el resumen cada vez que se recibe una solicitud de validación. Sin embargo, si un recurso tiene representaciones distintas que difieren solo en sus metadatos, como podría ocurrir con la negociación de contenido sobre tipos de medios que comparten el mismo formato de datos, entonces el servidor de origen debe incorporar información adicional en el validador para distinguir esas representaciones.

Por el contrario, un "validador débil" son metadatos de representación que podrían no cambiar con cada cambio en los datos de representación. Esta debilidad podría deberse a limitaciones en la forma en que se calcula el valor (por ejemplo, la resolución del reloj), una incapacidad para garantizar la unicidad de todas las representaciones posibles del recurso o un deseo del propietario del recurso de agrupar las representaciones por un conjunto autodeterminado de equivalencias en lugar de secuencias únicas de datos.

Un servidor de origen DEBERÍA cambiar una etiqueta de entidad débil siempre que considere que las representaciones anteriores son inaceptables como sustituto de la representación actual. En otras palabras, una etiqueta de entidad débil debería cambiar siempre que el servidor de origen desee que los cachés invaliden las respuestas antiguas.

Por ejemplo, la representación de un informe meteorológico cuyo contenido cambia cada segundo, según mediciones dinámicas, podría agruparse en conjuntos de representaciones equivalentes (desde la perspectiva del servidor de origen) con el mismo validador débil para permitir que las representaciones almacenadas en caché sean válidas durante un período de tiempo razonable (quizás ajustado dinámicamente según la carga del servidor o la calidad del clima). De la misma manera, el tiempo de modificación de una representación, si se define con una resolución de solo un segundo, podría ser un validador débil si es posible modificar la representación dos veces durante un solo segundo y recuperarla entre esas modificaciones.

De la misma manera, un validador es débil si es compartido por dos o más representaciones de un recurso determinado al mismo tiempo, a menos que esas representaciones tengan datos de representación idénticos. Por ejemplo, si el servidor de origen envía el mismo validador para una representación con una codificación de contenido gzip aplicada que para una representación sin codificación de contenido, entonces ese validador es débil. Sin embargo, dos representaciones simultáneas pueden compartir el mismo validador fuerte si difieren solo en los metadatos de la representación, como cuando hay dos tipos de medios diferentes disponibles para los mismos datos de representación.

Los validadores fuertes se pueden usar para todas las solicitudes condicionales, incluidas la validación de caché, los rangos de contenido parcial y la prevención de "actualizaciones perdidas". Los validadores débiles solo se pueden usar cuando el cliente no requiere una igualdad exacta con los datos de representación obtenidos previamente, como cuando se valida una entrada de caché o se limita un recorrido web a cambios recientes.

>Last-Modified

El campo de encabezado Last-Modified en una respuesta, proporciona una marca de tiempo que indica la fecha y la hora en que el servidor de origen cree que la representación seleccionada se modificó por última vez, según lo determinado al concluir el procesamiento de la solicitud.

```
Last-Modified = HTTP-date
```

Un ejemplo es

```
Last-Modified: Tue, 15 Nov 1994 12:45:26 GMT
```

>Generación

Un servidor de origen DEBERÍA enviar Last-Modified para cualquier representación seleccionada para la cual se pueda determinar de manera razonable y consistente una fecha de última modificación, ya que su uso en solicitudes condicionales y la evaluación de la frescura de la memoria caché puede reducir sustancialmente las transferencias innecesarias y mejorar significativamente la disponibilidad y la escalabilidad del servicio.

Una representación es típicamente la suma de muchas partes detrás de la interfaz de recursos. La hora de la última modificación normalmente sería la hora más reciente en que se modificó cualquiera de esas partes. La forma de algoritmo en que se determina ese valor para cualquier recurso dado es un detalle de implementación que está fuera del alcance de esta especificación.

Un servidor de origen DEBERÍA obtener el valor de Última modificación de la representación lo más cerca posible del momento en que genera el valor del campo Fecha para su respuesta. Esto permite que un destinatario haga una evaluación precisa del momento de modificación de la representación, especialmente si la representación cambia cerca del momento en que se genera la respuesta.

Un servidor de origen con un reloj NO DEBE generar una fecha de Última modificación que sea posterior a la hora de origen del mensaje del servidor. Si la hora de la última modificación se deriva de metadatos específicos de la implementación que evalúan algún momento en el futuro, según el reloj del servidor de origen, entonces el servidor de origen DEBE reemplazar ese valor con la fecha de origen del mensaje. Esto evita que una fecha de modificación futura tenga un impacto adverso en la validación de la memoria caché.

Un servidor de origen sin un reloj NO DEBE generar una fecha de última modificación para una respuesta, a menos que ese valor de fecha haya sido asignado al recurso por algún otro sistema (presumiblemente uno con un reloj).

>Comparación

Una hora de Última modificación, cuando se utiliza como validador en una solicitud, es implícitamente débil a menos que sea posible deducir que es fuerte, utilizando las siguientes reglas:

* El validador está siendo comparado por un servidor de origen con el validador actual real para la representación y,
* Ese servidor de origen sabe de manera confiable que la representación asociada no cambió dos veces durante el segundo cubierto por el validador presentado;

o

* El cliente va a utilizar el validador en un campo de encabezado If-Modified-Since, If-Unmodified-Since o If-Range, ya que el cliente tiene una entrada de caché para la representación asociada y
* Esa entrada de caché incluye un valor de Fecha que es al menos un segundo posterior al valor de Última modificación y el cliente tiene motivos para creer que fueron generados por el mismo reloj o que hay suficiente diferencia entre los valores de Última modificación y Fecha para hacer que los problemas de sincronización del reloj sean poco probables.

Este método se basa en el hecho de que si el servidor de origen envió dos respuestas diferentes durante el mismo segundo, pero ambas tenían la misma hora de última modificación, entonces al menos una de esas respuestas tendría un valor de Fecha igual a su hora de última modificación.

>ETag

El campo "ETag" en una respuesta, proporciona la etiqueta de entidad actual para la representación seleccionada, tal como se determina al concluir el procesamiento de la solicitud. 

Una etiqueta de entidad es un validador opaco para diferenciar entre múltiples representaciones del mismo recurso, independientemente de si esas múltiples representaciones se deben a cambios en el estado del recurso a lo largo del tiempo, a la negociación de contenido que da como resultado que múltiples representaciones sean válidas al mismo tiempo, o a ambos. Una etiqueta de entidad consiste en una cadena opaca entre comillas, posiblemente precedida por un indicador de debilidad.

```
ETag       = entity-tag

entity-tag = [ weak ] opaque-tag
weak       = %s"W/"
opaque-tag = DQUOTE *etagc DQUOTE
etagc      = %x21 / %x23-7E / obs-text
           ; VCHAR except double quotes, plus obs-text
```

Nota: Anteriormente, opaque-tag se definía como una cadena entre comillas, por lo tanto, algunos destinatarios podrían realizar un anulación del escape de la barra invertida, lo que hace que los servidores deban evitar los caracteres de barra invertida en las etiquetas de entidad.

Una etiqueta de entidad puede ser más confiable para la validación que una fecha de modificación en situaciones en las que es inconveniente almacenar fechas de modificación, donde la resolución de un segundo de los valores de fecha HTTP no es suficiente o donde las fechas de modificación no se mantienen de manera consistente.

Ejemplos:

```
ETag: "xyzzy"
ETag: W/"xyzzy"
ETag: ""
```

Una etiqueta de entidad puede ser un validador débil o fuerte, siendo fuerte el valor predeterminado. Si un servidor de origen proporciona una etiqueta de entidad para una representación, y la generación de esa etiqueta de entidad no satisface todas las características de un validador fuerte, entonces el servidor de origen DEBE marcar la etiqueta de entidad como débil anteponiendo su valor opaco con "W/" (distinguible entre mayúsculas y minúsculas).

Un remitente PUEDE enviar el campo ETag en una sección de tráiler. Sin embargo, dado que los tráileres a menudo se ignoran, es preferible enviar ETag como un campo de encabezado a menos que la etiqueta de entidad se genere mientras se envía el contenido.

>Generación

El principio detrás de las etiquetas de entidad es que solo el autor del servicio conoce la implementación de un recurso lo suficientemente bien como para seleccionar el mecanismo de validación más preciso y eficiente para ese recurso, y que cualquier mecanismo de ese tipo se puede asignar a una secuencia simple de octetos para una comparación sencilla. Dado que el valor es opaco, no es necesario que el cliente sepa cómo se construye cada etiqueta de entidad.

Por ejemplo, un recurso que tiene una versión específica de la implementación aplicada a todos los cambios podría usar un número de revisión interno, quizás combinado con un identificador de variación para la negociación de contenido, para diferenciar con precisión entre representaciones. Otras implementaciones podrían usar un hash resistente a colisiones del contenido de la representación, una combinación de varios atributos de archivo o una marca de tiempo de modificación que tenga una resolución inferior a un segundo.

Un servidor de origen DEBE enviar una ETag para cualquier representación seleccionada, para la cual se pueda determinar de manera razonable y consistente la detección de cambios, ya que el uso de la etiqueta de entidad en solicitudes condicionales y la evaluación de la frescura de la caché, puede reducir sustancialmente transferencias innecesarias y mejorar significativamente la disponibilidad, escalabilidad y confiabilidad del servicio.

>Comparación

Existen dos funciones de comparación de etiquetas de entidad, dependiendo de si el contexto de comparación permite o no el uso de validadores débiles:

* "Comparación fuerte":
dos etiquetas de entidad son equivalentes si ambas no son débiles y sus etiquetas opacas coinciden carácter por carácter.
* "Comparación débil":
dos etiquetas de entidad son equivalentes si sus etiquetas opacas coinciden carácter por carácter, independientemente de que una o ambas estén etiquetadas como "débiles".

El siguiente ejemplo muestra los resultados para un conjunto de pares de etiquetas de entidad y los resultados de la función de comparación fuerte y débil:

```
ETag 1	ETag 2	Strong Comparison   Weak Comparison
W/"1"	W/"1"	no match	    match
W/"1"	W/"2"	no match	    no match
W/"1"	"1"	no match	    match
"1"	"1"	match	            match
```

Ejemplo: Etiquetas de entidad que varían según los recursos negociados por contenido.

Considerar un recurso que está sujeto a la negociación de contenido, y donde las representaciones enviadas en respuesta a una solicitud GET varían según el campo de encabezado de solicitud Accept-Encoding.

* Solicitud:

```
GET /index HTTP/1.1
Host: www.example.com
Accept-Encoding: gzip
```

En este caso, la respuesta podría o no utilizar la codificación de contenido gzip. Si no lo hace, la respuesta podría verse así:

* Respuesta:

```
HTTP/1.1 200 OK
Date: Fri, 26 Mar 2010 00:05:00 GMT
ETag: "123-a"
Content-Length: 70
Vary: Accept-Encoding
Content-Type: text/plain

Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
```

Una representación alternativa que utiliza codificación de contenido gzip sería:

* Respuesta:

```
HTTP/1.1 200 OK
Date: Fri, 26 Mar 2010 00:05:00 GMT
ETag: "123-b"
Content-Length: 43
Vary: Accept-Encoding
Content-Type: text/plain
Content-Encoding: gzip

...binary data...
```

Nota: Las codificaciones de contenido son una propiedad de los datos de representación, por lo que una etiqueta de entidad fuerte para una representación codificada por contenido tiene que ser distinta de la etiqueta de entidad de una representación no codificada para evitar posibles conflictos durante las actualizaciones de caché y las solicitudes de rango. Por el contrario, las codificaciones de transferencia se aplican solo durante la transferencia de mensajes y no dan como resultado etiquetas de entidad distintas.

>Métodos

