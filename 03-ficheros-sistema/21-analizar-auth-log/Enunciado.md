# Ejercicio 21 · Analizar un `auth.log` simulado

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que lea un fichero de log de autenticación SSH y muestre:

1. Cuántos **inicios de sesión fallidos** hay en total.
2. Cuántos **inicios de sesión correctos** hay.
3. Los **fallos agrupados por IP**, de mayor a menor.
4. Qué IPs son **sospechosas** (3 o más fallos).

### Fichero de prueba

Crea `auth.log` en la carpeta del ejercicio con este contenido (son IPs de documentación, no son reales):

```
Oct  8 10:15:32 servidor sshd[1201]: Failed password for root from 203.0.113.5 port 52814 ssh2
Oct  8 10:15:35 servidor sshd[1201]: Failed password for root from 203.0.113.5 port 52816 ssh2
Oct  8 10:16:01 servidor sshd[1210]: Accepted password for david from 192.168.1.20 port 40022 ssh2
Oct  8 10:17:12 servidor sshd[1215]: Failed password for invalid user admin from 198.51.100.7 port 41000 ssh2
Oct  8 10:17:15 servidor sshd[1215]: Failed password for invalid user admin from 198.51.100.7 port 41002 ssh2
Oct  8 10:17:20 servidor sshd[1215]: Failed password for invalid user test from 198.51.100.7 port 41004 ssh2
Oct  8 10:18:44 servidor sshd[1222]: Failed password for root from 203.0.113.5 port 52900 ssh2
Oct  8 10:19:02 servidor sshd[1230]: Accepted password for ana from 192.168.1.30 port 40050 ssh2
Oct  8 10:20:10 servidor sshd[1240]: Failed password for root from 203.0.113.5 port 52950 ssh2
Oct  8 10:21:00 servidor sshd[1250]: Failed password for david from 192.168.1.20 port 40100 ssh2
```

### Ejemplo de salida

```
Intentos fallidos: 8
Inicios correctos: 2

Fallos por IP:
203.0.113.5: 4  ⚠️ SOSPECHOSA
198.51.100.7: 3  ⚠️ SOSPECHOSA
192.168.1.20: 1
```

---

## 📖 Conceptos nuevos

### 1. Anatomía de una línea de log

```
Oct  8 10:15:32 servidor sshd[1201]: Failed password for root from 203.0.113.5 port 52814 ssh2
```

- Cada línea tiene fecha, nombre del equipo, el proceso (`sshd`) y el mensaje.
- Lo que nos interesa: si contiene `Failed password` o `Accepted password`, y la **IP**, que aparece justo **después de la palabra `from`**.
- En un sistema Linux real, estos registros están en `/var/log/auth.log` (Debian/Ubuntu) y solo puede leerlos root. Aquí trabajamos con una copia de prueba.

### 2. Buscar texto dentro de una línea

```python
linea = "Failed password for root from 10.0.0.5"

if "Failed password" in linea:
    print("Es un fallo")
```

- El operador `in` también sirve para buscar un fragmento dentro de un texto.
- Distingue mayúsculas y minúsculas.

### 3. Extraer un dato por su posición: `.split()` e `.index()`

```python
palabras = "el servidor esta en 10.0.0.5 ahora".split()
posicion = palabras.index("en")
print(palabras[posicion + 1])
```

- `.split()` divide la línea en una lista de palabras.
- `.index("en")` devuelve la **posición** de esa palabra en la lista.
- `palabras[posicion + 1]` es la palabra siguiente. Resultado: `10.0.0.5`.
- Aplicado al log, la IP es la palabra que viene después de `"from"`.

### 4. Combinar ficheros y diccionarios

Es la unión de lo que ya sabes:

- Del ejercicio 19: recorrer un fichero línea a línea.
- Del ejercicio 13 y 15: el diccionario como contador y el umbral de sospechosos.

### 5. Líneas con formato diferente

Fíjate en que algunos fallos dicen `for invalid user admin from ...` y otros `for root from ...`. Como la IP siempre va tras `from`, tu método funciona en ambos casos. Esa es la ventaja de **no depender de posiciones fijas**.

---

## 💡 Pistas

- Esquema: abre el fichero, y por cada línea decide si es un fallo, un acierto o ninguna de las dos.
- Para cada fallo: extrae la IP y suma 1 en el diccionario.
- Separa el programa en fases: leer y contar, y luego mostrar resultados.
- Si te lías, empieza solo contando fallos totales y ve añadiendo cosas.

---

## 🚀 Retos extra

1. **Usuarios atacados:** cuenta también los fallos por usuario (`root`, `admin`, `test`...).
2. **Usuarios inválidos:** cuenta cuántos intentos fueron contra usuarios que no existen (`invalid user`).
3. **Exportar informe:** guarda el resultado en un fichero `informe.txt` con la fecha del análisis.
4. **Ruta por argumento:** acepta la ruta del log desde la línea de comandos con `sys.argv`.
5. **Expresiones regulares:** cuando llegues al bloque 5, repite el ejercicio extrayendo la IP con el módulo `re`.
6. **Log real:** si tienes una VM propia con SSH, prueba tu programa con **su** `auth.log` (con `sudo`) y comprueba qué ves.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

Con el `auth.log` del enunciado:

| Dato | Valor esperado |
|---|---|
| Fallos totales | 8 |
| Aciertos | 2 |
| `203.0.113.5` | 4 fallos (sospechosa) |
| `198.51.100.7` | 3 fallos (sospechosa) |
| `192.168.1.20` | 1 fallo |
