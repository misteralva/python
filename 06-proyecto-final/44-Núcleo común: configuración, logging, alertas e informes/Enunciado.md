# Fase 44 · Núcleo común: configuración, logging, alertas e informes

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea el **núcleo compartido** que usarán los cuatro módulos, para no repetir código:

1. **`config.py`:** carga la configuración desde un fichero TOML.
2. **Logging:** configura el registro de la herramienta (consola y fichero).
3. **`alerts.py`:** define qué es una **alerta** y cómo se emite y guarda.
4. **`report.py`:** genera un **informe** a partir de una lista de alertas.
5. Opciones **globales** en la CLI: `--config`, `-v/--verbose` y `--formato`.

Todavía no hay detección real: ahora construyes el "sistema nervioso" y lo pruebas con alertas de ejemplo.

### Ejemplo de uso previsto

```bash
$ toolkit --config config.toml -v logs
2026-10-12 10:15:30 INFO  toolkit.config: Configuración cargada de config.toml
...

$ toolkit alertas-demo --formato json
```

> Te propongo un subcomando oculto de pruebas (`alertas-demo`) que genera 3 alertas falsas y produce el informe, para poder probar el núcleo antes de tener módulos reales. Puedes quitarlo al final o dejarlo como `toolkit demo`.

---

## 📖 Conceptos nuevos

### 1. Configuración externa con TOML

Una herramienta seria **no tiene los parámetros fijos en el código**: los lee de un fichero. **TOML** es un formato de configuración legible, muy usado en Python (`pyproject.toml`). Ejemplo, `config.example.toml`:

```toml
[general]
nivel_log = "INFO"
fichero_log = "~/.toolkit/toolkit.log"
fichero_alertas = "~/.toolkit/alertas.jsonl"

[logs]
umbral_fallos = 5
ventana_segundos = 60
```

Leerlo en Python 3.11+ (módulo estándar `tomllib`):

```python
import tomllib

with open("config.toml", "rb") as f:
    datos = tomllib.load(f)

print(datos["general"]["nivel_log"])
```

- Se abre en modo **binario** (`"rb"`), es un requisito de `tomllib`.
- Devuelve **diccionarios anidados**.
- Lanza `tomllib.TOMLDecodeError` si el fichero está mal formado.

### 2. Valores por defecto y validación

El usuario puede no tener fichero de configuración o dejar claves sin poner. Define un diccionario `DEFAULTS` y **fusiónalo** con lo leído:

```python
resultado = {**DEFAULTS_SECCION, **leido_de_la_seccion}
```

- `{**a, **b}` crea un diccionario nuevo con las claves de `a` y las de `b`; **las de `b` ganan** si se repiten.
- Valida los tipos y rangos (por ejemplo, umbral entero y mayor que 0) y falla con un error claro si algo no es correcto.
- No olvides `.expanduser()` en las rutas con `~`.

### 3. El módulo `logging`

Con `print` no puedes filtrar por importancia, ni escribir en fichero y consola a la vez. `logging` sí:

```python
import logging

logger = logging.getLogger("toolkit.demo")

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(name)s: %(message)s",
    handlers=[
        logging.StreamHandler(),
        logging.FileHandler("toolkit.log", encoding="utf-8"),
    ],
)

logger.info("Arranca el módulo")
logger.warning("Algo raro")
logger.debug("Esto solo se ve con nivel DEBUG")
```

- Niveles de menor a mayor gravedad: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`.
- `level=` filtra: con `INFO` no ves los `DEBUG`.
- Los **handlers** deciden a dónde va cada mensaje (consola, fichero...).
- Cada módulo obtiene su logger con `logging.getLogger(__name__)`: el nombre del módulo aparece en el mensaje, útil para saber de dónde viene.
- **No uses `logging` para el resultado** que el usuario espera ver (los informes van por `print`/ficheros); úsalo para el funcionamiento interno y los avisos.
- Para ficheros que crecen sin límite, existe `logging.handlers.RotatingFileHandler(ruta, maxBytes=..., backupCount=...)`.

Crea una función `configurar_logging(nivel, fichero)` que haga esto una sola vez al arrancar. Con `-v` el nivel pasa a `DEBUG`.

### 4. Dataclasses: clases para guardar datos

```python
from dataclasses import dataclass, asdict

@dataclass
class Punto:
    x: int
    y: int = 0

p = Punto(3)
print(p)
print(p.x, p.y)
print(asdict(p))
```

- `@dataclass` genera automáticamente el constructor y una representación legible para una clase que **solo guarda datos**.
- Los campos se declaran con su **tipo** (`x: int`); un valor tras `=` es el por defecto.
- `asdict()` convierte la instancia en un **diccionario**, ideal para guardarla como JSON.
- Es como el diccionario de dispositivos del ejercicio 17, pero con nombres de campo fijos y comprobables.

Tu alerta puede ser:

```python
@dataclass
class Alerta:
    severidad: str      # "BAJA", "MEDIA", "ALTA", "CRITICA"
    fuente: str         # "logs", "audit", "netmon", "hids"
    mensaje: str
    momento: str        # fecha y hora en formato ISO
    detalles: dict      # datos extra (ip, usuario, ruta...)
```

Para `detalles`, un valor por defecto mutable como `{}` **no se puede poner directamente**; investiga `field(default_factory=dict)`.

### 5. Constantes para las severidades

Evita escribir `"ALTA"` a mano por todo el código (un error tipográfico pasaría desapercibido). Define constantes (o un `Enum`, si te animas a investigarlo):

```python
BAJA, MEDIA, ALTA, CRITICA = "BAJA", "MEDIA", "ALTA", "CRITICA"
ORDEN = {BAJA: 1, MEDIA: 2, ALTA: 3, CRITICA: 4}
```

- `ORDEN` te servirá para **ordenar** alertas de más a menos graves.

### 6. Fechas en formato ISO

```python
from datetime import datetime

