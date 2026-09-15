# Machine Learning · Portafolio

Proyectos de análisis de datos y machine learning que he desarrollado. Cada carpeta es un
proyecto independiente con su propio README, notebook y datos.

**Santiago Espinoza** · Universidad de Chile
<!-- Contacto: santiago.espinoza.correa@gmail.com-->

---

## Proyectos

| # | Proyecto | Qué resuelve | Técnicas | Stack |
|---|----------|--------------|----------|-------|
| 01 | [Segmentación de clientes para marketing](./01-segmentacion-clientes-marketing) | Segmenta ~2.200 clientes de una cadena de retail e identifica el perfil del consumidor de vino para dirigir campañas | EDA · K-Means · KNN | pandas, scikit-learn, seaborn, matplotlib |


---

## Estructura del repositorio

```
Machine-Learning/
├── README.md                              
├── .gitignore
└── 01-segmentacion-clientes-marketing/
    ├── README.md                          ← detalle del proyecto
    ├── segmentacion_clientes.ipynb
    └── data/
        └── marketing_campaign.csv
```

---

## Stack

**Lenguaje:** Python 3.10+

**Análisis y modelado:** pandas · NumPy · scikit-learn · statsmodels

**Visualización:** matplotlib · seaborn

**Entorno:** Google Colab / Jupyter Notebook

---

## Cómo ejecutar los proyectos

Cada notebook incluye un badge **Open in Colab** que lo abre listo para ejecutar, sin
instalar nada. Para correrlo localmente:

```bash
git clone https://github.com/santiaagoe/Machine-Learning.git
cd Machine-Learning/01-segmentacion-clientes-marketing
pip install pandas numpy scikit-learn seaborn matplotlib
jupyter notebook
```
