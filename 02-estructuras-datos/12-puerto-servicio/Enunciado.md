# Ejercicio 12 · Diccionario puerto → servicio

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐ Medio

---

## 🎯 Objetivo

Crea un programa que use un **diccionario** para asociar números de puerto con el servicio que suele usarlos.

Parte de este diccionario (cópialo en tu código):

| Puerto | Servicio |
|---|---|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 3389 | RDP |

El programa debe:

1. Pedir un número de puerto y mostrar su servicio, o indicar que es **desconocido** si no está.
2. Mostrar un **listado** de todos los puertos conocidos.

### Ejemplo de ejecución

```
Introduce un puerto: 22
El puerto 22 corresponde a: SSH
Introduce un puerto: 9999
El puerto 9999 es desconocido.
```

---

## 📖 Conceptos nuevos

### 1. Diccionarios

```python
capitales = {"España": "Madrid", "Francia": "París"}
print(capitales["España"])
```

- Un **diccionario** guarda pares **clave: valor** entre llaves `{}`.
- Se accede al valor escribiendo su clave entre corchetes: `capitales["España"]` da `"Madrid"`.
- A diferencia de una lista, no se busca por posición sino **por clave**, lo que es muy rápido.
- Las claves pueden ser textos o números. En este ejercicio, los puertos serán números enteros (`int`).

### 2. Acceder sin riesgo con `.get()`

```python
print(capitales.get("Italia", "No lo sé"))
```

- Si la clave no existe, `capitales["Italia"]` provoca un error (`KeyError`).
- `.get(clave, valor_por_defecto)` devuelve el valor por defecto en lugar de fallar.
- Aquí se muestra `No lo sé`.

### 3. Comprobar si una clave existe con `in`

```python
if "Francia" in capitales:
    print("Está")
```

- `in` en un diccionario comprueba las **claves**, no los valores.

### 4. Añadir y modificar elementos

```python
capitales["Italia"] = "Roma"
capitales["España"] = "Barcelona"
```

- Si la clave **no existe**, se **crea** un nuevo par.
- Si ya **existe**, se **sobrescribe** su valor.

### 5. Recorrer un diccionario

```python
for pais, ciudad in capitales.items():
    print(f"{pais} -> {ciudad}")
```

- `.items()` devuelve cada par (clave, valor) en cada vuelta.
- También existen `.keys()` (solo claves) y `.values()` (solo valores).

### 6. Cuidado con los tipos

`input()` devuelve texto, pero las claves del diccionario son **números**. `"22"` y `22` son datos distintos para Python: tendrás que convertir antes de buscar.

---

## 💡 Pistas

- Define el diccionario al principio, con los puertos como números sin comillas.
- Usa `.get()` con un valor por defecto para evitar el `if`.
- Para el listado, recorre con `.items()`.

---

## 🚀 Retos extra

1. **Añadir puertos:** permite al usuario añadir un puerto y servicio nuevos al diccionario mientras el programa corre.
2. **Búsqueda inversa:** pide el nombre de un servicio (por ejemplo `SSH`) y muestra su puerto. ¿Cómo lo harías sin repetir información?
3. **Listado ordenado:** muestra los puertos de menor a mayor (*investiga `sorted()`*).
4. **Menú:** convierte el programa en un menú con varias opciones (consultar, añadir, listar, salir).
5. **Más servicios:** amplía el diccionario con otros 10 puertos que investigues (por ejemplo 110 POP3, 143 IMAP, 5432 PostgreSQL).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Puerto | Resultado esperado |
|---|---|
| 22 | SSH |
| 443 | HTTPS |
| 3389 | RDP |
| 8080 | desconocido |
| 0 | desconocido |
