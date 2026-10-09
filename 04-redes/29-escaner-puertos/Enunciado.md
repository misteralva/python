# Ejercicio 29 · Escáner de puertos TCP

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

> ## ⚠️ Aviso legal y ético
> Escanea puertos **solo** en tu propio equipo (`127.0.0.1`), en tus máquinas virtuales o en sistemas que administres y para los que tengas **autorización por escrito**. Un escaneo no autorizado puede ser delito según la legislación aplicable y viola las políticas de casi cualquier red. En el trabajo o en prácticas, **nunca** sin permiso explícito.

---

## 🎯 Objetivo

Crea un programa que pida un **host** y un **rango de puertos** y muestre qué puertos TCP están **abiertos**.

### Preparación: un puerto abierto para probar

En una terminal, lanza un servidor web de prueba en el puerto 8000:

```bash
python3 -m http.server 8000
```

- `-m http.server` ejecuta un módulo de Python como programa: un servidor web sencillo que sirve la carpeta actual.
- `8000` es el puerto.
- Déjalo corriendo y abre **otra** terminal para tu escáner. Para pararlo, `Ctrl + C`.

### Ejemplo de ejecución

```
Host a escanear: 127.0.0.1
Puerto inicial: 1
Puerto final: 9000
Puerto 8000 abierto
Escaneo terminado. Puertos abiertos: 1
```

---

## 📖 Conceptos nuevos

### 1. ¿Qué es un escaneo de puertos TCP?

Para ver si un puerto está abierto, se intenta **abrir una conexión TCP** hacia él:

- Si la conexión se **establece**, hay un servicio escuchando: puerto **abierto**.
- Si es **rechazada**, no hay nada escuchando: puerto **cerrado**.
- Si **no hay respuesta**, probablemente un cortafuegos lo filtra: puerto **filtrado**.

### 2. El módulo `socket`

```python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

- Un **socket** es un extremo de una comunicación de red.
- `AF_INET` indica direcciones IPv4 y `SOCK_STREAM` indica **TCP**. (UDP sería `SOCK_DGRAM`.)

### 3. `connect_ex()`: intentar conectar sin excepciones

```python
resultado = s.connect_ex(("127.0.0.1", 8000))
print(resultado)
```

- `connect_ex((host, puerto))` intenta la conexión y devuelve un **número**: `0` si ha ido bien, y un código de error si no.
- Fíjate: la dirección es una **tupla** `(host, puerto)`, con paréntesis dobles.
- Es más cómodo para escanear que `connect()`, que lanza una excepción en cada fallo.

### 4. Tiempo límite con `settimeout()`

```python
s.settimeout(0.5)
```

- Sin tiempo límite, un puerto filtrado podría bloquear tu programa durante muchos segundos.
- `0.5` son medio segundo. Más bajo = más rápido, pero puede dar falsos negativos en redes lentas.

### 5. Cerrar siempre el socket: `with`

```python
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.settimeout(0.5)
    abierto = s.connect_ex(("127.0.0.1", 22)) == 0
```

- Igual que con los ficheros, `with` **cierra el socket** al terminar. Abre un socket **nuevo por cada puerto**.

### 6. Nombre del servicio de un puerto

```python
print(socket.getservbyport(80))
```

- Devuelve el nombre del servicio habitual (`http`). Lanza `OSError` si el puerto no está en la lista del sistema.
- Te puede servir para mostrar `22 (ssh)`, o puedes reutilizar el diccionario del ejercicio 12.

### 7. Funciones

Crea una función `puerto_abierto(host, puerto, timeout)` que devuelva `True` o `False`. Después, un bucle `for puerto in range(inicio, fin + 1)` que la llame. (Recuerda que `range` **no incluye** el final.)

---

## 💡 Pistas

- Valida que los puertos estén entre 0 y 65535 y que el inicial no sea mayor que el final.
- Si tecleas un host que no existe, `connect_ex` puede lanzar `socket.gaierror`. Captúralo.
- Prueba con puertos cerrados primero para ver cómo se comporta; después con el 8000 abierto.
- Para 65535 puertos con 0,5 s de timeout en puertos filtrados podrías tardar mucho. En `localhost` es rápido porque los cerrados rechazan al instante.

---

## 🚀 Retos extra

1. **Servicios:** muestra el nombre del servicio junto al puerto abierto.
2. **Lista de puertos:** acepta también una lista separada por comas (`22,80,443`).
3. **Más rápido:** lanza varios intentos a la vez con hilos (*investiga `ThreadPoolExecutor`*).
4. **Banner:** en un puerto abierto, intenta leer lo que el servicio envía al conectar (*investiga `recv()`; ¡con timeout!*).
5. **Informe:** guarda los resultados en un fichero con fecha y hora de inicio y fin.
6. **Comparar con `nmap`:** instala `nmap` (`sudo apt install nmap`) y compara resultados **solo en localhost**.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Situación | Resultado esperado |
|---|---|
| `127.0.0.1`, puertos 1-9000, con `http.server 8000` activo | puerto 8000 abierto |
| Paras el servidor y repites | ningún puerto abierto (salvo servicios locales tuyos) |
| Puerto final menor que el inicial | aviso de error |
| Host inexistente | mensaje de error claro, sin traceback |
