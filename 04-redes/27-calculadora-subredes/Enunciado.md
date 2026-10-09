# Ejercicio 27 · Calculadora de subredes

**Bloque:** 4 · Redes  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que pida una red en notación **CIDR** (por ejemplo `192.168.1.10/26`) y muestre:

- La **dirección de red**.
- La **máscara de subred**.
- La dirección de **broadcast**.
- El **primer** y el **último host** utilizable.
- El **número de hosts** utilizables.

Si el usuario escribe algo que no es una red válida, debe mostrar un aviso sin fallar.

### Ejemplo de ejecución

```
Introduce una red (ej. 192.168.1.0/24): 192.168.1.10/26
Dirección de red : 192.168.1.0
Máscara          : 255.255.255.192
Broadcast        : 192.168.1.63
Primer host      : 192.168.1.1
Último host      : 192.168.1.62
Hosts utilizables: 62
```

---

## 📖 Conceptos nuevos

### 1. Repaso: notación CIDR

`192.168.1.10/26` significa "la IP `192.168.1.10`, con una máscara de **26 bits** a 1". Esos 26 bits indican la parte de **red** y los 6 restantes la parte de **host**.

- Con 6 bits de host hay `2^6 = 64` direcciones en la subred.
- La primera es la **dirección de red** y la última es el **broadcast**, y no se pueden asignar a equipos. Quedan `64 - 2 = 62` hosts utilizables.

Hasta ahora los cálculos con IPs los hacías a mano con `split(".")`. Python incluye un módulo que lo hace todo por ti.

### 2. El módulo `ipaddress`

```python
import ipaddress

ip = ipaddress.ip_address("192.168.1.10")
print(ip)
print(ip.is_private)
```

- `ip_address()` crea un objeto IP. Si el texto no es una IP válida, lanza `ValueError`.
- Tiene propiedades útiles como `.is_private`, `.is_loopback` o `.version` (4 o 6).

### 3. Redes con `ip_network()`

```python
red = ipaddress.ip_network("192.168.1.0/24")

print(red.network_address)
print(red.netmask)
print(red.broadcast_address)
print(red.num_addresses)
print(red.prefixlen)
```

- `.network_address`: dirección de red.
- `.netmask`: máscara en formato decimal con puntos.
- `.broadcast_address`: broadcast.
- `.num_addresses`: total de direcciones (incluye red y broadcast).
- `.prefixlen`: el número tras la barra (aquí `24`).

### 4. `strict=False`: aceptar IPs que no son la dirección de red

```python
red = ipaddress.ip_network("192.168.1.10/26", strict=False)
```

- Por defecto, `ip_network("192.168.1.10/26")` da error, porque `.10` no es una dirección de red válida para ese prefijo.
- Con `strict=False` Python **calcula la red a la que pertenece** esa IP (`192.168.1.0/26`).

### 5. Recorrer los hosts utilizables

```python
hosts = list(red.hosts())
print(hosts[0])
print(hosts[-1])
print(len(hosts))
```

- `.hosts()` genera todas las IPs utilizables (sin red ni broadcast).
- Lo convertimos en lista con `list()`. Cuidado con redes grandes: una `/8` tiene más de 16 millones.
- Casos especiales: `/31` y `/32` no siguen esta regla (hacen enlaces punto a punto y hosts individuales).

### 6. Comprobar si una IP pertenece a una red

```python
print(ipaddress.ip_address("192.168.1.30") in red)
```

- El operador `in` funciona con objetos de `ipaddress`.

### 7. Capturar entradas inválidas

```python
try:
    red = ipaddress.ip_network(texto, strict=False)
except ValueError:
    print("Red no válida")
```

---

## 💡 Pistas

- Usa `strict=False` para aceptar tanto `192.168.1.0/24` como `192.168.1.10/24`.
- Todos los datos que se piden están como propiedades del objeto red.
- Para el primer y último host, convierte `.hosts()` en lista.
- Si lo prefieres, calcula los hosts utilizables como `num_addresses - 2`, y compara con `len(hosts)`.

---

## 🚀 Retos extra

1. **Dividir en subredes:** pide un nuevo prefijo (por ejemplo `/26`) y muestra las subredes en que se divide la red original (*investiga `.subnets(new_prefix=...)`*).
2. **Pertenencia:** pide además una IP y dile si pertenece a la red.
3. **Tipo de red:** indica si la red es privada (`.is_private`).
4. **Máscara a CIDR:** acepta también `192.168.1.0/255.255.255.0` y muestra su prefijo.
5. **Casos especiales:** gestiona `/31` y `/32` con un mensaje apropiado.
6. **Comparar con tu cálculo a mano:** compara con la tabla de CIDR de tus apuntes de ASIR.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Entrada | Red | Máscara | Broadcast | Hosts |
|---|---|---|---|---|
| `192.168.1.0/24` | 192.168.1.0 | 255.255.255.0 | 192.168.1.255 | 254 |
| `192.168.1.10/26` | 192.168.1.0 | 255.255.255.192 | 192.168.1.63 | 62 |
| `172.16.5.130/25` | 172.16.5.128 | 255.255.255.128 | 172.16.5.255 | 126 |
| `192.168.1.10/30` | 192.168.1.8 | 255.255.255.252 | 192.168.1.11 | 2 |
| `10.0.0.0/8` | 10.0.0.0 | 255.0.0.0 | 10.255.255.255 | 16777214 |
| `300.1.1.1/24` | *(entrada no válida)* | | | |
