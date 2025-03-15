El objetivo de este laboratorio es como identificar y explotar una vulnerabilidad de "inyección SQL" en un formulario de inicio de sesión. 

Ejemplo de la vulnerabilidad: 

> En una aplicación WEB vulnerable a inyección SQL, El servidor recibe entradas del usuario como nombre de usuario y la contraseña. las inserta directamente en una consulta SQL para autenticar al usuario. Si la entrada no se valida adecuadamente, un atacante puede inyectar código SQL malicioso para manipular la consulta y alternar su comportamiento, como en este caso eludir la autenticación. 


entramos al laboratorio: https://portswigger.net/web-security/sql-injection/lab-login-bypass

![image](https://github.com/user-attachments/assets/5e8fe8a1-9d00-4748-88ae-024bddaf1501)

Primero vamos a colocar el usuario que nos da el laboratorio que es "administrator" pero con una contraseña X

![image](https://github.com/user-attachments/assets/6a7a507f-c24d-42c4-87a9-b89a10ea64ee)

lo capturamos en burpsuite 

![image](https://github.com/user-attachments/assets/50be5d3c-f040-4b03-b152-c23691ba68a0)

en el "repeater" vamos a modificar el "administrator" 

![image](https://github.com/user-attachments/assets/53f563e0-6f3b-4cfe-8b04-2c4af4c77a90)

![image](https://github.com/user-attachments/assets/130e4cbd-6a38-490c-b525-22762002d20c)

ahora vamos a comprobar que funciona 

![[vulnerabilidades/web academy/SQL injection/test 6.png]]

y como se ve, es vulnerable a una injection SQL. 

> Mitigación 

Para mitigar se deben usar consultas parametrizadas para separar los datos de la lógica SQL, validar y sanitizar las entradas del usuario, limitar los privilegios de las cuentas de base de datos, evitar mensajes de error detallados que puedan revelar información sobre la estructura de la base de datos, emplear un firewall de aplicaciones web o sea un WAF para bloquear solicitudes maliciosas, auditar y monitorear las interacciones con la base de datos para detectar actividades sospechosas y almacenar contraseñas utilizando técnicas de hash y salting para garantizar sus seguridad. 

- El salting es un proceso de agregar un valor aleatorio a una contraseña antes de aplicar un algoritmo de hash, con el fin de asegurar que contraseñas idénticas no generen el mismo valor hash, protegiendo contra ataques de diccionario y fuerza bruta. 
