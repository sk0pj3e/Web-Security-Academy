buscamos la vulnerabilidad del panel de administración.

**vulnerabilidad de prototipos javascript**

- Esta vulnerabilidad en este caso laboratorio de **web security academy** se basa en escalada de privilegios mediante contaminación de prototipos del lado del servidor, se basa en una debilidad en aplicaciones "node.js" que utilizan el framework express. esta debilidad surge cuando la aplicación funciona de manera insegura en entradas controladas por el usuario en objetos javascript del lado del servidor.  esta practica permite a un atacante modificar el prototipo global de **object**, afectando asi a todoss los objetos que heredan de este prototipo. 
- y el "__proto__" es una propiedad especial en javascript que define el prototipo de un objeto. si una aplicación fusiona datos sin validar, un atacante puede modificar el prototipo global de **object**, afectando todos los objetos que hereden de el. 

primero abrimos burpsuite y nos vamos a su browser, ahí nos vamos a https://0a4100c80309f25780a1df3e0024006f.web-security-academy.net/

y empezamos a buscar la vulnerabilidad, nos vamos a "my account"

![image](https://github.com/user-attachments/assets/df5d0a47-2590-422d-aebc-622cd71553bd)

y tendremos un login que entraremos con las credenciales normales

![image](https://github.com/user-attachments/assets/3bc2f460-6f5b-423e-b52d-e6c463487c1e)

las credenciales son "wiener" y contraseña "peter"
entonces nos vamos a fijar en dos cosas

![image](https://github.com/user-attachments/assets/9b3a699a-8bfb-4142-9a54-801a1631d29c)

al costado de "home" deberá aparecer  un "admin panel" si es que funciona la vulnerabilidad de prototipos de java script.

![image](https://github.com/user-attachments/assets/67915ce7-b8e1-4eb7-bfac-cf6a2f6f3051)

tomamos la solicitud que capta burpsuite del navegador y obtenemos el cambio que le hicimos para ver si esta tomando o no, y vemos que esta tomando 

![image](https://github.com/user-attachments/assets/761b586e-4b6f-442b-91dc-589a9c177d23)

al enviar nos aparece como "isadmin: false" y eso es lo que vamos a intentar cambiar con la vulnerabilidad de prototipos. primero vamos a intentar inyectar una propiedad en el prototipo global de "object" y eso lo aplico con un "foo": "bar" lo que hace es que cualquier objeto creado posteriormente heredara ese código, entonces esto probara y confirmara si la aplicación es susceptible a la vulnerabilidad antes de colocar el "isadmin": true. 

![image](https://github.com/user-attachments/assets/e476a738-e444-439f-9223-93d9d8fde0cc)

y como vemos se agrega en el código, no da el error ni nada. asi que ahora vamos a cambiar el foo bar por un "isadmin": "true"

![image](https://github.com/user-attachments/assets/3a3cbade-2d2a-4471-947a-e8601ac4c863)

como vemos y nombre, veremos que al borrar el "foo" y colocar el "isadmin" aun aparece el foo el enviar otra vez. y me equivoque el enviar el codigo y lo modifique porque como se ve sigue sin funcionar el primer "false"

lo modificamos y lo dejamos así: 

     {
      "address_line_1": "Wiener HQ",
      
	   "address_line_2": "One Wiener Way",
	   
	   "city": "Wienerville",
	   
	   "postcode": "BU1 1RP",
	   
	   "country": "chile",
	   
		"sessionId": "9Yg8nX8MLgvkUvewyy9CGujXXPe371kd",
		
		"__proto__": {

    "isAdmin": true
    
	 }
	 
	 }

![image](https://github.com/user-attachments/assets/7f1fbd72-a6a1-4702-89bc-94d9874bf0f4)

y si lo enviamos obtendremos lo que buscábamos, tener acceso al panel de administración. 

![image](https://github.com/user-attachments/assets/db4b0925-5e2b-4752-9e32-af0b14c32c21)

y como se ve obtenemos lo que buscábamos. 

