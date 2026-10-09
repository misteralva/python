# Ejercicio 28 · Barrido de ping

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

> ## ⚠️ Aviso legal y ético
> Haz el barrido **solo** sobre tu propio equipo (`127.0.0.1`), tus máquinas virtuales o redes que administres y de las que tengas **autorización expresa**. Escanear redes ajenas, de una empresa o de un cliente sin permiso es ilegal en muchos países y puede acarrear sanciones o consecuencias laborales, aunque solo sea un ping.

---

## 🎯 Objetivo

Crea un programa que haga un **barrido de ping** (*ping sweep*) sobre un rango de IPs y muestre qué equipos responden.

1. Pide una red en CIDR (por ejemplo `127.0.0.0/30` o la de tu laboratorio).
2. Envía **un ping** a cada host de la red.
3. Muestra los equipos que **responden** y un resumen.

### Ejemplo de ejecución

```
Red a barrer: 192.168.56.0/29
192.168.56.1  -> responde
192.168.56.2  -> sin respuesta
192.168.56.3  -> sin respuesta
192.168.56.4  -> responde
...
Hosts activos: 2 de 6
```

---

## 📖 Conceptos nuevos

### 1. ¿Qué es un ping?

`ping` envía un paquete **ICMP echo request** a un equipo. Si el equipo está encendido, accesible y no lo bloquea un cortafuegos, responde con un *echo reply*. Un ping **sin respuesta no prueba** que el equipo esté apagado: puede estar filtrando ICMP.

### 2. El comando `ping` en Linux

```bash
ping -c 1 -W 1 127.0.0.1
```

- `-c 1`: enviar **solo 1** paquete (sin esto, `ping` en Linux nunca termina).
- `-W 1`: esperar como máximo **1 segundo** la respuesta.
- Si no tienes `ping`, instálalo con `sudo apt install iputils-ping`.
- El comando termina con **código de salida `0` si hubo respuesta** y distinto de 0 si no.

### 3. Ejecutarlo desde Python y mirar el código de salida

```python
import subprocess

resultado = subprocess.run(
    ["ping", "-c", "1", "-W", "1", "127.0.0.1"],
    stdout=subprocess.DEVNULL,
    stderr=subprocess.DEVNULL,
)

print(resultado.returncode)
```

- Repasa el ejercicio 25: la lista de argumentos y `returncode`.
- `stdout=subprocess.DEVNULL` **descarta** la salida del comando en vez de mostrarla o guardarla, y es lo que queremos aquí porque solo nos importa si respondió.
- `returncode == 0` significa que el host respondió.

### 4. Generar las IPs del rango

```python
import ipaddress

red = ipaddress.ip_network("192.168.56.0/29")
for host in red.hosts():
    print(host)
```

- Del ejercicio 27: `.hosts()` da cada IP utilizable de la red.
- El objeto `host` no es un texto: conviértelo con `str(host)` para pasárselo a `ping`.

### 5. Una función para un único host

```python
def responde(ip):
    ...
    return True  # o False
```

Separa en una función "¿responde esta IP?" y úsala en el bucle. El código queda mucho más claro y la reutilizarás en el siguiente ejercicio.

### 6. El tiempo importa

Si cada ping sin respuesta tarda 1 segundo, una red `/24` con 254 hosts tardaría **hasta 4 minutos**. Por eso conviene probar con redes pequeñas (`/29`, `/28`) mientras desarrollas.

---

## 💡 Pistas

- Prueba primero con `127.0.0.0/30`: `127.0.0.1` siempre debe responder.
- Imprime cada resultado en cuanto lo tengas, para ver que avanza.
- Cuenta cuántos hosts responden en una variable.
- Si usas WSL, tu programa puede no ver todas las máquinas de VirtualBox. Comprueba antes con `ping` a mano desde la terminal.

---

## 🚀 Retos extra

1. **Medir el tiempo:** muestra cuánto ha tardado el barrido completo (`time.perf_counter()`).
2. **En paralelo:** hazlo mucho más rápido lanzando varios pings a la vez (*investiga `concurrent.futures.ThreadPoolExecutor`*).
3. **Guardar el resultado:** escribe los hosts activos en un fichero con la fecha (reutiliza el ejercicio 20).
4. **Validación:** rechaza redes demasiado grandes (por ejemplo, más de 1024 hosts) con un aviso.
5. **Latencia:** captura la salida de `ping` y extrae el tiempo de respuesta en ms.
6. **Cuidado con el exceso:** discute por qué un barrido masivo podría saltar alarmas en un IDS/IPS.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Red | Resultado esperado |
|---|---|
| `127.0.0.0/30` | `127.0.0.1` responde, `127.0.0.2` también responde (todo `127.x.x.x` es de tu propio equipo) |
| tu red de laboratorio | responden tus VMs encendidas y el router/gateway |
| `abc/24` | aviso de red no válida, sin fallar |
