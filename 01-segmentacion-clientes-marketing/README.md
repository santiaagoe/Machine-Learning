# 01 · Segmentación de clientes para marketing basado en datos

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/santiaagoe/Machine-Learning/blob/main/01-segmentacion-clientes-marketing/segmentacion_clientes.ipynb)

Segmentación de la base de clientes de una cadena de retail para identificar qué perfil
concentra el consumo de vino, y construcción de un clasificador que permita etiquetar
clientes nuevos sin necesidad de conocer su historial de compra completo.

> Proyecto del Módulo Interdisciplinario, Universidad de Chile · Diciembre 2024

---

## Contexto y pregunta

Una empresa de retail quiere dirigir sus campañas de vino al segmento correcto en vez de
enviarlas a toda la base. El desafío tiene dos partes:

1. **¿Existe un segmento de clientes claramente asociado al consumo de vino?** (no supervisado)
2. **¿Se puede predecir si un cliente nuevo pertenece a ese segmento?** (supervisado)

---

## Datos

**Fuente:** [Customer Personality Analysis](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis) (Kaggle) · 2.240 registros × 29 variables

| Grupo | Variables |
|-------|-----------|
| Demográficas | `Year_Birth`, `Education`, `Marital_Status`, `Income`, `Kidhome`, `Teenhome` |
| Gasto (2 años) | `MntWines`, `MntFruits`, `MntMeatProducts`, `MntFishProducts`, `MntSweetProducts`, `MntGoldProds` |
| Canales | `NumWebPurchases`, `NumCatalogPurchases`, `NumStorePurchases`, `NumDealsPurchases`, `NumWebVisitsMonth` |
| Campañas | `AcceptedCmp1–5`, `Response`, `Complain` |

---

## Metodología

**1. Análisis exploratorio.** Dispersión de gasto en vino contra ingreso, año de nacimiento
y gasto en otras categorías. Se detectan outliers extremos en ingreso y edad.

**2. Limpieza y transformación.**

- Eliminación de identificadores no predictivos (`ID`, `Dt_Customer`)
- Imputación: se descartan los 24 registros sin `Income` (mapa de calor de nulos para verificar)
- Filtros de outliers: `Income < 100.000`, `Year_Birth > 1940`, `MntMeatProducts < 1500`
- Ingeniería de variables: `Edades` (a partir del año de nacimiento) y `N_hijos` (suma de `Kidhome` + `Teenhome`)
- Codificación de `Education` y `Marital_Status` a variables numéricas ordinales

**3. Segmentación con K-Means.** El método del codo sobre k = 1…10 indica que la curva de
inercia se aplana en **k = 6**. Se define como segmento objetivo el clúster cuyo centroide
tiene el mayor valor en `MntWines`.

**4. Clasificación con KNN.** Con la pertenencia al segmento objetivo como etiqueta binaria
(`Compra Vino`), se entrena un K-Nearest Neighbors con split 70/30. El número de vecinos se
elige por curva de tasa de error sobre k = 1…19.

**5. Interpretación.** Radar charts del segmento objetivo según estado civil, nivel
educativo, número de hijos y gasto en otras categorías.

---

## Resultados

### El segmento de vino es un perfil claramente diferenciado

De los 6 clústeres, el segmento objetivo (≈15% de la base) presenta:

| Característica | Segmento vino | Segmento de menor gasto |
|---|---|---|
| Ingreso promedio | $81.975 | $18.409 |
| Gasto en vino (2 años) | $676 | $11 |
| Gasto en carne | $478 | $15 |
| Hijos en el hogar | 0,31 | 0,88 |
| Compras por catálogo | 5,97 | 0,41 |
| Visitas web / mes | 2,70 | 7,31 |

**El hallazgo de negocio más útil es el último**: el segmento de mayor valor es el que
*menos* visita el sitio web (2,7 visitas mensuales frente a 7,3 del segmento de bajo gasto),
pero el que más compra por catálogo y en tienda. Una campaña de vino concentrada en canales
digitales estaría apuntando justamente donde este cliente no está.

El perfil se complementa con edad promedio de ~56 años, hogar sin hijos, y gasto elevado
también en carne — lo que sugiere oportunidades de venta cruzada.

### El clasificador KNN alcanza precisión perfecta — y ese es el problema

La matriz de confusión sobre el conjunto de prueba (658 registros) da 557 y 101 aciertos,
0 errores. Ver la sección siguiente.

---

## Limitaciones y trabajo futuro

Documentar esto es parte del proyecto: un modelo con 100% de accuracy casi nunca es un
buen modelo.

**1. Fuga de información en el KNN.** La etiqueta `Compra Vino` se deriva directamente de la
columna `cluster`, y esa columna permanece dentro de la matriz de features `X`. El
clasificador no está aprendiendo el perfil del cliente: está leyendo la respuesta. De ahí el
100% de accuracy. *Corrección:* eliminar `cluster` (y `MntWines`, que también determina la
etiqueta por construcción) de `X` antes de entrenar.

**2. K-Means sin escalamiento de variables.** `Income` se mueve en decenas de miles mientras
las variables de conteo están entre 0 y 10, de modo que la distancia euclidiana queda
dominada casi por completo por el ingreso. Esto explica la agrupación vertical observada en
el gráfico de clústeres: en la práctica el modelo está segmentando por tramo de ingreso.
*Corrección:* aplicar `StandardScaler` antes de `fit`.

**3. Variables constantes en el modelo.** `Z_CostContact` y `Z_Revenue` son constantes (3 y
11) y no aportan información; deberían excluirse.

**4. Validación única.** El split 70/30 con una sola semilla no permite estimar la varianza
del desempeño. *Corrección:* validación cruzada estratificada.

**5. Evaluación del clustering.** El método del codo es un criterio visual. 
---

## Archivos

```
01-segmentacion-clientes-marketing/
├── README.md
├── segmentacion_clientes.ipynb    # análisis completo
└── data/
    └── marketing_campaign.csv     # dataset (separado por tabulaciones)
```
