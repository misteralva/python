# Ejercicio 34 · Mini herramienta de línea de comandos con `argparse`

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

> ## ⚠️ Aviso legal y ético
> Esta herramienta incluye un escáner de puertos. Úsala **solo** sobre `127.0.0.1`, tus VMs o sistemas con autorización expresa. Ver el aviso del ejercicio 29.

---

## 🎯 Objetivo

Convierte tu escáner de puertos del ejercicio 29 en una **herramienta de línea de comandos** con argumentos, como las herramientas reales de administración. En lugar de preguntar con `input()`, el usuario lo indica al ejecutarla:

```bash
python3 solucion.py 127.0.0.1 -p 1-1024 -t 0.3
```

Debe admitir:

| Argumento | Descripción | Por defecto |
|---|---|---|
| `host` | Host a escanear (obligatorio) | — |
| `-p`, `--puertos` | Rango (`1-1024`) o lista (`22,80,443`) | `1-1024` |
| `-t`, `--timeout` | Segundos de espera por puerto | `0.5` |
| `-v`, `--verbose` | Muestra también los puertos cerrados | desactivado |

Además, `--help` debe mostrar una ayuda clara generada automáticamente.

### Ejemplo de ejecución

```
$ python3 solucion.py 127.0.0.1 -p 8000,8001 -t 0.3
Escaneando 127.0.0.1 (2 puertos, timeout 0.3 s)...
Puerto 8000 abierto
Resumen: 1 abierto(s) de 2
```

```
$ python3 solucion.py --help
usage: solucion.py [-h] [-p PUERTOS] [-t TIMEOUT] [-v] host
...
```

---

## 📖 Conceptos nuevos

### 1. Por qué usar argumentos

Una herramienta con argumentos se puede **automatizar** (cron, scripts, pipelines), documentar con `--help` y combinar con otras. Pedir datos con `input()` obliga a una persona a estar delante. Verás que casi todas las herramientas que usas (`ls`, `ping`, `nmap`, `ssh`) funcionan así.

### 2. El módulo `argparse`

```python
import argparse

parser = argparse.ArgumentParser(description="Saluda a alguien")
parser.add_argument("nombre", help="Nombre de la persona")
parser.add_argument("-v", "--veces", type=int, default=1, help="Cuántas veces saludar")
args = parser.parse_args()

for _ in range(args.veces):
    print(f"Hola, {args.nombre}")
```

- `ArgumentParser(description=...)` crea el analizador; la descripción aparece en `--help`.
- `add_argument("nombre")` define un argumento **posicional** (obligatorio).
- `add_argument("-v", "--veces", ...)` define uno **opcional** con forma corta y larga.
- `type=int` convierte el valor automáticamente; también existe `type=float`.
- `default=` es el valor si no se indica; `help=` es el texto de ayuda.
- `parse_args()` lee `sys.argv` y devuelve un objeto con los valores: `args.nombre`, `args.veces`.
- Si el usuario se equivoca o pide `-h`, `argparse` muestra el error o la ayuda y **termina el programa solo**.

### 3. Interruptores (flags) con `action="store_true"`

```python
parser.add_argument("-d", "--debug", action="store_true", help="Modo depuración")
```

- Sin valor: si aparece, `args.debug` es `True`; si no, `False`.

### 4. Estructura de un script profesional: `main()`

```python
def main():
    args = parsear_argumentos()
    ...

if __name__ == "__main__":
    main()
```

- La condición `if __name__ == "__main__":` significa "ejecuta esto **solo si** el fichero se lanza directamente, no si otro programa lo importa".
- Permite **reutilizar** tus funciones desde otros ficheros sin que se ejecute todo.
- Es el patrón estándar en los scripts de Python.

### 5. Interpretar el texto de los puertos

`-p` llega como **texto**: puede ser `"1-1024"`, `"22,80,443"` o `"8080"`. Escribe una función `parsear_puertos(texto)` que devuelva una **lista de enteros**:

- Si contiene `-`, es un rango.
- Si contiene `,`, es una lista.
- Si no, es un puerto único.

Para avisar de un valor incorrecto, `parser.error("mensaje")` muestra el error con el formato estándar y sale con código distinto de 0.

### 6. Códigos de salida

```python
import sys
sys.exit(1)
```

- Las herramientas devuelven `0` si todo fue bien y otro número si hubo problemas. Así otros scripts pueden reaccionar (en bash: `echo $?`).
- Decide un criterio para tu herramienta (por ejemplo, `0` si no hay puertos abiertos y `1` si los hay, o al revés), y documéntalo.

### 7. Reutilizar tu código

Copia la función `puerto_abierto()` del ejercicio 29 y úsala aquí. Mantén separadas: **lógica** (funciones), **argumentos** (`argparse`) y **salida** (`print`).

---

## 💡 Pistas

- Primero define los argumentos y comprueba `--help`; después conecta la lógica.
- Imprime lo que has recibido (`print(args)`) para depurar mientras avanzas.
- Valida: puertos entre 0 y 65535, timeout positivo, rango con inicio menor que fin.
- Con `-v`, muestra también los cerrados; sin él, solo los abiertos.
- Para probar un puerto abierto, `python3 -m http.server 8000` en otra terminal.

---

## 🚀 Retos extra

1. **Salida JSON:** añade `--json` para imprimir el resultado como JSON (ejercicio 26) y poder procesarlo con otras herramientas.
2. **Varios hosts:** acepta uno o más hosts (*investiga `nargs="+"`*).
3. **Fichero de hosts:** opción `-f` para leer una lista de hosts desde un fichero.
4. **Guardar en fichero:** opción `-o informe.txt`.
5. **Logging:** sustituye algunos `print` por el módulo `logging` y añade un nivel configurable.
6. **Subcomandos:** investiga `add_subparsers` para tener `herramienta.py scan ...`, `herramienta.py ping ...` y `herramienta.py dns ...`, juntando los ejercicios 28, 29 y 32.
7. **Hacerlo ejecutable:** añade `#!/usr/bin/env python3` en la primera línea y dale permisos con `chmod +x solucion.py` para ejecutarlo como `./solucion.py`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py --help
python3 solucion.py 127.0.0.1 -p 1-1024
python3 solucion.py 127.0.0.1 -p 22,80,443,8000 -t 0.3 -v
```

---

## ✅ Comprueba tu solución

| Comando | Resultado esperado |
|---|---|
| `python3 solucion.py --help` | ayuda con todos los argumentos |
| `python3 solucion.py` (sin host) | error indicando que falta el host |
| `python3 solucion.py 127.0.0.1 -p 8000` (con `http.server` activo) | puerto 8000 abierto |
| `python3 solucion.py 127.0.0.1 -p 100-50` | error de rango inválido |
| `python3 solucion.py 127.0.0.1 -p 70000` | error de puerto fuera de rango |
| `python3 solucion.py 127.0.0.1 -t -1` | error de timeout inválido |
