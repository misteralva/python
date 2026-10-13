# Fase 43 · Diseño y esqueleto del proyecto

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Prepara la **base del proyecto `toolkit`**, sin lógica de seguridad todavía:

1. Crea la **estructura de carpetas** del paquete.
2. Escribe el `pyproject.toml` para poder **instalarlo con `pip`**.
3. Crea una **CLI con 4 subcomandos** (`logs`, `audit`, `netmon`, `hids`) que de momento solo muestran `"Módulo X: aún no implementado"`.
4. Redacta un **README de diseño** (qué hace, arquitectura, cómo se usará).

Al terminar debe funcionar esto:

```bash
$ toolkit --help
usage: toolkit [-h] {logs,audit,netmon,hids} ...

Kit de herramientas de seguridad defensiva

positional arguments:
  {logs,audit,netmon,hids}
    logs                Analiza logs y detecta ataques
    audit               Audita la configuración de seguridad de este equipo
    netmon              Monitoriza hosts y puertos de tu red
    hids                Detecta cambios sospechosos en este equipo

$ toolkit logs
Módulo logs: aún no implementado
```

---

## 📖 Conceptos nuevos

### 1. Módulos y paquetes

- Un **módulo** es un fichero `.py`: `alerts.py`.
- Un **paquete** es una carpeta con un fichero `__init__.py` (aunque esté vacío) que agrupa módulos.
- Con paquetes puedes importar así:

```python
from toolkit.alerts import Alert
from toolkit.modules import logs
```

- Dentro del mismo paquete también existen las **importaciones relativas**: `from .alerts import Alert` (el punto significa "de este mismo paquete").

### 2. `src` layout (estructura recomendada)

```
toolkit/                   ← carpeta del proyecto
├── pyproject.toml
└── src/
    └── toolkit/           ← el paquete importable
        ├── __init__.py
        └── cli.py
```

- Poner el paquete dentro de `src/` evita errores típicos (como importar sin querer el código de la carpeta actual en lugar del instalado) y es lo recomendado hoy.

### 3. `pyproject.toml`: la ficha de tu proyecto

```toml
[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"

[project]
name = "toolkit"
version = "0.1.0"
description = "Kit de herramientas de seguridad defensiva"
requires-python = ">=3.11"
dependencies = []

[project.scripts]
toolkit = "toolkit.cli:main"
```

- `[build-system]`: con qué se construye el paquete (aquí, `setuptools`).
- `[project]`: nombre, versión, descripción, versión mínima de Python y dependencias externas (`dependencies`).
- `[project.scripts]`: define el **comando** `toolkit`, que ejecutará la función `main()` del módulo `toolkit/cli.py`.
- Comprueba que tienes Python 3.11 o superior con `python3 --version` (necesitarás `tomllib` en la fase 44).

### 4. Instalación en modo editable

```bash
python3 -m venv venv
source venv/bin/activate
pip install -e .
```

- `pip install -e .` instala **tu proyecto** en el entorno virtual de forma **editable**: el comando `toolkit` pasa a existir y cualquier cambio que hagas en el código se aplica **sin reinstalar**.
- El `.` significa "el proyecto de esta carpeta".

### 5. Subcomandos con `argparse`

Ya los viste como reto del ejercicio 34. Un ejemplo genérico:

```python
import argparse

def cmd_saludo(args):
    print(f"Hola, {args.nombre}")
    return 0

def main():
    parser = argparse.ArgumentParser(prog="demo", description="Ejemplo")
    sub = parser.add_subparsers(dest="comando", required=True)

    p_saludo = sub.add_parser("saludo", help="Saluda a alguien")
    p_saludo.add_argument("nombre")
    p_saludo.set_defaults(func=cmd_saludo)

    args = parser.parse_args()
    return args.func(args)
```

- `add_subparsers(dest="comando", required=True)` crea los subcomandos (como `git commit` o `docker run`).
- Cada subcomando tiene su propio `add_parser` con sus propios argumentos.
- `set_defaults(func=...)` asocia **la función** a ejecutar con cada subcomando; luego `args.func(args)` la llama. Evita una cadena de `if/elif`.
- **`main()` devuelve un entero**: es el código de salida. El script generado por `pip` ejecuta `sys.exit(main())`.

