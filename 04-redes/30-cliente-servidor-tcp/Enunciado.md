# Ejercicio 30 · Cliente y servidor TCP (eco)

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea **dos programas** que se comuniquen por TCP en tu propio equipo:

- **Servidor** (`solucion_servidor.py`): escucha en `127.0.0.1:5000`, acepta una conexión y **devuelve en mayúsculas** cada mensaje que recibe. Termina cuando el cliente se desconecta.
- **Cliente** (`solucion_cliente.py`): se conecta al servidor, envía mensajes que escribe el usuario y muestra la respuesta. Termina cuando el usuario escribe `salir`.

### Ejemplo de ejecución

Terminal 1:

```
$ python3 solucion_servidor.py
Servidor escuchando en 127.0.0.1:5000
Conexión de ('127.0.0.1', 51234)
Recibido: hola
Recibido: red
Cliente desconectado.
```

Terminal 2:

```
$ python3 solucion_cliente.py
Mensaje (salir para terminar): hola
Respuesta: HOLA
Mensaje (salir para terminar): red
Respuesta: RED
Mensaje (salir para terminar): salir
```

---

## 📖 Conceptos nuevos

### 1. Modelo cliente-servidor

- El **servidor** se queda **esperando** conexiones en una IP y un puerto.
- El **cliente** inicia la conexión hacia esa IP y puerto.
- Arrancas **primero** el servidor y **después** el cliente, cada uno en su terminal.

### 2. Pasos del servidor

```python
import socket

servidor = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
servidor.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
servidor.bind(("127.0.0.1", 5000))
servidor.listen()

conexion, direccion = servidor.accept()
```

- `bind((ip, puerto))`: **reserva** esa IP y puerto para este programa.
- `listen()`: empieza a escuchar conexiones.
- `accept()`: **se queda bloqueado** hasta que llega un cliente. Devuelve una **nueva conexión** (`conexion`) para hablar con ese cliente y su dirección `(ip, puerto)`.
- `SO_REUSEADDR` permite reiniciar el servidor sin esperar a que el sistema libere el puerto. Sin esto verás `Address already in use` al relanzarlo.

### 3. Pasos del cliente

```python
cliente = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
cliente.connect(("127.0.0.1", 5000))
```

- `connect()` establece la conexión. Si el servidor no está arrancado, lanza `ConnectionRefusedError`.

### 4. Enviar y recibir: `send()` y `recv()`

```python
cliente.send("hola".encode("utf-8"))
datos = cliente.recv(1024)
print(datos.decode("utf-8"))
```

- Los sockets envían **bytes**, no texto. Hay que convertir:
  - `.encode("utf-8")` convierte **texto → bytes** al enviar.
  - `.decode("utf-8")` convierte **bytes → texto** al recibir.
- `recv(1024)` recibe **hasta 1024 bytes** y también **se bloquea** hasta que llegan datos.
- Cuando el otro lado cierra la conexión, `recv()` devuelve **bytes vacíos** (`b""`). Es la señal de "terminé".

### 5. Recibir en bucle

```python
while True:
    datos = conexion.recv(1024)
    if not datos:
        break
    texto = datos.decode("utf-8")
    conexion.send(texto.upper().encode("utf-8"))
```

- `if not datos` es verdadero cuando el cliente se ha desconectado.
- Fíjate en el encadenado: se recibe bytes, se pasa a texto, se transforma y se vuelve a pasar a bytes.

### 6. Cerrar los recursos

Usa `with` (como en los ejercicios 19 y 29) para que los sockets se cierren siempre: `with socket.socket(...) as servidor:` y también `with conexion:`.

### 7. Seguridad: ¿en qué IP escuchar?

- `127.0.0.1` solo acepta conexiones **del propio equipo**. Es lo seguro para practicar.
- `0.0.0.0` escucha en **todas las interfaces**: cualquiera en tu red podría conectarse. No lo uses sin saber por qué, y nunca expongas un servicio de pruebas sin autenticación.

---

## 💡 Pistas

- Escribe y prueba primero **el servidor**; puedes comprobarlo sin tu cliente con `nc 127.0.0.1 5000` (si tienes `netcat`).
- Si cambias algo y al relanzar ves `Address already in use`, revisa que el servidor antiguo no sigue vivo (`Ctrl + C`) y que pusiste `SO_REUSEADDR`.
- El cliente debe controlar el caso de que el servidor no esté activo con `try/except ConnectionRefusedError`.
- Imprime lo que pasa en el servidor: te ayuda a entender el orden de los eventos.

---

## 🚀 Retos extra

1. **Varios clientes:** haz que el servidor atienda a varios clientes a la vez (*investiga `threading.Thread`*).
2. **Registro:** guarda cada conexión (IP, puerto, hora) en un fichero de log.
3. **Mini chat:** que servidor y cliente puedan enviarse mensajes por turnos.
4. **Comandos:** el servidor entiende `HORA` (devuelve la hora), `ECO texto` y `SALIR`.
5. **Mensajes largos:** investiga por qué `recv(1024)` puede no recibir un mensaje completo y cómo se resuelve (delimitadores o longitud previa).
6. **Mirar la conexión:** con el servidor activo, ejecuta `ss -tln` en otra terminal y localiza tu puerto 5000 en estado `LISTEN`.
7. **Reutiliza el escáner:** ejecuta el ejercicio 29 sobre `127.0.0.1` con el servidor activo y comprueba que detecta el puerto 5000.

---

## ▶️ Cómo ejecutarlo

En dos terminales distintas, en este orden:

```bash
python3 solucion_servidor.py
```

```bash
python3 solucion_cliente.py
```

---

## ✅ Comprueba tu solución

- Cliente envía `hola` → recibe `HOLA`.
- El servidor muestra la conexión y los mensajes recibidos.
- Cliente escribe `salir` → ambos programas terminan sin errores.
- Cliente lanzado con el servidor apagado → mensaje claro, sin traceback.
- Relanzar el servidor justo después de cerrarlo → arranca sin `Address already in use`.
