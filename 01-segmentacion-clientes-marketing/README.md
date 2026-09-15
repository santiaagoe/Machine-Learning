
# 01 · Segmentación de clientes y predicción de respuesta a campañas

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/santiaagoe/Machine-Learning/blob/main/01-segmentacion-clientes-marketing/segmentacion_clientes.ipynb)

Segmentación de la base de clientes de una cadena de retail y construcción de un modelo que
predice quién aceptará la próxima campaña de marketing. **Contactar solo al 10% mejor
rankeado cuadruplica la tasa de aceptación**, de 14,9% a 61,5%.

> Proyecto del Módulo Interdisciplinario, Universidad de Chile · Diciembre 2024

---

## Contexto y preguntas

Una cadena de retail quiere dejar de enviar sus campañas a toda la base. Dos preguntas:

1. **¿Qué segmentos de clientes existen y por qué canal se les llega?** (no supervisado)
2. **¿A quién conviene contactar en la próxima campaña?** (supervisado)

---

## Datos

**Fuente:** [Customer Personality Analysis](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis) (Kaggle) · 2.240 registros × 29 variables · 2.191 tras la limpieza

| Grupo | Variables |
|-------|-----------|
| Demográficas | `Year_Birth`, `Education`, `Marital_Status`, `Income`, `Kidhome`, `Teenhome` |
| Gasto (2 años) | `MntWines`, `MntFruits`, `MntMeatProducts`, `MntFishProducts`, `MntSweetProducts`, `MntGoldProds` |
| Canales | `NumWebPurchases`, `NumCatalogPurchases`, `NumStorePurchases`, `NumDealsPurchases`, `NumWebVisitsMonth` |
| Campañas | `AcceptedCmp1–5`, `Response`, `Complain` |

---

## Metodología

**1. Análisis exploratorio.** Dispersión de gasto en vino contra ingreso, edad y gasto en
otras categorías. Se detectan outliers extremos en ingreso y edad.

**2. Limpieza y transformación.**

- Eliminación de identificadores no predictivos (`ID`, `Dt_Customer`) y de las variables
  constantes `Z_CostContact` y `Z_Revenue`
- Se descartan los 24 registros sin `Income` (mapa de calor de nulos para verificar)
- Filtros de outliers: `Income < 100.000`, `Year_Birth > 1940`, `MntMeatProducts ≤ 1500`
- Ingeniería de variables: `Edades` y `N_hijos` (`Kidhome` + `Teenhome`)
- Codificación numérica de `Education` y `Marital_Status`

**3. Segmentación con PCA + K-Means.** Estandarización de las 23 variables y reducción a 2
componentes principales (37,7% de varianza). El número de clusters se elige por **coeficiente
de silueta**, no por el método del codo: k = 4 entrega silueta de 0,444 con segmentos
balanceados y accionables.

> El escalamiento es indispensable acá. `Income` se mueve en decenas de miles y las variables
> de conteo entre 0 y 10; como K-Means usa distancia euclidiana, sin estandarizar el ingreso
> domina el cálculo y la segmentación se reduce a cortar la base por tramos de ingreso.

**4. Predicción de respuesta a campaña.** Comparación de KNN, regresión logística y Random
Forest para predecir `Response`, con validación cruzada estratificada de 5 pliegues.
Evaluación por **ROC-AUC y F1**: accuracy se descarta porque con una clase positiva del 14,9%
un modelo que responda "nadie acepta" acierta el 85% de las veces y es inservible.

> Las campañas anteriores (`AcceptedCmp1–5`) se excluyen de las variables predictoras: predicen
> muy bien pero solo existen para clientes con historial, lo que dejaría el modelo inutilizable
> justamente con los clientes nuevos.

---

## Resultados

### Cuatro segmentos con perfiles distintos

| Cluster | Perfil | Clientes | Ingreso | Vino | Carne | Edad | Hijos | Catálogo | Tienda | Visitas web | Tasa resp. |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | Presupuesto limitado | 983 (45%) | 34.054 | 37 | 22 | 52,1 | 1,2 | 0,5 | 3,2 | **6,4** | 10% |
| 1 | Aficionados al vino | 415 (19%) | 65.838 | 575 | 231 | 58,5 | 0,8 | 4,4 | 8,6 | 4,6 | 20% |
| 2 | Premium | 421 (19%) | **77.237** | **597** | **499** | 54,6 | 0,1 | **6,1** | 8,6 | **2,5** | **30%** |
| 3 | Cazadores de ofertas | 372 (17%) | 52.580 | 387 | 90 | 59,9 | 1,4 | 2,2 | 6,5 | 6,6 | 20% |

