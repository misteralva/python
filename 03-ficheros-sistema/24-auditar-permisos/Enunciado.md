# Ejercicio 24 · Auditar permisos de ficheros

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea un programa que **recorra una carpeta** (y sus subcarpetas) y encuentre los ficheros con **permiso de escritura para "otros"**, es decir, que **cualquier usuario del sistema puede modificar**. Es un fallo de seguridad habitual.

Para cada fichero encontrado, muestra su ruta y sus permisos en formato `rw-rw-rw-`.

### Carpeta de prueba

```bash
mkdir -p ~/auditoria/sub
cd ~/auditoria
echo "a" > normal.txt          && chmod 644 normal.txt
echo "b" > abierto.txt         && chmod 666 abierto.txt
echo "c" > sub/script.sh       && chmod 777 sub/script.sh
echo "d" > sub/privado.txt     && chmod 600 sub/privado.txt
```

- `chmod 644` pone permisos `rw-r--r--`; `666` es `rw-rw-rw-` y `777` es `rwxrwxrwx`.

### Ejemplo de ejecución

```
Carpeta a auditar: /home/david/auditoria
⚠️  /home/david/auditoria/abierto.txt  (rw-rw-rw-)
⚠️  /home/david/auditoria/sub/script.sh  (rwxrwxrwx)
Ficheros revisados: 4 | Con escritura para todos: 2
```

---

## 📖 Conceptos nuevos

### 1. Recordatorio: permisos en Linux

Cada fichero tiene permisos para tres grupos: **propietario**, **grupo** y **otros**. Cada grupo puede tener lectura (`r`), escritura (`w`) y ejecución (`x`).

```
-rw-r--r--
 │└┬┘└┬┘└┬┘
 │ │  │  └─ otros: solo lectura
 │ │  └──── grupo: solo lectura
 │ └─────── propietario: lectura y escritura
 └───────── tipo (- fichero, d carpeta)
```

El permiso de **escritura para otros** (`w` en la última terna) permite a cualquier cuenta modificar el fichero.

### 2. Recorrer carpetas con `os.walk()`

```python
import os

for ruta_carpeta, subcarpetas, ficheros in os.walk("/home/david/prueba"):
    for nombre in ficheros:
        print(os.path.join(ruta_carpeta, nombre))
```

- `os.walk()` recorre una carpeta **y todas sus subcarpetas**.
- En cada vuelta entrega tres cosas: la ruta de la carpeta actual, la lista de subcarpetas y la lista de ficheros que contiene.
- `os.path.join()` une la carpeta y el nombre para obtener la ruta completa.

### 3. Leer los permisos con `os.stat()`

```python
import os
import stat

info = os.stat("fichero.txt")
print(info.st_mode)
print(stat.filemode(info.st_mode))
```

- `os.stat()` devuelve información del fichero (tamaño, propietario, permisos...).
- `st_mode` es un número que codifica el tipo de fichero y sus permisos.
- `stat.filemode()` lo traduce al formato legible: `-rw-r--r--`.

### 4. Comprobar un permiso con operaciones a nivel de bits (`&`)

```python
import stat

if info.st_mode & stat.S_IWOTH:
    print("Escribible por otros")
```

- Los permisos se guardan como **bits** (0 y 1). `stat.S_IWOTH` es una constante con solo el bit "escritura para otros" activado.
- `&` (AND de bits) se queda con esos bits: si el resultado no es cero, el permiso está activo.
- No hace falta entender el binario a fondo: úsalo como patrón. Otras constantes: `stat.S_IROTH` (leer, otros), `stat.S_IWGRP` (escribir, grupo), `stat.S_IWUSR` (escribir, propietario).

### 5. Ver permisos en octal

```python
print(oct(info.st_mode & 0o777))
```

- `& 0o777` se queda solo con los bits de permisos, y `oct()` lo muestra en octal: `0o644`, `0o666`...
- Es la misma notación que usas con `chmod 644`.

### 6. Enlaces simbólicos

Los *symlinks* pueden aparentar permisos `777` sin que el problema sea real, y `os.stat` sigue el enlace. Para este ejercicio puedes ignorarlos con `os.path.islink()`, o investigar `os.lstat()`.

---

## 💡 Pistas

- Estructura: `os.walk` para recorrer, `os.stat` para cada fichero, `&` con `S_IWOTH` para decidir.
- Lleva dos contadores: ficheros revisados y ficheros con el permiso.
- Usa `stat.filemode()` para el formato `rw-rw-rw-`.
- Si algún fichero da `PermissionError`, captúralo para que el programa no se detenga.

---

## 🚀 Retos extra

1. **Grupo:** detecta también los ficheros escribibles por el grupo.
2. **Ejecutables peligrosos:** detecta ficheros con el bit SUID (`stat.S_ISUID`).
3. **Propietario:** muestra el propietario de cada fichero (*investiga `pwd.getpwuid` y `st_uid`*).
4. **Carpetas:** audita también las carpetas escribibles por todos que no tengan el bit *sticky* (como ocurre con `/tmp`).
5. **Corregir:** ofrece quitar el permiso con `os.chmod(...)`, siempre pidiendo confirmación antes de cambiar nada.
6. **Informe:** guarda el resultado en un fichero con fecha.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

⚠️ Audita solo carpetas **tuyas** o de tu laboratorio. Auditar `/` o carpetas del sistema puede dar muchos errores de permisos y mucha salida.

---

## ✅ Comprueba tu solución

Con la carpeta de prueba debes detectar exactamente:

| Fichero | Permisos | ¿Detectado? |
|---|---|---|
| `normal.txt` | `rw-r--r--` | ❌ No |
| `abierto.txt` | `rw-rw-rw-` | ✅ Sí |
| `sub/script.sh` | `rwxrwxrwx` | ✅ Sí |
| `sub/privado.txt` | `rw-------` | ❌ No |

Resumen esperado: 4 ficheros revisados, 2 con escritura para todos.
