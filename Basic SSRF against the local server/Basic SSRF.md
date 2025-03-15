
El laboratorio se llama "Server-side Request Forgery (SSRF)". Explotación de un servicio interno accesible desde "localhost"

Objetivo de este laboratorio:   Consiste en manipular la aplicación web para hacer que el servidor  realice una solicitud HTTP a un recurso interno o sea el localhost, lo que puede revelar información sensible o permitir acceso no autorizado a servicios internos. 

La vulnerabilidad suele estar en una funcionalidad que recupera recursos de una URL externa proporcionado por el usuario. si esta entrada no es validada correctamente, se puede inyectar "http://localhost" u otras direcciones internar como "127.0.0.1" o "0.0.0.0". para acceder a servicios internos como base de datos, paneles de administración o API ocultas. 

> Lo que se busca con esta explotación -> Modifica la URL en un parámetro vulnerables para que el servidor haga una petición a localhost y extraer la información expuesta en la repuesta. 

primero entramos al primer "producto" y le presionamos "detalle" 

![image](https://github.com/user-attachments/assets/48b56bf0-f23b-4c57-9048-117438d77027)

luego bajamos y le damos a  "check stock" 

![[test 2.png]]

y en burpsuite deberemos capturar ese "stock api", entonces si lo enviamos tal y como esta, nos mostrara los stock, así que modificamos con el "localhost" 

![[tet 4.png]]

veremos eso con la URL como viene, le agregamos el "localhost"

![[vulnerabilidades/web academy/basic SSRF against/test 5.png]]

y la respuesta es completamente diferente. entonces le presionamos aqui

![[vulnerabilidades/web academy/basic SSRF against/test 6.png]]

en "show response in browser" y nos dará una URL 

![[test 7.png]]

veremos dos cosas al entrar

![[test 8.png]]

primero que tenemos el "panel de admin" y podemos eliminar usuarios, y uno de los objetivos del laboratorio es poder eliminar al usuario "carlos" 

![[test 9.png]]

pero nos pedirá que nos entremos como administrador desde el login lo cual no podemos. así que bajaremos en el código y lo agregamos al "stockApi" que tenemos

![[test 10.png]]

en burpsuite nos saldrá así

![[test 11.png]]

y en la pagina 

![[vulnerabilidades/web academy/basic SSRF against/final.png]]

el laboratorio se logo con éxito. 

> Mitigación 

Para mitigar un SSRF se debe implementar un enfoque de defensa en profundidad que combine validación de entrada, restricciones de red y controles de acceso.

En el lado de la aplicación, es fundamental validar y sanitizar las URLs proporcionadas por el usuario, asegurándose de que solo apunten a dominios externos permitidos. Esto se complementa con el uso de listas blancas y restricciones en la resolución de nombres de dominios para evitar bypass mediante redirecciones o registros DNS inalámbricos. 

a nivel red, se debe configurar reglas de firewall para bloquear el trafico de salida hacia direcciones internas, evitando que el servidor realice peticiones no autorizadas a servicios locales. también se recomienda implementar un proxy de salida que filtre las solicitudes HTTP y limite la exposición de información sensible.

para mejorar la seguridad en entornos cloud, se deben deshabilitar los metadatos accesibles a través de solicitudes HTTP y establecer autenticación en los servicios internos. y finalmente la detección de trafico anómalo mediante monitoreo activo y registros detallados de solicitudes HTTP ayuda a identificar intentos de explotación antes de que causen impacto.  
