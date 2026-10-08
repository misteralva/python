# Ejercicio 22 · Backup de una carpeta con fecha

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que haga una **copia de seguridad** de una carpeta:

1. Pide la **carpeta origen**.
2. Comprueba que existe y que es una carpeta.
3. Crea una copia en `~/backups/` con el nombre `nombre_AAAA-MM-DD_HHMMSS`.
4. Muestra dónde se ha guardado el backup.

### Carpeta de prueba

```bash
mkdir -p ~/prueba/subcarpeta
echo "hola" > ~/prueba/a.txt
echo "adios" > ~/prueba/subcarpeta/b.txt
```

### Ejemplo de ejecución

```
Carpeta a copiar: /home/david/prueba
Backup creado en: /home/david/backups/prueba_2026-10-08_143210
```

---

## 📖 Conceptos nuevos

### 1. `pathlib.Path`: rutas como objetos

```python
from pathlib import Path

carpeta = Path("/home/david/prueba")
print(carpeta.name)
print(carpeta.exists())
print(carpeta.is_dir())
```

- `Path(...)` representa una ruta. Tiene muchos métodos útiles:
  - `.name`: el último componente (`prueba`).
  - `.exists()`: ¿existe?
  - `.is_dir()` / `.is_file()`: ¿es una carpeta / un fichero?
- Es más moderno y claro que manipular rutas con texto.

### 2. Construir rutas con `/`

```python
base = Path.home() / "backups" / "copia1"
print(base)
```

- `Path.home()` devuelve tu carpeta personal (`/home/david`).
- El operador `/` **une** partes de una ruta de forma correcta, sin preocuparte por las barras.
- `Path("~/x").expanduser()` convierte el `~` en la ruta real.

### 3. Crear carpetas con `.mkdir()`

```python
Path("/tmp/a/b/c").mkdir(parents=True, exist_ok=True)
```

- `parents=True` crea también las carpetas intermedias que falten.
- `exist_ok=True` evita el error si la carpeta ya existe.
- Es el equivalente de `mkdir -p` en bash.

### 4. Copiar una carpeta entera con `shutil`

```python
import shutil

shutil.copytree("origen", "destino")
```

- `shutil` (de *shell utilities*) ofrece operaciones de alto nivel sobre ficheros.
- `copytree(origen, destino)` copia la carpeta **con todo su contenido**, incluidas subcarpetas.
- ⚠️ La carpeta destino **no debe existir** antes (si existe, da error). Por eso le ponemos fecha y hora al nombre: así es siempre nueva.
- Para copiar un solo fichero se usa `shutil.copy(origen, destino)`.

### 5. Fecha en el nombre

```python
from datetime import datetime

sello = datetime.now().strftime("%Y-%m-%d_%H%M%S")
print(sello)
```

- Evita espacios y `:` en nombres de fichero: dan problemas en algunos sistemas.

### 6. Validar antes de actuar

Antes de copiar nada, asegúrate de que el origen existe y es una carpeta. Un buen script **falla pronto y con un mensaje claro**, nunca a medias.

---

## 💡 Pistas

- Orden: pedir ruta, validar, construir destino, crear `~/backups` si no existe, `copytree`, mostrar mensaje.
- Cuidado con el destino: `copytree` crea la carpeta final, así que solo necesitas que exista `~/backups`.
- Si el usuario escribe `~/prueba`, el `~` no se expande solo: usa `.expanduser()`.

---

## 🚀 Retos extra

1. **Backup comprimido:** genera un `.zip` o `.tar.gz` en vez de una copia (*investiga `shutil.make_archive`*).
2. **Rotación:** conserva solo los 5 backups más recientes y borra los anteriores (*investiga `sorted()` y `shutil.rmtree`*).
3. **Informe:** muestra cuántos ficheros se han copiado y el tamaño total.
4. **Log:** registra cada backup en un fichero `backups.log` (reutiliza el ejercicio 20).
5. **Exclusiones:** ignora carpetas como `.git` o `__pycache__` (*investiga `ignore=shutil.ignore_patterns(...)`*).
6. **Argumento por terminal:** `python3 solucion.py /ruta/origen`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- Carpeta existente → se crea `~/backups/prueba_<fecha>` con `a.txt` y `subcarpeta/b.txt` dentro.
- Ejecutarlo dos veces seguidas → dos backups distintos, sin error.
- Ruta que no existe → mensaje de error claro.
- Ruta que es un fichero y no una carpeta → mensaje de error claro.
- Verifica con `ls -R ~/backups`.
