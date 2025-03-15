Este laboratorio: https://portswigger.net/web-security/prototype-pollution/client-side/browser-apis/lab-prototype-pollution-client-side-prototype-pollution-via-browser-apis

tiene como objetivo enseñar e identificar y explotar vulnerabilidades de contaminación de prototipos en el lado del cliente a través de APIs del navegador. estas vulnerabilidades permiten a un atacante modificar el comportamiento de objetos javascript, lo que puede conducir a la ejecución de scripts maliciosos en el contexto del usuario. 

**El objetivo del laboratorio**: Comprender como las vulnerabilidades de contaminación de prototipos pueden ser explotadas mediante APIs del navegador y aprender a identificar y mitigar estos riesgos en aplicaciones web.  

Primero vamos a la pagina e introducimos un "proto"

![image](https://github.com/user-attachments/assets/11ec7cd9-0b0e-4403-bc9c-cefa9c1919d7)

pero la inyección no funciono así que la vamos a seguir modificando 

![[vulnerabilidades/web academy/client-side prototype/test2.png]]

le vamos agregar corchetes al "foo" y funciona

![[vulnerabilidades/web academy/client-side prototype/test3.png]]

lo que hace esto es que nos muestra como funciona el código por detrás

![[codigo.png]]

y lo que vemos es esto: 

![[logger.png]]

al cargar la pagina se genera un "searchlogger()" en este contexto probablemente se usa para registrar consultar de búsquedas y detectar intentos de manipulación maliciosa en la aplicación 
los demás marcados con el cuadro rojo como "transport_url"; su protección contra "object.defineproperty()" se logra su modificación directa en el prototipo. esto evita que un atacante altere su comportamiento mediante contaminación de prototipos, asegurando que la propiedad mantenga su valor original y seguro. cuanto el código se ejecute  el "if(config.transport_url)" el código intentara acceder explícitamente a propiedades del objeto o sea el "object.defineproperty()" así que podemos contaminar el prototipo dejando la URL así. 

    ?__proto__[value]=data:,alert(vulnerabilidad encontrada);

esto hará que el URL que es "false" se convierta en "objeto", lo que hará que busque la propiedad del objeto y nosotros modificamos esos entonces buscara el "value" o sea que lo tomara del prototipo contraminado. lo que esto lleva es que se convierta el "false" a un "true" y esto lo lleve a crear un elemento script, lo que el "src" se establece con nuestro valor malicioso entonces ejecuta el "script xss"

también si no funciona con este se puede probar con otro parámetro como este 

    ?__proto__[value]=data:text/javascript,alert()

![[vulnerabilidades/web academy/client-side prototype/final.png]]

y tenemos la vulnerabilidad y el laboratorio completado

![[vulnerabilidades/web academy/client-side prototype/final1.png]]
