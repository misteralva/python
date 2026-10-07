# Ejercicio 15 · Intentos fallidos por usuario

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Un servidor ha registrado los usuarios de varios **intentos de inicio de sesión fallidos**. Crea un programa que:

1. **Cuente** cuántos intentos fallidos ha tenido cada usuario.
2. Muestre el resultado.
3. Marque como **sospechosos** a los usuarios con **3 o más** intentos fallidos.

Usa estos datos simulados (cópialos en tu código):

```python
intentos = ["root", "admin", "root", "david", "root", "admin", "ana", "root", "admin", "david"]
```

### Ejemplo de salida

```
root: 4 intentos  ⚠️ SOSPECHOSO
admin: 3 intentos  ⚠️ SOSPECHOSO
david: 2 intentos
ana: 1 intentos
```

---

## 📖 Conceptos nuevos

### 1. Contexto: ¿por qué contar intentos fallidos?

En seguridad, muchos fallos de login seguidos de un mismo usuario (o desde una misma IP) son la huella típica de un **ataque de fuerza bruta**. Este ejercicio es una versión simplificada de lo que hacen herramientas como `fail2ban`: leer registros y detectar patrones.

### 2. Repaso: el diccionario como contador

Ya lo viste en el ejercicio 13: recorres los datos y, por cada elemento, sumas 1 a su contador.

```python
colores = ["rojo", "azul", "rojo"]
conteo = {}

for c in colores:
    conteo[c] = conteo.get(c, 0) + 1

print(conteo)
```

- `.get(c, 0)` devuelve el valor actual, o `0` si es la primera vez que aparece.
- Resultado: `{'rojo': 2, 'azul': 1}`.

### 3. Umbrales

```python
UMBRAL = 3

if conteo["rojo"] >= UMBRAL:
    print("Superado")
```

- Un **umbral** es el valor a partir del cual se dispara una alerta.
- Define el umbral como una **constante** al principio, así puedes ajustarlo sin buscar por todo el código.

### 4. Ordenar un diccionario por valor

```python
ordenado = sorted(conteo.items(), key=lambda par: par[1], reverse=True)
print(ordenado)
```

- `conteo.items()` entrega pares `(clave, valor)`.
- `key=lambda par: par[1]` indica a `sorted()` que ordene por el **segundo elemento** del par (el valor).
- `reverse=True` ordena de mayor a menor.
- No tienes que memorizarlo: úsalo como receta. `lambda` es una mini función sin nombre.
- El resultado es una **lista de tuplas**, por ejemplo `[('rojo', 2), ('azul', 1)]`.

### 5. Desempaquetar pares en un `for`

```python
for nombre, cantidad in ordenado:
    print(nombre, cantidad)
```

- Cada tupla `(nombre, cantidad)` se separa automáticamente en dos variables.

---

## 💡 Pistas

- Primero construye el diccionario de conteo; después imprime el resultado en un segundo bucle.
- Para el listado ordenado, usa la receta del apartado 4. Si te resulta complicado, hazlo primero **sin ordenar** y mejóralo después.
- Dentro del bucle de impresión, decide con un `if` si añades el aviso de sospechoso.

---

## 🚀 Retos extra

1. **Por IP:** usa la lista `["10.0.0.5", "10.0.0.8", "10.0.0.5", ...]` y detecta IPs sospechosas.
2. **Líneas de log:** parte de líneas reales como `"Failed password for root from 10.0.0.5"` y extrae usuario e IP con `.split()`.
3. **Total y porcentaje:** muestra qué porcentaje del total de intentos corresponde a cada usuario.
4. **Umbral configurable:** pide el umbral al usuario.
5. **Resumen:** al final, indica cuántos usuarios son sospechosos y cuántos no.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Usuario | Intentos | ¿Sospechoso? |
|---|---|---|
| root | 4 | ✅ Sí |
| admin | 3 | ✅ Sí |
| david | 2 | ❌ No |
| ana | 1 | ❌ No |
