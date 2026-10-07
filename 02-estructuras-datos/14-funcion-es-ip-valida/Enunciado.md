# Ejercicio 14 · La función `es_ip_valida()`

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea una **función** llamada `es_ip_valida` que reciba un texto y devuelva `True` si es una dirección IPv4 válida, o `False` si no lo es.

Una IPv4 es válida cuando:

- Tiene **exactamente 4 partes** separadas por puntos.
- Cada parte es un **número entero** (solo dígitos).
- Cada número está entre **0 y 255**.

Después, en el programa principal, pide una IP al usuario y muestra si es válida usando la función.

### Ejemplo de ejecución

```
Introduce una IP: 192.168.1.1
La IP 192.168.1.1 es válida.
```

```
Introduce una IP: 300.1.1.1
La IP 300.1.1.1 no es válida.
```

---

## 📖 Conceptos nuevos

### 1. Funciones: `def`

```python
def saludar(nombre):
    print(f"Hola, {nombre}")

saludar("Ana")
saludar("Luis")
```

- `def` **define** una función: un bloque de código con nombre que puedes reutilizar.
- `nombre` entre paréntesis es un **parámetro**: el dato que la función recibe.
- La función **no se ejecuta** hasta que la **llamas** con su nombre y un valor: `saludar("Ana")`.
- Igual que `if` y `for`, lleva dos puntos y el cuerpo va con sangría.
- Ventaja: escribes la lógica una vez y la usas cuantas veces quieras.

### 2. `return`: devolver un resultado

```python
def doble(numero):
    return numero * 2

resultado = doble(5)
print(resultado)
```

- `return` hace que la función **devuelva un valor** a quien la llamó, y **termina** la función en ese punto.
- Una función sin `return` devuelve `None` (nada).
- Aquí `resultado` vale `10`.

### 3. Devolver `True` o `False`

```python
def termina_en_com(dominio):
    return dominio.endswith(".com")

print(termina_en_com("google.com"))
print(termina_en_com("wikipedia.org"))
```

- Una función que responde a una pregunta de sí/no suele devolver un **booleano** (`True` / `False`).
- `.endswith()` comprueba cómo termina un texto. Resultado: `True` y `False`.
- Estas funciones se usan después directamente en un `if`: `if termina_en_com(x):`.

### 4. Salir antes con `return`

```python
def es_positivo(texto):
    if not texto.isdigit():
        return False
    return int(texto) > 0
```

- Puedes poner varios `return`. En cuanto se ejecuta uno, la función acaba.
- Es útil para **descartar casos inválidos pronto**, evitando errores más adelante (aquí, no intentamos `int()` si no son dígitos).

### 5. Dividir una IP

```python
partes = "10.0.0.1".split(".")
print(partes)
print(len(partes))
```

- `.split(".")` divide por puntos y devuelve una lista.
- Resultado: `['10', '0', '0', '1']` y `4`. ¡Son textos, no números!

### 6. Docstrings (documentar la función)

```python
def doble(numero):
    """Devuelve el doble de un número."""
    return numero * 2
```

- El texto entre triples comillas justo debajo del `def` describe qué hace la función. Es una buena práctica.

---

## 💡 Pistas

- Primero comprueba que hay 4 partes. Si no, `return False` ya.
- Recorre las partes con un `for`. Si alguna **no es un número** o está **fuera de 0-255**, `return False` dentro del bucle.
- Si el bucle termina sin haber devuelto `False`, la IP es válida: `return True` al final.
- ¡Ojo con el orden! Comprueba `.isdigit()` **antes** de convertir con `int()`.
- Reutiliza lo que hiciste en el ejercicio 08.

---

## 🚀 Retos extra

1. **Ceros a la izquierda:** una parte como `"01"` no suele considerarse válida. Haz que la función la rechace.
2. **Espacios:** ¿qué pasa con `" 192.168.1.1 "`? Decide si debes limpiar los espacios (*investiga `.strip()`*).
3. **Función de IP privada:** crea `es_ip_privada()` que indique si la IP es de red privada.
4. **Validar una lista:** recibe una lista de IPs y muestra cuáles son válidas y cuáles no.
5. **Comparar con la librería:** investiga el módulo `ipaddress` y compara su resultado con el de tu función.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| IP | ¿Válida? |
|---|---|
| `192.168.1.1` | ✅ Sí |
| `0.0.0.0` | ✅ Sí |
| `255.255.255.255` | ✅ Sí |
| `256.1.1.1` | ❌ No |
| `192.168.1` | ❌ No |
| `1.2.3.4.5` | ❌ No |
| `a.b.c.d` | ❌ No |
| `192.168.1.` | ❌ No |
| `-1.2.3.4` | ❌ No |
