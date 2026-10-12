# Ejercicio 38 · Detector de fuerza bruta con alertas

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐⭐⭐ Muy alto

---

## 🎯 Objetivo

Crea un detector que analice un log de autenticación y genere una **alerta** cuando una misma IP acumule **5 o más fallos de login en una ventana de 60 segundos**.

- Las alertas se muestran por pantalla y se guardan en `alertas.log`.
- **No** debe alertar por IPs que acumulan fallos pero **repartidos en mucho tiempo** (eso no es fuerza bruta rápida).

### Fichero de prueba (`acceso.log`)

```
2026-10-08 10:15:00 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:05 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:09 FAILED user=admin ip=203.0.113.5
2026-10-08 10:15:10 FAILED user=david ip=198.51.100.7
2026-10-08 10:15:20 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:30 FAILED user=root ip=203.0.113.5
2026-10-08 10:16:00 FAILED user=david ip=192.168.1.20
2026-10-08 10:16:20 SUCCESS user=david ip=192.168.1.20
2026-10-08 10:17:10 FAILED user=david ip=198.51.100.7
2026-10-08 10:19:10 FAILED user=david ip=198.51.100.7
2026-10-08 10:21:10 FAILED user=david ip=198.51.100.7
2026-10-08 10:23:10 FAILED user=david ip=198.51.100.7
```

### Ejemplo de salida

```
🚨 ALERTA: 203.0.113.5 ha tenido 5 fallos en 30 s (último a las 2026-10-08 10:15:30)
Análisis terminado: 12 líneas, 1 alerta.
```

---

## 📖 Conceptos nuevos

### 1. Por qué la ventana de tiempo importa

Contar fallos totales (como en el ejercicio 21) genera **falsos positivos**: un usuario que se equivoca 5 veces **a lo largo de un año** no es un ataque. Un atacante que prueba contraseñas lo hace **muy rápido**. Detectar "muchos fallos en poco tiempo" se llama aplicar una **ventana deslizante** (*sliding window*) y es la lógica base de herramientas como `fail2ban`.

### 2. Convertir texto en fecha: `strptime`

```python
from datetime import datetime

momento = datetime.strptime("2026-10-08 10:15:30", "%Y-%m-%d %H:%M:%S")
print(momento)
```

- `strptime(texto, formato)` convierte un **texto** en un objeto `datetime` (el inverso de `strftime`, que lo convierte en texto).
- Usa los mismos códigos: `%Y`, `%m`, `%d`, `%H`, `%M`, `%S`.

### 3. Restar fechas: `timedelta`

```python
a = datetime.strptime("2026-10-08 10:15:00", "%Y-%m-%d %H:%M:%S")
b = datetime.strptime("2026-10-08 10:15:45", "%Y-%m-%d %H:%M:%S")

diferencia = b - a
print(diferencia.total_seconds())
```

- Restar dos `datetime` da un `timedelta`.
- `.total_seconds()` lo convierte en un número de segundos (aquí, `45.0`).
- También puedes crear una duración: `timedelta(seconds=60)`.

### 4. Extraer campos con regex

```python
import re

patron = re.compile(
    r"(?P<fecha>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}) (?P<estado>FAILED|SUCCESS) user=(?P<usuario>\S+) ip=(?P<ip>\S+)"
)
m = patron.match(linea)
if m:
    print(m.group("fecha"), m.group("ip"))
```

- `(?P<nombre>...)` crea un **grupo con nombre** que luego recuperas con `.group("nombre")`.
- `FAILED|SUCCESS` significa "una u otra" (`|` es "o").
- `\S+` es "uno o más caracteres que no son espacio".
- `.match()` devuelve `None` si la línea no encaja: **siempre comprueba** antes de usar el resultado.

### 5. Un diccionario de listas

```python
fallos = {}
fallos.setdefault("203.0.113.5", []).append(momento)
```

- Guardas, **por cada IP, la lista de momentos** de sus fallos.
- `.setdefault(clave, valor)` devuelve la lista existente o crea una vacía si no hay: una forma corta de no repetir un `if`.

### 6. Algoritmo de la ventana deslizante

Para cada nuevo fallo de una IP:

1. Añade su hora a la lista de esa IP.
2. **Descarta** de la lista las horas con más de 60 segundos de antigüedad respecto al fallo actual.
3. Si la lista tiene **5 o más** elementos → **alerta**.

```python
ventana = [t for t in lista if (momento - t).total_seconds() <= 60]
```

- Una comprensión de listas (ejercicio 18) que se queda solo con los fallos recientes.

### 7. Evitar alertas repetidas

Si una IP hace 50 intentos seguidos, no quieres 46 alertas. Decide una regla simple: por ejemplo, **una alerta por IP** y la ignoras después, o vuelves a alertar solo tras vaciar su lista.

### 8. Constantes de configuración

```python
UMBRAL = 5
VENTANA_SEGUNDOS = 60
```

- Colócalas al principio del programa para ajustarlas fácilmente.

---

## 💡 Pistas

- Procesa el fichero **en orden** (está ordenado por hora).
- Ignora líneas `SUCCESS` en el contador (pero ¿qué podrías hacer con ellas? Mira los retos).
- Las líneas que no coincidan con tu regex deben ignorarse sin romper el programa.
- Cuando generes la alerta, escribe también en `alertas.log` con la hora del último fallo.
- Prueba la lógica con tu fichero de ejemplo y luego con otros que crees tú.

---

## 🚀 Retos extra

1. **Parámetros por argumento:** umbral, ventana y fichero desde la línea de comandos (ejercicio 34).
2. **Éxito tras fallos:** detecta el caso más peligroso: una IP con **muchos fallos seguidos de un `SUCCESS`**, señal de que el atacante acertó.
3. **Varias IPs coordinadas:** detecta muchos fallos contra **el mismo usuario** desde distintas IPs (*password spraying*).
4. **Sugerir el bloqueo:** al alertar, **muestra** (sin ejecutarlo) el comando de cortafuegos que bloquearía la IP, por ejemplo `sudo ufw deny from IP`. Un script que bloquea solo puede dejarte fuera de tu propio servidor.
5. **Modo "en vivo":** en vez de leer el fichero entero, sigue el final del fichero como `tail -f` (*investiga `f.seek(0, 2)` y `time.sleep`*).
6. **Lista blanca:** ignora IPs de confianza (tu red interna).
7. **Informe final:** muestra las 3 IPs con más fallos totales aunque no hayan alertado.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| IP | Fallos | Ventana | ¿Alerta? |
|---|---|---|---|
| `203.0.113.5` | 5 | todos en 30 s | ✅ Sí |
| `198.51.100.7` | 5 | repartidos en ~8 min | ❌ No |
| `192.168.1.20` | 1 | — | ❌ No |

Con el fichero de prueba debe salir **exactamente 1 alerta**, y `alertas.log` debe contener esa línea.
