# 🐍 Python: From Zero to Automation, Networking and Cybersecurity

🌐 **English** | [Español](README.es.md)

A repository of exercises to learn **Python from scratch**, with a clear goal: to be able to **automate tasks, write scripts and build networking and cybersecurity tools**.

Each exercise comes with its own educational statement (which explains the new concepts) and my solution. Difficulty increases gradually, from a simple "Hello, how old are you?" up to a complete command-line tool.

> 📚 This is the continuation of my [Bash](https://github.com/misteralva/Bash) exercises repository, using the same format.

---

## 🎯 Who is this repository for?

- For me, as a learning journal.
- For anyone who wants to learn Python **starting from zero**, with examples focused on **networking and systems** (IPs, ports, logs, passwords...) instead of generic ones.

> ℹ️ The exercise statements (`Enunciado.md`) and code comments are currently written in Spanish.

---

## 📁 Structure

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

Each exercise folder contains:

| File | Content |
|---|---|
| `Enunciado.md` | Goal, new concepts explained, hints and extra challenges |
| `solucion.py` | My solution to the exercise |

---

## ▶️ How to run the exercises

**Requirements:** Python 3 (usually preinstalled on Linux/WSL2).

Check your version:

```bash
python3 --version
```

Run any solution from its folder:

```bash
cd 01-fundamentos/01-presentacion
python3 solucion.py
```

- `python3` is the interpreter that reads and runs your code.
- `solucion.py` is the file containing the program.

**My setup:** WSL2 (Ubuntu) + Visual Studio Code.

---

## 🗺️ Learning path

### Block 1 · Fundamentals (with a networking flavor)

| # | Exercise | Concepts | Status |
|---|---|---|---|
| 01 | [Introduction](01-fundamentos/01-presentacion/Enunciado.md) | `input()`, `print()`, `int()`, `if/else` | ⏳ |
| 02 | [Basic calculator](01-fundamentos/02-calculadora/Enunciado.md) | operators, variables | ⏳ |
| 03 | [Even or odd](01-fundamentos/03-par-impar/Enunciado.md) | `%` operator | ⏳ |
| 04 | [Unit converter (bytes, MB, GB)](01-fundamentos/04-conversor-unidades/Enunciado.md) | f-strings, decimals | ⏳ |
| 05 | [Port classifier](01-fundamentos/05-clasificador-puertos/Enunciado.md) | `if/elif/else`, ranges | ⏳ |
| 06 | [Multiplication table and countdown](01-fundamentos/06-tabla-multiplicar/Enunciado.md) | `for`, `range()` | ⏳ |
| 07 | [Guess the number](01-fundamentos/07-adivina-numero/Enunciado.md) | `while`, `random` | ⏳ |
| 08 | [Validate an IP octet (0-255)](01-fundamentos/08-validar-octeto-ip/Enunciado.md) | `and/or`, input validation with retries | ⏳ |
| 09 | [Check password length](01-fundamentos/09-longitud-password/Enunciado.md) | `len()`, strings | ⏳ |
| 10 | [Interactive menu](01-fundamentos/10-menu-interactivo/Enunciado.md) | `while True`, `break`, `elif` | ⏳ |

### Block 2 · Data structures and functions

| # | Exercise | Concepts | Status |
|---|---|---|---|
| 11 | [IP list manager](02-estructuras-datos/11-gestor-ips/Enunciado.md) | lists, `append`, `remove` | ⏳ |
| 12 | [Port → service dictionary](02-estructuras-datos/12-puerto-servicio/Enunciado.md) | dictionaries, `.get()` | ⏳ |
| 13 | [Word counter](02-estructuras-datos/13-contador-palabras/Enunciado.md) | `split()`, dictionary as a counter | ⏳ |
| 14 | [`is_valid_ip()` function](02-estructuras-datos/14-funcion-es-ip-valida/Enunciado.md) | functions, `return` | ⏳ |
| 15 | [Failed attempts per user](02-estructuras-datos/15-intentos-fallidos/Enunciado.md) | dictionary + `for` | ⏳ |
| 16 | [Robust password validator](02-estructuras-datos/16-validador-passwords/Enunciado.md) | `any()`, string methods | ⏳ |
| 17 | [Network device inventory](02-estructuras-datos/17-inventario-dispositivos/Enunciado.md) | nested dictionaries | ⏳ |
| 18 | [Filter and sort IPs by subnet](02-estructuras-datos/18-filtrar-ordenar-ips/Enunciado.md) | `sorted()`, list comprehensions | ⏳ |

### Block 3 · Files and system

| # | Exercise | Concepts | Status |
|---|---|---|---|
| 19 | [Read a file and count lines and words](03-ficheros-sistema/19-leer-fichero/Enunciado.md) | `open()`, `with`, `try/except` | ⏳ |
| 20 | [Journal/log with timestamps](03-ficheros-sistema/20-diario-log/Enunciado.md) | modes `"w"`/`"a"`, `datetime` | ⏳ |
| 21 | [Analyze a simulated `auth.log`](03-ficheros-sistema/21-analizar-auth-log/Enunciado.md) | files + dictionaries | ⏳ |
| 22 | [Folder backup with date](03-ficheros-sistema/22-backup-carpeta/Enunciado.md) | `shutil`, `pathlib` | ⏳ |
| 23 | [Organize files by extension](03-ficheros-sistema/23-organizar-por-extension/Enunciado.md) | `pathlib`, `shutil.move` | ⏳ |
| 24 | [Audit file permissions](03-ficheros-sistema/24-auditar-permisos/Enunciado.md) | `os.walk`, `os.stat`, `stat` | ⏳ |
| 25 | [Run system commands](03-ficheros-sistema/25-comandos-sistema/Enunciado.md) | `subprocess.run` | ⏳ |
| 26 | [Save and load the inventory (JSON/CSV)](03-ficheros-sistema/26-guardar-inventario/Enunciado.md) | `json`, `csv` | ⏳ |

### Block 4 · Networking

| # | Exercise | Concepts | Status |
|---|---|---|---|
| 27 | [Subnet calculator](04-redes/27-calculadora-subredes/Enunciado.md) | `ipaddress` | ⏳ |
| 28 | [Ping sweep](04-redes/28-barrido-ping/Enunciado.md) | `subprocess`, `ipaddress` | ⏳ |
| 29 | [TCP port scanner](04-redes/29-escaner-puertos/Enunciado.md) | `socket` | ⏳ |
| 30 | [TCP client and server (echo)](04-redes/30-cliente-servidor-tcp/Enunciado.md) | `socket`, `bind`, `listen` | ⏳ |
| 31 | [Query an API](04-redes/31-consultar-api/Enunciado.md) | `requests`, JSON, `venv` | ⏳ |
| 32 | [Forward and reverse DNS lookup](04-redes/32-resolucion-dns/Enunciado.md) | `socket`, network errors | ⏳ |
| 33 | [Run commands over SSH on a VM](04-redes/33-comandos-ssh/Enunciado.md) | `paramiko`, `getpass` | ⏳ |
| 34 | [CLI tool with arguments](04-redes/34-herramienta-cli/Enunciado.md) | `argparse` | ⏳ |

### Block 5 · Defensive cybersecurity 🔜

File integrity with hashes, detection of suspicious IPs in logs, basic packet sniffer, password generator and validator. Modules: `hashlib`, `re`, `scapy`.

### Block 6 · Final project 🔜

A complete CLI tool (network monitor or log analyzer with alerts) with arguments, logging, tests and a virtual environment.

**Legend:** ✅ done · ⏳ pending · 🔜 to be detailed

---

## 🧭 How to use this repository to learn

1. Read the exercise's `Enunciado.md`.
2. Try to solve it **on your own** using the concepts and hints.
3. Try the extra challenges.
4. Compare with `solucion.py` only at the end.

> Making mistakes and debugging is the best part of learning. 💪
