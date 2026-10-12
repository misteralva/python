# 🛡️ Bloque 6 · Proyecto final: `toolkit`, un kit de seguridad defensiva

En este bloque dejas de hacer ejercicios sueltos y construyes **un único proyecto real**, por fases: una herramienta de línea de comandos con cuatro módulos que reutilizan lo que has aprendido en los bloques anteriores.

```bash
toolkit logs ...     # analizador de logs con alertas
toolkit audit ...    # auditor de seguridad de un servidor Linux
toolkit netmon ...   # monitor de red
toolkit hids ...     # mini HIDS (detección de intrusiones en un equipo)
```

> ℹ️ `toolkit` es un nombre de trabajo. Puedes ponerle el tuyo (por ejemplo, `misterkit`). Si lo cambias, cámbialo en todos los sitios: carpeta del paquete, `pyproject.toml` y comandos.

---

## 🗺️ Fases

| Fase | Contenido | Dificultad |
|---|---|---|
| [43](43-diseno-esqueleto/Enunciado.md) | Diseño y esqueleto: paquete, `pyproject.toml`, subcomandos | ⭐⭐⭐ |
| [44](44-nucleo-comun/Enunciado.md) | Núcleo común: configuración, `logging`, alertas e informes | ⭐⭐⭐⭐ |
| [45](45-modulo-logs/Enunciado.md) | Módulo `logs`: detección de fuerza bruta y más | ⭐⭐⭐⭐ |
| [46](46-modulo-audit/Enunciado.md) | Módulo `audit`: auditoría de hardening de Linux | ⭐⭐⭐⭐ |
| [47](47-modulo-netmon/Enunciado.md) | Módulo `netmon`: monitor de red con línea base | ⭐⭐⭐⭐⭐ |
| [48](48-modulo-hids/Enunciado.md) | Módulo `hids`: integra todo en un mini HIDS | ⭐⭐⭐⭐⭐ |
| [49](49-tests-empaquetado/Enunciado.md) | Tests con `pytest`, empaquetado y README final | ⭐⭐⭐⭐ |

---

## 📁 Dónde va el código

A diferencia de los bloques anteriores, **aquí no hay un `solucion.py` por fase**. Todo el código vive en **una sola carpeta compartida**, `toolkit/`, que creas en la fase 43:

```
06-proyecto-final/
├── README.md                  ← este fichero
├── 43-diseno-esqueleto/
│   └── Enunciado.md
├── 44-nucleo-comun/
│   └── Enunciado.md
├── ...
├── 49-tests-empaquetado/
│   └── Enunciado.md
└── toolkit/                   ← EL PROYECTO (lo creas tú)
    ├── pyproject.toml
    ├── README.md
    ├── LICENSE
    ├── .gitignore
    ├── config.example.toml
    ├── src/
    │   └── toolkit/
    │       ├── __init__.py
    │       ├── __main__.py
    │       ├── cli.py
    │       ├── config.py
    │       ├── alerts.py
    │       ├── report.py
    │       └── modules/
    │           ├── __init__.py
    │           ├── logs.py
    │           ├── audit.py
    │           ├── netmon.py
    │           └── hids.py
    └── tests/
```

Cuando termines cada fase, **marca el punto con una etiqueta de Git**:

```bash
git add .
git commit -m "Fase 44: núcleo común"
git tag fase-44
git push --tags
```

- `git tag fase-44` pone una etiqueta en ese commit.
- `git push --tags` sube las etiquetas a GitHub.
- Así, cualquiera puede ver el proyecto tal como estaba al acabar cada fase.

---

## 🧠 Principios de diseño del proyecto

1. **Separar la lógica de la entrada/salida.** Las funciones que *deciden* (por ejemplo, "¿esta IP hace fuerza bruta?") no deben leer ficheros ni imprimir: reciben datos y devuelven datos. Así se pueden **testear** (fase 49).
2. **No repetir código.** Si dos módulos necesitan lo mismo, va al núcleo común o se importa; no se copia y pega.
3. **Seguro por diseño.** Las herramientas **solo leen**: nunca borran, bloquean ni modifican nada solas. Los módulos de red solo trabajan sobre **redes privadas** (las tuyas).
4. **Códigos de salida útiles.** Cada comando devuelve `0` si todo bien y distinto de `0` si encuentra problemas, para poder automatizarlo.
5. **Configurable, sin secretos en el código.**

---

## ⚠️ Aviso legal y ético

Esta herramienta es **defensiva**. Úsala solo en **tus propios equipos y en tu laboratorio** (tus VMs), o en sistemas que administres con autorización expresa por escrito. El módulo de red hace escaneos: nunca lo uses sobre redes ajenas.

---

## ✅ Qué sabrás hacer al terminar

- Estructurar un programa grande en paquetes y módulos.
- Crear una CLI profesional con subcomandos.
- Usar `logging`, configuración en TOML, informes y alertas.
- Escribir tests con `pytest`.
- Empaquetar e instalar tu herramienta con `pip`.
- Presentar un proyecto de seguridad en tu portfolio de GitHub.
