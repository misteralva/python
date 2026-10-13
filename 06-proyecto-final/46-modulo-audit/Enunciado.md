# Fase 46 · Módulo `audit`: auditor de seguridad de un servidor Linux

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

> ## ⚠️ Aviso
> Esta herramienta **solo lee** y **nunca modifica nada** del sistema. Úsala únicamente en equipos **tuyos** o que administres con autorización. Las comprobaciones se inspiran en buenas prácticas públicas de *hardening* (por ejemplo, las guías **CIS Benchmarks** y herramientas como **Lynis**); estúdialas para profundizar.

---

## 🎯 Objetivo

Implementa `toolkit audit`, que revisa una **lista de comprobaciones de seguridad** (*checks*) del equipo y da un **informe con puntuación y recomendaciones**.

```bash
toolkit audit
toolkit audit --formato markdown --ruta-world-writable /etc
```

### Comprobaciones obligatorias

| # | Comprobación | Resultado |
|---|---|---|
| 1 | `/etc/passwd`: propietario `root` y **sin escritura** para grupo ni otros | OK / FALLO |
| 2 | `/etc/shadow`: **sin ningún permiso** para "otros" | OK / FALLO |
| 3 | `sshd_config`: `PermitRootLogin` **no** vale `yes` | OK / FALLO / OMITIDO |
| 4 | `sshd_config`: `PasswordAuthentication` desactivado (se recomienda usar claves) | OK / AVISO / OMITIDO |
| 5 | **Cuentas con UID 0** distintas de `root` | OK / FALLO |
| 6 | **Puertos en escucha** (`ss -tln`): avisa de servicios inseguros (21 FTP, 23 Telnet...) | OK / AVISO / FALLO |
| 7 | **Ficheros escribibles por todos** en una carpeta (por defecto `/etc`) | OK / FALLO |

Cada comprobación devuelve un **resultado** con: nombre, estado, detalle y recomendación.

### Estados

| Estado | Significado |
|---|---|
| `OK` | cumple |
| `AVISO` | no es crítico, pero conviene revisarlo |
| `FALLO` | problema de seguridad que corregir |
| `OMITIDO` | no se pudo comprobar (el servicio no está, falta permiso...) |

### Ejemplo de salida

```
AUDITORÍA DE SEGURIDAD
[OK]      Permisos de /etc/passwd
[OK]      Permisos de /etc/shadow
[FALLO]   PermitRootLogin: está en 'yes'
          → Recomendación: ponerlo en 'no' o 'prohibit-password'.
[AVISO]   PasswordAuthentication está activado
          → Recomendación: usar autenticación por clave.
[OK]      Solo root tiene UID 0
[AVISO]   Puertos en escucha: 22, 631
[OK]      Sin ficheros escribibles por todos en /etc

Puntuación: 71 %  (5 OK, 2 AVISO, 1 FALLO)
```

---

## 📖 Conceptos nuevos

### 1. Diseño: una función por comprobación

Cada *check* es una función que **recibe sus datos como parámetros** (con valores por defecto) y **devuelve un `Resultado`**:

```python
from dataclasses import dataclass

@dataclass
class Resultado:
    nombre: str
    estado: str
    detalle: str
    recomendacion: str = ""

def comprobar_passwd(ruta="/etc/passwd"):
    ...
    return Resultado("Permisos de /etc/passwd", "OK", "...")
```

- Como la ruta es un **parámetro**, en los tests (fase 49) la apuntas a un fichero de prueba y no al real.
- Mantén una **lista de todas las comprobaciones** y recórrela con un `for`: añadir una nueva será añadir una función a la lista.

```python
CHECKS = [comprobar_passwd, comprobar_shadow, ...]
resultados = [check() for check in CHECKS]
```

- En Python las **funciones son objetos**: puedes guardarlas en una lista y llamarlas después con `()`.

### 2. Comprobar permisos con `stat`

Ya usaste esto en el ejercicio 24:

```python
import os
import stat

modo = os.stat("/etc/passwd").st_mode
print(oct(modo & 0o777))

if modo & stat.S_IWOTH:
    print("Escribible por otros")
```

