# Machine Learning · Portafolio

Proyectos de análisis de datos y machine learning que he desarrollado. Cada carpeta es un
proyecto independiente con su propio README, notebook y datos.

**Santiago Espinoza** · Universidad de Chile
<!-- Agrega tu LinkedIn y correo acá: se ve mejor que dejarlo vacío -->

---

## Proyectos

| # | Proyecto | Qué resuelve | Técnicas | Stack |
|---|----------|--------------|----------|-------|
| 01 | [Segmentación de clientes y predicción de campañas](./01-segmentacion-clientes-marketing) | Segmenta ~2.200 clientes de retail y predice quién aceptará la próxima campaña: contactar al 10% mejor rankeado cuadruplica la tasa de aceptación | EDA · PCA · K-Means · Random Forest | pandas, scikit-learn, seaborn, matplotlib |

---

## Estructura del repositorio

```
Machine-Learning/
├── README.md                              ← estás aquí
├── .gitignore
└── 01-segmentacion-clientes-marketing/
    ├── README.md                          ← detalle del proyecto
    ├── segmentacion_clientes.ipynb
    └── data/
        └── marketing_campaign.csv
```

Cada proyecto sigue la misma convención: `NN-nombre-descriptivo/` con el notebook en la raíz
de la carpeta y los datos en `data/`. El prefijo numérico mantiene el orden cronológico.

---

## Stack

**Lenguaje:** Python 3.10+

**Análisis y modelado:** pandas · NumPy · scikit-learn

**Visualización:** matplotlib · seaborn

**Entorno:** Google Colab / Jupyter Notebook

---

## Cómo ejecutar los proyectos

Cada notebook incluye un badge **Open in Colab** que lo abre listo para ejecutar, sin
instalar nada y sin descargar datos. Para correrlo localmente:

```bash
git clone https://github.com/santiaagoe/Machine-Learning.git
cd Machine-Learning/01-segmentacion-clientes-marketing
pip install pandas numpy scikit-learn seaborn matplotlib
jupyter notebook
```
