# Ejercicio 23 · Organizar archivos por extensión

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que ordene los ficheros de una carpeta, moviendo cada uno a una **subcarpeta según su extensión**:

```
descargas/                        descargas/
├── foto1.jpg                     ├── jpg/
├── informe.pdf          ──►      │   ├── foto1.jpg
├── notas.txt                     │   └── foto2.jpg
├── foto2.jpg                     ├── pdf/
└── datos.csv                     │   └── informe.pdf
                                  ├── txt/
                                  │   └── notas.txt
                                  └── csv/
                                      └── datos.csv
```

### Carpeta de prueba

**Usa siempre una carpeta de prueba**: este programa mueve ficheros y un error en tu carpeta real sería molesto.

```bash
mkdir -p ~/descargas-prueba
cd ~/descargas-prueba
touch foto1.jpg foto2.jpg informe.pdf notas.txt datos.csv sinextension
```

- `touch` crea ficheros vacíos.

### Ejemplo de ejecución

```
Carpeta a organizar: /home/david/descargas-prueba
Movido: foto1.jpg -> jpg/
Movido: foto2.jpg -> jpg/
Movido: informe.pdf -> pdf/
...
Ficheros sin extensión (no movidos): 1
```

---

## 📖 Conceptos nuevos

### 1. Listar el contenido de una carpeta

```python
from pathlib import Path

carpeta = Path("/home/david/prueba")

for elemento in carpeta.iterdir():
    print(elemento)
```

- `.iterdir()` recorre lo que hay dentro de la carpeta (ficheros **y** subcarpetas).
- Cada `elemento` es otro objeto `Path`.

### 2. Distinguir ficheros de carpetas

```python
for elemento in carpeta.iterdir():
    if elemento.is_file():
        print("Fichero:", elemento.name)
```

- `.is_file()` es `True` solo para ficheros. Así ignoras las subcarpetas (incluidas las que vayas creando tú).

### 3. La extensión: `.suffix`

```python
ruta = Path("informe.final.PDF")
print(ruta.suffix)
print(ruta.suffix.lower())
print(ruta.stem)
```

- `.suffix` devuelve la extensión **con el punto**: `.PDF`.
- `.lower()` la pasa a minúsculas, para tratar `.JPG` y `.jpg` igual.
- `.stem` es el nombre sin extensión: `informe.final`.
- Un fichero sin extensión devuelve `""` (texto vacío).
- Para quitar el punto: `ruta.suffix[1:]` (de la posición 1 en adelante).

### 4. Mover ficheros con `shutil.move`

```python
import shutil

shutil.move("origen/a.txt", "destino/a.txt")
```

- Mueve el fichero (lo borra del origen). Si el destino ya existe, puede **sobrescribirlo** sin avisar.
- Con `Path` también puedes usar `.rename(destino)`, pero `shutil.move` es más flexible.
- Comprueba antes si el destino existe para no perder datos.

### 5. Crear la subcarpeta solo si hace falta

```python
destino = carpeta / "jpg"
destino.mkdir(exist_ok=True)
```

- Ya conoces `exist_ok=True` del ejercicio 22: no falla si la carpeta ya existe.

### 6. Pensar en seguridad de los datos

Los scripts que **mueven o borran** son peligrosos. Buenas prácticas:
- Probar siempre con datos de prueba.
- Ofrecer un modo "simulación" que solo muestre lo que haría (*dry run*).
- No sobrescribir sin avisar.

---

## 💡 Pistas

- Solo procesa elementos que sean ficheros (`.is_file()`).
- Si el fichero no tiene extensión, ignóralo y cuéntalo aparte.
- Construye la ruta destino con `/` y crea la carpeta antes de mover.
- Si tienes dudas, empieza con un `print` de lo que **harías** y solo cuando salga bien, mueve de verdad.

---

## 🚀 Retos extra

1. **Modo simulación:** pregunta si quiere solo simular (sin mover nada) o ejecutar de verdad.
2. **Categorías:** agrupa por tipo en vez de por extensión, con un diccionario (`{".jpg": "imagenes", ".png": "imagenes", ".pdf": "documentos", ...}`).
3. **No sobrescribir:** si ya existe un fichero con el mismo nombre en destino, renómbralo (`foto1_1.jpg`).
4. **Resumen:** al final, muestra cuántos ficheros se han movido por categoría.
5. **Deshacer:** guarda un registro de los movimientos para poder revertirlos.
6. **Recursivo:** investiga `.rglob("*")` para procesar también las subcarpetas.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

Con la carpeta de prueba, después de ejecutar:

```bash
ls -R ~/descargas-prueba
```

- `jpg/` contiene `foto1.jpg` y `foto2.jpg`.
- `pdf/` contiene `informe.pdf`; `txt/` contiene `notas.txt`; `csv/` contiene `datos.csv`.
- `sinextension` sigue en la carpeta original.
- Ejecutarlo una segunda vez no rompe nada (los ficheros ya movidos no se vuelven a tocar).
