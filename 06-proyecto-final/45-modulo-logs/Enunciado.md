# Fase 45 · Módulo `logs`: analizador de logs con alertas

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Implementa el subcomando `toolkit logs`, que lee un fichero de log de autenticación, **detecta comportamientos sospechosos** y emite alertas con el núcleo de la fase 44.

```bash
toolkit logs acceso.log
toolkit logs /var/log/auth.log --umbral 5 --ventana 60 --formato markdown
```

Debe aceptar **dos formatos de log** (detectados automáticamente) y aplicar **tres reglas de detección**:

| Regla | Descripción | Severidad |
|---|---|---|
| **Fuerza bruta** | una IP acumula ≥ `umbral` fallos dentro de `ventana` segundos | ALTA |
| **Éxito tras fallos** | una IP consigue entrar justo después de haber alcanzado el umbral de fallos | CRITICA |
| **Enumeración / *spraying*** | una IP prueba ≥ `umbral` **usuarios distintos** dentro de la ventana | MEDIA |

Las reglas son **independientes**, y debe emitirse **como máximo una alerta por IP y regla**.

### Formato 1: simple (el del ejercicio 38)

```
2026-10-08 10:15:00 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:40 SUCCESS user=root ip=203.0.113.5
```

### Formato 2: `auth.log` de SSH (el del ejercicio 21)

```
Oct  8 10:15:32 servidor sshd[1201]: Failed password for root from 203.0.113.5 port 52814 ssh2
Oct  8 10:16:01 servidor sshd[1210]: Accepted password for david from 192.168.1.20 port 40022 ssh2
```

### Fichero de prueba (`prueba.log`, formato simple)

```
2026-10-08 10:15:00 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:05 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:09 FAILED user=admin ip=203.0.113.5
2026-10-08 10:15:20 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:30 FAILED user=root ip=203.0.113.5
2026-10-08 10:15:40 SUCCESS user=root ip=203.0.113.5
2026-10-08 10:16:00 FAILED user=ana ip=192.0.2.9
2026-10-08 10:16:03 FAILED user=bob ip=192.0.2.9
2026-10-08 10:16:06 FAILED user=carlos ip=192.0.2.9
2026-10-08 10:16:09 FAILED user=dora ip=192.0.2.9
2026-10-08 10:16:12 FAILED user=eva ip=192.0.2.9
2026-10-08 10:20:00 FAILED user=david ip=198.51.100.7
2026-10-08 10:30:00 FAILED user=david ip=198.51.100.7
```

Con umbral 5 y ventana 60 s debe producir **4 alertas**:

- `203.0.113.5`: fuerza bruta (ALTA) y éxito tras fallos (CRITICA).
- `192.0.2.9`: fuerza bruta (ALTA) y *spraying* (MEDIA).
- `198.51.100.7`: ninguna (solo 2 fallos y muy separados).

---

## 📖 Conceptos nuevos

### 1. Arquitectura en tres capas: parsear → detectar → informar

El principio de diseño más importante del proyecto: **separa** estos pasos.

```
fichero ──► parsear ──► [Evento, Evento, ...] ──► detectar ──► [Alerta, ...] ──► emitir/informe
 (I/O)      (texto→datos)                       (lógica pura)
```

- `parsear_*(lineas)` convierte texto en objetos `Evento`. **No** detecta nada.
- `detectar_*(eventos, ...)` recibe eventos y devuelve alertas. **No** lee ficheros, **no** imprime. Es lógica **pura**, y por eso fácil de comprobar con tests (fase 49).
- Solo la función de arriba (`ejecutar(args)`) toca el disco y la pantalla.

### 2. Una dataclass para los eventos

```python
from dataclasses import dataclass
from datetime import datetime

@dataclass
class Evento:
    momento: datetime
    usuario: str
    ip: str
    exito: bool
```

- Un evento es "alguien intentó entrar": cuándo, con qué usuario, desde qué IP y si lo consiguió. Ambos formatos de log se convierten a este **mismo tipo**, y el resto del código ya no sabe de qué formato venía.

### 3. Dos parsers, una interfaz

```python
def parsear_simple(lineas): ...
def parsear_auth_log(lineas, anio): ...
```

- Ambos reciben las líneas y devuelven una **lista de `Evento`**, ignorando las líneas que no entienden (¡sin fallar!).
- **Detección automática:** mira la primera línea que encaje con cada patrón y elige el parser. Si ninguna encaja, error claro: "formato de log no reconocido".

### 4. El año que falta en `auth.log`

Las líneas de syslog traen `Oct  8 10:15:32` **sin año**. Hay que añadirlo para construir un `datetime`:

```python
from datetime import datetime

momento = datetime.strptime(f"{anio} Oct  8 10:15:32", "%Y %b %d %H:%M:%S")
```

- `%b` es el **nombre abreviado del mes**. Funciona con los nombres en inglés (`Oct`, `Jan`...), que es lo que escribe syslog, si tu sistema usa la configuración regional por defecto (`C`/inglés). Si en tu equipo falla, investiga cómo lo gestiona `locale`.
- Para el año, usa el actual (`datetime.now().year`) y acepta `--anio` por si analizas logs antiguos.
- Fíjate en el doble espacio de `Oct  8`: `strptime` lo admite porque un espacio en el formato **coincide con uno o más** espacios.

### 5. `defaultdict` y `Counter`

Dos herramientas del módulo `collections` que simplifican lo que hacías a mano en los ejercicios 13 y 38:

```python
from collections import defaultdict, Counter

fallos = defaultdict(list)
fallos["203.0.113.5"].append("10:15:00")

conteo = Counter(["root", "admin", "root"])
print(conteo.most_common(1))
```

