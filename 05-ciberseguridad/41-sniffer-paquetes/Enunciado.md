# Ejercicio 41 · Sniffer básico de paquetes con `scapy`

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐⭐⭐ Muy alto

---

> ## ⚠️ Aviso legal y ético
> Captura tráfico **solo** en tu propio equipo (interfaz `lo`) o en tu laboratorio (tus VMs). **Interceptar comunicaciones de otras personas, o de una red que no administras, sin autorización es un delito** (descubrimiento y revelación de secretos, acceso ilícito a comunicaciones...). En el trabajo o en prácticas, **jamás** sin permiso expreso por escrito.

---

## 🎯 Objetivo

Crea un programa que **capture paquetes en la interfaz loopback** de tu equipo mientras tú generas tráfico, y los analice:

1. Captura un número de paquetes (por ejemplo 20).
2. Para cada paquete IP, muestra **origen → destino**, el **protocolo** (TCP/UDP/otro) y los **puertos**.
3. Al final, muestra un **resumen** (cuántos paquetes TCP, UDP y otros).
4. Si el paquete lleva datos de aplicación, muestra los primeros caracteres imprimibles, para **ver con tus propios ojos** que HTTP sin cifrar viaja en texto claro.

### Preparar el tráfico de prueba

Terminal 1, servidor web local:

```bash
python3 -m http.server 8000
```

Terminal 2, tu sniffer (necesita permisos de administrador, ver abajo).

Terminal 3, genera tráfico:

```bash
curl http://127.0.0.1:8000/
```

- `curl` descarga una URL y muestra la respuesta.

### Ejemplo de ejecución

```
Capturando 20 paquetes en 'lo' (puerto 8000)...
TCP 127.0.0.1:51812 -> 127.0.0.1:8000
TCP 127.0.0.1:8000 -> 127.0.0.1:51812
TCP 127.0.0.1:51812 -> 127.0.0.1:8000   datos: GET / HTTP/1.1..Host: 127.0.0.1:8000..
...
Resumen: TCP=20, UDP=0, otros=0
```

---

## 📖 Conceptos nuevos

### 1. Contexto: ¿para qué sirve esto en defensa?

Los analistas de seguridad capturan y analizan tráfico para **detectar escaneos, malware que se comunica con servidores externos o fugas de datos**. Herramientas como **Wireshark**, **tcpdump** o **Zeek** hacen esto. Aquí construyes un mini analizador para **entender** qué hay en un paquete.

Y este ejercicio demuestra por qué **HTTP sin cifrar es inseguro** y por qué existe HTTPS: cualquiera que vea el tráfico lee el contenido.

### 2. Preparar el entorno

`scapy` se instala en un entorno virtual (ejercicio 31):

```bash
python3 -m venv venv
source venv/bin/activate
pip install scapy
sudo apt install tcpdump
```

- `scapy` es una librería para construir, enviar y analizar paquetes.
- `tcpdump` es necesario para que `scapy` pueda usar **filtros** en Linux.

### 3. Capturar paquetes requiere permisos de administrador

Capturar tráfico a bajo nivel necesita permisos de **root**. Como `sudo python3` usaría el Python del sistema (sin `scapy`), ejecuta el del entorno virtual:

```bash
sudo venv/bin/python solucion.py
```

- `venv/bin/python` es el intérprete de tu entorno virtual, que sí tiene `scapy`.
- Ejecuta con `sudo` **solo** lo necesario y revisa tu código antes: un script con privilegios elevados puede hacer mucho daño si tiene un fallo.

### 4. Capturar con `sniff()`

```python
from scapy.all import sniff

paquetes = sniff(iface="lo", count=10, timeout=30)
print(len(paquetes))
```

- `iface="lo"` indica la interfaz de **loopback** (tráfico interno de tu equipo). Para ver tus interfaces: `ip a`.
- `count=10` captura 10 paquetes y para.
- `timeout=30` se rinde a los 30 segundos aunque no haya llegado la cantidad pedida.
- Devuelve una lista de paquetes.
- Con `filter="tcp port 8000"` capturas solo ese tráfico (sintaxis **BPF**, la misma que `tcpdump`).

### 5. Procesar cada paquete en vivo: `prn=`

```python
def procesar(paquete):
    print(paquete.summary())

sniff(iface="lo", count=10, prn=procesar)
```

