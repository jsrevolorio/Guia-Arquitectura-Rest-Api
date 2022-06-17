[<img src="https://ws.apms.io/api/_files/NydJSQz2pxfUmD5yTEe2FR/download/" width="800" height="400" />](dedde)

# Guía de arquitectura REST API
<br/>

### **API**
Una API (interfaz de programación de aplicaciones) es un conjunto de definiciones y protocolos que se usa para diseñar e integrar el software de las aplicaciones. Son construcciones disponibles en los lenguajes de programación que permiten a los desarrolladores crear funcionalidades complejas de una manera simple. Las API permiten que sus productos y servicios se comuniquen con otros, sin necesidad de saber cómo están implementados.


### **REST**
REST (“Representational State Transfer” o traducido “Transferencia de presentación de estado”) es una técnica de arquitectura de software usada para construir APIs que permitan comunicar a nuestro servidor con sus clientes usando el **`protocolo HTTP`**. La arquitectura REST se basa en que el cliente envía peticiones para recuperar o modificar recursos, y el servidor responde con el resultado, que puede ser con los datos que hemos pedido o el **estado** de la petición. **`Restful`** Web Service o Restful API hace referencia a un servicio web que implementa la arquitectura REST.  

### **Recursos REST**
La base de la arquitectura REST define una interacción orientada a recursos. Los recursos son cualquier tipo de objeto, dato, o servicio al que el cliente pueda acceder. Un recurso tiene un identificador que es un URI que identifica de forma única de ese recurso.

Para el diseño del recurso se debe seguir buenas prácticas.

1. **Sustantivos no verbos** <br/>
Los recursos debe ser sustantivos no debe contener palabras que impliquen acciones, por lo que deben evitarse los verbos en su construcción. El nombre que decidamos poner al recurso debería representar un sustantivo dentro del contexto funcional del API. **`Ejemplo:`** **Cliente, Auto, Persona etc**...

2. **URL (endpoint) en plural** <br/>
URL hace de identificador único de cada recurso del sistema REST, por lo que no puede ser compartida por más de un recurso. Hay un debate si colocar los nombres de los recursos en `plural o en singular`. En lo particular se prefiere que en **plural**, ya que indican un conjunto de características. permite ordenar los recursos y englobar las distintas funcionalidades entre sí. **`Ejemplo:`** **Clientes, Autos, Personas etc**..


|[<span style="color:lime">Correcto</span>]()|[<span style="color:red">Incorrecto</span>]()|
|--------|-----|
|`api/v1/clientes` | `api/v1/getclientes`|
|`api/v1/productos`| `api/v1/getallproductos`|
|`api/v1/autos`    | `api/v1/updateautos`|
|`api/v1/libros`   | `api/v1/libros/crear`|
|`api/v1/telefonos`| `api/v1/creartelefonos`|
<br/>

3. **Verbo HTTP Correcto** <br/>
Cuando se realizan operaciones que cambian el estado de un objeto o que alteren los estados de los recursos utiliza los verbos que corresponden como POST, PUT y DELETE., PATH. El método GET, como se intuye por su nombre, debe devolver sólo una versión de lectura del recurso **no** utilizar para alterar el recurso.

|verbo |[<span style="color:lime">Correcto</span>]()|[<span style="color:red">Incorrecto</span>]()|
|------|---------------------|---|
|GET   |`api/v1/productos`   |`api/v1/getproductos`|
|GET   |`api/v1/productos/{id}`|`api/v1/obtenerproductos`|
|GET   |`api/v1/productos?categoria=`*`{categoria}`*|`api/v1/productos/categoria/{id}`|
|POST  |`api/v1/productos`   |`api/v1/crearproductos`|
|PUT   |`api/v1/productos`   |`api/v1/actulizarproductos`|
|DELETE|`api/v1/productos`   |`api/v1/deleteproductos`|
|DELETE|`api/v1/productos/{id}`|`api/v1/productos/delete`|
<br/>

4. **Sub-Recursos**<br/>
Hay situaciones en las que un recurso está relacionado con otro. Esto es habitual cuando existe una jerarquía de objetos y recursos. Para devolver recursos asociados a otro recurso utiliza url que incorporen subrecursos.