- `modo & 0o022` es distinto de 0 si el grupo **o** otros pueden escribir (`0o020` = escritura de grupo, `0o002` = de otros).
- `modo & 0o007` mira **cualquier permiso** de otros.
- Propietario: `os.stat(...).st_uid` (el UID de `root` es `0`).

### 3. Leer `/etc/passwd`

Cada línea tiene 7 campos separados por `:`

```
david:x:1000:1000:David:/home/david:/bin/bash
```

El 1º es el usuario y el **3º es el UID**. Un usuario con UID `0` tiene **los mismos privilegios que root**: si hay uno distinto de `root`, es un fallo grave (suele ser señal de una puerta trasera).

```python
campos = linea.strip().split(":")
usuario, uid = campos[0], campos[2]
```

### 4. Leer `sshd_config`

El fichero es de texto con líneas tipo `Directiva valor`. Reglas para leerlo:

- Ignora **líneas vacías** y las que empiezan por `#` (comentarios).
- Los nombres de directiva **no distinguen mayúsculas** (`permitrootlogin` = `PermitRootLogin`): usa `.lower()`.
- Si una directiva aparece varias veces, **vale la primera**.
- Si no aparece, aplica el **valor por defecto** de OpenSSH. `PermitRootLogin` es `prohibit-password` en versiones recientes (conviene verificar la tuya) y `PasswordAuthentication` es `yes`.
- ⚠️ En muchas distribuciones, parte de la configuración está en `/etc/ssh/sshd_config.d/*.conf`, incluidos desde el fichero principal con `Include`. Para esta fase basta leer `sshd_config`, pero **documenta esa limitación**; como reto puedes leer también esa carpeta.
- Con permisos de root, `sudo sshd -T` muestra la configuración **efectiva**, pero tu herramienta no debería exigir root.

```python
partes = linea.split(None, 1)   # separa por espacios en blanco, máximo 2 trozos
directiva, valor = partes[0].lower(), partes[1].strip().lower()
```

- `split(None, 1)` divide por cualquier espacio en blanco y hace **como mucho un corte**, de modo que el valor conserva sus espacios internos.
- Si el fichero no existe (la máquina no tiene SSH), devuelve `OMITIDO`, no error.

### 5. Puertos en escucha con `ss`

```python
import subprocess

res = subprocess.run(["ss", "-tln"], capture_output=True, text=True)
```

- `ss -tln` lista los **sockets TCP** (`-t`) en **escucha** (`-l`), con direcciones **numéricas** (`-n`), y se puede ejecutar **sin root**.
- La columna `Local Address:Port` es del estilo `0.0.0.0:22`, `127.0.0.1:631` o `[::]:22`. El puerto es lo que hay **tras el último `:`** (`direccion.rsplit(":", 1)`).
- ⚠️ Importa **a qué dirección escucha**: `127.0.0.1` solo es accesible desde el propio equipo; `0.0.0.0` y `[::]` lo son desde la red. Distingue ambos casos en el detalle del resultado.
- Si `ss` no existe, devuelve `OMITIDO`.

Define un diccionario de **puertos inseguros** con su motivo:

```python
INSEGUROS = {21: "FTP sin cifrar", 23: "Telnet sin cifrar", 69: "TFTP", 512: "rexec", 513: "rlogin"}
```

### 6. Ficheros escribibles por todos

Es el ejercicio 24 convertido en comprobación. Recomendaciones:

- Recorre con `os.walk`, ignora enlaces simbólicos y captura `PermissionError`.
- **Limita el número de resultados** mostrados (por ejemplo, los 10 primeros y "y N más").
- Algunas carpetas tienen **permisos especiales legítimos** (como `/tmp`, con el bit *sticky*). Por eso la comprobación por defecto se hace sobre `/etc`, y la carpeta se puede cambiar con `--ruta-world-writable`.

### 7. La puntuación

Define una fórmula sencilla y **documéntala**. Por ejemplo, `OK` vale 1 punto, `AVISO` 0,5 y `FALLO` 0; los `OMITIDO` no cuentan:

```python
puntuacion = (puntos_obtenidos / comprobaciones_evaluadas) * 100
```

- Cuidado con la **división entre cero** si todas están omitidas (ejercicio 02).

### 8. Convertir resultados en alertas

