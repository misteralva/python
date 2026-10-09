# Ejercicio 31 · Consultar una API con `requests`

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que consulte dos **APIs públicas** y muestre la información de forma legible:

1. **Tu IP pública**, con el servicio `https://api.ipify.org?format=json`.
2. **Datos de un usuario de GitHub** (pregunta el nombre), con `https://api.github.com/users/NOMBRE`: nombre de usuario, número de repositorios públicos, seguidores y fecha de creación de la cuenta.

El programa debe gestionar **errores de red** y un usuario que no existe, sin fallar.

### Ejemplo de ejecución

```
Tu IP pública: 203.0.113.45

Usuario de GitHub: misteralva
Usuario: misteralva
Repositorios públicos: 12
Seguidores: 3
Cuenta creada: 2022-05-14
```

> Los datos que ves dependerán de la cuenta consultada. Los servicios externos pueden cambiar su formato o dejar de estar disponibles, así que revisa su documentación si algo no coincide.

---

## 📖 Conceptos nuevos

### 1. ¿Qué es una API?

Una **API web** es un servicio al que un programa pide datos mediante una URL y que responde, normalmente, en formato **JSON** (el mismo que usaste en el ejercicio 26). Muchas herramientas de redes y seguridad (consulta de reputación de IPs, geolocalización, tickets, etc.) funcionan así.

### 2. Preparar el entorno: instalar `requests`

`requests` no viene con Python. Desde Ubuntu 23.04, el sistema no permite instalar paquetes de Python "sueltos" con `pip` para no romper cosas, así que se usa un **entorno virtual**:

```bash
python3 -m venv venv
source venv/bin/activate
pip install requests
```

- `python3 -m venv venv` crea una carpeta `venv` con una instalación de Python aislada para este proyecto.
- `source venv/bin/activate` la **activa**: verás `(venv)` al principio de la línea de la terminal.
- `pip install requests` instala la librería **solo dentro** de ese entorno.
- Para salir del entorno: `deactivate`.
- Si falta `venv`: `sudo apt install python3-venv`.

⚠️ **No subas la carpeta `venv/` a GitHub.** Crea un fichero `.gitignore` con la línea `venv/`. Para que otros puedan reproducir tu entorno, guarda las dependencias:

```bash
pip freeze > requirements.txt
```

- `pip freeze` lista los paquetes instalados con su versión y `>` lo guarda en un fichero.
- Otra persona los instalaría con `pip install -r requirements.txt`.

### 3. Hacer una petición GET

```python
import requests

respuesta = requests.get("https://api.ipify.org?format=json", timeout=5)
print(respuesta.status_code)
print(respuesta.text)
```

- `requests.get(url)` hace una petición **GET** (pedir datos).
- **Pon siempre `timeout=`**: sin él, tu programa podría esperar indefinidamente.
- `.status_code` es el código HTTP: `200` = OK, `404` = no encontrado, `403` = prohibido, `429` = demasiadas peticiones, `500` = error del servidor.
- `.text` es la respuesta como texto.

### 4. Leer el JSON

```python
datos = respuesta.json()
print(datos["ip"])
```

- `.json()` convierte la respuesta en un **diccionario** (o lista) de Python.
- A partir de ahí, accedes con claves como en el ejercicio 12.
- Si una clave puede no existir, usa `datos.get("clave", "no disponible")`.

### 5. Gestionar errores

```python
try:
    respuesta = requests.get(url, timeout=5)
    respuesta.raise_for_status()
except requests.exceptions.Timeout:
    print("Tiempo de espera agotado")
except requests.exceptions.ConnectionError:
    print("No hay conexión")
except requests.exceptions.HTTPError:
    print("Error HTTP:", respuesta.status_code)
```

- `.raise_for_status()` lanza `HTTPError` si el código es 4xx o 5xx.
- Captura primero los errores más específicos.
- `requests.exceptions.RequestException` engloba a todos los anteriores.

### 6. Construir la URL con el dato del usuario

```python
url = f"https://api.github.com/users/{usuario}"
```

- Cuidado: nunca incluyas **contraseñas o tokens** directamente en tu código si lo subes a GitHub.

### 7. Límites de uso (*rate limit*)

Las APIs limitan cuántas peticiones puedes hacer. La de GitHub, sin autenticar, permite un número reducido por hora. Si te pasas, devuelve `403` o `429`. Respeta siempre los límites y evita bucles que repitan consultas sin necesidad.

---

## 💡 Pistas

- Escribe una función `obtener_json(url)` que haga la petición, controle los errores y devuelva el diccionario (o `None`).
- Los campos de GitHub que necesitas se llaman `login`, `public_repos`, `followers` y `created_at`. Comprueba con `print(datos)` el contenido real.
- Para mostrar solo la fecha de `created_at`, quédate con los 10 primeros caracteres (`[:10]`).
- Prueba con un usuario inexistente para ver el error 404.

---

## 🚀 Retos extra

1. **Token por variable de entorno:** si tienes un token de GitHub, léelo con `os.environ.get("GITHUB_TOKEN")` y envíalo en una cabecera `Authorization`. **Nunca** lo escribas en el código.
2. **Repos del usuario:** consulta `https://api.github.com/users/NOMBRE/repos` y muestra los nombres de sus repositorios.
3. **Tu propia IP en dos servicios:** compara el resultado con otro servicio similar y con el que ves en el navegador.
4. **Guardar la respuesta:** guarda el JSON completo en un fichero (ejercicio 26).
5. **Reintentos:** si falla por red, reintenta hasta 3 veces con una pausa entre intentos.
6. **Otra API a tu elección:** busca una API pública sin clave (por ejemplo, de datos abiertos) y haz un pequeño informe.

---

## ▶️ Cómo ejecutarlo

Con el entorno virtual activado:

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Prueba | Resultado esperado |
|---|---|
| Tu IP pública | coincide con la que muestra una web de "mi IP" en tu navegador (si usas VPN, será la de la VPN) |
| Usuario de GitHub existente | muestra repositorios, seguidores y fecha |
| Usuario que no existe | mensaje de "usuario no encontrado" (404), sin traceback |
| Sin conexión (desactiva la red) | mensaje de error de conexión, sin traceback |
