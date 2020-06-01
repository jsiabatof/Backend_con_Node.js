Stack de seguridad moderno

Anteriormente las compañías se comunicaban mediante un intranet que actualmente ha sido reemplazado con un stack de seguridad moderno usando:
•	JSON Web Tokens: Nos permite comunicarnos entre dos clientes de una manera más segura.
•	OAuth 2.0: Un estándar de la industria que nos permite implementar autorización.
•	OpenID Connect: Es una capa de autenticación que funciona por encima de Oauth 2.0.

---------------------

¿Qué es la autenticación y la autorización ?

La autenticación sirve para verificar la identidad de un usuario, verificar si el usuario existe y si los datos que está colocando son correctos.
La autorización es la acción de permitir a un usuario acceso limitado a nuestro recursos.

---------------------

Introducción a las sesiones

Cuando visitamos un sitio por primera vez se crea una sesión con los ajustes que se configuran. Por ejemplo, en un sitio web de reserva de hoteles, a medida que buscamos y ponemos preferencias de precios y demás, éstas se irán guardando en dicha sesión. Y luego estos datos se convertirán en un ID que será almacenado en una cookie en tu navegador.

----------------------

Anatomía de un JWT

JWT es un estándar de la industria que nos permite manejar demandas de información entre dos clientes.

---------------------

Autenticación tradicional vs JWT

Cuando usamos una autenticación tradicional se crea una sesión y el ID de esa sesión se almacena en una cookie del navegador, pero cuando utilizamos JWT firmamos un token y este se guarda en el navegador el cual permite a una SPA actualizarse sin refrescar la ventana.

----------------------------

Firmando y Verificando nuestro JWT

Para firmar nuestro token utilizaremos un paquete de node llamado jsonwebtoken y al usarlo en nuestro código se verá de esta manera:

jwt.sign({ sub: user.id }, 'secret', options);

El primer atributo que recibe es el payload o sea los datos que guardaremos en ese token. De segundo atributo recibe una clave secreta con la cual será firmado y finalmente podremos pasarle opciones si es nuestro caso.
Para verificar nuestro token lo haremos de la siguiente manera:

jwt.verify(token, 'secret', function(err, decoded){});

Como primer atributo recibiremos el token, de segundo atributo el secreto de la firma y como tercer argumento (opcional) recibiremos el token decodificado.

•	Vamos a inicializar nuestro proyecto con npm init -y
•	Crearemos el archivo index.js
•	Vamos a instalar los paquetes necesarios con npm i jsonwebtoken
•	En el index.js vamos a hacer toda la lógica de nuestra aplicación

---------------------------

Server-side vs Client-side sessions

Sesiones del lado del servidor vs sesiones del lado del cliente

¿Qué es una sesión?

En terminos generales una sesion es una manera de preservar un estado deseado.

¿Qué es una sesion del lado del servidor?

La sesión en el lado del servidor suele ser una pieza de información que se guarda en memoria o en una base de datos y esta permite hacerle seguimiento a la información de autenticación, con el fin de identificar al usuario y determinar cuál es el estado de autenticación. Mantener la sesión de esta manera en el lado del servidor es lo que se considera “stateful”, es decir que maneja un estado.

¿Qué es una sesión del lado del cliente?

Las SPA (Single-page apps) requieren una manera de saber si el usuario esta autenticado o no. Pero esto no se puede hacer de una manera tradicional porque suelen ser muy desacopladas con el backend y no suelen refrescar la página como lo hacen las aplicaciones renderizadas en el servidor.

JWT (JSON Web Token) es un mecanismo de autenticación sin estado, lo que conocemos como “stateless”. Lo que significa que no hay una sesión que exista del lado del servidor.

La manera como se comporta la sesión del lado del cliente es:

• Cuando el usuario hace “login” agregamos una bandera para indicar que lo esta.
• En cualquier punto de la aplicación verificamos la expiración del token.
• Si el token expira, cambiamos la bandera para indicar que el usuario no está logueado.
• Se suele chequear cuando la ruta cambia.
• Si el token expiró lo redireccionamos a la ruta de “login” y actualizamos el estado como “logout”.
• Se actualiza la UI para mostrar que el usuario ha cerrado la sesión.

-----------------------

Buenas prácticas con JWT

Buenas practicas con JSON Web token

En los últimos años se ha criticado fuertemente el uso de JSON Web Tokens como buena practica de seguridad. La realidad es que muchas compañías hoy en día los usan sin ningún problema siguiendo unas buenas practicas de seguridad, que aseguran su uso sin ningún inconveniente.

A continuación listaremos unos consejos que se deben tener en cuenta:

Evitar almacenar información sensible

Debido a que los JSON Web tokens son decodificables es posible visualizar la información del payload, por lo que ningún tipo de información sensible debe ser expuesto como contraseñas, keys, etc. Tampoco debería agregarse información confidencial del usuario como su numero de identificación o información medica, ya que como hablamos anteriormente, los hackers pueden usar esta información para hacer ingeniería social.

Mantener su peso lo más liviano posible

Suele tenerse la tentación de guardar toda la información del perfil en el payload del JWT, pero esto no debería hacerse ya que necesitamos que el JWT sea lo más pequeño posible debido a que al enviarse con todos los request estamos consumiendo parte del ancho de banda.

Establecer un tiempo de expiración corto

Debido a que los tokens pueden ser robados si no se toman las medidas correctas de almacenamiento seguro, es muy importante que estos tengan unas expiración corta, el tiempo recomendado es desde 15 minutos hasta un maximo de 2 horas.

Tratar los JWT como tokens opacos

Aunque los tokens se pueden decodificar, deben tratarse como tokens opacos, es decir como si no tuviesen ningún valor legible. Esto es porque desde el lado del cliente no tenemos manera de verificar si la firma es correcta, así que si confiamos en la información decodificada del token, alguien podría introducir un token invalido con otra información a propósito. Lo mejor, es siempre enviar el token del lado del servidor y hacer las verificaciones allí.

¿Donde guardar los tokens?

Cuando estamos trabajando con SPA (Single Page apps) debemos evitar almacenar los tokens en Local Storage o Session Storage. Estos deben ser almacenados en memoria o en una Cookie, pero solo de manera segura y con el flag httpOnly, esto quiere decir que la cookie debe venir del lado del servidor con el token almacenado. Más información: https://auth0.com/docs/security/store-tokens#single-page-apps

Silent authenticacion vs Refresh tokens

Debido a que es riesgoso almacenar tokens del lado del cliente, no se deberian usar Refresh Tokens cuando se trabaja solo con una SPA. Lo que se debe implementar es Silent Authentication, para ello se debe seguir el siguiente flujo:

• La SPA obtiene un access token al hacer login o mediante cualquier flujo de OAuth.
• Cuando el token expira el API retornara un error 401.
• En este momento se debe detectar el error y hacer un request para obtener de nuevo un access token.
• Si nuestro backend server tiene una sesión valida (Se puede usar una cookie) entonces respondemos con un nuevo access token.

Más información:

• https://auth0.com/docs/api-auth/tutorials/silent-authentication
• https://auth0.com/docs/tokens/refresh-token/current

Hay que tener en cuenta que para implementar Silent authentication y Refresh tokens, se require tener un tipo de sesión valida del lado del servidor por lo que en una SPA es posible que sea necesario una especie de backend-proxy, ya que la sesión no debería convivir en el lado del API server.

En el paso 2, si se esta usando alguna librería para manejo de estado como redux, se puede implementar un middleware que detecte este error y proceda con el paso 3.

-----------------------

