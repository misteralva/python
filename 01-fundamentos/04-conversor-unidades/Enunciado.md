# Ejercicio 04 · Conversor de unidades

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐ Básico

---

## 🎯 Objetivo

Crea un programa que pida una cantidad en **bytes** y la muestre convertida a **KB**, **MB** y **GB**, con 2 decimales.

Usa la equivalencia **binaria**: 1 KB = 1024 bytes, 1 MB = 1024 KB, 1 GB = 1024 MB.

### Ejemplo de ejecución

```
Introduce una cantidad en bytes: 5368709120
5368709120.0 bytes son:
5242880.00 KB
5120.00 MB
5.00 GB
```

> La salida puede variar en el formato. Lo importante es que los valores sean correctos y tengan 2 decimales.

---

## 📖 Conceptos nuevos

### 1. Potencias con `**`

```python
print(2 ** 10)
print(1024 ** 2)
```

- `2 ** 10` es 2 elevado a 10, es decir `1024`.
- `1024 ** 2` da `1048576`, que es el número de bytes que hay en 1 MB.
- Calcular las potencias con `**` es más claro que escribir números enormes a mano.

### 2. Constantes (por convención, en MAYÚSCULAS)

```python
IVA = 0.21
precio = 100
print(precio * IVA)
```

- Python no tiene constantes reales, pero por convención los valores que **no deben cambiar** se escriben en mayúsculas.
- Ayuda a evitar números "mágicos" repetidos por todo el código. Si el valor cambia, solo lo modificas en un sitio.

### 3. Formatear decimales en un f-string

```python
pi = 3.14159265
print(f"{pi:.2f}")
```

- Después del nombre de la variable, `:.2f` significa "mostrar como decimal (`f`) con 2 cifras después del punto (`.2`)".
- El resultado es `3.14`. Redondea, pero **solo para mostrar**; la variable sigue valiendo lo mismo.
- Puedes cambiar el `2` por otro número para más o menos decimales.

### 4. Bytes, KB, MB, GB: ¿1000 o 1024?

- En informática, el sistema operativo y la memoria usan **1024** (potencias de 2).
- Los fabricantes de discos suelen usar **1000** (potencias de 10), por eso un disco de "500 GB" muestra menos en tu sistema.
- Para este ejercicio usa 1024.

---

## 💡 Pistas

- Puedes dividir los bytes entre `1024`, entre `1024 ** 2` y entre `1024 ** 3`.
- Pide la cantidad con `float()` para admitir decimales.
- Define las constantes `KB`, `MB` y `GB` al principio del programa.

---

## 🚀 Retos extra

1. **Sentido inverso:** pide GB y conviértelos a bytes, MB y KB.
2. **Menú de unidades:** pregunta al usuario en qué unidad introduce el dato (bytes, KB, MB o GB) y conviértelo a las demás.
3. **Unidad más legible:** muestra automáticamente el resultado en la unidad más adecuada (por ejemplo, `1.5 GB` en vez de `1610612736 bytes`).
4. **Velocidad de red:** convierte entre Mbps (megabits por segundo) y MB/s. Pista: 1 byte = 8 bits.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Bytes | KB | MB | GB |
|---|---|---|---|
| 2048 | 2.00 | 0.00 | 0.00 |
| 1048576 | 1024.00 | 1.00 | 0.00 |
| 1073741824 | 1048576.00 | 1024.00 | 1.00 |
| 5368709120 | 5242880.00 | 5120.00 | 5.00 |
