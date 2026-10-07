# 🐍 Python: de cero a automatización, redes y ciberseguridad

🌐 [English](README.md) | **Español**

Repositorio de ejercicios para aprender **Python desde cero**, con un objetivo claro: llegar a **automatizar tareas, escribir scripts y crear herramientas de redes y ciberseguridad**.

Cada ejercicio tiene su propio enunciado educativo (que explica los conceptos nuevos) y mi solución. La idea es ir subiendo la dificultad poco a poco, de un simple "Hola, ¿cuántos años tienes?" hasta una herramienta de línea de comandos completa.

> 📚 Es la continuación de mi repositorio de ejercicios de [Bash](https://github.com/misteralva/Bash), con el mismo formato.

---

## 🎯 ¿Para quién es este repositorio?

- Para mí, que lo uso como diario de aprendizaje.
- Para cualquiera que quiera aprender Python **sin saber nada** y con ejemplos orientados a **redes y sistemas** (IPs, puertos, logs, contraseñas...) en vez de ejemplos genéricos.

---

## 📁 Estructura

```
Python/
├── README.md
├── README.es.md
├── 01-fundamentos/
│   ├── 01-presentacion/
│   │   ├── Enunciado.md
│   │   └── solucion.py
│   └── 02-.../
├── 02-estructuras-datos/
├── 03-ficheros-sistema/
├── 04-redes/
├── 05-ciberseguridad/
└── 06-proyecto-final/
```

Cada carpeta de ejercicio contiene:

| Archivo | Contenido |
|---|---|
| `Enunciado.md` | Objetivo, conceptos nuevos explicados, pistas y retos extra |
| `solucion.py` | Mi solución al ejercicio |

---

## ▶️ Cómo ejecutar los ejercicios

**Requisitos:** Python 3 (en Linux/WSL2 normalmente ya viene instalado).

Comprueba tu versión:

```bash
python3 --version
```

Ejecuta cualquier solución desde su carpeta:

```bash
cd 01-fundamentos/01-presentacion
python3 solucion.py
```

- `python3` es el intérprete que lee y ejecuta tu código.
- `solucion.py` es el archivo con el programa.

**Entorno que uso:** WSL2 (Ubuntu) + Visual Studio Code.

---

## 🗺️ Ruta de aprendizaje

### Bloque 1 · Fundamentos (con sabor a redes)

| Nº | Ejercicio | Conceptos | Estado |
|---|---|---|---|
| 01 | [Presentación](01-fundamentos/01-presentacion/Enunciado.md) | `input()`, `print()`, `int()`, `if/else` | ⏳ |
| 02 | [Calculadora básica](01-fundamentos/02-calculadora/Enunciado.md) | operadores, variables | ⏳ |
| 03 | Par o impar | operador `%` | ⏳ |
| 04 | Conversor de unidades (bytes, MB, GB) | f-strings, decimales | ⏳ |
| 05 | Clasificador de puertos | `if/elif/else`, rangos | ⏳ |
| 06 | Tabla de multiplicar y cuenta atrás | `for`, `range()` | ⏳ |
| 07 | Adivina el número | `while`, `random` | ⏳ |
| 08 | Validar un octeto de IP (0-255) | `and/or`, validación con reintentos | ⏳ |
| 09 | Comprobar longitud de contraseña | `len()`, cadenas | ⏳ |
| 10 | Menú interactivo | `while True`, `break`, `elif` | ⏳ |

### Bloque 2 · Estructuras de datos y funciones

| Nº | Ejercicio | Conceptos | Estado |
|---|---|---|---|
| 11 | Gestor de lista de IPs | listas, `append`, `remove` | ⏳ |
| 12 | Diccionario puerto → servicio | diccionarios, `.get()` | ⏳ |
| 13 | Contador de palabras | `split()`, diccionario como contador | ⏳ |
| 14 | Función `es_ip_valida()` | funciones, `return` | ⏳ |
| 15 | Intentos fallidos por usuario | diccionario + `for` | ⏳ |
| 16 | Validador de contraseñas robusto | `any()`, métodos de cadenas | ⏳ |
| 17 | Inventario de dispositivos de red | diccionarios anidados | ⏳ |
| 18 | Filtrar y ordenar IPs por subred | `sorted()`, comprensión de listas | ⏳ |

### Bloque 3 · Ficheros y sistema 🔜

Leer y escribir archivos, analizar un `auth.log` para detectar fuerza bruta, backups automáticos, auditar permisos. Módulos: `os`, `pathlib`, `subprocess`, `shutil`.

### Bloque 4 · Redes 🔜

Escáner de puertos, barrido de ping, cliente/servidor TCP, consultas a APIs, ejecución de comandos por SSH. Módulos: `socket`, `ipaddress`, `requests`, `paramiko`.

### Bloque 5 · Ciberseguridad defensiva 🔜

Integridad de ficheros con hashes, detección de IPs sospechosas en logs, sniffer básico, generador y validador de contraseñas. Módulos: `hashlib`, `re`, `scapy`.

### Bloque 6 · Proyecto final 🔜

Una herramienta CLI completa (monitor de red o analizador de logs con alertas) con argumentos, logs, tests y entorno virtual.

**Leyenda:** ✅ hecho · ⏳ pendiente · 🔜 por detallar

---

## 🧭 Cómo usar este repositorio para aprender

1. Lee el `Enunciado.md` del ejercicio.
2. Intenta resolverlo **tú solo** usando los conceptos y las pistas.
3. Prueba los retos extra.
4. Compara con `solucion.py` solo al final.

> Equivocarse y depurar es la mejor parte del aprendizaje. 💪

---

## 👤 Autor

**David** · [@misteralva](https://github.com/misteralva) · [misteralva.github.io](https://misteralva.github.io)

Estudiante de ASIR, interesado en redes, ciberseguridad y automatización.
