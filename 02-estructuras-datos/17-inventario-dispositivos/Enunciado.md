# Ejercicio 17 · Inventario de dispositivos de red

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa con menú que gestione un **inventario de dispositivos de red**. Cada dispositivo tiene:

- `nombre` (por ejemplo `sw-core-01`)
- `ip`
- `tipo` (`router`, `switch`, `firewall`, `servidor`...)
- `estado` (`activo` o `inactivo`)

Menú:

```
1. Añadir dispositivo
2. Listar dispositivos
3. Buscar por nombre
4. Filtrar por tipo
5. Eliminar dispositivo
0. Salir
```

Para probar, empieza con este inventario:

```python
inventario = [
    {"nombre": "rt-borde-01", "ip": "10.0.0.1", "tipo": "router", "estado": "activo"},
    {"nombre": "sw-core-01", "ip": "10.0.0.2", "tipo": "switch", "estado": "activo"},
    {"nombre": "fw-01", "ip": "10.0.0.254", "tipo": "firewall", "estado": "inactivo"},
]
```

### Ejemplo de ejecución (opción 2)

```
1. rt-borde-01 | 10.0.0.1 | router | activo
2. sw-core-01 | 10.0.0.2 | switch | activo
3. fw-01 | 10.0.0.254 | firewall | inactivo
```

---

## 📖 Conceptos nuevos

### 1. Diccionarios para representar objetos

```python
libro = {"titulo": "Dune", "autor": "Frank Herbert", "paginas": 700}
print(libro["titulo"])
libro["paginas"] = 688
```

- Un diccionario con varias claves describe **una cosa con varios atributos** (un libro, un dispositivo, una persona).
- Se lee con `libro["clave"]` y se modifica asignando: `libro["paginas"] = 688`.

### 2. Lista de diccionarios

```python
biblioteca = [
    {"titulo": "Dune", "paginas": 700},
    {"titulo": "1984", "paginas": 350},
]

for libro in biblioteca:
    print(libro["titulo"])
```

- Una **lista de diccionarios** es como una tabla: cada diccionario es una fila y cada clave una columna.
- Se recorre con `for` y, dentro, cada elemento es un diccionario.
- Es una estructura muy común: de este tipo son los datos que devuelven las APIs en formato JSON.

### 3. Añadir un diccionario a la lista

```python
biblioteca.append({"titulo": "Ubik", "paginas": 250})
```

- Construyes el diccionario con los datos que pides al usuario y lo añades con `.append()`.

### 4. Buscar dentro de una lista de diccionarios

```python
for libro in biblioteca:
    if libro["titulo"] == "1984":
        print("Encontrado")
```

- Recorres la lista y comparas la clave que te interesa.
- Si solo quieres el **primero** que coincida, puedes salir con `break` o con `return` dentro de una función.

### 5. Eliminar un elemento de la lista

```python
biblioteca.remove(libro)
```

- `.remove()` también funciona con diccionarios: le pasas el diccionario encontrado.
- ⚠️ **Nunca borres elementos de una lista mientras la recorres** con `for`; puede saltarse elementos. Mejor: encuentra el elemento, sal del bucle y luego bórralo.

### 6. Organizar con funciones

Un menú con 5 opciones queda mucho más limpio si cada opción es una **función** (`anadir()`, `listar()`, `buscar()`...). Practica lo aprendido en el ejercicio 14 y mantén el menú con llamadas cortas.

---

## 💡 Pistas

- Empieza solo con `listar` y comprueba que funciona antes de añadir más opciones.
- Para buscar, decide si diferenciar mayúsculas de minúsculas (`.lower()` ayuda).
- Si usas funciones, pásales el inventario como parámetro.
- Para filtrar por tipo, recorre la lista y muestra solo los que coincidan.

---

## 🚀 Retos extra

1. **Evitar duplicados:** no permitas dos dispositivos con el mismo nombre o la misma IP.
2. **Validar IP:** reutiliza `es_ip_valida()` del ejercicio 14.
3. **Cambiar estado:** añade una opción para activar o desactivar un dispositivo.
4. **Resumen:** muestra cuántos dispositivos hay por tipo y cuántos están inactivos.
5. **Ordenar:** lista los dispositivos ordenados por nombre (*investiga `sorted()` con `key=lambda d: d["nombre"]`*).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- Listar muestra los 3 dispositivos iniciales con todos sus datos.
- Añadir un cuarto dispositivo y listar → aparecen 4.
- Buscar `sw-core-01` → muestra sus datos. Buscar `xyz` → avisa de que no existe.
- Filtrar por `router` → solo `rt-borde-01`.
- Eliminar un dispositivo → deja de aparecer al listar.
