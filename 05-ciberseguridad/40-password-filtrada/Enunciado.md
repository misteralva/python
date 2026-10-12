# Ejercicio 40 · Comprobar si una contraseña ha sido filtrada

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea un programa que compruebe si una contraseña aparece en **filtraciones de datos conocidas**, usando la API pública **Pwned Passwords** de *Have I Been Pwned*, **sin enviar nunca la contraseña** (ni su hash completo) a Internet.

1. Pide la contraseña **sin mostrarla** al escribir.
2. Calcula su hash **SHA-1**.
3. Envía a la API **solo los 5 primeros caracteres** del hash.
4. Busca en la respuesta el resto del hash y muestra **cuántas veces** ha aparecido en filtraciones (o que no aparece).

### Ejemplo de ejecución

```
Contraseña a comprobar: 
⚠️ Esta contraseña ha aparecido en filtraciones 12345678 veces. No la uses.
```

```
Contraseña a comprobar: 
✅ No se ha encontrado en filtraciones conocidas.
```

> Las cifras exactas cambian con el tiempo: la API se actualiza continuamente.

---

## 📖 Conceptos nuevos

### 1. El problema: comprobar sin revelar

Si enviases tu contraseña a un servicio para ver si está filtrada, **le estarías entregando tu contraseña**. La solución se llama **k-anonimato** (*k-anonymity*):

1. Calculas el hash de la contraseña **en tu equipo**.
2. Envías **solo los 5 primeros caracteres** del hash.
3. El servidor devuelve **todos** los hashes (restos) que empiezan por esos 5 caracteres: cientos de resultados.
4. **Tú** buscas localmente si el resto de tu hash está en esa lista.

El servidor **nunca sabe** cuál de todos esos resultados es el tuyo. Es un excelente ejemplo de diseño que protege la privacidad.

### 2. SHA-1 en este caso

```python
import hashlib

hash_sha1 = hashlib.sha1("password".encode("utf-8")).hexdigest().upper()
print(hash_sha1)
```

- Resultado: `5BAA61E4C9B93F3F0682250B6CF8331B7EE68FD8`.
- `.upper()` pasa a **mayúsculas**, porque la API devuelve los hashes en mayúsculas.
- ⚠️ SHA-1 está **roto para uso criptográfico** (ejercicio 35). Aquí **no lo usamos para proteger nada**, sino porque la API identifica así las contraseñas filtradas. No lo uses para guardar contraseñas de verdad.

### 3. Dividir el hash en prefijo y sufijo

```python
prefijo = hash_sha1[:5]
sufijo = hash_sha1[5:]
```

- Esto se llama **troceado** (*slicing*): `[:5]` son los 5 primeros caracteres y `[5:]` es todo lo que viene después.
- Con el hash de ejemplo: prefijo `5BAA6` y sufijo `1E4C9B93F3F0682250B6CF8331B7EE68FD8`.

### 4. Consultar la API

```python
import requests

url = f"https://api.pwnedpasswords.com/range/{prefijo}"
respuesta = requests.get(url, timeout=5)
```

- Solo envías el prefijo en la URL.
- La respuesta es **texto**, con una línea por cada hash filtrado cuyo prefijo coincide:

```
0018A45C4D1DEF81644B54AB7F969B88D65:1
00D4F6E8FA6EECAD2A3AA415EEC418D38EC:2
...
```

- Cada línea es `SUFIJO:VECES`. Los sufijos son los **35 caracteres restantes** del hash.
- Necesitas `requests` en tu entorno virtual (ejercicio 31).

### 5. Recorrer la respuesta

```python
for linea in respuesta.text.splitlines():
    sufijo_api, veces = linea.split(":")
    if sufijo_api == sufijo:
        print(int(veces))
```

- `.splitlines()` separa el texto en una lista de líneas.
- `linea.split(":")` divide cada una en dos partes, que desempaquetas en dos variables (como en el ejercicio 32).
- `int(veces)` convierte el contador en número.

### 6. Contraseña sin mostrarla

Usa `getpass.getpass("...")` (ejercicio 33) para que no se vea mientras la escribes.

### 7. Comparar y diseñar bien

- **Compara siempre el sufijo en mayúsculas** con el de la API, o fallarás por diferencias de mayúsculas y minúsculas.
- **Gestiona errores de red** (`requests.exceptions...`, ejercicio 31) y códigos HTTP distintos de 200.
- **Nunca imprimas ni guardes** la contraseña en claro, ni la escribas en logs.

---

## 💡 Pistas

- Estructura: función `veces_filtrada(password)` que devuelva un `int` (0 si no aparece) o `None` si hubo un error de red.
- Comprueba tu hash con el ejemplo (`password` → `5BAA6...`) antes de llamar a la API.
- Para probar un caso positivo, usa una contraseña muy mala y muy típica (`password`, `123456`). Para uno negativo, una contraseña larga y aleatoria **de las que genera tu ejercicio 39** (que aún no uses en ningún sitio real).
- No uses contraseñas reales tuyas para pruebas.

---

## 🚀 Retos extra

1. **Verificar la contraseña de ejercicio 39:** integra ambos programas: genera una contraseña y compruébala antes de mostrarla.
2. **Varias contraseñas desde un fichero:** comprueba una lista y muestra un resumen (¡y no guardes las contraseñas en claro!).
3. **Relleno de respuesta (*padding*):** investiga la cabecera `Add-Padding: true` de la API y por qué mejora la privacidad.
4. **Reintentos:** si falla la red, reintenta con pausa.
5. **Política completa:** une el validador (ejercicio 16), la entropía (ejercicio 39) y esta comprobación en una única herramienta de auditoría.
6. **Explicarlo:** escribe en tu README, con tus palabras, por qué enviar solo 5 caracteres protege la privacidad.

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
| `password` | SHA-1 `5BAA61E4C9B93F3F0682250B6CF8331B7EE68FD8`, prefijo `5BAA6`, y aparece en filtraciones muchísimas veces |
| Contraseña larga y aleatoria generada por ti | no aparece (0 veces) |
| Sin conexión a Internet | mensaje de error de red, sin traceback |
| Se ve la contraseña mientras escribes | ❌ revisa que usas `getpass` |