**El hallazgo de canal.** Los segmentos de mayor valor son los que menos usan el canal
digital: el Premium visita el sitio 2,5 veces al mes frente a las 6,4 del segmento de menor
gasto, que navega y no convierte. Una campaña de vino concentrada en canales digitales
estaría llegando justamente a quienes no compran — catálogo y tienda física son los canales
de este público.

**El hallazgo de producto.** El cluster 1 gasta menos en términos absolutos que el 2, pero es
el único donde el vino domina sobre el resto del gasto (575 en vino contra 231 en carne). Una
campaña de vino premium apunta al cluster 2; una de volumen o fidelización de categoría, al 1.

### Comparación de modelos predictivos

Validación cruzada estratificada de 5 pliegues sobre el conjunto de entrenamiento:

| Modelo | ROC-AUC | F1 |
|---|---|---|
| KNN (k=6) | 0,708 ± 0,054 | 0,117 ± 0,047 |
| Regresión logística | 0,811 ± 0,043 | **0,452 ± 0,060** |
| Random Forest | **0,830 ± 0,040** | 0,292 ± 0,089 |

El KNN queda último, y la razón es instructiva: con 18 variables las distancias euclidianas
pierden capacidad de discriminar, y el algoritmo no maneja bien el desbalance de clases.
Como el uso real es priorizar a quién contactar, lo que importa es la capacidad de
ordenamiento, no la clasificación binaria: se elige Random Forest.

**Random Forest en el conjunto de prueba: ROC-AUC 0,870 · PR-AUC 0,508.**

La variable más informativa es `Recency` —días desde la última compra— por encima del
ingreso. **La actividad reciente predice mejor que el poder adquisitivo.**

### El resultado operativo

Si la campaña tiene costo por contacto, lo que importa no es el accuracy sino cuánto mejora
el retorno al priorizar. Ordenando la base por la probabilidad que asigna el modelo:

| % de la base contactado | Tasa de aceptación | Lift | % del total de aceptaciones captado |
|---|---|---|---|
| 5% | 56,2% | 3,8x | 18% |
| **10%** | **61,5%** | **4,1x** | **41%** |
| 20% | 48,9% | 3,3x | 65% |
| 30% | 40,1% | 2,7x | 81% |
| 50% | 27,4% | 1,8x | 92% |
| 100% (sin modelo) | 14,9% | 1,0x | 100% |

Contactando al 30% de la base se captura el 81% de todas las aceptaciones posibles.

---

## Limitaciones

- Los 2 componentes principales retienen **37,7% de la varianza** total: la segmentación
  captura la estructura dominante, no toda. Con más componentes la silueta baja y los grupos
  se vuelven menos interpretables; es un intercambio explícito.
- El clasificador tiene **recall bajo (0,173)** en el umbral por defecto. No afecta el uso
  operativo, que es de ranking, pero un despliegue real requiere calibrar el umbral según el
  costo por contacto y el margen por conversión.
- Los datos no tienen una marca temporal utilizable, así que **no se puede validar contra un
  período futuro** — la prueba definitiva para un modelo de campañas.
- La segmentación se valida solo con silueta. Métricas complementarias (Davies-Bouldin,
  Calinski-Harabasz) o una validación cualitativa con el área de negocio darían más respaldo.

---

## Reproducir

**En Colab:** haz clic en el badge de arriba. No requiere instalación ni descargar datos —
el notebook lee el dataset directamente desde este repositorio.

**Localmente:**

```bash
git clone https://github.com/santiaagoe/Machine-Learning.git
cd Machine-Learning/01-segmentacion-clientes-marketing
pip install pandas numpy scikit-learn seaborn matplotlib
jupyter notebook segmentacion_clientes.ipynb
```

---

## Archivos

```
01-segmentacion-clientes-marketing/
├── README.md
├── segmentacion_clientes.ipynb    # análisis completo, ejecutado
└── data/
    └── marketing_campaign.csv     # dataset (separado por tabulaciones)
```
