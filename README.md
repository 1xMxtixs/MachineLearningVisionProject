# Reconocimiento de Patrones Visuales en Medicina: BloodMNIST (Hito 1)

Este repositorio contiene el desarrollo del **Hito 1** del proyecto para el curso **Aprendizaje de Máquinas y Visión Artificial (ADM)** en la **Universidad Andrés Bello (UNAB)**. El objetivo principal es establecer un baseline convolucional de alto rendimiento para el diagnóstico y clasificación automatizada de células sanguíneas a partir de imágenes de frotis microscópicos en su resolución nativa de **$28\times28$ píxeles**, cumpliendo rigurosamente con las restricciones académicas de escala espacial.

---

## 📋 Resumen de Resultados Destacados

Nuestra implementación incorpora una **innovación de diseño espacial ad-hoc** sobre la arquitectura pre-entrenada **ResNet-18** que evita el colapso temprano de las características espaciales. Los resultados en el conjunto de prueba (**3,421 imágenes independientes**) son sumamente robustos y superan la aproximación tradicional de reescalar imágenes:

*   **Exactitud Global (Accuracy):** **$91.38\%$** (Superando por un **$+4.42\%$** absoluto a la red reescalada a $128\times128$ y por un **$+16.31\%$** a la red estándar en $28\times28$).
*   **F1-Score Ponderado:** **$91.40\%$** (Garantiza alta consistencia ante el desbalance de clases).
*   **AUC-ROC Macro:** **$99.21\%$** (Discriminación probabilística interclase casi perfecta).
*   **Clasificación Perfecta en Plaquetas:** F1-Score de **$1.00$ ($100\%$ precisión y $100\%$ recall)** sobre las 470 plaquetas de prueba.
*   **Rescate de Monocitos (La clase morfológicamente más compleja):** F1-Score del **$81\%$** (frente a un pobre **$24\%$** de la red estándar sin nuestra adaptación espacial), marcando un incremento del **$+57\%$ absoluto**.

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

## 🛠️ Innovación de Diseño: Adaptación Espacial para $28\times28$ px

### El Reto del Campo Receptivo
La arquitectura estándar de ResNet-18 está diseñada para procesar imágenes grandes de $224\times224$ píxeles. Tiene 5 etapas de reducción espacial de factor 2 (división por 32). Al ingresar una imagen de $28\times28$ px sin reescalar, el mapa de características se reduce a un tamaño menor a $1\times1$ en las etapas profundas mucho antes de llegar a la capa de clasificación. Esto provoca la **destrucción masiva de la estructura morfológica celular** y limita severamente el Fine-Tuning.

### Nuestra Modificación de Rediseño
Para solventar este problema y mantener estrictamente el entrenamiento en su resolución nativa de $28\times28$ píxeles, reestructuramos las capas iniciales de la red:
1.  **Modificación de `conv1`:** Reemplazamos la primera convolución de $7\times7$ (stride 2) por un kernel de **$3\times3$ con stride 1 y padding 1** (manteniendo el mapa inicial en $28\times28$ px).
2.  **Identidad de `maxpool`:** Reemplazamos el operador `maxpool` inicial por una Identidad (`nn.Identity()`).
3.  **Adaptador Convolucional Activo:** Habilitamos el entrenamiento de los gradientes de `conv1` desde la fase inicial de extracción de características, forzando a que la primera capa actúe como un acoplador espacial dinámico entre las imágenes microscópicas originales y los ricos pesos pre-entrenados del cuerpo de la ResNet-18 (entrenados en ImageNet).

*Gracias a este rediseño, al llegar al bloque profundo `layer4`, el mapa de características preserva un tamaño de **$4\times4$ píxeles (¡16 veces más detalles espaciales!)**, permitiendo al Fine-Tuning extraer la textura nuclear fina de las células.*

---

## 📊 Curvas de Aprendizaje y Estabilidad

El entrenamiento se realizó bajo una rigurosa división de hold-out y una robusta regularización por aumentación de datos (*Data Augmentation*):
*   **Fase 1 (Extracción de Características - Épocas 1-5):** Entrenamiento rápido del clasificador final y de la primera convolución adaptiva. La exactitud de validación se disparó de forma instantánea de **$70.09\%$ a $85.11\%$**.
*   **Fase 2 (Fine-Tuning - Épocas 6-12):** Descongelación conjunta de `layer4`, `conv1` y `fc` a tasa de aprendizaje reducida ($10^{-5}$). La precisión de validación escaló sin tropiezos hasta un estable **$92.23\%$** en la época 12.
*   **Control de Sobreajuste:** Las pérdidas (*Loss*) de validación y entrenamiento descendieron en paralelo, cerrando con una pérdida de validación mínima de **$0.2287$**.

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
