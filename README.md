# Análisis del Mercado Inmobiliario Español (2008–2022)

## Descripción General
Proyecto de análisis del mercado inmobiliario español desde 2008 hasta 2022, basado en datos reales de **coste de vivienda**, **salarios**, **población** e **hipotecas**.  
El objetivo es medir y comparar la **asequibilidad de la vivienda** y la **demanda potencial hipotecaria** por comunidad autónoma.

El análisis combina SQL para la modelización relacional y Python (pandas + matplotlib) para la exploración visual y narrativa de resultados.

---

## Estructura del Proyecto

### 1. **Modelado SQL**
Se construyó un esquema relacional en MySQL llamado `vivienda`, compuesto por las tablas:

| Tabla | Campos principales | Descripción |
|--------|--------------------|-------------|
| `coste_vivienda` | comunidad, anio, coste_medio | Precio medio €/m² |
| `salarios` | comunidad, anio, salario_mediana | Salario anual mediano |
| `hipotecas` | comunidad, anio, n_hipotecas | Nº de hipotecas concedidas |
| `censo` | comunidad, anio, poblacion | Población por comunidad |
| `ipc` | comunidad, anio, ipc | Índice de precios al consumo |
| `comunidad_anio` | comunidad, anio | Tabla puente de referencia |

---

## Principales Consultas SQL

### 🔹 Índice de Asequibilidad
> Relación entre el salario mediano anual y el precio medio €/m².

```sql
SELECT 
    cv.comunidad,
    cv.anio,
    ROUND(s.salario_mediana / NULLIF(cv.coste_medio, 0), 2) AS indice_asequibilidad
FROM coste_vivienda AS cv
JOIN salarios AS s USING (comunidad, anio)
ORDER BY cv.comunidad, cv.anio;
```

### 🔹 Índice de Demanda Potencial
> Relación entre la población y el número de hipotecas.

```sql
SELECT 
    h.comunidad,
    h.anio,
    ROUND(c.poblacion / NULLIF(h.n_hipotecas, 0), 2) AS indice_demanda_potencial
FROM hipotecas AS h
JOIN censo AS c USING (comunidad, anio)
ORDER BY h.comunidad, h.anio;
```

Ambos índices se calcularon anualmente y se compararon entre 2008 y 2022 para observar su evolución.

---

## Visualizaciones en Python

Librerías utilizadas:
```python
import pandas as pd
import matplotlib.pyplot as plt
```

### 1. Evolución nacional del índice de asequibilidad
```python
df_ase = pd.read_csv("...asequibilidad.csv")
esp_ase = df_ase.groupby("anio", as_index=False)["indice_asequibilidad"].mean()
plt.plot(esp_ase["anio"], esp_ase["indice_asequibilidad"], marker="o")
```

### 2. Evolución nacional del índice de demanda potencial
```python
df_dem = pd.read_csv("...demanda.csv")
esp_dem = df_dem.groupby("anio", as_index=False)["indice_demanda_potencial"].mean()
plt.plot(esp_dem["anio"], esp_dem["indice_demanda_potencial"], color="firebrick")
```

### 3. Comparativas por comunidades
Líneas múltiples con especial foco en:
**Madrid**, **Islas Baleares**, **Catalunya**, **Andalucía** y **Comunitat Valenciana**, las más representativas del mercado.

---

## Interpretación de Indicadores

| Indicador | Fórmula | Significado |
|------------|----------|-------------|
| **Asequibilidad** | salario / coste vivienda | M² que puede comprar un salario anual (↑ mejor) |
| **Demanda potencial** | población / hipotecas | Habitantes por hipoteca (↓ mejor, indica mayor demanda) |

---

## Insights Clave (2008–2022)

1️⃣ **Vivienda más cara, salarios planos** → asequibilidad se desploma; se compran menos m² por salario.  
2️⃣ **Menos hipotecas por habitante** → demanda hipotecaria aún muy por debajo de 2008.  
3️⃣ **Brecha territorial** → Madrid, Baleares y Cataluña disparadas; interior estancado.  
4️⃣ **Cambio de perfil** → menos compra residencial, más inversión y alquiler.

---

## Conclusión General

El mercado inmobiliario español muestra un **desequilibrio estructural**:  
los precios crecen más rápido que los salarios, la demanda hipotecaria sigue contenida y el acceso a la vivienda se ha polarizado territorialmente.  
Mientras unas regiones viven tensiones de precios e inversión, otras se estancan o pierden población activa.

---

## Archivos del Proyecto

| Archivo | Descripción |
|----------|-------------|
| `coste_vivienda.xls` | Precio medio €/m² por comunidad y año |
| `salarios.xls` | Salario mediano anual |
| `hipotecas.xls` | Hipotecas concedidas |
| `censo.xls` | Población anual |
| `% variacion anual ind. asequibilidad por comunidades.csv` | Evolución anual del índice de asequibilidad |
| `% variacion anual ind. demanda por comunidades.csv` | Evolución anual del índice de demanda potencial |

---

## Autor
**Elías Chafih**  
https://github.com/eliasch-bot

**Ignacio Sabatell**  
https://github.com/ignaciosabatell

Proyecto desarrollado en Python y SQL (MySQL Workbench + VS Code).
