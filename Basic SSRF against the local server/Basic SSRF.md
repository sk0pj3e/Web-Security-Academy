
El laboratorio se llama "Server-side Request Forgery (SSRF)". Explotación de un servicio interno accesible desde "localhost"

Objetivo de este laboratorio:   Consiste en manipular la aplicación web para hacer que el servidor  realice una solicitud HTTP a un recurso interno o sea el localhost, lo que puede revelar información sensible o permitir acceso no autorizado a servicios internos. 

La vulnerabilidad suele estar en una funcionalidad que recupera recursos de una URL externa proporcionado por el usuario. si esta entrada no es validada correctamente, se puede inyectar "http://localhost" u otras direcciones internar como "127.0.0.1" o "0.0.0.0". para acceder a servicios internos como base de datos, paneles de administración o API ocultas. 

> Lo que se busca con esta explotación -> Modifica la URL en un parámetro vulnerables para que el servidor haga una petición a localhost y extraer la información expuesta en la repuesta. 

primero entramos al primer "producto" y le presionamos "detalle" 

![image](https://github.com/user-attachments/assets/48b56bf0-f23b-4c57-9048-117438d77027)

luego bajamos y le damos a  "check stock" 

![image](https://github.com/user-attachments/assets/2d0e0850-a469-4b31-9b58-2298651dadb3)

y en burpsuite deberemos capturar ese "stock api", entonces si lo enviamos tal y como esta, nos mostrara los stock, así que modificamos con el "localhost" 

![image](https://github.com/user-attachments/assets/4c855086-c18b-402f-9441-be79e23455dc)

![image](https://github.com/user-attachments/assets/08b88123-2b88-4189-b789-5e849edac39f)

veremos eso con la URL como viene, le agregamos el "localhost"

![image](https://github.com/user-attachments/assets/f48d5655-fe67-4b7f-a31c-cbe5b2f7b746)

y la respuesta es completamente diferente. entonces le presionamos aqui

![image](https://github.com/user-attachments/assets/2a0a3004-c3b8-439f-9a7f-62c4f0b6360d)

en "show response in browser" y nos dará una URL 

![image](https://github.com/user-attachments/assets/c5b31a6f-da4e-472b-8a9d-1c5196bd2188)

veremos dos cosas al entrar

![image](https://github.com/user-attachments/assets/c5047af8-9bcc-4c4b-b247-9c6472a33f7e)

primero que tenemos el "panel de admin" y podemos eliminar usuarios, y uno de los objetivos del laboratorio es poder eliminar al usuario "carlos" 

![image](https://github.com/user-attachments/assets/e491dd02-97e5-4924-b52d-656c00ac5328)

pero nos pedirá que nos entremos como administrador desde el login lo cual no podemos. así que bajaremos en el código y lo agregamos al "stockApi" que tenemos

![image](https://github.com/user-attachments/assets/c5547620-96db-4148-9cc1-360549ba7ef3)

en burpsuite nos saldrá así

![image](https://github.com/user-attachments/assets/6f897e9d-6312-419b-a5bd-55651acd38cd)

y en la pagina 

![image](https://github.com/user-attachments/assets/aeeed1ab-c421-472c-9cad-1a6f877197b3)

el laboratorio se logo con éxito. 

> Mitigación 

Para mitigar un SSRF se debe implementar un enfoque de defensa en profundidad que combine validación de entrada, restricciones de red y controles de acceso.

En el lado de la aplicación, es fundamental validar y sanitizar las URLs proporcionadas por el usuario, asegurándose de que solo apunten a dominios externos permitidos. Esto se complementa con el uso de listas blancas y restricciones en la resolución de nombres de dominios para evitar bypass mediante redirecciones o registros DNS inalámbricos. 

a nivel red, se debe configurar reglas de firewall para bloquear el trafico de salida hacia direcciones internas, evitando que el servidor realice peticiones no autorizadas a servicios locales. también se recomienda implementar un proxy de salida que filtre las solicitudes HTTP y limite la exposición de información sensible.

para mejorar la seguridad en entornos cloud, se deben deshabilitar los metadatos accesibles a través de solicitudes HTTP y establecer autenticación en los servicios internos. y finalmente la detección de trafico anómalo mediante monitoreo activo y registros detallados de solicitudes HTTP ayuda a identificar intentos de explotación antes de que causen impacto.  