- `defaultdict(list)` es un diccionario que **crea automáticamente una lista vacía** si pides una clave inexistente. Se acabó el `setdefault`.
- `Counter(iterable)` cuenta cuántas veces aparece cada elemento. `.most_common(n)` devuelve los `n` más frecuentes como pares `(elemento, veces)`.

### 6. Detección con ventana deslizante

Es la lógica del ejercicio 38. Procesa los eventos **ordenados por momento** (`sorted(eventos, key=lambda e: e.momento)`: los logs pueden no venir ordenados). Para cada IP, mantén los fallos recientes y descarta los que superen la ventana.

- **Éxito tras fallos:** cuando llega un evento con `exito=True`, mira cuántos fallos recientes tiene **esa IP** en la ventana. Si alcanzan el umbral, alerta CRITICA.
- **Spraying:** además de los momentos, guarda el **usuario** de cada fallo. Dentro de la ventana, cuenta cuántos usuarios distintos hay con `len(set(...))`.

### 7. Una alerta por IP y regla

Mantén un conjunto de pares ya alertados:

```python
ya_alertado = set()
clave = (ip, "fuerza_bruta")
if clave not in ya_alertado:
    ya_alertado.add(clave)
    ...
```

- Las tuplas se pueden guardar en conjuntos (los diccionarios y las listas, no).

### 8. Datos de la alerta

Rellena `detalles` con datos útiles para investigar: `ip`, `usuarios`, `num_fallos`, `primer_fallo`, `ultimo_fallo`. El mensaje debe ser claro y breve: `"Posible fuerza bruta desde 203.0.113.5: 5 fallos en 30 s"`.

### 9. Leer ficheros grandes

Procesa el fichero **línea a línea** (`for linea in f`), sin `.read()` entero (ejercicio 19), ya que un `auth.log` real puede pesar cientos de MB.

---

## 📋 Requisitos

- [ ] `modules/logs.py` con: `Evento`, `parsear_simple`, `parsear_auth_log`, `detectar_formato`, `detectar_fuerza_bruta`, `detectar_exito_tras_fallos`, `detectar_spraying` y `ejecutar(args)`.
- [ ] Una función `analizar(eventos, umbral, ventana)` que une las tres reglas y devuelve las alertas.
- [ ] Argumentos: `fichero`, `--umbral`, `--ventana`, `--anio`, tomando por defecto los valores de `config.toml`, sobrescribibles por la línea de comandos.
- [ ] Todas las alertas pasan por `emitir()` y el resultado final por `generar_informe()` con el formato elegido.
- [ ] Al final, un **resumen**: líneas leídas, eventos reconocidos, líneas ignoradas, alertas por severidad, y las **3 IPs con más fallos** (`Counter`).
- [ ] **Código de salida:** `0` si no hay alertas, `1` si hay alguna ALTA o CRITICA... y documenta tu criterio en el README.
- [ ] Fichero inexistente, sin permisos o formato desconocido → error claro, sin traceback.
- [ ] Ninguna de las funciones `detectar_*` lee ficheros ni imprime.

---

## 💡 Pistas

- Empieza solo con el formato simple y la regla de fuerza bruta (es el ejercicio 38). Después añade las demás, una por una, comprobando contra `prueba.log`.
- Crea tu propio fichero de pruebas con casos límite: exactamente `umbral - 1` fallos, fallos justo en el límite de la ventana, líneas basura, líneas vacías.
- Si el resultado no cuadra, depura con `logger.debug` (activa `-v`) en lugar de `print`.
- Calcula la ventana con `(evento.momento - antiguo).total_seconds()`.
- Para la prueba del segundo formato, reutiliza tu `auth.log` del ejercicio 21.

---

## 🚀 Retos extra

1. **Usuarios inexistentes:** detecta intentos contra usuarios que no existen (`invalid user` en `auth.log`) y emite una alerta aparte.
2. **Horario inusual:** alerta de inicios de sesión correctos fuera de horario laboral (configurable).
3. **IPs de confianza:** una lista blanca en `config.toml` cuyas alertas se ignoran (por ejemplo, tu red interna).
4. **Contexto de la IP:** indica si la IP es privada o pública (`ipaddress`) y resuélvele el nombre inverso (ejercicio 32), con tiempo límite.
5. **Seguimiento en vivo:** `--seguir`, como `tail -f` (investiga `f.seek(0, 2)` y `time.sleep`).
6. **Sugerencia de bloqueo:** en las alertas de fuerza bruta, **muestra** (sin ejecutar nunca) el comando de cortafuegos recomendado.
7. **Más formatos:** un parser para logs de acceso web (Apache/Nginx) detectando muchos `401/403` seguidos.

---

## ▶️ Cómo comprobarlo

```bash
toolkit logs prueba.log
toolkit logs prueba.log --umbral 3 --formato json
toolkit logs /fichero/que/no/existe.log
```

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| `toolkit logs prueba.log` | exactamente 4 alertas (2 para `203.0.113.5`, 2 para `192.0.2.9`) |
| `198.51.100.7` | sin alertas |
| `--umbral 6` | ninguna alerta (ninguna IP llega a 6 fallos ni a 6 usuarios distintos) |
| Líneas basura mezcladas en el log | se ignoran y se cuentan como "ignoradas" |
| Log en formato `auth.log` | se detecta solo y produce las mismas conclusiones |
| Fichero sin formato reconocible | error claro |
| `echo $?` tras alertas ALTA/CRITICA | `1` |
| Las alertas aparecen en `~/.toolkit/alertas.jsonl` | sí |

Al terminar: `git tag fase-45`.
