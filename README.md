# Tarea 7

##Pool de Conexiones
En computación se denomina *connection pool* al manejo de conexiones abiertas a una base de datos de manera que puedan ser reutilizadas al realizar múltiples consultas.

El pool de conexiones consiste en tener un conjunto (pool) de conexiones ya conectadas a la base de datos que puedan ser reutilizadas entre distintas peticiones.

Veamos como funciona:

* El servidor web tiene “n” conexiones ya creadas y conectadas a la base de datos (Se llaman conexiones esperando)
* Cuando llegan “m” peticines web , la aplicación pide “m” conexiones al pool de conexiones,quedando esperando en el pool “n-m” conexiones. Esta operación es muy rápida ya que la conexión ya está creada y solo hay que marcarla como que alguien la está usando. Ahora hay “m” conexiones activas que está usando la aplicación.
* Cuando las peticiones web finalizan, las conexiones no se cierran sino que se devuelven al pool indicándole que ya se han acabado de usar las conexiones. Ahora vuelve a quedar “n” conexiones esperando en el pool. Esta operación también es muy rápida ya que realmente no se cierra ninguna conexión sino que simplemente se marcan como que ya no las están usando nadie.
* Si se piden más conexiones de las que hay esperando en el pool se crearán en ese instante nuevas conexiones hasta el máximo de conexiones que permita el pool
* Al devolver una conexión al pool , ésta se queda esperando para que otra petición la pueda usar. Si hay ya demasiadas conexiones esperando a ser usadas se cerrarán para ahorrar recursos en el servidor de base de datos.
### Ejemplo con Java.sql
```java
DataSource dataSource;
Connection connection=dataSource.getConnection(); //Obtener la conexión del pool
//Usar la conexión
connection.close(); //No se cierra realmente la conexión sino que se retorna al pool
```
### Ejemplo usando Spring
Configuramos la conexión en `application.properties` y esto generará un Bean que manejará las conexiones a la base de datos.
```
spring.datasource.url=jdbc:mysql://localhost/demo
spring.datasource.username=user
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

##Algoritmo SHA256
El SHA *Secure Hash Algorithm, Algoritmo de Hash Seguro* es una familia de funciones hash de cifrado publicadas por el Instituto Nacional de Normas y Tecnología (**NIST**) de EE. UU. La primera versión del algoritmo se creó en 1993 con el nombre de *SHA*, aunque en la actualidad se la conoce como *SHA-0* para evitar confusiones con las versiones posteriores. La segunda versión del sistema, publicada con el nombre de *SHA-1*, fue publicada dos años más tarde. Posteriormente se han publicado *SHA-2* en 2001 (formada por diversas funciones: *SHA-224*, *SHA-256*, *SHA-384*, y *SHA-512*) y la más reciente, *SHA-3*, que fue seleccionada en una competición de funciones hash celebrada por el NIST en 2012. Esta última versión se caracteriza por ser la que más difiere de sus predecesoras.

SHA es una de las muchas funciones hash. Una función hash es como una firma para un texto o fichero. SHA-256 es un hash de 64 dígitos hexadecimales, por ejemplo:  `bd4526534df7b33772c2f1ee26d97c39ff11379c8848e4e19d74ad849ef66423` casi único de un tamaño fijo de 256 bits (32 bytes). Un hash solo se calcula en una dirección y no se puede decodificar de vuelta.
SHA-2 se utiliza en un gran número de herramientas de seguridad y protocolos. Algunos de ellos son:
* TLS
* SSL
* PGP
* SSH
* S/MIME
* IPsec
* Bitcoin.
En el protocolo Bitcoin, SHA-256 se utiliza en la creación de claves o direcciones públicas y en la minería de Bitcoin.


##Algoritmo MD5
En criptografía, MD5 *Message-Digest Algorithm 5, Algoritmo de Resumen del Mensaje 5* es un algoritmo de reducción criptográfico de 128 bits ampliamente usado. Uno de sus usos es el de comprobar que algún archivo no haya sido modificado.

MD5 es uno de los algoritmos de reducción criptográficos diseñados por el profesor **Ronald Rivest** del MIT. Fue desarrollado en 1991 como reemplazo del algoritmo MD4 después de que **Hans Dobbertin** descubriese su debilidad.

A pesar de su amplia difusión actual, la sucesión de problemas de seguridad detectados desde que, en 1996, **Hans Dobbertin** anunciase una colisión de hash, plantea una serie de dudas acerca de su uso futuro.
Ciertas investigaciones han revelado vulnerabilidades que hacen cuestionable el uso futuro del MD5. En agosto de 2004, **Xiaoyun Wang**, **Dengguo Feng**, **Xuejia Lai** y **Hongbo Yu** anunciaron el descubrimiento de colisiones de hash para MD5. Su ataque se consumó en una hora de cálculo con un clúster IBM P690.
Aunque dicho ataque era analítico, el tamaño del hash (128 bits) es lo suficientemente pequeño como para que resulte vulnerable frente a ataques de *fuerza bruta* tipo *cumpleaños*. El proyecto de computación distribuida MD5CRK arrancó en marzo de 2004 con el propósito de demostrar que MD5 es inseguro frente a uno de tales ataques, aunque acabó poco después del aviso de la publicación de la vulnerabilidad del equipo de Wang.
Debido al descubrimiento de métodos sencillos para generar colisiones de hash, muchos investigadores recomiendan su sustitución por algoritmos alternativos tales como `SHA-256` o `RIPEMD-160`. Algunos de estos métodos para generar colisiones (como el de Wang) tienen su implementación disponible en la red: Implementación de colisión en MD5.

##Inyección de SQL
Inyección SQL es un método de infiltración de código intruso que se vale de una vulnerabilidad informática presente en una aplicación en el nivel de validación de las entradas para realizar operaciones sobre una base de datos.

El origen de la vulnerabilidad radica en el incorrecto chequeo o filtrado de las variables utilizadas en un programa que contiene, o bien genera, código SQL. Es, de hecho, un error de una clase más general de vulnerabilidades que puede ocurrir en cualquier lenguaje de programación o script que esté embebido dentro de otro.

Se conoce como Inyección SQL, indistintamente, al tipo de vulnerabilidad, al método de infiltración, al hecho de incrustar código SQL intruso y a la porción de código incrustado.

###Ejemplo en un Login
```Java
String query = "SELECT * FROM Login where username ="+username+" AND password = "+password;
```
Si hacemos un input de password con código malintencionado tipo:
```Java
String password = "dummydata OR 0=0;";
```
La consulta nos devolverá todos los elementos de la tabla Login (username, password) de todos los registros.
Para evitar esto se emplean los PreparedStatement en JDBC.

```java
Connection con = (acquire Connection) //Conexión del pool de conexiones.
PreparedStatement pstmt = con.prepareStatement("SELECT * FROM Login WHERE username = ? AND password = ? ");
pstmt.setString(1, username);
pstmt.setString(2, password);
ResultSet rset = pstmt.executeQuery();
```
##Servicio REST
**REST** cambió por completo la ingeniería de software a partir del 2000. Este nuevo enfoque de desarrollo de proyectos y servicios web fue definido por **Roy Fielding**, el padre de la especificación HTTP y uno los referentes internacionales en todo lo relacionado con la Arquitectura de Redes, en su disertación *Architectural Styles and the Design of Network-based Software Architectures*. En el campo de las APIs, REST (*Representational State Transfer- Transferencia de Estado Representacional*) es lo más usado.

Buscando una definición sencilla, **REST** es cualquier interfaz entre sistemas que use HTTP para obtener datos o generar operaciones sobre esos datos en todos los formatos posibles, como XML y JSON. Es una alternativa en auge a otros protocolos estándar de intercambio de datos como **SOAP** (*Simple Object Access Protocol*), que disponen de una gran capacidad pero también mucha complejidad. A veces es preferible una solución más sencilla de manipulación de datos como **REST**.

Características:
* Protocolo cliente/servidor sin estado: cada petición HTTP contiene toda la información necesaria para ejecutarla, lo que permite que ni cliente ni servidor necesiten recordar ningún estado previo para satisfacerla.
*  Las operaciones más importantes relacionadas con los datos en cualquier sistema REST y la especificación HTTP son cuatro: POST (crear), GET (leer y consultar), PUT (editar) y DELETE (eliminar).
* Los objetos en REST siempre se manipulan a partir de la URI. Es la URI y ningún otro elemento el identificador único de cada recurso de ese sistema REST. La URI nos facilita acceder a la información para su modificación o borrado, o, por ejemplo, para compartir su ubicación exacta con terceros.
*  Interfaz uniforme: para la transferencia de datos en un sistema REST, este aplica acciones concretas (POST, GET, PUT y DELETE) sobre los recursos, siempre y cuando estén identificados con una URI. Esto facilita la existencia de una interfaz uniforme que sistematiza el proceso con la información.

Ventajas:
*  Separación entre el cliente y el servidor: el protocolo REST separa totalmente la interfaz de usuario del servidor y el almacenamiento de datos.
*  Visibilidad, fiabilidad y escalabilidad. La separación entre cliente y servidor tiene una ventaja evidente y es que cualquier equipo de desarrollo puede escalar el producto sin excesivos problemas.
* La API REST siempre es independiente del tipo de plataformas o lenguajes: la API REST siempre se adapta al tipo de sintaxis o plataformas con las que se estén trabajando, lo que ofrece una gran libertad a la hora de cambiar o probar nuevos entornos dentro del desarrollo. Con una API REST se pueden tener servidores PHP, Java, Python o Node.js. Lo único que es indispensable es que las respuestas a las peticiones se hagan siempre en el lenguaje de intercambio de información usado, normalmente XML o JSON.

Actualmente la mayoría de las compañias tienen servicios de consumo REST para los desarrolladores: Twitter, Facebook, Bitso, Bancos, INEGI, INE, datos.gob.mx, etc.