ahora = datetime.now().astimezone().isoformat(timespec="seconds")
print(ahora)
```

- Formato estándar `2026-10-12T10:15:30+02:00`: ordenable y sin ambigüedades, y válido para JSON. `.astimezone()` añade la zona horaria.

### 7. JSON Lines: un registro por línea

Para guardar alertas, en vez de un JSON gigante que hay que reescribir, usa **JSON Lines** (`.jsonl`): **un objeto JSON por línea**, que se añade al final del fichero:

```python
import json

with open("alertas.jsonl", "a", encoding="utf-8") as f:
    f.write(json.dumps(asdict(alerta), ensure_ascii=False) + "\n")
```

- Añadir es rápido y seguro (modo `"a"`, ejercicio 20).
- Se lee línea a línea con `for linea in f: json.loads(linea)`.
- Es el formato habitual de logs estructurados.

### 8. Emitir una alerta

Crea una función `emitir(alerta, fichero)` que:

1. La registre con `logging` con el nivel adecuado a su severidad (`CRITICA` → `error`, `ALTA` → `warning`...).
2. La añada al fichero `.jsonl`.

Los módulos **solo llamarán a `emitir()`**; así si mañana quieres además mandar un email, solo cambias esa función.

### 9. Generar informes en varios formatos

`report.py` recibe una lista de alertas y produce un texto en el formato pedido:

| `--formato` | Salida |
|---|---|
| `texto` | tabla legible para consola (por defecto) |
| `json` | lista JSON completa |
| `markdown` | tabla Markdown para pegar en un README o ticket |

Debe incluir un **resumen** (total por severidad) y las alertas ordenadas de más a menos graves con `sorted(..., key=lambda a: ORDEN[a.severidad], reverse=True)` (ejercicio 15).

### 10. Opciones globales de la CLI

Opciones que van **antes** del subcomando (`toolkit -v --config x.toml logs`) se definen en el parser principal. Se usan en todos los módulos. Pásale al módulo la configuración ya cargada, por ejemplo guardándola en `args`.

---

## 📋 Requisitos

- [ ] `config.py`: función `cargar_config(ruta=None)` con valores por defecto, validación y errores claros. Un `config.example.toml` en la raíz del proyecto.
- [ ] `configurar_logging(nivel, fichero)` con consola + fichero, y `-v` activa `DEBUG`.
- [ ] `alerts.py`: dataclass `Alerta`, constantes de severidad, `crear_alerta(...)` (rellena el momento) y `emitir(alerta)`.
- [ ] `report.py`: `generar_informe(alertas, formato)` con los 3 formatos.
- [ ] Opciones globales `--config`, `-v` y `--formato` funcionando.
- [ ] Un subcomando temporal de prueba que genera 3 alertas falsas de distintas severidades y muestra el informe.
- [ ] Las carpetas de estado (`~/.toolkit/`) se **crean si no existen** y los permisos son restrictivos (solo tu usuario).
- [ ] Ninguna función del núcleo hace `print` salvo las de presentación del informe.

---

## 💡 Pistas

- Escribe y prueba **cada pieza por separado** en un script suelto antes de integrarla.
- Si una clave de config no existe, usa el valor por defecto, no falles.
- Piensa qué debe pasar si el fichero de configuración **no existe**: ¿error o valores por defecto? Decídelo y documéntalo en el README. (Recomendación: si se pasa `--config` explícito y no existe, error; si no se pasa, usa los defectos.)
- Si ves los mensajes de log **duplicados**, estás llamando varias veces a `configurar_logging()`.
- Usa `Path(...).expanduser()` y `mkdir(parents=True, exist_ok=True)` (ejercicio 22) para las rutas de estado, y `os.chmod(ruta, 0o700)` para la carpeta.

---

## 🚀 Retos extra

1. **Informe HTML:** añade `--formato html` con una tabla simple y colores por severidad (sin librerías externas).
2. **Filtro de severidad:** `--min-severidad ALTA` para mostrar solo alertas graves.
3. **Rotación de logs:** usa `RotatingFileHandler`.
4. **Variables de entorno:** permite sobrescribir parte de la configuración con variables (`TOOLKIT_NIVEL_LOG`), útil para automatizar.
5. **Más canales de alerta (diseño):** diseña (aunque no lo implementes) cómo añadirías el envío por email o a un webhook sin tocar los módulos.
6. **Secretos fuera del código:** si añades algún canal que requiera token, léelo de una variable de entorno, **nunca** del TOML que subes a GitHub.

---

## ▶️ Cómo comprobarlo

```bash
toolkit --config config.example.toml -v demo
toolkit demo --formato json
toolkit demo --formato markdown
cat ~/.toolkit/alertas.jsonl
```

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| Sin fichero de configuración | arranca con valores por defecto |
| `--config` con ruta inexistente | error claro, sin traceback |
| Config con `umbral_fallos = -3` | error de validación comprensible |
| Config con TOML mal formado | error indicando que el fichero no es válido |
| `demo --formato json` | JSON válido (compruébalo con `python3 -m json.tool`) |
| `demo` | alertas ordenadas de más a menos graves, con resumen |
| `-v` | aparecen mensajes `DEBUG` |
| `~/.toolkit/alertas.jsonl` | una línea JSON por alerta emitida |
| `ls -ld ~/.toolkit` | permisos `drwx------` |

Al terminar: `git tag fase-44`.
