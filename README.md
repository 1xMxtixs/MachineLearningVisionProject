# Reconocimiento de Patrones Visuales en Medicina: BloodMNIST (Hito 1)

Este repositorio contiene el desarrollo del **Hito 1** del proyecto para el curso **Aprendizaje de Máquinas y Visión Artificial (ADM)** en la **Universidad Andrés Bello (UNAB)**. El objetivo principal es establecer un baseline convolucional de alto rendimiento para el diagnóstico y clasificación automatizada de células sanguíneas a partir de imágenes de frotis microscópicos en su resolución nativa de **$28\times28$ píxeles**, cumpliendo rigurosamente con las restricciones académicas de escala espacial.

---

## 📋 Resumen de Resultados Destacados

Nuestra implementación incorpora una **innovación de diseño espacial ad-hoc** sobre la arquitectura pre-entrenada **ResNet-18** que evita el colapso temprano de las características espaciales. Los resultados en el conjunto de prueba (**3,421 imágenes independientes**) son sumamente robustos y superan la aproximación tradicional de reescalar imágenes.

---

## 🧬 Contexto Clínico y Dataset

El conjunto de datos utilizado es **BloodMNIST**, perteneciente al benchmark oficial de **MedMNIST v3.0.2**. Consiste en un conjunto curado de 17,092 imágenes a color (RGB de 3 canales) de células individuales normales de sangre periférica, divididas en 8 clases celulares críticas para el diagnóstico clínico:

1.  **Basófilos (basophil):** Involucrados en respuestas alérgicas sistémicas.
2.  **Eosinófilos (eosinophil):** Marcadores clásicos de alergias y parasitosis.
3.  **Eritroblastos (erythroblast):** Glóbulos rojos inmaduros nucleados, cuya presencia señala anomalías severas en la médula ósea.
4.  **Granulocitos Inmaduros (ig):** Precursores mieloides (mielocitos, metamielocitos, promielocitos) clave para detectar leucemias.
5.  **Linfocitos (lymphocyte):** Células del sistema inmune adaptativo frente a virus.
6.  **Monocitos (monocyte):** Células fagocíticas grandes con núcleos pleomórficos variables.
7.  **Neutrófilos (neutrophil):** Células maduras segmentadas multilobuladas, primera línea de defensa bacteriana.
8.  **Plaquetas (platelet):** Fragmentos citoplasmáticos esenciales para la coagulación.

---

## 📂 Estructura del Directorio

El proyecto se encuentra organizado siguiendo los más altos estándares de desarrollo y limpieza de código:

```
MachineLearningVisionProject/
├── notebooks/
│   ├── Proyecto_BloodMNIST.ipynb          # Cuaderno Jupyter oficial totalmente ejecutado con exactitud de 91.38%
│   └── CV_Transfer_learning.ipynb          # Cuaderno de referencia/pruebas de transferencia
├── presentation/
│   └── Presentacion_BloodMNIST_Hito1.pptx  # Presentación formal de 13 slides en 16:9 ilustrada con gráficos reales
├── docs/
│   └── Proyecto_ML_2_26.pdf               # Documento oficial de directrices del proyecto
├── results/
│   ├── class_distribution.png              # Gráfico EDA de distribución de clases extraído
│   ├── cell_samples.png                    # Cuadrícula visual de muestras de células extraída
│   ├── training_curves.png                 # Curvas de pérdida y exactitud del entrenamiento real
│   └── confusion_matrix.png                # Matriz de confusión final sobre el conjunto de prueba
├── .gitignore                             # Configuración Git para proteger el entorno virtual y temporales
└── README.md                              # Documentación del proyecto (este archivo)
```

---

## 🚀 Instrucciones de Configuración y Ejecución

Si deseas replicar este experimento en tu máquina local:

1.  **Clonar el repositorio:**
    ```bash
    git clone https://github.com/1xMxtixs/MachineLearningVisionProject.git
    cd MachineLearningVisionProject
    ```
2.  **Activar el Entorno Virtual:**
    *   *En Windows (PowerShell):*
        ```powershell
        .venv\Scripts\Activate.ps1
        ```
3.  **Instalar dependencias necesarias:**
    Todas las dependencias ya se encuentran instaladas en tu entorno local. Si utilizas una máquina nueva:
    ```bash
    pip install torch torchvision medmnist numpy pandas matplotlib seaborn scikit-learn
    ```
4.  **Ejecutar el Jupyter Notebook:**
    Abre tu editor de código preferido (VS Code o Jupyter Lab), selecciona el kernel del entorno virtual `.venv` y ejecuta secuencialmente las celdas en `notebooks/Proyecto_BloodMNIST.ipynb`.
