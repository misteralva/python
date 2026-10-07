# Ejercicio 16 · Validador de contraseñas robusto

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea una función `validar_password(password)` que compruebe si una contraseña cumple una política de seguridad y **devuelva una lista con los problemas encontrados**. Si la lista está vacía, la contraseña es válida.

**Política:**

- Mínimo **12 caracteres**.
- Al menos **una mayúscula**.
- Al menos **una minúscula**.
- Al menos **un número**.
- Al menos **un carácter especial** de este conjunto: `!@#$%&*`.

En el programa principal, pide una contraseña y muestra el resultado.

### Ejemplo de ejecución

```
Introduce una contraseña: hola123
La contraseña no es segura:
- Debe tener al menos 12 caracteres.
- Debe contener al menos una mayúscula.
- Debe contener al menos un carácter especial (!@#$%&*).
```

```
Introduce una contraseña: MiClave2026!Segura
La contraseña cumple la política.
```

---

## 📖 Conceptos nuevos

### 1. Devolver una lista de problemas

```python
def revisar_nombre(nombre):
    problemas = []
    if len(nombre) < 3:
        problemas.append("Muy corto")
    if not nombre.isalpha():
        problemas.append("Solo debe tener letras")
    return problemas

print(revisar_nombre("A1"))
```

- Una función puede construir una lista, ir añadiéndole elementos con `.append()` y devolverla al final.
- Aquí devuelve `['Muy corto', 'Solo debe tener letras']`.
- Fíjate en que se usan varios `if` seguidos (no `elif`), porque queremos **detectar todos los problemas**, no solo el primero.

### 2. `any()`: ¿se cumple al menos una vez?

```python
print(any(c.isdigit() for c in "hola5"))
print(any(c.isdigit() for c in "hola"))
```

- `any(...)` devuelve `True` si **al menos uno** de los elementos cumple la condición.
- `c.isdigit() for c in "hola5"` genera un `True`/`False` por cada carácter, y `any` los resume.
- Resultado: `True` y `False`.
- Equivale a la bandera + `for` del ejercicio 09, pero en una sola línea. Si te resulta confuso, usa primero la versión larga.

### 3. Comprobar caracteres de un conjunto

```python
ESPECIALES = "!@#$%&*"
print("!" in ESPECIALES)
print(any(c in ESPECIALES for c in "hola!"))
```

- Combinando `in` con `any`, comprobamos si algún carácter de la contraseña está en un conjunto de símbolos.
- Resultado: `True` y `True`.

### 4. Métodos de cadenas útiles

| Método | Comprueba si el carácter... |
|---|---|
| `.isupper()` | es mayúscula |
| `.islower()` | es minúscula |
| `.isdigit()` | es un dígito |
| `.isalpha()` | es una letra |

### 5. Mostrar una lista con un bucle

```python
for problema in problemas:
    print(f"- {problema}")
```

- Una lista vacía es "falsa" en una condición, por lo que `if problemas:` significa "si hay algún problema".

---

## 💡 Pistas

- Estructura: crea la lista `problemas = []`, haz una comprobación por regla (cada una con su `if`) y devuelve la lista.
- Cada comprobación es independiente. Usa `any()` o el patrón de la bandera.
- Define el conjunto de caracteres especiales como constante.
- En el programa principal, decide con `if problemas:` qué mensaje mostrar.

---

## 🚀 Retos extra

1. **Lista negra:** rechaza contraseñas comunes (`password`, `123456`, `qwerty`...) guardadas en una lista.
2. **No contener el usuario:** recibe también el nombre de usuario y rechaza la contraseña si lo contiene.
3. **Sin repeticiones:** rechaza contraseñas con 3 o más caracteres iguales seguidos (`aaa`).
4. **Puntuación:** calcula una nota de 0 a 5 según los requisitos cumplidos.
5. **Generador:** crea una función que genere una contraseña aleatoria que cumpla la política (*investiga `random.choice` y el módulo `secrets`*).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Contraseña | ¿Válida? | Problemas esperados |
|---|---|---|
| `hola` | ❌ | longitud, mayúscula, número, especial |
| `Holaholahola` | ❌ | número, especial |
| `Holaholahola1` | ❌ | especial |
| `MiClave2026!Segura` | ✅ | ninguno |
| `ABCDEFGHIJKL1!` | ❌ | minúscula |
