# Ejercicio 36 · Monitor de integridad de una carpeta

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea una versión simplificada de un **FIM** (*File Integrity Monitoring*): una herramienta que detecta si alguien ha **modificado, añadido o borrado** ficheros de una carpeta.

Debe tener dos modos:

1. **Crear línea base** (`baseline`): recorre la carpeta, calcula el SHA-256 de cada fichero y guarda `ruta → hash` en un fichero `baseline.json`.
2. **Comprobar** (`check`): vuelve a calcular los hashes y compara con la línea base, informando de:
   - Ficheros **modificados** (el hash ha cambiado).
   - Ficheros **nuevos** (no estaban en la línea base).
   - Ficheros **eliminados** (estaban y ya no están).

### Carpeta de prueba

```bash
mkdir -p ~/vigilada/sub
echo "uno" > ~/vigilada/a.txt
echo "dos" > ~/vigilada/b.txt
echo "tres" > ~/vigilada/sub/c.txt
```

### Ejemplo de ejecución

```
1. Crear línea base
2. Comprobar integridad
Elige: 1
Línea base creada: 3 ficheros.

(... ahora, en otra terminal: echo "x" >> ~/vigilada/a.txt; rm ~/vigilada/b.txt; touch ~/vigilada/nuevo.txt ...)

Elige: 2
⚠️ MODIFICADO: a.txt
⚠️ ELIMINADO : b.txt
⚠️ NUEVO     : nuevo.txt
Resumen: 1 modificado(s), 1 eliminado(s), 1 nuevo(s)
```

---

## 📖 Conceptos nuevos

### 1. Contexto: ¿para qué sirve?

Un atacante que compromete un servidor suele **modificar ficheros**: binarios del sistema, configuraciones, scripts web. Herramientas como **AIDE**, **Tripwire** u **OSSEC** guardan una "foto" (línea base) de los hashes y avisan cuando algo cambia. Este ejercicio es una mini versión.

### 2. Reutiliza lo que ya sabes

Este ejercicio **combina** ejercicios anteriores:

- `hash_fichero()` del ejercicio 35.
- Recorrer una carpeta con `os.walk` (ejercicio 24) o con `Path.rglob`.
- Guardar y cargar JSON (ejercicio 26).
- Un diccionario `{ruta: hash}`.

### 3. Rutas relativas a la carpeta vigilada

```python
from pathlib import Path

base = Path("/home/david/vigilada")
fichero = Path("/home/david/vigilada/sub/c.txt")
print(fichero.relative_to(base))
```

- `.relative_to(base)` devuelve la ruta **relativa** (`sub/c.txt`).
- Guardar rutas relativas permite mover la carpeta vigilada sin romper la línea base.
- Para guardarla en JSON necesitas convertirla en texto: `str(...)`.

### 4. Recorrer todo el árbol con `rglob`

```python
for ruta in base.rglob("*"):
    if ruta.is_file():
        print(ruta)
```

- `.rglob("*")` recorre **recursivamente** todo el contenido; `.is_file()` filtra solo los ficheros.

### 5. Comparar dos diccionarios

```python
antes = {"a": 1, "b": 2, "c": 3}
ahora = {"a": 1, "b": 99, "d": 4}

print(set(ahora) - set(antes))
print(set(antes) - set(ahora))
print(set(antes) & set(ahora))
```

- `set(diccionario)` crea un **conjunto** con sus claves.
- Los conjuntos permiten operaciones de teoría de conjuntos:
  - `A - B`: elementos de A que **no** están en B (aquí, los nuevos: `{'d'}`).
  - `B - A`: los que ya no están (los eliminados: `{'c'}`).
  - `A & B`: los que están en **ambos** (candidatos a haber cambiado).
- Para los comunes, comparas los valores: `antes[k] != ahora[k]` significa modificado.

### 6. Proteger la propia línea base

Si un atacante puede editar `baseline.json`, puede **ocultar sus cambios**. En un sistema real la línea base se guarda en un lugar protegido (otro equipo, solo lectura, firmada). Para este ejercicio basta saber que **la herramienta es tan fiable como la protección de su línea base**.

### 7. Excluir el propio `baseline.json`

Guarda la línea base **fuera** de la carpeta vigilada (o exclúyela) para que no aparezca como "nuevo" o "modificado".

---

## 💡 Pistas

- Divide el programa en funciones: `calcular_hashes(carpeta)`, `guardar_baseline(...)`, `cargar_baseline()` y `comparar(antes, ahora)`.
- `calcular_hashes` devuelve el diccionario `{ruta_relativa: hash}`.
- `comparar` devuelve tres listas: modificados, nuevos y eliminados.
- Si no existe `baseline.json` al comprobar, avisa de que primero hay que crearla.

---

## 🚀 Retos extra

1. **Informe con fecha:** guarda los cambios detectados en un fichero de log con fecha y hora (ejercicio 20).
2. **Actualizar la línea base:** tras revisar los cambios, ofrece aceptarlos y regenerar la línea base.
3. **Carpeta por argumento:** `python3 solucion.py baseline /ruta` y `python3 solucion.py check /ruta` (ejercicio 34).
4. **Más metadatos:** guarda también permisos y fecha de modificación y detecta cambios solo de permisos (ejercicio 24).
5. **Exclusiones:** ignora patrones como `*.log` o `__pycache__`.
6. **Ejecución periódica:** investiga `cron` para comprobar la carpeta cada hora y guardar un resumen.
7. **Firmar la línea base:** calcula el hash del propio `baseline.json` y guárdalo aparte para detectar manipulaciones.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

Tras crear la línea base con la carpeta de prueba:

| Cambio | Resultado esperado |
|---|---|
| Sin cambios | "Sin cambios", 0 modificados, nuevos y eliminados |
| `echo x >> ~/vigilada/a.txt` | `a.txt` modificado |
| `rm ~/vigilada/b.txt` | `b.txt` eliminado |
| `touch ~/vigilada/nuevo.txt` | `nuevo.txt` nuevo |
| Modificar un fichero y **restaurar** su contenido original | sin cambios (el hash vuelve a coincidir) |