Los `FALLO` pueden emitirse como alertas del núcleo (ALTA) y los `AVISO` como BAJA/MEDIA, de manera que todo el sistema funcione igual en todos los módulos. Además de la **tabla de resultados**, que es el informe propio de `audit`.

---

## 📋 Requisitos

- [ ] `modules/audit.py` con las 7 comprobaciones obligatorias como funciones independientes, con parámetros de ruta por defecto.
- [ ] Una función **reutilizable** `puertos_en_escucha()` que devuelva la lista de pares `(dirección, puerto)` en escucha, sin imprimir nada: la necesitará el módulo `hids` (fase 48).
- [ ] Una lista `CHECKS` y una función `ejecutar_auditoria(...)` que las ejecuta todas, capturando **cualquier excepción inesperada** de un check y marcándolo como `OMITIDO` con el motivo (un check roto no debe tumbar la auditoría).
- [ ] Informe con estado, detalle y recomendación de cada una, y la **puntuación**.
- [ ] Argumentos: `--formato`, `--ruta-world-writable`, `--solo-fallos` (muestra solo `FALLO` y `AVISO`).
- [ ] **Códigos de salida:** `0` si no hay `FALLO`, `2` si hay alguno.
- [ ] Los `FALLO` se emiten como alertas (núcleo, fase 44).
- [ ] El módulo **no escribe ni cambia permisos de nada**, y no pide `sudo`.
- [ ] Todo funciona sin root. Si algo requeriría root (por ejemplo, leer `/etc/shadow` entero), se explica en el detalle.

---

## 💡 Pistas

- Haz primero 2 comprobaciones sencillas (`passwd` y UID 0) y la estructura de informe; añade las demás de una en una.
- **Prueba cada check con ficheros falsos** que crees tú en una carpeta temporal (`/tmp/prueba/passwd` con un usuario con UID 0 extra, un `sshd_config` con `PermitRootLogin yes`...). Así compruebas los `FALLO` sin tocar el sistema.
- Compara tus resultados con comandos manuales: `ls -l /etc/shadow`, `ss -tln`, `grep -i permitroot /etc/ssh/sshd_config`.
- Si `ss` te muestra las direcciones de una forma que no esperas, imprime la salida cruda para ver su formato antes de interpretarla.

---

## 🚀 Retos extra

1. **Más comprobaciones:** cortafuegos activo (`ufw status`, requiere root: devuélvelo `OMITIDO` si no puedes), actualizaciones pendientes, `sudo` sin contraseña en `/etc/sudoers.d`, `umask`, versión del kernel...
2. **Cuentas sin contraseña:** si se ejecuta como root, revisa `/etc/shadow` buscando contraseñas vacías.
3. **Leer `sshd_config.d/`:** procesa también los ficheros incluidos.
4. **Línea base:** guarda la puntuación en cada ejecución y avisa si **empeora** respecto a la anterior (como en el ejercicio 36).
5. **Perfiles:** un perfil `servidor` y otro `escritorio` con comprobaciones y exigencias distintas, definidos en `config.toml`.
6. **Informe HTML o Markdown** presentable, con colores o iconos por estado.
7. **Referencias:** enlaza cada comprobación con el control equivalente de la guía CIS o la documentación oficial.

---

## ▶️ Cómo comprobarlo

```bash
toolkit audit
toolkit audit --solo-fallos --formato markdown
echo $?
```

Y las pruebas con ficheros falsos en una carpeta temporal.

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| `toolkit audit` en tu VM o WSL | informe con las 7 comprobaciones sin errores ni traceback |
| `passwd` falso con un 2.º usuario UID 0 | `FALLO` en la comprobación 5 |
| `sshd_config` falso con `PermitRootLogin yes` | `FALLO` |
| `sshd_config` falso con la línea comentada (`# PermitRootLogin yes`) | no es `FALLO` |
| Equipo sin SSH instalado | comprobaciones 3 y 4 en `OMITIDO` |
| Fichero con permisos `666` creado en la carpeta auditada | aparece como `FALLO` |
| Un check que lanza una excepción (simúlala) | `OMITIDO`; el resto continúa |
| `echo $?` con algún `FALLO` | `2` |
| Comando ejecutado sin root | funciona; nunca pide `sudo` |

Al terminar: `git tag fase-46`.
