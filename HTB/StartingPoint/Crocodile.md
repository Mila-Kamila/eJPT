# PARTE 1: RESPUESTAS DE LAS TASKS

- **Task 1:** `-sC`
    
- **Task 2:** `vsftpd 3.0.3`
    
- **Task 3:** `230`
    
- **Task 4:** `anonymous`
    
- **Task 5:** `get`
    
- **Task 6:** `admin`
    
- **Task 7:** `Apache httpd 2.4.41 (Ubuntu)`
    
- **Task 8:** `-t` _(Gobuster filetype switch es -x en versiones modernas, pero en HTB suele aceptarse -x)_
    
- **Task 9:** `login.php`
    

---

#  PARTE 2: RESOLUCIÓN PASO A PASO

---

##  1. Escaneo inicial con Nmap

Lo primero fue realizar reconocimiento completo del host:

```bash
sudo nmap -sV -Pn -sC --min-rate 500 -p- 10.129.129.120
```

---

###  Explicación del comando

- `sudo`: permisos de administrador para ejecutar scripts
    
- `-sV`: detecta versiones de servicios
    
- `-Pn`: evita ping y asume el host activo
    
- `-sC`: ejecuta scripts por defecto de Nmap
    
- `--min-rate 500`: acelera el escaneo
    
- `-p-`: escanea todos los puertos (1–65535)
    

---

###  Resultado del escaneo

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2026-05-13 07:23 -05
Nmap scan report for 10.129.129.120
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp ftp 33 Jun 08  2021 allowed.userlist
| -rw-r--r--    1 ftp ftp 62 Apr 20  2021 allowed.userlist.passwd

80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Smash - Bootstrap Business Template
```

---

###  Hallazgos importantes

- FTP con **login anónimo habilitado**
    
- Dos archivos interesantes en FTP:
    
    - `allowed.userlist`
        
    - `allowed.userlist.passwd`
        
- Servicio web en puerto 80 con Apache
    

---

##  2. Acceso al servicio FTP

Se conectó al FTP:

```bash
ftp 10.129.129.120
```

Salida:

```bash
220 (vsFTPd 3.0.3)
Name (10.129.129.120:camila): anonymous
230 Login successful.
```

---

###  Interpretación

- `230 Login successful` → acceso anónimo permitido sin contraseña
    

---

##  3. Descarga de archivos del FTP

Dentro del FTP:

```bash
ftp> ls
```

Resultado:

```bash
-rw-r--r--    1 ftp ftp 33 allowed.userlist
-rw-r--r--    1 ftp ftp 62 allowed.userlist.passwd
```

Descarga:

```bash
ftp> get allowed.userlist
ftp> get allowed.userlist.passwd
```

Salida:

```bash
100% transfer complete
226 Transfer complete.
```

---

##  4. Análisis de los archivos

###  allowed.userlist

```bash
aron
pwnmeow
egotisticalsw
admin
```

---

###  allowed.userlist.passwd

```bash
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```

---

###  Análisis

- Lista de usuarios potenciales
    
- Lista de contraseñas filtradas
    
- Usuario más interesante: `admin`
    

---

##  5. Enumeración web con Gobuster

Al no encontrar login visible en el sitio principal, se realizó fuerza bruta de directorios:

```bash
gobuster dir -u http://10.129.129.120 -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

---

###  Resultado

```bash
/assets               (301)
/css                  (301)
/js                   (301)
/fonts                (301)
/dashboard            (301)
/index.html           (200)
/server-status        (403)
```

---

###  Hallazgo clave

```bash
/dashboard → redirección a login
```

---

##  6. Acceso al panel oculto

Se abrió en navegador:

```
http://10.129.129.120/dashboard
```

Resultado:

- Redirección a un **login panel**
    

---

##  7. Acceso al sistema

Se probaron credenciales obtenidas del FTP:

- Usuario: `admin`
    
- Password: `rKXM59ESxesUFHAd`
    

---

###  Resultado

Login exitoso.

---

##  8. Obtención de la flag

Dentro del dashboard se obtuvo acceso a la aplicación interna, donde finalmente se encontró la **flag de la máquina Cocodrile**.