|verbo |Ejemplo              |
|------|---------------------|
|GET   |`api/v1/characters`  |
|GET   |`api/v1/characters/{id}`|
|GET   |`api/v1/characters/{id}/comics`|
|GET   |`api/v1/characters/{id}/events/{id}`|
<br/>

5. **HATEOAS (Navegabilidad vía vínculos)** <br/>
HATEOAS (Hypermedia as the Engine of Application State) Es un estilo arquitectónico REST, que nos permite usar los enlaces hipermedia en los contenidos de respuesta de la API. Esto permite que el cliente navegue dinámicamente a los recursos apropiados atravesando los enlaces hipermedia. Usamos enlaces para indicar relaciones con otros recursos. Los enlaces también se pueden utilizar para indicar posibles acciones en un recurso. La hipermedia también se refiere a cualquier contenido en forma de medios, como imágenes, películas y texto.
```json

# Ejemplo 1
{
    "buyer_url": "/clientes/456",
    "fecha_pedido": "2020-15-08T09:30:00",
    "precio_total": 4,99,
    "fecha_pago": nulo,
    "estado": "abierto",
    "elementos": [
        {
            "url_producto": "/productos/789",
            "cantidad": 5,
            "precio": 4,99
        }
    ]
}

# Ejemplo 2
{ 
    "cuenta": { 
        "número_cuenta": 12345, 
        "saldo": { 
            "moneda": "usd", 
            "valor": 100.00 
        }, 
        "enlaces": { 
            "depósitos": "/cuentas/ 12345/depósitos", 
            "retiros": "/cuentas/12345/retiros", 
            "transferencias": "/cuentas/12345/transferencias", 
            "solicitudes de cierre": "/cuentas/12345/solicitudes de cierre" 
        } 
    } 
}
```

6. **Paginación, búsqueda y ordenación** <br/>
En este escenario de trabajo, realizar consultas sobre una base de datos que devuelvan una gran cantidad de registros, puede causar graves problemas de rendimiento en los servidores, a la hora de tratar estos datos para ser enviados a los clientes finales. implementar un sistema de paginación, búsqueda y ordenación de registros en un API REST ayuda a disminuir la carga en los servidores.<br/>
**Ejemplo Link:** ```//api.github.com/search?code=3&Amozilla=5&page=34```

```C#
public ViewResult Index(string sortOrder, string currentFilter, string searchString, int? page)
{
   ViewBag.CurrentSort = sortOrder;
   ViewBag.NameSortParm = String.IsNullOrEmpty(sortOrder) ? "name_desc" : "";
   ViewBag.DateSortParm = sortOrder == "Date" ? "date_desc" : "Date";

   if (searchString != null)
   {
      page = 1;
   }
   else
   {
      searchString = currentFilter;
   }

   ViewBag.CurrentFilter = searchString;

   var students = from s in db.Students
                  select s;
   if (!String.IsNullOrEmpty(searchString))
   {
      students = students.Where(s => s.LastName.Contains(searchString)
                             || s.FirstMidName.Contains(searchString));
   }
   switch (sortOrder)
   {
      case "name_desc":
         students = students.OrderByDescending(s => s.LastName);
         break;
      case "Date":
         students = students.OrderBy(s => s.EnrollmentDate);
         break;
      case "date_desc":
         students = students.OrderByDescending(s => s.EnrollmentDate);
         break;
      default:  // Name ascending 
         students = students.OrderBy(s => s.LastName);
         break;
   }

   int pageSize = 3;
   int pageNumber = (page ?? 1);
   return View(students.ToPagedList(pageNumber, pageSize));
}
```

7. **versionado API REST** <br/>
El versionado de servicios es una práctica por la cual, al producirse un cambio en el API de un servicio se libera una nueva versión de ese servicio de manera que la versión nueva y la anterior conviven durante un periodo de tiempo. De esta manera, los clientes se irán migrando a la nueva versión del servicio de manera secuencial. Cuando todos los clientes estén consumiendo la última versión del servicio, se retira la anterior. La version se coloca en la Ruta o URL de los recursos

