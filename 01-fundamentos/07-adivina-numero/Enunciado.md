# Ejercicio 07 · Adivina el número

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐⭐ Básico-medio

---

## 🎯 Objetivo

Crea un juego en el que el programa **elige un número secreto al azar entre 1 y 100** y el usuario intenta adivinarlo.

- Tras cada intento, el programa indica si el número secreto es **mayor** o **menor**.
- Cuando el usuario acierte, felicítalo y muestra **cuántos intentos** ha necesitado.

### Ejemplo de ejecución

```
He pensado un número entre 1 y 100. ¡Adivínalo!
Tu intento: 50
El número secreto es mayor.
Tu intento: 75
El número secreto es menor.
Tu intento: 62
¡Correcto! Lo has adivinado en 3 intentos.
```

---

## 📖 Conceptos nuevos

### 1. Importar módulos: `import`

```python
import random
```

- Python incluye muchos **módulos** (conjuntos de funciones ya hechas) que no se cargan solos. Para usarlos hay que **importarlos** al principio del programa.
- `random` es el módulo para generar números aleatorios.

### 2. Número aleatorio con `random.randint()`

```python
import random

dado = random.randint(1, 6)
print(dado)
```

- `random.randint(a, b)` devuelve un entero aleatorio entre `a` y `b`, **ambos incluidos**.
- Cada vez que ejecutes el programa, `dado` valdrá un número distinto entre 1 y 6.
- Se escribe `random.` delante porque la función `randint` pertenece al módulo `random`.

### 3. El bucle `while`

```python
contador = 3

while contador > 0:
    print(contador)
    contador = contador - 1
```

- `while` repite su bloque **mientras la condición sea verdadera**.
- Es distinto de `for`: aquí no sabes de antemano cuántas vueltas habrá.
- Cuidado: si la condición nunca deja de cumplirse, tienes un **bucle infinito**. Aquí `contador` baja en cada vuelta y acaba siendo 0. Para detener un programa atascado, pulsa `Ctrl + C`.

### 4. Contadores y `+=`

```python
vueltas = 0
vueltas += 1
print(vueltas)
```

- `vueltas += 1` es una forma corta de escribir `vueltas = vueltas + 1`.
- Sirve para **contar** cuántas veces ha pasado algo.

### 5. `break`: salir de un bucle

```python
while True:
    clave = input("Escribe 'salir': ")
    if clave == "salir":
        break
```

- `while True` es un bucle que no termina por sí solo, porque la condición siempre es verdadera.
- `break` **sale inmediatamente** del bucle. Es la forma habitual de terminarlo cuando se cumple algo.

---

## 💡 Pistas

- Genera el número secreto **una sola vez, fuera del bucle**. Si lo generas dentro, ¡cambiaría en cada intento!
- Necesitarás una variable contadora de intentos que empiece en 0.
- Dentro del bucle: pide el número, suma un intento y compara con el secreto (`==`, `>`, `<`).
- Piensa dónde poner el `break` para que el juego acabe al acertar.

---

## 🚀 Retos extra

1. **Intentos limitados:** el jugador solo tiene 7 intentos. Si los agota, muestra cuál era el número.
2. **Dificultad:** deja elegir entre fácil (1-50), medio (1-100) y difícil (1-1000).
3. **Jugar otra vez:** al terminar, pregunta si quiere jugar de nuevo.
4. **Entrada inválida:** si escribe algo que no es un número, no gastes un intento y no hagas fallar al programa.
5. **Estrategia:** ¿cuál es el máximo de intentos necesarios con la mejor estrategia posible (búsqueda binaria) para un rango de 1 a 100? Compruébalo.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- El número secreto es distinto en cada ejecución.
- Si pruebas con intentos como `50`, `25`, `75`... el programa siempre te indica correctamente si es mayor o menor.
- Al acertar, el contador de intentos es correcto (si aciertas a la primera, muestra `1`).
- El programa termina al acertar.
