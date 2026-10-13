# Fase 49 · Tests, empaquetado y README final

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Convierte tu proyecto en algo **fiable y presentable**:

1. Escribe **tests automáticos** con `pytest` para las funciones puras de todos los módulos (mínimo **15 tests**).
2. Declara las **dependencias de desarrollo** en `pyproject.toml`.
3. **Construye el paquete** (`wheel`) y compruébalo instalándolo en un entorno limpio.
4. Escribe el **README final** del proyecto y publica una **versión** (`v0.1.0`) en GitHub.

---

## 📖 Conceptos nuevos

### 1. ¿Por qué hacer tests?

Cuando cambias algo en un programa grande, ¿cómo sabes que no has roto otra cosa? **Comprobándolo a mano cada vez no escala.** Un **test automático** es una pequeña función que ejecuta tu código con unos datos conocidos y comprueba que el resultado es el esperado. Los ejecutas todos en segundos.

En seguridad es especialmente importante: **una regla de detección que falla en silencio es peor que no tener ninguna**, porque te da una falsa sensación de protección.

### 2. `pytest` básico

Instalación en tu entorno virtual:

```bash
pip install pytest
```

Un test es una función cuyo nombre empieza por `test_`, en un fichero `test_*.py` dentro de `tests/`:

```python
# tests/test_ejemplo.py

def doble(n):
    return n * 2

def test_doble_de_cinco():
    assert doble(5) == 10

def test_doble_de_cero():
    assert doble(0) == 0
```

- `assert condicion` falla el test si la condición es falsa.
- Ejecútalos con `pytest` (o `pytest -v` para más detalle) desde la raíz del proyecto.
- Si un test falla, `pytest` te muestra exactamente qué valor esperaba y cuál obtuvo.

### 3. Qué testear: la lógica pura

Gracias a la separación que hiciste (**parsear → detectar → informar**), puedes probar la lógica **sin ficheros ni red**: le pasas datos creados en el propio test.

```python
from datetime import datetime
from toolkit.modules.logs import Evento, detectar_fuerza_bruta

def _evento(segundo, ip="1.2.3.4"):
    return Evento(datetime(2026, 10, 8, 10, 0, segundo), "root", ip, False)

def test_detecta_fuerza_bruta():
    eventos = [_evento(s) for s in range(5)]
    alertas = detectar_fuerza_bruta(eventos, umbral=5, ventana=60)
    assert len(alertas) == 1

def test_no_alerta_por_debajo_del_umbral():
    eventos = [_evento(s) for s in range(4)]
    assert detectar_fuerza_bruta(eventos, umbral=5, ventana=60) == []
```

- Un test de **caso límite** (justo en el umbral, justo bajo él) vale más que diez de casos obvios.
- Una función auxiliar con `_` al principio (`_evento`) evita repetir código dentro de los tests.
- Ajusta los nombres y firmas a **cómo hayas escrito tú** tus funciones.

### 4. `parametrize`: muchos casos en un test

```python
import pytest

@pytest.mark.parametrize("entrada, esperado", [
    ("192.168.1.1", True),
    ("256.1.1.1", False),
    ("1.2.3", False),
])
def test_es_ip_valida(entrada, esperado):
    assert es_ip_valida(entrada) == esperado
```

- `pytest` ejecuta el test **una vez por cada fila** y te dice cuáles fallan.
- Perfecto para tablas de casos, como las de los ejercicios (las de "Comprueba tu solución" son tests esperando a ser escritos).

### 5. `tmp_path`: ficheros temporales seguros

```python
def test_lee_log(tmp_path):
    fichero = tmp_path / "prueba.log"
    fichero.write_text("2026-10-08 10:15:00 FAILED user=root ip=1.2.3.4\n", encoding="utf-8")
    eventos = leer_eventos(fichero)
    assert len(eventos) == 1
```