|Ejemplo|
|--|
|`api/v1/characters/{id}/events/{id}`|
|`api/v2/characters/{id}/events/{id}`|
<br/>

### **Protocolo HTTP**
El Protocolo de transferencia de hipertexto (en inglés, Hypertext Transfer Protocol, abreviado HTTP) es el protocolo de comunicación que permite las transferencias de información, petición de datos y recursos a través de archivos (XHML, HTML, XML, JSON, TEXT PLAIN) en la World Wide Web. Es la base de cualquier intercambio de datos en la Web, y un protocolo de estructura cliente-servidor, esto quiere decir que una petición de datos es iniciada por el elemento que recibe los datos (el cliente) y el servidor se encarga de enviar tales datos.
<br/>

#### **Una `petición` de HTTP, está formado  por los siguientes campos:**

| Campos          | Descripción   |
|-----------------|---------------|
|**`Method Http`**| Son verbos como (GET, POST, PUT, DELETE) que definen las operaciones que realizara el cliente.   |
|**`Path`**       | La dirección o ruta del recurso pedido. Son elementos como el protocolo, el dominio, el puerto TCP, y el recurso. |
|**`Headers`**    | Opcionales, es el encabezado que pueden aportar información adicional a los servidores.   |
|**`Body`**       | Opcional, es el cuerpo de mensaje, el cual contiene la información que se enviara al servidor. El mensaje puede ser enviado el cualquier formato (`json`, `xml`, `html`, `texto plano`) de pendiendo del servidor  |
<br/>


#### **Una `respuesta` de HTTP, está formado  por los siguientes campos:**

| Campos          | Descripción   |
|-----------------|---------------|
|**`status code`**| Un código de estado, indicando si la petición ha sido exitosa, o no, y debido a que|
|**`status message`**       | Un mensaje de estado, una breve descripción del código de estado.|
|**`Headers`**    | Opcionales, es el encabezado que pueden aportar información adicional a los servidores.   |
|**`Body`**       | Opcional, es el cuerpo de mensaje, el cual contiene la información de respuesta del servidor. El mensaje puede ser en cualquier formato (`json`, `xml`, `html`, `texto plano`) de pendiendo del servidor  |
<br/>


[<img src="https://ws.apms.io/api/_files/WScS4PCt2atRRbGB8YUCE8/download/" width="800" height="400" />](dedde)


### **Metodos HTTP**
El Protocolo HTTP define una serie predefinida de métodos de petición (algunas veces referido como "verbos") que pueden utilizarse. Cada método indica la acción que desea que se efectúe sobre el recurso identificado. Lo que este recurso representa depende de la aplicación del servidor.

|Verbo         | Descripción  |
|--------------|--------------|
|**`GET`**     |Nos sirve para hacer una petición para que el servidor envíe información al cliente.|
|**`POST`**    |Sirve para enviar información desde el cliente hacia el servidor.|
|**`PUT`**     |Para actualizar todo un recurso.|
|**`PATCH`**   |Es utilizado para actualizar parcialmente un recurso, este viene bien para solo actualizar solo una parte del recurso y no todo completo.|
|**`DELETE`**  |Es para borrar, eliminar, desactivar un recurso. |
|**`HEAD`**    |Nos permite consultar la existencia de un recurso, es diferente al método GET porque este le pide al servidor que le envíe el recurso, mientras que HEADER solo pregunta al servidor si existe, no envía ningún recurso.|
|**`CONNECT`** | el método CONNECT se utiliza para establecer un túnel HTTP de un extremo a otro a través de un servidor proxy.|
|**`OPTIONS`** |El método OPTIONS solicita opciones de comunicación permitidas para un recurso determinado. Esta solicitud se puede enviar para averiguar los métodos HTTP admitidos y otras opciones admitidas sin solicitar el recurso.|
|**`TRACE`**   |El método TRACE solicita al servidor que envíe la solicitud exacta, tal como la recibió, en el cuerpo de la respuesta. Es útil para fines de diagnóstico.|
<br/>


### **Codigo de estado HTTP**
Los códigos de estado de respuesta HTTP indican el estado de la petición HTTP realizada al servidor esto código se dividen en cinco grupos.

