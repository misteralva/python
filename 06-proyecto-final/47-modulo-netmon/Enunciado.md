# Fase 47 · Módulo `netmon`: monitor de red con línea base

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐⭐⭐ Muy alto

---

> ## ⚠️ Aviso legal y ético
> Este módulo **hace escaneos**. Úsalo **solo** sobre tu propio equipo (`127.0.0.0/24`), tus máquinas virtuales o redes que administres con **autorización expresa por escrito**. Por diseño, la herramienta **se negará a escanear redes públicas**. No intentes saltarte esa protección.

---

## 🎯 Objetivo

Implementa `toolkit netmon`, que **descubre los equipos activos y sus puertos abiertos** en una red privada, guarda una **línea base** y, en ejecuciones posteriores, **avisa de los cambios**.

```bash
toolkit netmon baseline 192.168.56.0/28 --puertos 22,80,443,8000
toolkit netmon check    192.168.56.0/28 --puertos 22,80,443,8000
```

Dos subcomandos:

| Subcomando | Qué hace |
|---|---|
| `baseline RED` | escanea y **guarda** el estado actual como referencia |
| `check RED` | escanea, **compara** con la línea base y emite alertas |

### Alertas de `check`

| Cambio | Severidad |
|---|---|
| **Host nuevo** que no estaba en la línea base | ALTA |
| **Puerto nuevo abierto** en un host conocido | ALTA |
| **Host desaparecido** (estaba y ya no responde) | BAJA |
| **Puerto cerrado** que antes estaba abierto | BAJA |

### Ejemplo de uso

```
$ toolkit netmon baseline 127.0.0.0/30 --puertos 8000,8001
Escaneando 2 hosts, 2 puertos cada uno...
Línea base guardada: 2 hosts activos, 1 puerto abierto.

(...ahora lanzas python3 -m http.server 8001 en otra terminal...)

$ toolkit netmon check 127.0.0.0/30 --puertos 8000,8001
🚨 ALTA  Puerto nuevo abierto en 127.0.0.1: 8001
Resumen: 1 cambio(s).
```

---

## 📖 Conceptos nuevos

### 1. Seguro por diseño: rechazar redes públicas

```python
import ipaddress

red = ipaddress.ip_network("192.168.56.0/28")
print(red.is_private)
```

