# Fase 48 · Módulo `hids`: un mini sistema de detección de intrusiones en el equipo

**Bloque:** 6 · Proyecto final  
**Dificultad:** ⭐⭐⭐⭐⭐ Muy alto

---

## 🎯 Objetivo

Implementa `toolkit hids`, que **vigila un equipo** combinando tres fuentes de información y centralizando todo en el sistema de alertas:

| Fuente | Qué vigila | Reutiliza |
|---|---|---|
| **Integridad de ficheros** | ficheros y carpetas importantes: cambios, altas y bajas | ejercicio 36 |
| **Puertos en escucha** | servicios nuevos que empiezan a escuchar | fase 46 (`ss`) y fase 47 |
| **Logs de autenticación** | fuerza bruta y otros ataques | fase 45 (`logs`) |

(HIDS = *Host-based Intrusion Detection System*; herramientas reales: **OSSEC/Wazuh**, **AIDE**, **Tripwire**.)

Subcomandos:

```bash
toolkit hids init        # crea la línea base (ficheros y puertos)
toolkit hids check       # una comprobación única
toolkit hids watch       # comprobaciones periódicas hasta Ctrl+C
```

La **configuración** viene de `config.toml`:

```toml
[hids]
rutas = ["/etc/ssh", "~/vigilada"]
logs = ["~/prueba.log"]
intervalo = 60
excluir = ["*.log", "__pycache__"]
```

---

## 📖 Conceptos nuevos

### 1. Composición: construir sobre lo que ya tienes

Esta fase **casi no tiene lógica nueva**: su trabajo es **integrar** módulos que ya existen. Importa las funciones puras de las fases anteriores:

```python
from toolkit.modules.logs import parsear_simple, analizar
from toolkit.modules.audit import puertos_en_escucha
```

- Si al intentarlo notas que **no puedes reutilizar** una función porque está mezclada con `print` o con lectura de ficheros, **refactoriza** la fase anterior para separarla. Eso es ingeniería de software real: las pruebas de que la arquitectura estaba bien (o mal) llegan ahora.
- Lo que sea común a varios módulos (por ejemplo, calcular el hash de un fichero) va al núcleo o a un módulo de utilidades compartido, no se duplica.

### 2. Línea base de ficheros (repaso del ejercicio 36)

- `rutas` pueden ser **ficheros o carpetas**: si es una carpeta, recórrela con `rglob`.
- Guarda `{ruta: {"hash": ..., "permisos": ..., "propietario": ...}}`: así detectas también cambios de **permisos**, no solo de contenido.
- Aplica las **exclusiones** con patrones tipo `*.log`: investiga `fnmatch.fnmatch(nombre, patron)`.
- Gestiona los ficheros ilegibles (`PermissionError`): no los ignores en silencio, regístralos con `logger.warning`.

### 3. Línea base de puertos

Un conjunto de puertos en escucha, guardado junto al de ficheros. Compárala con `set`: un puerto nuevo escuchando es sospechoso (alerta ALTA); uno que desaparece, informativo.

### 4. Estado persistente en `~/.toolkit/hids/`

```
~/.toolkit/hids/
├── baseline_ficheros.json
└── baseline_puertos.json
```

- Crea la carpeta si no existe, con permisos restrictivos.
- **Protege la línea base:** si un atacante puede editarla, oculta sus cambios. Calcula el SHA-256 del propio fichero de línea base y guárdalo aparte, y verifícalo antes de usarla (reto relacionado en el ejercicio 36). Avisa si ha sido alterada.

### 5. Evitar alertas repetidas en `watch`

En modo continuo, si un fichero sigue modificado, no quieres la **misma alerta cada 60 segundos**. Guarda una **huella** de cada alerta ya emitida y solo emite las nuevas:

```python
huellas = set()

huella = (alerta.fuente, alerta.mensaje)
if huella not in huellas:
    huellas.add(huella)
    emitir(alerta)
```

- Los elementos de un `set` deben ser inmutables: una tupla de textos sirve.
- Si el problema desaparece y reaparece, decide qué quieres que pase (¿volver a alertar?) y documéntalo.

### 6. Bucle con salida limpia

```python
import time

try:
    while True:
        comprobar()
        time.sleep(intervalo)
except KeyboardInterrupt:
    print("\nVigilancia detenida.")
```

- `time.sleep(n)` pausa `n` segundos.
- `Ctrl+C` lanza `KeyboardInterrupt`. Al capturarla, terminas **sin traceback** y puedes liberar recursos.
- Para poder probar con rapidez, permite `--intervalo 5` por línea de comandos (sobrescribe el de la config).
- Un fallo en **una** comprobación (por ejemplo, un fichero que desaparece justo mientras lo lees) **no debe matar el bucle**: captura excepciones por ciclo y regístralas.

