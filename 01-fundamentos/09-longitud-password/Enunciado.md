# Ejercicio 09 · Comprobar la longitud de una contraseña

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐⭐ Básico-medio

---

## 🎯 Objetivo

Crea un programa que pida una **contraseña** y evalúe su fortaleza según su longitud:

| Longitud | Resultado |
|---|---|
| Menos de 8 caracteres | **Débil** |
| De 8 a 11 caracteres | **Media** |
| 12 o más caracteres | **Fuerte** |

Además, el programa debe avisar si la contraseña **no contiene ningún número**.

### Ejemplo de ejecución

```
Introduce una contraseña: hola123
Longitud: 7 caracteres
Fortaleza: Débil
```

```
Introduce una contraseña: MiClaveSegura
Longitud: 13 caracteres
Fortaleza: Fuerte
Aviso: la contraseña no contiene ningún número.
```

---

## 📖 Conceptos nuevos

### 1. `len()`: contar elementos

```python
palabra = "servidor"
print(len(palabra))
```

- `len()` devuelve la **longitud** de un texto, es decir, el número de caracteres.
- Los espacios y símbolos también cuentan. El resultado del ejemplo es `8`.

### 2. Las cadenas son secuencias de caracteres

```python
palabra = "red"
print(palabra[0])
print(palabra[-1])
```

- Puedes acceder a cada carácter con corchetes y su **posición** (índice). **Se empieza a contar en 0.**
- `palabra[0]` es `"r"`. Con un índice negativo se cuenta desde el final: `palabra[-1]` es `"d"`.

### 3. Métodos de cadenas: `.isdigit()`, `.isupper()`...

```python
print("7".isdigit())
print("a".isdigit())
print("A".isupper())
```

- Un **método** es una función que se escribe después del dato, con un punto.
- `.isdigit()` devuelve `True` si el texto son solo dígitos. `.isupper()` y `.islower()` comprueban mayúsculas y minúsculas.
- Resultado del ejemplo: `True`, `False`, `True`.

### 4. Recorrer una cadena con `for`

```python
for letra in "abc":
    print(letra)
```

- Un `for` puede recorrer un texto **carácter a carácter**.
- Así puedes examinar cada carácter de la contraseña con `.isdigit()` para saber si hay algún número.

### 5. Variables "bandera" (flags)

```python
hay_mayuscula = False

for c in "Hola":
    if c.isupper():
        hay_mayuscula = True

print(hay_mayuscula)
```

- Una variable `True/False` que empieza en `False` y se cambia a `True` cuando encuentras lo que buscabas.
- Después del bucle, su valor te dice si lo encontraste o no.

### 6. ¿Y el operador `in`?

```python
print("a" in "casa")
print("z" in "casa")
```

- `in` comprueba si un texto está contenido en otro. Da `True` y `False`.

---

## 💡 Pistas

- Guarda `len(password)` en una variable para no calcularla varias veces.
- Para la fortaleza, usa `if/elif/else` con los tres rangos.
- Para saber si hay un número, usa una variable bandera y un `for` que recorra la contraseña.

---

## 🚀 Retos extra

1. **Más requisitos:** avisa también si no hay mayúsculas ni minúsculas.
2. **Símbolos:** comprueba si contiene algún carácter especial como `!@#$%`.
3. **Puntuación:** calcula una nota de 0 a 5 sumando un punto por cada requisito cumplido.
4. **Contraseña oculta:** investiga el módulo `getpass` para que la contraseña no se vea mientras se escribe.
5. **Contraseñas prohibidas:** rechaza contraseñas muy comunes como `123456` o `password`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Contraseña | Fortaleza | ¿Aviso de número? |
|---|---|---|
| abc | Débil | sí |
| abcdefgh | Media | sí |
| abcdefg1 | Media | no |
| abcdefghijkl | Fuerte | sí |
| MiClave2026Segura | Fuerte | no |
