# Ejercicio 20 · Diario / log con marca de tiempo

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐ Medio

---

## 🎯 Objetivo

Crea un programa con menú que gestione un **diario** guardado en un fichero `diario.txt`:

```
1. Añadir una entrada
2. Leer el diario
0. Salir
```

- **Añadir:** pide un texto y lo guarda al **final del fichero** con la fecha y hora.
- **Leer:** muestra todas las entradas. Si el fichero aún no existe, avisa de que el diario está vacío.
- Los datos deben **persistir**: si cierras el programa y lo vuelves a abrir, las entradas siguen ahí.

### Formato de cada línea

```
[2026-10-08 14:32:10] Hoy he empezado el bloque de ficheros
```

---

## 📖 Conceptos nuevos

### 1. Contexto: ¿por qué un log?

Casi todos los programas y servidores registran lo que ocurre en **ficheros de log** con una marca de tiempo por línea. Aquí construyes la versión más simple: es el mismo mecanismo que usarás para guardar alertas de tus scripts de seguridad.

### 2. Modos de apertura

| Modo | Qué hace |
|---|---|
| `"r"` | Leer. Falla si no existe. |
| `"w"` | Escribir. **Borra** todo el contenido anterior (o crea el fichero). |
| `"a"` | Añadir (*append*) al final. Crea el fichero si no existe, y **no borra** lo anterior. |

⚠️ Abrir con `"w"` un fichero con datos los **destruye**. Para un diario necesitas `"a"`.

### 3. Escribir con `.write()`

```python
with open("notas.txt", "a", encoding="utf-8") as f:
    f.write("Primera nota\n")
    f.write("Segunda nota\n")
```

- `.write()` escribe el texto **tal cual**: no añade salto de línea. Tienes que poner tú el `\n` al final.
- `\n` es el carácter especial de "nueva línea".

### 4. Fecha y hora en un texto

```python
from datetime import datetime

ahora = datetime.now()
marca = ahora.strftime("%Y-%m-%d %H:%M:%S")
print(marca)
```

- `%Y` año, `%m` mes, `%d` día, `%H` hora, `%M` minutos y `%S` segundos.
- Con este formato, las líneas quedan **ordenables alfabéticamente** por fecha.

### 5. Comprobar si un fichero existe

```python
from pathlib import Path

if Path("diario.txt").exists():
    print("Existe")
```

- `Path` (del módulo `pathlib`) representa una ruta. `.exists()` devuelve `True` o `False`.
- Alternativa: capturar `FileNotFoundError` con `try/except`, como en el ejercicio 19. Las dos formas son válidas.

---

## 💡 Pistas

- Para añadir, usa el modo `"a"`. No olvides el `\n` al final de cada entrada.
- Para leer, abre con `"r"` y recorre las líneas con `for`.
- Usa f-strings para montar la línea: marca de tiempo entre corchetes y el texto.
- Si usas `print(linea)` al leer, saldrán líneas en blanco de más. ¿Cómo lo evitas? (mira `.strip()` o el parámetro `end=""` de `print`).

---

## 🚀 Retos extra

1. **Contador:** al leer, muestra cuántas entradas hay.
2. **Buscar:** añade una opción para buscar entradas que contengan una palabra.
3. **Últimas N:** muestra solo las últimas 5 entradas.
4. **Niveles de log:** guarda cada línea con un nivel (`INFO`, `WARNING`, `ERROR`) y permite filtrar por nivel.
5. **Como función reutilizable:** crea `registrar(mensaje)` que añada una línea de log, y úsala desde otros ejercicios (por ejemplo, para registrar cuándo se detecta una IP sospechosa).
6. **Más adelante:** investiga el módulo `logging` de Python, que hace esto mismo de forma profesional.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- Leer sin haber añadido nada → aviso de diario vacío, sin error.
- Añadir dos entradas y leerlas → salen en orden, cada una con su fecha y hora.
- Cerrar el programa, volver a ejecutarlo y leer → las entradas siguen ahí.
- Abre `diario.txt` con `cat diario.txt` y verifica que cada entrada ocupa una línea.