### 7. Qué incluir en cada alerta

| Hallazgo | Severidad | Detalles útiles |
|---|---|---|
| Fichero **modificado** | ALTA | ruta, hash antes y después |
| Fichero **nuevo** | MEDIA | ruta, propietario |
| Fichero **eliminado** | ALTA | ruta |
| Cambio de **permisos** | ALTA | permisos antes y después |
| Puerto **nuevo** en escucha | ALTA | puerto, dirección |
| Alertas del módulo de **logs** | las que ya traen | — |
| Línea base **manipulada** | CRITICA | fichero afectado |

Una alerta sobre un fichero **crítico** (por ejemplo, `/etc/passwd` o `authorized_keys`) puede subir de severidad: haz una lista configurable en `config.toml`.

---

## 📋 Requisitos

- [ ] `modules/hids.py` con `ejecutar(args)` y subcomandos `init`, `check` y `watch`.
- [ ] Lee la configuración de `[hids]` en `config.toml`, con valores por defecto y validación.
- [ ] **Reutiliza** funciones de los módulos `logs`, `audit` y el hash del núcleo/utilidades, **sin copiar código**.
- [ ] Línea base de ficheros (hash, permisos, propietario) y de puertos en `~/.toolkit/hids/`, con permisos restrictivos.
- [ ] `check` detecta: modificados, nuevos, eliminados, cambios de permisos y puertos nuevos; y analiza los logs configurados.
- [ ] `watch` repite cada `intervalo` segundos, **sin duplicar alertas**, y termina limpio con Ctrl+C.
- [ ] Verifica la integridad de las propias líneas base antes de usarlas.
- [ ] Todo pasa por `emitir()` y el informe final usa `generar_informe()`.
- [ ] **Códigos de salida:** `0` sin hallazgos, `1` con alertas ALTA/CRITICA.
- [ ] El módulo **solo lee y compara**: no repara, no borra, no bloquea.

---

## 💡 Pistas

- Primero haz **solo el apartado de ficheros** (con `init` y `check`), que es una evolución del 36. Después puertos, después logs, después `watch`.
- Si `check` es una función que **devuelve** alertas y `watch` solo la llama en bucle, `watch` es casi trivial.
- Prueba con carpetas y logs **de prueba**: `~/vigilada` y `~/prueba.log`; no con `/etc` de entrada.
- Para simular un ataque, en otra terminal: modifica un fichero, abre un puerto con `python3 -m http.server 9000`, añade líneas de fuerza bruta al log de prueba (`echo "..." >> ~/prueba.log`).
- Si la salida es demasiado ruidosa, revisa las exclusiones y la deduplicación.

---

## 🚀 Retos extra

1. **Ficheros críticos:** severidad CRITICA configurable para rutas concretas.
2. **Procesos:** vigila procesos nuevos (lista de procesos con `ps`) respecto a la línea base.
3. **Usuarios y claves SSH:** alerta si aparece un usuario nuevo en `/etc/passwd` o cambia algún `authorized_keys`.
4. **Informe diario:** un resumen con las alertas de las últimas 24 h leyendo `alertas.jsonl`.
5. **Aceptar cambios:** `hids accept` regenera la línea base tras revisar los cambios legítimos.
6. **Como servicio:** investiga cómo ejecutar `watch` como servicio `systemd` (usuario sin privilegios).
7. **Notificaciones:** diseña (aunque no lo implementes) cómo enviar un aviso por correo o webhook con el sistema de alertas.

---

## ▶️ Cómo comprobarlo

```bash
toolkit hids init
toolkit hids check
toolkit hids watch --intervalo 5
```

Mientras `watch` corre, en otra terminal: `echo x >> ~/vigilada/a.txt`, `touch ~/vigilada/nuevo.txt`, `python3 -m http.server 9000`.

---

## ✅ Criterios de aceptación

| Prueba | Resultado esperado |
|---|---|
| `check` justo tras `init` | sin alertas |
| Modificas un fichero vigilado | alerta ALTA de modificación |
| Añades un fichero | alerta MEDIA de fichero nuevo |
| Borras un fichero | alerta ALTA de eliminado |
| `chmod 777` a un fichero vigilado | alerta ALTA de cambio de permisos |
| Abres un puerto nuevo | alerta ALTA de puerto nuevo |
| Añades líneas de fuerza bruta al log vigilado | alerta del módulo `logs` |
| `watch` con un cambio que persiste | **una sola** alerta, no una por ciclo |
| Editas a mano `baseline_ficheros.json` | alerta CRITICA de línea base manipulada |
| Ctrl+C en `watch` | termina limpio |
| Un fichero que desaparece durante el ciclo | el bucle sigue funcionando |

Al terminar: `git tag fase-48`.
