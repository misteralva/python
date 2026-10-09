# Ejercicio 33 · Ejecutar comandos por SSH con `paramiko`

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

> ## ⚠️ Aviso legal y ético
> Conéctate **solo** a máquinas **tuyas** o de tu laboratorio (por ejemplo, una VM de VirtualBox) o a sistemas que administres con autorización. Automatizar accesos SSH a equipos ajenos, probar contraseñas o usar credenciales que no son tuyas es ilegal. **Jamás** uses credenciales del trabajo o de un cliente para pruebas de aprendizaje.

---

## 🎯 Objetivo

Crea un programa que se **conecte por SSH a una máquina de tu laboratorio** y ejecute una serie de comandos, mostrando su salida:

1. Pide host, usuario y contraseña (la contraseña **sin mostrarla** al escribir).
2. Se conecta por SSH.
3. Ejecuta estos comandos: `hostname`, `uptime -p` y `df -h /`.
4. Muestra el resultado de cada uno y cierra la conexión.
5. Gestiona los errores más habituales: sin conexión, credenciales incorrectas y tiempo agotado.

### Preparar tu laboratorio

En la **VM de destino** (Ubuntu/Debian):

```bash
sudo apt install openssh-server
sudo systemctl status ssh
ip a
```

- `openssh-server` instala el servicio SSH.
- `systemctl status ssh` comprueba que está activo.
- `ip a` muestra la IP de la VM. Prueba antes a mano desde tu terminal: `ssh usuario@IP_DE_LA_VM`.
- Si la VM usa una red NAT, es posible que necesites una red *host-only* o puente para llegar a ella.

### Ejemplo de ejecución

```
Host: 192.168.56.101
Usuario: david
Contraseña: 
Conectado a 192.168.56.101

$ hostname
servidor-lab

$ uptime -p
up 3 hours, 12 minutes

$ df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G  5.1G   14G  27% /
```

---

## 📖 Conceptos nuevos

### 1. SSH y `paramiko`

**SSH** (*Secure Shell*) permite administrar un equipo remoto de forma cifrada. **Paramiko** es una librería de Python que implementa SSH y permite automatizarlo. Instálala en un entorno virtual (ejercicio 31):

```bash
python3 -m venv venv
source venv/bin/activate
pip install paramiko
```

### 2. Pedir la contraseña sin mostrarla

```python
import getpass

clave = getpass.getpass("Contraseña: ")
```

- `getpass` no muestra lo que escribes.
- **Nunca** escribas contraseñas directamente en el código, ni las subas a GitHub.

### 3. Conectar con `SSHClient`

```python
import paramiko

cliente = paramiko.SSHClient()
cliente.set_missing_host_key_policy(paramiko.AutoAddPolicy())
cliente.connect(hostname="192.168.56.101", port=22, username="david", password=clave, timeout=5)
```

- `SSHClient()` crea el cliente.
- `connect()` abre la conexión. `timeout=5` evita esperar indefinidamente.

### 4. ⚠️ La política de claves de host (importante en seguridad)

La primera vez que te conectas a un servidor SSH, este te presenta su **clave de host**, que sirve para comprobar que **realmente** hablas con ese servidor y no con un impostor (ataque *man-in-the-middle*).

- `AutoAddPolicy()` **acepta cualquier clave sin comprobarla**. Es cómodo para un laboratorio, pero **inseguro en producción**.
- En entornos reales se cargan las claves conocidas con `cliente.load_system_host_keys()` y se rechazan los servidores desconocidos (`RejectPolicy`).
- Para este ejercicio de laboratorio puedes usar `AutoAddPolicy`, pero entiende por qué no es seguro.

### 5. Ejecutar un comando remoto

```python
entrada, salida, errores = cliente.exec_command("hostname")

texto = salida.read().decode("utf-8")
estado = salida.channel.recv_exit_status()
print(texto, estado)
```

- `exec_command()` ejecuta el comando en el equipo remoto y devuelve tres "canales": entrada, salida normal y errores.
- `.read()` devuelve **bytes**, que decodificas a texto (como en el ejercicio 30).
- `recv_exit_status()` devuelve el **código de salida** remoto (`0` = éxito), igual que `returncode` en el ejercicio 25.
- `errores.read()` te da los mensajes de error del comando.

### 6. Cerrar siempre la conexión

```python
try:
    ...
finally:
    cliente.close()
```

- `finally` se ejecuta **siempre**, haya error o no. Es ideal para cerrar recursos.

### 7. Excepciones habituales

| Excepción | Cuándo ocurre |
|---|---|
| `paramiko.AuthenticationException` | usuario o contraseña incorrectos |
| `paramiko.SSHException` | problemas del protocolo SSH |
| `TimeoutError` / `OSError` | no se alcanza el equipo o el puerto 22 está cerrado |

Captura primero las más específicas.

---

## 💡 Pistas

- Antes de programar, comprueba que `ssh usuario@IP` funciona a mano. Así descartas problemas de red.
- Guarda los comandos en una **lista** y recórrela con un `for`.
- Crea una función `ejecutar_remoto(cliente, comando)` que devuelva salida, errores y código.
- Si el puerto 22 aparece cerrado, usa tu escáner del ejercicio 29 sobre **tu VM** para comprobarlo.

---

## 🚀 Retos extra

1. **Autenticación por clave:** crea un par de claves (`ssh-keygen`) y conéctate con `key_filename=` en vez de contraseña. Es la forma recomendada en la vida real.
2. **Varios servidores:** recorre los dispositivos del inventario del ejercicio 26 (los que sean tus VMs) y ejecuta los comandos en cada uno.
3. **Informe:** guarda los resultados en un fichero con fecha.
4. **Alerta de disco:** extrae el porcentaje de uso de `df` y avisa si supera el 80 %.
5. **Claves de host seguras:** cambia `AutoAddPolicy` por `RejectPolicy` con `load_system_host_keys()` y prueba qué pasa.
6. **Subir/bajar ficheros:** investiga `cliente.open_sftp()` y copia un fichero de prueba a la VM.

---

## ▶️ Cómo ejecutarlo

Con el entorno virtual activado:

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Prueba | Resultado esperado |
|---|---|
| Datos correctos de tu VM | salida de los 3 comandos |
| Contraseña incorrecta | mensaje de autenticación fallida, sin traceback |
| IP que no existe | mensaje de tiempo agotado / sin conexión |
| Compara con ejecutar los mismos comandos con `ssh` a mano | misma salida |