- `tmp_path` es una **carpeta temporal única por test**, que pytest crea y limpia sola.
- Así pruebas lectura de ficheros, línea base, permisos o `sshd_config` falsos **sin tocar tu sistema ni tus datos**.
- `Path.write_text()` crea el fichero de un golpe.

### 6. Probar que se lanza un error

```python
def test_red_publica_rechazada():
    with pytest.raises(ValueError):
        validar_red("8.8.8.0/24")
```

- `pytest.raises(Tipo)` pasa si el bloque lanza esa excepción, y falla si no la lanza.
- Haz esto con tus validaciones de entrada: son **parte del comportamiento** de la herramienta.

### 7. Qué NO testear de forma automática

Lo que depende del mundo real (escanear una red de verdad, `ss` del sistema, SSH...) es lento y poco fiable. **Aísla** esa parte en funciones pequeñas y testea la lógica que las rodea. Si quieres probar una función que llama a un comando, investiga `monkeypatch` (sustituir temporalmente una función por una falsa).

### 8. Dependencias de desarrollo en `pyproject.toml`

```toml
[project.optional-dependencies]
dev = ["pytest"]
```

- Es una lista de dependencias **opcionales**: no se instalan con el programa, solo si las pides.
- Instálalas junto al proyecto con:

```bash
pip install -e ".[dev]"
```

- Las comillas evitan que la terminal interprete los corchetes.
- Cualquiera que clone tu repo podrá preparar el entorno de desarrollo con un único comando.

### 9. Cobertura (opcional)

```bash
pip install pytest-cov
pytest --cov=toolkit
```

- Muestra qué porcentaje del código ejecutan tus tests. No busques el 100 %: busca que **las reglas de detección y las validaciones** estén cubiertas.

### 10. Construir el paquete

```bash
pip install build
python3 -m build
```

- Crea la carpeta `dist/` con dos ficheros: un `.whl` (*wheel*, instalable) y un `.tar.gz` (código fuente).
- **Compruébalo en un entorno limpio**, para asegurar que no dependes de nada "de tu máquina":

```bash
python3 -m venv /tmp/prueba-venv
source /tmp/prueba-venv/bin/activate
pip install dist/toolkit-0.1.0-py3-none-any.whl
toolkit --help
deactivate
```

- Ese `toolkit --help` debe funcionar en un entorno donde nunca instalaste nada más. Si falla, probablemente falta algo en `pyproject.toml`.
- `dist/` y `build/` van en el `.gitignore`: **no** se suben al repositorio.

### 11. Versiones y etiquetas

- Usa **versionado semántico**: `MAYOR.MENOR.PARCHE`. Para tu primera versión funcional, `0.1.0`.
- Las etiquetas de Git (`git tag v0.1.0`) marcan una versión. En GitHub puedes crear una **Release** desde esa etiqueta y adjuntar el `.whl`.
- Lleva un `CHANGELOG.md` sencillo con lo que trae cada versión.

### 12. Un buen README de proyecto

El README es la **cara** de tu proyecto en tu portfolio. Debe incluir:

1. **Qué es y para qué sirve** (en dos frases).
2. **Aviso legal y ético** (uso defensivo y autorizado).
3. **Instalación** (copiable y que funciona).
4. **Ejemplos de uso** de los 4 módulos, con **salidas reales** copiadas de tu terminal.
5. **Arquitectura:** un diagrama (puedes usar **Mermaid**, que GitHub renderiza) o la lista de módulos y cómo se relacionan.
6. **Configuración:** explicación de `config.toml`.
7. **Tests:** cómo ejecutarlos.
8. **Limitaciones conocidas** (sé honesto: es lo que más valora quien lo lee).
9. **Hoja de ruta** (qué harías después).
10. **Licencia y créditos.**

---

## 📋 Requisitos

