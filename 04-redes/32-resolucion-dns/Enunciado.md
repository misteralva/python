# Ejercicio 32 · Resolución DNS directa e inversa

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa con menú que haga consultas DNS:

```
1. Resolver un nombre de dominio (nombre -> IP)
2. Resolución inversa (IP -> nombre)
0. Salir
```

- **Resolución directa:** pide un dominio y muestra su(s) dirección(es) IP.
- **Resolución inversa:** pide una IP y muestra el nombre asociado, si existe.
- Debe controlar los errores (dominio inexistente, IP sin nombre, sin conexión) **sin romperse**.

### Ejemplo de ejecución

```
Elige una opción: 1
Dominio: example.com
Direcciones IPv4 de example.com:
  93.184.216.34
Elige una opción: 2
IP: 8.8.8.8
Nombre: dns.google
```

> Las IPs reales de un dominio pueden cambiar y varias consultas pueden dar resultados distintos. Es normal.

---

## 📖 Conceptos nuevos

### 1. ¿Qué es el DNS?

El **DNS** (*Domain Name System*) traduce **nombres** (`example.com`) en **direcciones IP** y viceversa. Sin DNS tendrías que memorizar la IP de cada web.

- **Directa**: nombre → IP (registros `A` para IPv4 y `AAAA` para IPv6).
- **Inversa**: IP → nombre (registros `PTR`). No todas las IPs tienen nombre asociado.

### 2. Resolución directa con `socket`

```python
import socket

ip = socket.gethostbyname("example.com")
print(ip)
```

- `gethostbyname(nombre)` devuelve **una** dirección IPv4 como texto.
- Para obtener **todas** las IPv4:

```python
nombre, alias, ips = socket.gethostbyname_ex("example.com")
print(ips)
```

- Devuelve una tupla de tres elementos: el nombre, la lista de alias y la lista de IPs. Puedes **desempaquetarla** en tres variables como arriba.

### 3. Resolución inversa

```python
nombre, alias, ips = socket.gethostbyaddr("8.8.8.8")
print(nombre)
```

- `gethostbyaddr(ip)` devuelve la misma tupla de tres elementos. El primero es el nombre.

### 4. Errores específicos de DNS

```python
try:
    socket.gethostbyname("dominio-que-no-existe-12345.com")
except socket.gaierror:
    print("No se pudo resolver el nombre")

try:
    socket.gethostbyaddr("10.255.255.1")
except socket.herror:
    print("Esa IP no tiene nombre inverso")
```

- `socket.gaierror` aparece cuando **falla la resolución directa** (nombre inexistente o sin conexión).
- `socket.herror` aparece cuando **falla la inversa**.
- Estas dos son subclases de `OSError`, por si quieres capturar cualquier error de red a la vez.

### 5. Validar la entrada

Antes de la resolución inversa, comprueba que lo escrito es una IP válida con la función del ejercicio 14, o con `ipaddress.ip_address()` del ejercicio 27. Así evitas consultas sin sentido.

### 6. Comprobar con herramientas del sistema

Para verificar que tu programa funciona, compara con:

```bash
nslookup example.com
dig example.com
host 8.8.8.8
```

- Si no tienes `dig`/`nslookup`: `sudo apt install dnsutils`.
- Son las herramientas con las que trabajarás a diario como administrador.

### 7. `localhost`

`socket.gethostbyname("localhost")` devuelve `127.0.0.1`, que se resuelve con el fichero `/etc/hosts` de tu equipo, no por DNS. Mira el fichero con `cat /etc/hosts`.

---

## 💡 Pistas

- Estructura de menú del ejercicio 10 y funciones aparte para cada consulta.
- Cada función debe devolver el resultado o `None`, y el menú decide qué mensaje mostrar.
- Recuerda desempaquetar la tupla: `nombre, alias, ips = ...`.
- Si falla todo, comprueba tu conexión y que `ping 8.8.8.8` funciona.

---

## 🚀 Retos extra

1. **Medir el tiempo:** muestra cuánto tarda cada consulta (`time.perf_counter()`).
2. **Varios dominios:** lee una lista de dominios desde un fichero y resuélvelos todos.
3. **IPv6:** obtén también las direcciones IPv6 (*investiga `socket.getaddrinfo` con `socket.AF_INET6`*).
4. **Otros registros:** instala `dnspython` (en un `venv`, ejercicio 31) y consulta registros `MX` y `TXT` de un dominio.
5. **Comparar servidores DNS:** con `dnspython` consulta el mismo dominio usando `8.8.8.8` y `1.1.1.1`.
6. **Resolver una subred entera:** haz la inversa de cada IP de una red pequeña **propia** (usa el ejercicio 27).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Entrada | Resultado esperado |
|---|---|
| `localhost` | `127.0.0.1` |
| `example.com` | una o más IPv4 (compara con `nslookup example.com`) |
| `dominio-que-no-existe-12345.com` | mensaje de no resuelto, sin traceback |
| `8.8.8.8` (inversa) | un nombre, normalmente `dns.google` |
| `abc` como IP (inversa) | aviso de IP no válida |