- `prn` recibe una **función** que se ejecuta con cada paquete capturado.
- `.summary()` da una línea con un resumen del paquete.

### 6. Paquetes como capas

Un paquete de red está formado por **capas** (modelo OSI/TCP-IP) apiladas: Ethernet → IP → TCP/UDP → datos. En `scapy` accedes a cada una con corchetes:

```python
from scapy.all import IP, TCP, UDP, Raw

if paquete.haslayer(IP):
    print(paquete[IP].src, paquete[IP].dst)

if paquete.haslayer(TCP):
    print(paquete[TCP].sport, paquete[TCP].dport)
```

- `.haslayer(X)` comprueba si el paquete tiene esa capa.
- `[IP].src` / `.dst`: IP de **origen** y de **destino**.
- `[TCP].sport` / `.dport`: puerto de **origen** y de **destino**.
- `Raw` es la capa con los **datos de aplicación** (el contenido de la petición).

### 7. Mostrar datos de forma segura

```python
if paquete.haslayer(Raw):
    datos = bytes(paquete[Raw].load)
    texto = datos[:60].decode("utf-8", errors="replace")
    print(texto.replace("\r\n", ".."))
```

- `.load` son los **bytes** de los datos.
- `[:60]` limita a los 60 primeros bytes.
- `errors="replace"` evita que falle si hay bytes que no son texto válido.
- Sustituir los saltos de línea hace que la salida quepa en una sola línea.

### 8. Contar con un diccionario

Mantén un diccionario `{"TCP": 0, "UDP": 0, "otros": 0}` fuera de la función `procesar` y actualízalo en cada paquete. Dentro de una función, para modificar una variable externa que sea un diccionario basta con `contador["TCP"] += 1`; no necesitas `global` porque no reasignas el diccionario.

---

## 💡 Pistas

- Empieza con `sniff(count=5, prn=lambda p: p.summary())` para ver qué hay.
- Primero comprueba con `tcpdump -i lo port 8000` en otra terminal que el tráfico se genera como esperas.
- Ejecuta el `curl` **después** de arrancar el sniffer (o se perderá la captura).
- Si ves un error de filtro, comprueba que tienes `tcpdump` instalado.
- Si no capturas nada, revisa que la interfaz sea `lo` y que el puerto del filtro coincida.

---

## 🚀 Retos extra

1. **Detector de escaneos:** captura mientras ejecutas tu escáner del ejercicio 29 contra `127.0.0.1` y detecta muchos paquetes **SYN** (`paquete[TCP].flags == "S"`) hacia **puertos distintos** desde la misma IP en pocos segundos. Es la firma de un escaneo.
2. **Guardar en `.pcap`:** guarda la captura con `wrpcap("captura.pcap", paquetes)` y ábrela con **Wireshark** en Windows.
3. **Leer un `.pcap`:** analiza una captura con `rdpcap("fichero.pcap")` (puedes descargar capturas de práctica públicas de sitios dedicados al análisis de tráfico).
4. **DNS:** captura consultas DNS (`filter="udp port 53"`) mientras haces un `nslookup` (ejercicio 32) y muestra el nombre consultado.
5. **Estadísticas:** muestra los puertos de destino más frecuentes y el total de bytes.
6. **Comparar HTTP y HTTPS:** razona por qué con HTTPS no verías el contenido de las peticiones (investiga TLS).
7. **Contraseñas en claro (conceptual):** investiga por qué protocolos como Telnet y FTP son inseguros, y qué reemplazan SSH y SFTP. **Haz la prueba solo con servicios tuyos en tu laboratorio.**

---

## ▶️ Cómo ejecutarlo

```bash
sudo venv/bin/python solucion.py
```

---

## ✅ Comprueba tu solución

| Prueba | Resultado esperado |
|---|---|
| `curl http://127.0.0.1:8000/` con la captura activa | paquetes TCP entre un puerto alto y el `8000`, en ambas direcciones |
| Algún paquete con datos | se ve una línea tipo `GET / HTTP/1.1` en texto claro |
| Sin generar tráfico | termina por `timeout` con resumen a cero, sin error |
| Ejecutar sin `sudo` | mensaje de permisos insuficientes, gestionado con un aviso claro |
