# Ejercicio 37 · Extraer IPs y emails de un log con expresiones regulares

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea un programa que lea un fichero de log y extraiga con **expresiones regulares**:

1. Todas las **direcciones IPv4**.
2. Todos los **correos electrónicos**.

Debe mostrar las IPs **únicas** con cuántas veces aparecen, indicar cuáles son **válidas** (octetos 0-255) y listar los emails únicos.

### Fichero de prueba (`servidor.log`)

```
Oct  8 10:15:32 srv sshd[1]: Failed password for root from 203.0.113.5 port 22
Oct  8 10:16:01 srv app: user ana@empresa.com logged in from 192.168.1.30
Oct  8 10:17:12 srv app: password reset requested by luis.perez@ejemplo.org from 198.51.100.7
Oct  8 10:18:00 srv app: invalid address 999.12.1.4 rejected
Oct  8 10:19:00 srv mail: sent to soporte@empresa.com and ana@empresa.com
```

### Ejemplo de ejecución

```
IPs encontradas:
  203.0.113.5   (1 vez)  válida
  192.168.1.30  (1 vez)  válida
  198.51.100.7  (1 vez)  válida
  999.12.1.4    (1 vez)  NO válida

Emails únicos:
  ana@empresa.com
  luis.perez@ejemplo.org
  soporte@empresa.com
```

---

## 📖 Conceptos nuevos

### 1. ¿Qué es una expresión regular?

Una **expresión regular** (*regex*) es un **patrón** que describe un tipo de texto: "cuatro grupos de números separados por puntos", "algo con una @ en medio"... Sirve para **buscar y extraer** información en textos enormes. Es una herramienta esencial para analizar logs y para tareas de seguridad.

### 2. El módulo `re`

```python
import re

texto = "Contacta en 10.0.0.5 o en 10.0.0.9"
resultado = re.findall(r"\d+\.\d+\.\d+\.\d+", texto)
print(resultado)
```

- `re.findall(patrón, texto)` devuelve una **lista con todas las coincidencias**.
- Resultado: `['10.0.0.5', '10.0.0.9']`.
- El prefijo `r` delante de las comillas crea un **raw string**: Python no interpreta las barras invertidas. **Úsalo siempre** para regex.

### 3. Piezas básicas del patrón

| Patrón | Significa |
|---|---|
| `\d` | un dígito (0-9) |
| `\w` | una letra, dígito o `_` |
| `\s` | un espacio, tabulador o salto de línea |
| `.` | **cualquier** carácter |
| `\.` | un punto **literal** (escapado con `\`) |
| `+` | **una o más** veces lo anterior |
| `*` | cero o más veces |
| `?` | cero o una vez |
| `{1,3}` | entre 1 y 3 veces |
| `[abc]` | uno de esos caracteres |
| `[a-z]` | un carácter del rango a-z |
| `\b` | límite de palabra |
| `( )` | agrupa |
| `(?: )` | agrupa sin capturar |

⚠️ El punto `.` sin escapar significa "cualquier carácter". Para un punto de verdad, escribe `\.`.

### 4. Construir el patrón de una IP

```python
patron_ip = r"\b(?:\d{1,3}\.){3}\d{1,3}\b"
```

Léelo así:
- `\b`: inicio de palabra.
- `(?:\d{1,3}\.){3}`: **tres veces** "de 1 a 3 dígitos seguidos de un punto".
- `\d{1,3}`: el último grupo de 1 a 3 dígitos.
- `\b`: fin de palabra.

⚠️ **Este patrón también acepta `999.12.1.4`**: la regex solo comprueba el *formato*, no el rango. Después debes validar cada octeto (0-255), por ejemplo con tu función del ejercicio 14 o con `ipaddress`.

### 5. Un patrón sencillo de email

```python
patron_email = r"[\w.+-]+@[\w-]+\.[\w.-]+"
```

- `[\w.+-]+`: uno o más caracteres válidos antes de la `@`.
- `@`: la arroba literal.
- `[\w-]+`: el dominio.
- `\.[\w.-]+`: un punto y la terminación (`com`, `org`, `co.uk`...).
- Es una versión **simplificada**. La regex completa para validar emails según el estándar es enormemente compleja; para extraer de logs, esta basta.

### 6. Otras funciones de `re`

```python
re.search(r"\d+", "puerto 8080")
re.match(r"\d+", "8080 abierto")
re.sub(r"\d", "#", "ip 10.0.0.1")
```

- `re.search()` busca la **primera** coincidencia en cualquier posición y devuelve un objeto (o `None`). Se obtiene el texto con `.group()`.
- `re.match()` solo mira **al principio** del texto.
- `re.sub(patrón, reemplazo, texto)` **sustituye** coincidencias. Útil para **anonimizar** logs.

### 7. Compilar el patrón

```python
ip_re = re.compile(r"\b(?:\d{1,3}\.){3}\d{1,3}\b")
ips = ip_re.findall(linea)
```

- `re.compile()` prepara el patrón una vez y lo reutiliza: más eficiente y legible en bucles.

### 8. Contar y eliminar duplicados

Ya sabes contar con un diccionario (ejercicio 13). Para deduplicar una lista, `set(lista)` o `sorted(set(lista))`.

---

## 💡 Pistas

- Prueba tus patrones en una web como **regex101.com** (marca el sabor Python) antes de ponerlos en el código. Verás qué coincide y por qué.
- Empieza solo con las IPs, luego añade emails.
- Valida cada IP encontrada con `ipaddress.ip_address()` dentro de un `try/except ValueError`.
- Ten en cuenta que el puerto `22` de la primera línea **no** debe salir como IP: ¿por qué no coincide con el patrón?

---

## 🚀 Retos extra

1. **Anonimizar:** genera una copia del log con las IPs sustituidas por `x.x.x.x` y los emails por `[email]` (*`re.sub`*).
2. **Ranking:** muestra las IPs ordenadas de más a menos frecuentes.
3. **IPs privadas vs públicas:** clasifícalas con `ipaddress`.
4. **Grupos con nombre:** extrae fecha, proceso y mensaje de cada línea con grupos `(?P<fecha>...)`.
5. **Otros indicadores:** extrae también URLs (`https?://\S+`) y hashes de 64 caracteres hexadecimales.
6. **Aplicarlo al ejercicio 21:** reescribe el análisis del `auth.log` extrayendo la IP con regex en vez de `.split()`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Dato | Valor esperado |
|---|---|
| IPs encontradas (formato) | 4: `203.0.113.5`, `192.168.1.30`, `198.51.100.7`, `999.12.1.4` |
| IPs válidas | 3 (`999.12.1.4` no lo es) |
| Emails únicos | 3 |
| Apariciones de `ana@empresa.com` | 2 |
| El puerto `22` | no aparece como IP |