+ Respuestas informativas (100–199)
+ Respuestas satisfactorias (200–299)
+ Redirecciones (300–399)
+ Errores de los clientes (400–499)
+ Errores de los servidores (500–599)

|Codigo  |Repuesta   | Descripcion   |
|--------|-----------|---------------|
|**`100`**|Continuar|Esto significa que el servidor en cuestión ha recibido las cabeceras de solicitud de tu navegador, y ahora está listo para que el cuerpo de la solicitud sea enviado también. Esto hace que el proceso de solicitud sea más eficiente ya que evita que el navegador envíe una solicitud de cuerpo aunque los encabezados hayan sido rechazados.|
|**`101`**|Cambiando protocolos|Tu navegador ha pedido al servidor que cambie los protocolos, y el servidor ha cumplido.|
|**`103`**|Primeros avisos|Esto devuelve algunos encabezados de respuesta antes de que el resto de la respuesta del servidor esté lista.|
|**`200`**|Todo está bien|Este es el código que se entrega cuando una página web o recurso actúa exactamente como se espera.|
|**`201`**|Creado|El servidor ha cumplido con la petición del navegador y, como resultado, ha creado un nuevo recurso.|
|**`202`**|Aceptado|El servidor ha aceptado la solicitud de tu navegador pero aún la está procesando. La solicitud puede, en última instancia, dar lugar o no a una respuesta completa.|
|**`203`**|Información no autorizada|Este código de estado puede aparecer cuando se utiliza un apoderado. Significa que el servidor proxy recibió un código de estado de 200 «Todo está bien» del servidor de origen, pero ha modificado la respuesta antes de pasarla a su navegador.|
|**`204`**|Sin contenido|Este código significa que el servidor ha procesado con éxito la solicitud, pero no va a devolver ningún contenido.|
|**`205`**|Restablecer el contenido|Como un código 204, esto significa que el servidor ha procesado la solicitud pero no va a devolver ningún contenido. Sin embargo, también requiere que tu navegador restablezca la vista del documento.|
|**`206`**|Contenido parcial|Puedes ver este código de estado si tu cliente HTTP (también conocido como tu navegador) usa «cabeceras de rango». Esto permite a tu navegador reanudar las descargas en pausa, así como dividir una descarga en múltiples flujos. Se envía un código 206 cuando un encabezado de rango hace que el servidor envíe sólo una parte del recurso solicitado.|
|**`300`**|Opciones Múltiples|A veces, puede haber múltiples recursos posibles con los que el servidor puede responder para cumplir con la solicitud de su navegador. Un código de estado 300 significa que tu navegador ahora tiene que elegir entre ellos. Esto puede ocurrir cuando hay múltiples extensiones de tipo de archivo disponibles, o si el servidor está experimentando desambiguación del sentido de las palabras.|
|**`301`**|El recurso solicitado ha sido trasladado permanentemente|Este código se entrega cuando una página web o un recurso ha sido reemplazado permanentemente por un recurso diferente. Se utiliza para la redirección permanente del URL.|
|**`302`**|El recurso solicitado se ha movido, pero fue encontrado|Este código se utiliza para indicar que el recurso solicitado se encontró, pero no en el lugar donde se esperaba. Se utiliza para la redirección temporal de la URL.|
|**`303`**|Ver otros|Para entender un código de estado 303 es necesario conocer la diferencia entre los cuatro métodos de solicitud HTTP principales. Esencialmente, un código 303 le dice a tu navegador que encontró el recurso que el navegador solicitó vía POST, PUT o DELETE. Sin embargo, para recuperarlo usando GET, necesita hacer la solicitud apropiada a un URL diferente al que usó anteriormente.|
|**`304`**|El recurso solicitado no ha sido modificado desde la última vez que accedió a él|Este código le dice al navegador que los recursos almacenados en la caché del navegador no han cambiado. Se usa para acelerar la entrega de páginas web reutilizando los recursos descargados previamente.|
|**`307`**|Redireccionamiento temporal|Este código de estado ha reemplazado a 302 «Encontrado» como la acción apropiada cuando un recurso ha sido movido temporalmente a una URL diferente. A diferencia del código de estado 302, no permite que el método HTTP cambie.|
|**`308`**|Redireccionamiento permanente|El código de estado 308 es el sucesor del código 301 «Movido permanentemente». No permite que el método HTTP cambie e indica que el recurso solicitado está ahora localizado permanentemente en una nueva URL.|
|**`400`**|Mala petición|El servidor no puede devolver una respuesta debido a un error del cliente. Vea nuestra guía para resolver este error.|
|**`401`**|No autorizado|o «Se requiere autorización». Esto es devuelto por el servidor cuando el recurso de destino carece de credenciales de autenticación válidas. Podrías ver esto si has configurado la autenticación básica de HTTP usando htpasswd.|
|**`402`**|Pago requerido|Originalmente, este código fue creado para ser usado como parte de un sistema de dinero digital. Sin embargo, ese plan nunca se llevó a cabo. |
|**`403`**|El acceso a ese recurso está prohibido|Este código se devuelve cuando un usuario intenta acceder a algo a que no tiene permiso para ver. Por ejemplo, intentar acceder a un contenido protegido por contraseña sin registrarse podría producir un error 403.|
|**`404`**|No se encontró el recurso solicitado|Este es el mensaje de error más común de todos ellos. Este código significa que el recurso solicitado no existe, y el servidor no sabe si alguna vez existió.|
|**`405`**|Método no permitido|Esto se genera cuando el servidor de alojamiento (servidor de origen) soporta el método recibido, pero el recurso de destino no lo hace.|
|**`406`**|Respuesta no aceptable|El recurso solicitado es capaz de generar sólo contenido que no es aceptable según los encabezamientos de aceptación enviados en la solicitud.|
|**`407`**|Se requiere autenticación de proxy|Se está utilizando un servidor proxy que requiere que el navegador se autentifique antes de continuar.|
|**`408`**|El servidor se agotó esperando el resto de la petición del navegador|Este código se genera cuando un servidor se apaga mientras espera la solicitud completa del navegador. En otras palabras, el servidor no recibió la solicitud completa que fue enviada por el navegador. Una posible causa podría ser la saturación de la red, lo que provoca la pérdida de paquetes de datos entre el navegador y el servidor.|
|**`409`**|Conflicto|Un código de estado 409 significa que el servidor no pudo procesar la solicitud de su navegador porque hay un conflicto con el recurso correspondiente. Esto ocurre a veces debido a múltiples ediciones simultáneas.|
|**`410`**|El recurso solicitado se ha ido y no volverá|Esto es similar a un código 404 «No encontrado», excepto que un 410 indica que la condición es esperada y permanente.|
|**`411`**|Longitud requerida|Esto significa que el recurso solicitado requiere que el cliente especifique una cierta longitud y que no lo hizo.|
|**`412`**|La condición previa falló|Tu navegador incluyó ciertas condiciones en sus encabezados de solicitud, y el servidor no cumplió con esas especificaciones.|
|**`413`**|Carga útil demasiado grande|o «Entidad solicitante demasiado grande». Su solicitud es más grande de lo que el servidor está dispuesto o es capaz de procesar.|
|**`414`**|URI demasiado largo|Esto suele ser el resultado de una solicitud GET que ha sido codificada como una cadena de consulta demasiado grande para que el servidor la procese.|
|**`415`**|Tipo de medios de comunicación sin apoyo|La solicitud incluye un tipo de medio que el servidor o recurso no soporta.|
|**`416`**|Rango no satisfactorio|Su solicitud fue por una porción de un recurso que el servidor no puede devolver.|
|**`417`**|La expectativa fracasó|El servidor no puede cumplir los requisitos especificados en el campo de cabecera de la solicitud.|
|**`418`**|Soy una tetera|Este código es devuelto por las teteras que reciben solicitudes para preparar café. |
|**`422`**|Entidad no procesable|La solicitud del cliente contiene errores semánticos, y el servidor no puede procesarla.|
|**`425`**|Demasiado pronto|Este código se envía cuando el servidor no está dispuesto a procesar una solicitud porque puede ser reproducida.|
|**`426`**|Se requiere actualización|Debido al contenido del campo de cabecera de la solicitud, el cliente debería cambiar a un protocolo diferente.|
|**`428`**|Se requiere condición previa|El servidor requiere que se especifiquen las condiciones antes de procesar la solicitud.|
|**`429`**|Demasiadas peticiones|Esto es generado por el servidor cuando el usuario ha enviado demasiadas solicitudes en un determinado período de tiempo (con límite de velocidad). Esto puede ocurrir a veces debido a los bots o scripts que intentan acceder a tu sitio. En este caso, tal vez quieras intentar|
|**`431`**|Campos de la Cabecera de la Solicitud Demasiado Grandes|El servidor no puede procesar la solicitud porque los campos de cabecera son demasiado grandes. Esto puede indicar un problema con un solo campo de cabecera, o con todos en general.|
|**`451`**|No disponible por razones legales|El operador del servidor ha recibido una demanda para prohibir el acceso al recurso que has solicitado (o a un conjunto de recursos, incluido el que has solicitado). Dato curioso: Este código es una referencia a la novela Fahrenheit 451 de Ray Bradbury.|
|**`499`**|Solicitud de cliente cerrada|Esto es devuelto por NGINX cuando el cliente cierra la solicitud mientras Nginx aún la está procesando.|
|**`500`**|Hubo un error en el servidor y la solicitud no pudo ser completada|Este es un código genérico que simplemente significa «error interno del servidor». Algo salió mal en el servidor y el recurso solicitado no fue entregado.|
|**`501`**|No implementado|Este error indica que el servidor no es compatible con la funcionalidad necesaria para cumplir con la solicitud. Esto es casi siempre un problema en el propio servidor web, y por lo general debe ser resuelto por el host. Revisa nuestras recomendaciones sobre cómo resolver un error 501 no implementado.|
|**`502`**|Mala entrada|Este código de error significa típicamente que un servidor ha recibido una respuesta inválida de otro, como cuando se utiliza un servidor proxy. Otras veces una consulta o petición tardará demasiado, y así es cancelada o asesinada por el servidor y la conexión a la base de datos se rompe. Para más detalles, consulta nuestro tutorial en profundidad sobre cómo arreglar el error del 502 Bad Gateway.|
|**`503`**|El servidor no está disponible para manejar esta solicitud en este momento.|La solicitud no puede ser completada en este momento. Este código puede ser devuelto por un servidor sobrecargado que no puede manejar solicitudes adicionales. Tenemos una guía completa sobre cómo arreglar el error de no disponibilidad del servicio 503.|
|**`504`**|El servidor, actuando como una puerta de enlace, se ha agotado esperando a que otro servidor responda|Este es el código devuelto cuando hay dos servidores involucrados en el procesamiento de una solicitud, y el primer servidor se apaga esperando que el segundo servidor responda. Puedes leer más sobre cómo corregir los errores del 504 en nuestra guía dedicada.|
|**`505`**|Versión HTTP no soportada|El servidor no soporta la versión HTTP que el cliente usó para hacer la solicitud.|
|**`508`**|Se ha alcanzado el límite de recursos|Se han alcanzado los límites de recursos establecidos por tu alojamiento web. Consulta nuestro tutorial sobre cómo resolver el error «508 Resource Limit Is Reached«.|
|**`509`**|Límite de Ancho de Banda Excedido|significa que tu sitio web está utilizando más ancho de banda del que permite tu proveedor de hosting.|
|**`511`**|Se requiere autenticación de la red|Este código de estado se envía cuando la red que está tratando de usar requiere alguna forma de autenticación antes de enviar su solicitud al servidor. Por ejemplo, es posible que tenga que aceptar los términos y condiciones de un punto de acceso Wi-Fi público.|
|**`521`**|El servidor web está caído|El error 521 es un mensaje de error específico de Cloudflare. Significa que su navegador web fue capaz de conectarse con éxito a Cloudflare, pero Cloudflare no fue capaz de conectarse al servidor web de origen.|
|**`525`**|SSL Handshake Failed|El error 525 significa que el Protocolo de enlace SSL entre un dominio que usa Cloudflare y el servidor web de origen falló. Si estás experimentando problemas, hay cinco métodos para intentar arreglar fácilmente el error 525.|
<br/>


