# Ejercicio 08 · Validar un octeto de IP

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐⭐ Básico-medio

---

## 🎯 Objetivo

Crea un programa que pida un **octeto de una dirección IP** (un número entre 0 y 255) y **siga preguntando hasta que el usuario escriba uno válido**. Al final, muestra el valor aceptado.

### Ejemplo de ejecución

```
Introduce un octeto (0-255): 300
Valor no válido. Debe estar entre 0 y 255.
Introduce un octeto (0-255): -5
Valor no válido. Debe estar entre 0 y 255.
Introduce un octeto (0-255): 192
Octeto aceptado: 192
```

---

## 📖 Conceptos nuevos

### 1. ¿Por qué 0-255?

Una dirección IPv4 (como `192.168.1.10`) está formada por **4 octetos**. Cada octeto ocupa 8 bits, por lo que puede valer desde `00000000` (0) hasta `11111111` (255).

### 2. Validar con un `while`

```python
clave = input("Contraseña: ")

while clave != "secreto":
    print("Incorrecta.")
    clave = input("Contraseña: ")

print("Acceso concedido")
```

- Se pide el dato **una vez antes** del bucle.
- El `while` se repite **mientras el dato no sea válido**, y dentro vuelve a pedirlo.
- Cuando el dato es válido, la condición del `while` falla y el programa continúa.

### 3. Condiciones con `or` y `not`

```python
n = 120

if n < 10 or n > 100:
    print("Fuera del rango 10-100")

if not (10 <= n <= 100):
    print("Esto es equivalente")
```

- Un valor está **fuera de un rango** cuando es menor que el mínimo **o** mayor que el máximo.
- `not (...)` invierte una condición completa. Las dos formas son válidas; elige la que te resulte más clara.

### 4. Otra forma: `while True` + `break`

```python
while True:
    texto = input("Escribe algo no vacío: ")
    if texto != "":
        break
    print("No puede estar vacío.")
```

- En vez de pedir el dato antes del bucle, entras en un bucle infinito y sales con `break` cuando el dato es válido.
- Ambos estilos funcionan. Este evita repetir la línea del `input()`.

---

## 💡 Pistas

- La condición de repetición es "el dato **no** es válido", es decir, menor que 0 **o** mayor que 255.
- Convierte lo leído a entero antes de comparar.
- Puedes escribirlo con cualquiera de los dos estilos (`while condición` o `while True` con `break`). Prueba los dos.

---

## 🚀 Retos extra

1. **IP completa:** pide los 4 octetos (validando cada uno) y muestra la IP completa en formato `a.b.c.d`.
2. **Clase de IP:** según el primer octeto, indica la clase (A: 1-126, B: 128-191, C: 192-223).
3. **IP privada:** indica si la IP es privada (`10.x.x.x`, `172.16.x.x`-`172.31.x.x` o `192.168.x.x`).
4. **Intentos máximos:** tras 3 intentos fallidos, el programa termina con un mensaje.
5. **Entrada inválida:** si el usuario escribe texto en vez de un número, vuelve a preguntar sin fallar (*investiga `try/except`*).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Valor introducido | Resultado esperado |
|---|---|
| 0 | aceptado |
| 255 | aceptado |
| 192 | aceptado |
| -1 | rechazado, vuelve a preguntar |
| 256 | rechazado, vuelve a preguntar |
| 1000 | rechazado, vuelve a preguntar |
