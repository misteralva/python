# Ejercicio 10 · Menú interactivo

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐⭐ Medio

---

## 🎯 Objetivo

Crea un programa con un **menú que se repite** hasta que el usuario elija salir. Debe ofrecer estas opciones:

```
===== MENÚ =====
1. Saludar
2. Par o impar
3. Mostrar la hora actual
0. Salir
```

- **1:** pide el nombre y lo saluda.
- **2:** pide un número y dice si es par o impar (como en el ejercicio 03).
- **3:** muestra la hora actual.
- **0:** termina el programa con un mensaje de despedida.
- Cualquier otra opción: muestra `Opción no válida` y vuelve a mostrar el menú.

### Ejemplo de ejecución

```
===== MENÚ =====
1. Saludar
2. Par o impar
3. Mostrar la hora actual
0. Salir
Elige una opción: 1
¿Cómo te llamas? Ana
¡Hola, Ana!

===== MENÚ =====
...
Elige una opción: 9
Opción no válida.

===== MENÚ =====
...
Elige una opción: 0
¡Hasta pronto!
```

---

## 📖 Conceptos nuevos

### 1. Estructura de un menú

```python
while True:
    print("1. Opción A")
    print("0. Salir")
    opcion = input("Elige: ")

    if opcion == "1":
        print("Has elegido A")
    elif opcion == "0":
        break
    else:
        print("Opción no válida")
```

- `while True` repite el menú indefinidamente.
- Cada opción se comprueba con `if/elif`.
- La opción de salir ejecuta `break`, que corta el bucle.
- El `else` final captura cualquier opción desconocida.

### 2. Comparar texto en lugar de números

```python
opcion = input("Elige: ")
print(opcion == "1")
```

- `input()` devuelve **texto**, así que puedes comparar directamente con `"1"` (entre comillas) sin convertir a entero.
- Si lo convirtieras con `int()`, el programa fallaría al escribir una letra. Comparar como texto es más seguro para menús.

### 3. Fecha y hora con `datetime`

```python
from datetime import datetime

ahora = datetime.now()
print(ahora.strftime("%d/%m/%Y"))
```

- `from datetime import datetime` importa solo la parte que necesitas del módulo.
- `datetime.now()` devuelve el momento actual.
- `.strftime()` da formato al resultado. Estos son los códigos más usados: `%H` hora, `%M` minutos, `%S` segundos, `%d` día, `%m` mes y `%Y` año.
- El ejemplo muestra una fecha como `07/10/2026`.

### 4. Reutilizar código de ejercicios anteriores

Los menús suelen combinar funcionalidades que ya has hecho. Aquí puedes reutilizar lo que hiciste en los ejercicios 01 y 03 dentro de cada opción.

---

## 💡 Pistas

- Muestra el menú **dentro** del bucle para que reaparezca tras cada opción.
- Cada rama `if/elif` ejecuta su bloque de código, que a su vez puede tener otro `if/else` dentro (por ejemplo, en la opción 2).
- Deja una línea en blanco (`print()`) entre iteraciones para que se lea mejor.

---

## 🚀 Retos extra

1. **Más opciones:** añade una calculadora (ejercicio 02) y un clasificador de puertos (ejercicio 05).
2. **Contador de usos:** al salir, muestra cuántas opciones ha usado el usuario.
3. **Pausa:** tras cada opción, espera a que pulse Enter antes de volver al menú (*investiga `input()` sin guardar el resultado*).
4. **Limpiar pantalla:** investiga cómo limpiar la terminal (`import os` y `os.system("clear")`).
5. **Entrada inválida:** controla que el número de la opción 2 sea realmente un número.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- El menú vuelve a aparecer después de cada opción, excepto al salir.
- Una opción desconocida (`9`, `hola`, vacío) muestra el aviso y no rompe el programa.
- `0` termina el programa con el mensaje de despedida.
- La hora mostrada coincide con la de tu sistema.