- [ ] Carpeta `tests/` con al menos **15 tests** repartidos entre los módulos (núcleo, `logs`, `audit`, `netmon`, `hids`).
- [ ] Al menos un test con `parametrize`, uno con `tmp_path` y uno con `pytest.raises`.
- [ ] Tests de **casos límite** para las reglas de detección (umbral exacto, justo debajo, fuera de ventana).
- [ ] `pytest` pasa sin fallos, y **ningún test toca tu sistema real** (ni `/etc`, ni red, ni tu `~/.toolkit`).
- [ ] `[project.optional-dependencies] dev` en `pyproject.toml`; `pip install -e ".[dev]"` funciona.
- [ ] `python3 -m build` genera el `.whl` y se instala y ejecuta en un entorno limpio.
- [ ] `README.md` final con los 10 apartados.
- [ ] `CHANGELOG.md` y etiqueta `v0.1.0` subida a GitHub.
- [ ] Revisión final de seguridad del repositorio: sin contraseñas, tokens ni claves; sin `venv/`, `dist/` ni ficheros de estado; `.gitignore` completo.

---

## 💡 Pistas

- Escribe los tests **módulo a módulo**. Empieza por funciones muy sencillas para coger ritmo.
- Si no puedes testear una función porque lee ficheros dentro, es señal de que hay que **refactorizarla** separando la lectura de la lógica. Es normal descubrirlo ahora.
- Las tablas de "Criterios de aceptación" de las fases 45 a 48 te dan los casos de prueba ya hechos.
- Si un test falla, **no cambies el test para que pase** sin entender por qué: podrías haber encontrado un error real en tu código.
- Para revisar secretos antes de subir: `git diff`, `git status` y buscar a mano palabras como `password`, `token`, `key`.

---

## 🚀 Retos extra

1. **Integración continua:** crea `.github/workflows/tests.yml` para que **GitHub Actions** ejecute `pytest` en cada `push`. Investiga cómo usar `actions/checkout` y `actions/setup-python`.
2. **Insignia:** añade al README el badge del estado de los tests.
3. **Análisis estático:** pasa `ruff` (estilo y errores) y `mypy` (tipos) y arregla lo que encuentren.
4. **`--version`:** que muestre la versión leyendo los metadatos del paquete.
5. **Seguridad del propio proyecto:** ejecuta `pip-audit` para comprobar si alguna dependencia tiene vulnerabilidades conocidas y `bandit` para analizar tu código buscando malas prácticas.
6. **Documentación:** añade una carpeta `docs/` con una explicación de cada regla de detección y por qué existe.
7. **Publicación:** investiga cómo se publicaría un paquete en PyPI (sin publicarlo: es un nombre real y conviene hacerlo con cuidado).
8. **Demo:** graba un GIF o una captura de la herramienta funcionando para el README.

---

## ▶️ Cómo comprobarlo

```bash
cd toolkit
source venv/bin/activate
pip install -e ".[dev]"
pytest -v
python3 -m build
```

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| `pytest -v` | todos los tests en verde (≥ 15) |
| Cambias a propósito un umbral en el código y ejecutas `pytest` | **algún test falla** (si no, tus tests no protegen nada) |
| `python3 -m build` | genera un `.whl` en `dist/` |
| Instalas el `.whl` en un entorno nuevo | `toolkit --help` funciona |
| `git status` | sin `venv/`, `dist/`, `.pytest_cache/` ni ficheros de estado |
| Buscas tokens y claves en el repo | ninguno |
| README | los 10 apartados, con ejemplos reales |
| Etiqueta `v0.1.0` | visible en GitHub |

Al terminar: `git tag fase-49` y `git tag v0.1.0`.

---

## 🎉 ¡Proyecto terminado!

Has pasado de no saber Python a tener una herramienta de seguridad defensiva con CLI, configuración, alertas, tests y empaquetado. Es un proyecto sólido para tu portfolio de GitHub. Desde aquí, el siguiente paso natural es seguir ampliándolo con tus propias ideas.
