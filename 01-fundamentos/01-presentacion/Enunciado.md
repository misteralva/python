# Ejercicio 01 · Presentación

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐ Básico

---

## 🎯 Objetivo

Crea un programa que:

1. Pregunte al usuario su **nombre**.
2. Pregunte su **edad**.
3. Lo salude por su nombre.
4. Le diga si es **mayor o menor de edad** (mayor de edad = 18 años o más).

### Ejemplo de ejecución

```
¿Cómo te llamas? Ana
¿Cuántos años tienes? 25
Hola, Ana. Encantado de conocerte.
Eres mayor de edad.
```

```
¿Cómo te llamas? Leo
¿Cuántos años tienes? 15
Hola, Leo. Encantado de conocerte.
Eres menor de edad.
```

---

## 📖 Conceptos nuevos

Antes de empezar, aquí tienes las herramientas que vas a necesitar. Los ejemplos **no resuelven el ejercicio**, solo muestran cómo funciona cada pieza.

### 1. `print()`: mostrar texto en pantalla

```python
print("Hola, mundo")
```

- `print` es una **función** que escribe en pantalla lo que le pasas entre paréntesis.
- El texto va entre comillas (`"..."` o `'...'`). A un texto se le llama **cadena** (*string*).

### 2. Variables: guardar datos

```python
ciudad = "Barcelona"
print(ciudad)
```

- `ciudad` es una **variable**: un nombre que apunta a un dato guardado.
- El `=` **no** significa "igual" como en matemáticas, sino "guarda lo de la derecha en lo de la izquierda".
- Esta vez `print(ciudad)` no lleva comillas porque queremos mostrar el **contenido** de la variable, no la palabra "ciudad".

### 3. `input()`: preguntar al usuario

```python
animal = input("¿Cuál es tu animal favorito? ")
print(animal)
```

- `input()` muestra el texto que le pasas, **espera** a que el usuario escriba y pulse Enter, y **devuelve** lo escrito.
- Lo que devuelve se guarda en la variable `animal`.
- ⚠️ `input()` **siempre devuelve texto**, aunque el usuario escriba un número.

### 4. `int()`: convertir texto en número

```python
texto = "7"
numero = int(texto)
print(numero + 3)
```

- `"7"` es un texto, no se puede comparar ni sumar como número.
- `int(texto)` lo convierte en un número entero (*integer*).
- Aquí `numero + 3` da `10`. Sin `int()`, Python daría un error.

También puedes convertir directamente al recibir el dato:

```python
numero = int(input("Dame un número: "))
```

### 5. Condiciones: `if`, `else` y comparaciones

```python
temperatura = 30

if temperatura > 25:
    print("Hace calor")
else:
    print("No hace tanto calor")
```

- `if` ejecuta su bloque **solo si** la condición es verdadera. Si no, se ejecuta el bloque de `else`.
- **Los dos puntos `:`** al final de `if` y `else` son obligatorios.
- **La sangría (indentación)** es obligatoria en Python: las líneas con 4 espacios más a la derecha son las que pertenecen a ese bloque.

Operadores de comparación:

| Operador | Significa |
|---|---|
| `>` | mayor que |
| `<` | menor que |
| `>=` | mayor o igual que |
| `<=` | menor o igual que |
| `==` | igual que (doble `=`) |
| `!=` | distinto de |

### 6. f-strings: mezclar texto y variables

```python
pais = "España"
print(f"Vivo en {pais}")
```

- La `f` antes de las comillas activa el formato especial.
- Lo que va entre llaves `{}` se sustituye por el valor de la variable.
- Resultado: `Vivo en España`.

---

## 💡 Pistas

- Necesitarás **dos variables**: una para el nombre y otra para la edad.
- La edad debe ser un **número** para poder compararla. ¿Qué función viste para eso?
- ¿Cuál es la condición exacta para ser mayor de edad? Piensa en qué pasa justo con 18 años: ¿`>` o `>=`?
- Para el saludo, un f-string te ahorra concatenar textos.

---

## 🚀 Retos extra

Cuando tengas la versión básica funcionando, prueba a mejorarla:

1. **Tres categorías:** en vez de dos, di si es niño (menos de 12), adolescente (12 a 17) o adulto (18 o más). *Investiga: `elif`.*
2. **Edad imposible:** si la edad es negativa o mayor que 120, muestra un mensaje de error en vez de clasificarla.
3. **Cuántos años faltan:** si es menor de edad, calcula e indica cuántos años le faltan para cumplir 18.
4. **Entrada inválida:** ¿qué pasa si el usuario escribe "veinte" en vez de `20`? Investiga qué es `try/except` y cómo evitar que el programa falle.

---

## ▶️ Cómo ejecutarlo

Guarda tu código en un archivo llamado `solucion.py` y, desde esta carpeta, ejecuta:

```bash
python3 solucion.py
```

- `python3` es el intérprete de Python.
- `solucion.py` es el archivo que quieres ejecutar.

---

## ✅ Comprueba tu solución

Prueba estos casos y verifica que el resultado es el esperado:

| Edad | Resultado esperado |
|---|---|
| 17 | menor de edad |
| 18 | mayor de edad |
| 40 | mayor de edad |
| 5 | menor de edad |
