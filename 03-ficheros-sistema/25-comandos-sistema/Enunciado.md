# Ejercicio 25 · Ejecutar comandos del sistema

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que ejecute comandos de Linux desde Python y genere un **mini informe del sistema** con:

- El **usuario** actual (`whoami`).
- El **tiempo encendido** del equipo (`uptime -p`).
- El **espacio en disco** de la raíz (`df -h /`).
- La **memoria** (`free -h`).

El informe se muestra por pantalla y además se guarda en `informe.txt`.

### Ejemplo de salida

```
===== INFORME DEL SISTEMA =====
Usuario: david
Encendido: up 2 hours, 15 minutes

Disco:
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        251G   12G  227G   5% /

Memoria:
               total        used        free ...
Mem:           7.7Gi       1.2Gi       5.9Gi ...
```

---

## 📖 Conceptos nuevos

### 1. Ejecutar un comando con `subprocess.run`

```python
import subprocess

resultado = subprocess.run(["echo", "hola"], capture_output=True, text=True)
print(resultado.stdout)
```

- `subprocess` permite lanzar programas del sistema desde Python.
- El comando se pasa como **lista**: el primer elemento es el programa y los siguientes son sus argumentos. `df -h /` se escribe `["df", "-h", "/"]`.
- `capture_output=True` captura la salida en lugar de mostrarla directamente.
- `text=True` convierte la salida a texto (si no, llega como bytes).
- `resultado.stdout` contiene lo que el comando ha escrito normalmente.

### 2. Qué devuelve `run()`

```python
resultado = subprocess.run(["ls", "/noexiste"], capture_output=True, text=True)

print(resultado.returncode)
print(resultado.stderr)
```

- `.stdout`: salida normal.
- `.stderr`: mensajes de error.
- `.returncode`: **código de salida**. `0` significa éxito; cualquier otro valor indica error.
- Un programa de automatización siempre debe mirar el `returncode`.

### 3. Comandos que no existen

```python
try:
    subprocess.run(["comandoinventado"], capture_output=True, text=True)
except FileNotFoundError:
    print("Ese comando no existe")
```

- Si el programa no está instalado, Python lanza `FileNotFoundError`.

### 4. Seguridad: no uses `shell=True` con datos del usuario

```python
# ❌ PELIGROSO
subprocess.run("ls " + carpeta_del_usuario, shell=True)

# ✅ SEGURO
subprocess.run(["ls", carpeta_del_usuario])
```

- Con `shell=True` el texto se interpreta como una orden de la terminal. Si el usuario escribe `; rm -rf ~`, se ejecutaría.
- Eso se llama **inyección de comandos** y es una vulnerabilidad real y muy conocida.
- Pasando una **lista**, cada elemento se trata como un argumento y no como código.

### 5. Limpiar la salida

```python
usuario = resultado.stdout.strip()
```

- La salida de los comandos acaba en salto de línea. `.strip()` lo elimina.

### 6. Funciones reutilizables

Tendrás varios comandos casi idénticos, así que conviene crear una función como `ejecutar(comando)` que haga el `run()`, controle el error y devuelva el texto. Aplica lo aprendido en los ejercicios 14 y 17.

---

## 💡 Pistas

- Primero consigue que un solo comando funcione (`whoami`) y generalízalo en una función.
- Cada comando en su propia lista: `["uptime", "-p"]`, `["df", "-h", "/"]`, `["free", "-h"]`.
- Guarda el informe como un único texto (o lista de líneas) y escríbelo al final con `with open(...)`.
- Maneja el caso de que un comando falle o no exista, sin que se rompa todo el informe.

---

## 🚀 Retos extra

1. **Alerta de disco:** analiza la salida de `df` y avisa si el uso supera el 80 %.
2. **Más datos:** añade el nombre del equipo (`hostname`) y la versión del kernel (`uname -r`).
3. **Procesos:** muestra los 5 procesos que más CPU consumen (`ps aux --sort=-%cpu`, y recorta la salida).
4. **Fecha en el informe:** incluye fecha y hora de generación y usa un nombre de fichero con fecha.
5. **Ping previo:** como adelanto del bloque de redes, ejecuta `ping -c 1 127.0.0.1` y comprueba el `returncode`.
6. **Tiempo límite:** investiga el parámetro `timeout=` de `subprocess.run`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- El informe aparece por pantalla y se crea `informe.txt` con el mismo contenido.
- Compara cada sección con ejecutar a mano `whoami`, `uptime -p`, `df -h /` y `free -h` en la terminal: deben coincidir.
- Si cambias temporalmente un comando por uno inexistente, el programa muestra un aviso y continúa con el resto.
