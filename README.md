# Proyecto Individual Integrador — Predicción de Reingresos Hospitalarios en Pacientes con Diabetes

**Autora:** Luciana Chutte  
**Materia:** Ciencia de Datos (INA)  
**Contexto organizacional:** Red Sanatorial de Alta Complejidad — Consorcio Clínico (*Health Facts* EHR, 130 hospitales)

## 1. Tema elegido y fuentes de datos
Elegí trabajar sobre la **predicción de reingresos hospitalarios tempranos (`<30` días) en pacientes con diabetes** al momento del alta. Detectar a tiempo qué pacientes tienen mayor riesgo de volver a internarse permite organizar controles post-alta preventivos y mejorar la gestión de camas.

Utilicé dos archivos base complementarios (del repositorio público de UCI / consorcio *Health Facts*) y un dataset limpio generado en el pipeline:
* **`diabetic_data.csv`:** registro crudo de **101.766 episodios de internación y 50 variables** (demografía, tipo y destino de alta, días de internación, estudios, diagnósticos CIE-9, medicación y readmisión).
* **`IDS_mapping.csv`:** diccionario maestro con las descripciones de los códigos numéricos de admisión, disposición de alta y origen de admisión.
* **`diabetic_data_clean.csv`:** dataset saneado y consolidado a **69.987 pacientes únicos y 34 columnas**, exportado en la etapa ETL.

## 2. Diagnóstico organizacional (Las Dos Capas)
* **Capa 1 — Diagnóstico:** observé decisiones de alta fragmentadas según el criterio individual de cada servicio y problemas de gobernanza en el origen (faltantes codificados como `?` o `NaN`, como `weight` con 96,86% de omisión o `A1Cresult` con 83,28% sin registrar; códigos sin mapear; múltiples internaciones de un mismo `patient_nbr`; y pacientes fallecidos o en *Hospice* mezclados con altas regulares).
* **Capa 2 — Propuesta:** propuse la creación de un área de **Analítica Clínica y Seguimiento Post-Alta** para estimar el riesgo de reingreso a `<30` días al momento del egreso y apoyar la gestión operativa del alta.
* **Prioridad:** resolví primero la **Capa 1** (limpieza, mapeo con `IDS_mapping.csv`, exclusión de fallecidos/hospice y deduplicación por paciente único) para evitar *data leakage* y sesgos clínicos antes de entrenar los modelos.

## 3. Estructura del trabajo (`Proyecto_Individual_Diabetes.ipynb`)
1. **Diagnóstico y setup:** contexto de las dos capas, justificación de prioridad e importación de librerías (sin *warnings* y con paleta pastel).
2. **Auditoría de calidad y EDA:** revisión de completitud, unicidad, consistencia y validez; clasificación de faltantes (MNAR / MAR) y análisis exploratorio frente al desbalance de la clase positiva (`<30` días: 8,98% en la base limpia).
3. **Pipeline ETL:** exclusión de fallecidos/hospice y registros de género inválidos, conservación del primer episodio cronológico por `patient_nbr`, cruce con `IDS_mapping.csv`, agrupación de diagnósticos CIE-9, ingeniería de variables clínicas (`visitas_previas_totales`, `ratio_severidad_previa`, `intensidad_tratamiento_diaria`, `cant_farmacos_diabetes`) y exportación de `diabetic_data_clean.csv`.
4. **Modelado y evaluación:** partición estratificada (`train_test_split`), preprocesamiento dentro de `Pipeline` (`StandardScaler` y `OneHotEncoder`) y comparación entre **Regresión Logística balanceada** y **Random Forest balanceado** priorizando *Recall*, *ROC-AUC* y *PR-AUC* por el costo clínico de los falsos negativos.

## 4. Archivos del repositorio
* `Proyecto_Individual_Diabetes.ipynb`
* `diabetic_data.csv`
* `IDS_mapping.csv`
* `diabetic_data_clean.csv`
* `README.md`