### 6. `__main__.py`: ejecutar el paquete con `python -m`

Si añades `src/toolkit/__main__.py` con este contenido, `python3 -m toolkit` funciona igual que el comando `toolkit`:

```python
import sys
from toolkit.cli import main

sys.exit(main())
```

### 7. `.gitignore` y licencia

Añade un `.gitignore` con, al menos: `venv/`, `__pycache__/`, `*.egg-info/`, `dist/`, `build/`, `*.key`, `.pytest_cache/` y los ficheros de estado y logs que vaya generando la herramienta. Añade también tu **fichero `LICENSE`** (el mismo que usas en tus otros repositorios).

---

## 📋 Requisitos

- [ ] Estructura de carpetas como la del `README.md` del bloque (con `__init__.py` en el paquete y en `modules/`).
- [ ] Un fichero `.py` vacío o con un comentario por cada módulo (`config.py`, `alerts.py`, `report.py`, `modules/logs.py`...).
- [ ] `pyproject.toml` correcto; `pip install -e .` funciona sin errores.
- [ ] `toolkit --help` y `python3 -m toolkit --help` muestran los 4 subcomandos.
- [ ] Cada subcomando está en **su propio módulo** (`modules/logs.py`...) con una función `registrar(subparsers)` que añade su `add_parser`, y el `cli.py` solo las llama. Así cada módulo define sus propios argumentos.
- [ ] `toolkit` sin subcomando muestra un error claro (no un traceback).
- [ ] README de diseño en `toolkit/README.md`, con: descripción, diagrama o lista de la arquitectura, ejemplos de uso previstos y aviso legal.
- [ ] `.gitignore` y `LICENSE`.

---

## 💡 Pistas

- Crea la estructura con `mkdir -p` y `touch` desde la terminal. Después ábrela con `code .`.
- Si `toolkit` no se reconoce tras instalar, comprueba que el entorno virtual está activado (`(venv)` en la terminal) y que `[project.scripts]` apunta a `toolkit.cli:main`.
- Si ves `ModuleNotFoundError: No module named 'toolkit'`, revisa que el paquete está en `src/toolkit/` y que reinstalaste con `pip install -e .`.
- Haz primero una versión con un solo subcomando y, cuando funcione, copia el patrón a los demás.
- Piensa ya cómo se llamarán los argumentos más importantes de cada módulo, y apúntalo en el README de diseño. Cambiarlo después es más costoso.

---

## 🚀 Retos extra

1. **`--version`:** añade `toolkit --version` que muestre `0.1.0`. Para no repetir el número, investiga `importlib.metadata.version("toolkit")`.
2. **Autocompletado:** investiga `argcomplete` para completar subcomandos con Tab.
3. **Diagrama:** añade al README un diagrama con **Mermaid** (GitHub lo renderiza) de la arquitectura.
4. **Entorno reproducible:** crea un `Makefile` o un script con los comandos habituales (instalar, ejecutar, tests).
5. **Rama de trabajo:** haz el proyecto en una rama (`git switch -c fase-43`) y fusiónala al terminar.

---

## ▶️ Cómo comprobarlo

```bash
cd toolkit
python3 -m venv venv
source venv/bin/activate
pip install -e .
toolkit --help
toolkit logs
python3 -m toolkit audit
```

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| `pip install -e .` | instala sin errores |
| `toolkit --help` | lista los 4 subcomandos con su descripción |
| `toolkit logs` | `Módulo logs: aún no implementado` y código de salida `0` (`echo $?`) |
| `toolkit` (sin nada) | error de uso, sin traceback |
| `toolkit inventado` | error de comando no válido |
| `python3 -m toolkit netmon` | funciona igual que el comando |
| `git status` | sin `venv/` ni `__pycache__/` pendientes |

Al terminar: `git tag fase-43`.