- `.is_private` es `True` para los rangos privados (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`), el *loopback* (`127.0.0.0/8`) y algunos otros reservados.
- **Si la red no es privada, aborta** con un error claro. Una herramienta de seguridad bien diseñada **impide el mal uso por accidente**.
- Además, **limita el tamaño**: rechaza redes de más de 256 hosts (por ejemplo, prefijo menor que `/24`) salvo con un argumento explícito, para no lanzar escaneos enormes por error.

### 2. Escanear en paralelo con `ThreadPoolExecutor`

En los ejercicios 28 y 29 escaneabas **uno por uno**. Esperar a que cada puerto responda o se agote el tiempo es tiempo perdido: el programa pasa casi todo el rato **esperando a la red** (tareas *I/O-bound*). Los **hilos** permiten esperar a muchos a la vez.

```python
from concurrent.futures import ThreadPoolExecutor

def tarea(n):
    return n * n

with ThreadPoolExecutor(max_workers=20) as pool:
    resultados = list(pool.map(tarea, range(5)))

print(resultados)
```

- `ThreadPoolExecutor(max_workers=20)` crea un grupo de hasta 20 hilos.
- `pool.map(funcion, iterable)` ejecuta la función sobre cada elemento **en paralelo** y devuelve los resultados **en el mismo orden**.
- `with` cierra el grupo al terminar.
- **No pongas un número enorme de hilos**: demasiados escaneos simultáneos pueden saturar la red, o saltar alarmas (un IDS/IPS puede detectarte y bloquearte). Hazlo configurable (`--hilos`, por defecto 20-50).
- Para esperas de red, los hilos funcionan bien en Python (no así para cálculos pesados).

### 3. Diseño de las funciones

```
descubrir_hosts(red)        → lista de IPs que responden al ping
escanear_puertos(ip, ptos)  → lista de puertos abiertos
escanear_red(red, ptos)     → {ip: {"puertos": [...], "nombre": "..."}}
comparar(base, actual)      → lista de Alerta
```

- `escanear_*` hacen la parte lenta (red) y reutilizan **tu código de los ejercicios 28, 29 y 32**. Importa tus funciones; **no copies y pegues**.
- `comparar` es **lógica pura**: recibe dos diccionarios y devuelve alertas. Es fácil de testear en la fase 49.

### 4. El formato de la línea base

Un JSON con el estado de cada host:

```json
{
  "red": "192.168.56.0/28",
  "fecha": "2026-10-12T10:15:30+02:00",
  "puertos_escaneados": [22, 80, 443, 8000],
  "hosts": {
    "192.168.56.1": {"nombre": "gateway.local", "puertos": [80]},
    "192.168.56.101": {"nombre": "", "puertos": [22]}
  }
}
```

- Guárdala en `~/.toolkit/netmon/` con un nombre que dependa de la red (por ejemplo, `192.168.56.0_28.json`: sustituye `/` por `_`, porque no puede ir en un nombre de fichero).
- Guarda **la lista de puertos escaneados** y compara solo contra esos: si en `check` usas otros puertos, avisa de que **no son comparables**.
- Las claves JSON siempre son texto: los puertos van como **lista de enteros**, que sí se conserva como número.

### 5. Comparar con conjuntos

Es lo del ejercicio 36 aplicado a la red:

```python
hosts_base = set(base["hosts"])
hosts_ahora = set(actual["hosts"])

nuevos = hosts_ahora - hosts_base
desaparecidos = hosts_base - hosts_ahora
comunes = hosts_base & hosts_ahora
```

Para cada host común, compara `set(puertos_antes)` con `set(puertos_ahora)` y saca los puertos nuevos y los cerrados.

### 6. Resolver nombres con cuidado

La resolución inversa (ejercicio 32) puede tardar o fallar. Hazla **dentro del escaneo en paralelo**, con captura de `socket.herror`/`socket.gaierror`, y que un fallo no tumbe el escaneo.

### 7. Falsos positivos

Un host que un día no responde a ping no está necesariamente caído (puede estar ocupado o filtrando ICMP). Por eso **"host desaparecido" es solo BAJA**. Documenta esta limitación en el README.

---

## 📋 Requisitos

- [ ] `modules/netmon.py` con `descubrir_hosts`, `escanear_puertos`, `escanear_red`, `comparar` y `ejecutar(args)`.
- [ ] Subcomandos `baseline` y `check`, con argumentos `red`, `--puertos` (rango o lista, reutiliza `parsear_puertos` del ejercicio 34), `--hilos`, `--timeout`.
- [ ] **Rechaza redes no privadas** y redes demasiado grandes, con mensajes claros.
- [ ] Escaneo **en paralelo** con `ThreadPoolExecutor`; muestra cuánto ha tardado.
- [ ] Línea base guardada como JSON en `~/.toolkit/netmon/` con permisos restrictivos.
- [ ] `check` sin línea base previa → error claro que sugiere ejecutar `baseline`.
- [ ] Las 4 alertas descritas, emitidas con el núcleo.
- [ ] **Código de salida:** `0` sin cambios, `1` con cambios.
- [ ] Ctrl+C durante el escaneo termina limpiamente, sin traceback.
- [ ] La herramienta **no envía nada** salvo ping y conexiones TCP simples a los puertos indicados. No hace ninguna otra prueba.

---

## 💡 Pistas

- Desarrolla por capas: primero `escanear_puertos` en un solo host, luego `descubrir_hosts`, luego el paralelismo, luego la línea base y al final `comparar`.
- Prueba **siempre en `127.0.0.0/30`** (`127.0.0.1` y `127.0.0.2` responden) con servidores propios: `python3 -m http.server 8000` y `python3 -m http.server 8001`.
- Para simular un "host nuevo" sin una segunda máquina, edita a mano el JSON de la línea base y **quita** un host: `check` lo verá como nuevo.
- Mide el tiempo con `time.perf_counter()`. Con 2 hilos y con 50 hilos, ¿cuánto cambia?
- Si usas VirtualBox, verifica antes **a mano** con `ping` que WSL llega a tus VMs (puede requerir red *host-only* o puente).

---

## 🚀 Retos extra

1. **Huella de servicios:** guarda también el *banner* que el servicio devuelve al conectar (con tiempo límite) y avisa si cambia (¡un servicio cambiado puede significar una actualización o una intrusión!).
2. **Puertos "peligrosos":** sube la severidad a CRITICA si el puerto nuevo es de la lista de inseguros (21, 23, 3389...).
3. **Lista de equipos autorizados:** en `config.toml`, una lista de hosts esperados; alerta de cualquiera que no esté en ella, aunque estuviera en la línea base.
4. **Aceptar cambios:** `netmon accept RED` actualiza la línea base tras revisarla.
5. **Historial:** guarda cada escaneo con fecha y muestra la evolución de un host.
6. **Informe de la red:** una tabla con IP, nombre, puertos abiertos y servicios habituales (ejercicio 12).
7. **Ejecución programada:** investiga `cron` para lanzar `check` cada hora y revisar `alertas.jsonl`.

---

## ▶️ Cómo comprobarlo

```bash
python3 -m http.server 8000 &
toolkit netmon baseline 127.0.0.0/30 --puertos 8000,8001
toolkit netmon check 127.0.0.0/30 --puertos 8000,8001
```

- El `&` lanza el servidor en segundo plano. Recuerda pararlo después (`kill %1` o `jobs` + `fg` y `Ctrl+C`).

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| `baseline` en `127.0.0.0/30` con `http.server 8000` activo | 2 hosts, puerto 8000 abierto en ellos |
| `check` sin cambios | sin alertas, código de salida `0` |
| Arrancas otro servidor en el 8001 y `check` | alerta ALTA de puerto nuevo |
| Paras el servidor del 8000 y `check` | alerta BAJA de puerto cerrado |
| Quitas un host del JSON y `check` | alerta ALTA de host nuevo |
| `toolkit netmon check 8.8.8.0/24` | **rechazado** por no ser red privada |
| `toolkit netmon check 10.0.0.0/8` | **rechazado** por ser demasiado grande |
| `check` sin línea base | error claro |
| Ctrl+C a mitad | salida limpia |
| Con 50 hilos | claramente más rápido que con 1 |

Al terminar: `git tag fase-47`.
