---
title: 'Tutorial: entrenamiento de un modelo de clasificación con aprendizaje automático: servicio Azure Machine Learning'
description: Aprenda a generar un modelo mediante el aprendizaje automático automatizado.  Azure Machine Learning realiza automáticamente el procesamiento previo de los datos, y la selección del algoritmo y de los hiperparámetros. Después, el modelo final se implementa con el servicio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 11/21/2018
ms.openlocfilehash: 76436da1013c6747e9167c006e0d7c5e89ec8d9c
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284751"
---
# <a name="tutorial-train-a-classification-model-with-automated-machine-learning-in-azure-machine-learning-service"></a>Tutorial: Entrenamiento de un modelo de clasificación con aprendizaje automático en el servicio Azure Machine Learning

En este tutorial aprenderá a generar un modelo mediante el aprendizaje automático automatizado (ML automatizado).  El servicio Azure Machine Learning puede realizar automáticamente el procesamiento previo de los datos y la selección del algoritmo y los hiperparámetros. Después, el modelo final se puede implementar mediante el flujo de trabajo del tutorial sobre [implementación de un modelo](tutorial-deploy-models-with-aml.md).

![diagrama de flujo](./media/tutorial-auto-train-models/flow2.png)

Al igual que en el [tutorial de entrenamiento de modelos](tutorial-train-models-with-aml.md), en este se clasifican las imágenes manuscritas de dígitos (0-9) del conjunto de datos [MNIST](http://yann.lecun.com/exdb/mnist/). Pero esta vez no es necesario especificar un algoritmo ni ajustar los hiperparámetros. La técnica de ML automatizado recorre en iteración muchas combinaciones de algoritmos e hiperparámetros hasta que encuentra el mejor modelo según su criterio.

Aprenderá a:

> [!div class="checklist"]
> * Configuración de su entorno de desarrollo
> * Acceder y examinar los datos
> * Entrenar mediante un clasificador automatizado en la máquina local
> * Explorar los resultados
> * Revisar los resultados del entrenamiento
> * Registro del mejor modelo

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://aka.ms/AMLfree) antes de empezar.

>[!NOTE]
> El código de este artículo se ha probado con el SDK de Azure Machine Learning, versión 0.1.79

## <a name="get-the-notebook"></a>Obtención del cuaderno

Para su comodidad, este tutorial está disponible como un [cuaderno de Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/03.auto-train-models.ipynb). Ejecute el cuaderno `03.auto-train-models.ipynb` en Azure Notebooks o en su propio servidor de cuadernos de Jupyter.


[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]


## <a name="set-up-your-development-environment"></a>Configuración de su entorno de desarrollo

Toda la configuración para el trabajo de desarrollo puede realizarse en el cuaderno de Python.  La configuración incluye:

* La importación de los paquetes de Python
* La configuración de un área de trabajo para habilitar la comunicación entre la máquina local y los recursos remotos
* La creación de un directorio para almacenar los scripts de entrenamiento

### <a name="import-packages"></a>Importar paquetes
Importe los paquetes de Python que necesite para este tutorial.


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging
from sklearn import datasets
from matplotlib import pyplot as plt
from matplotlib.pyplot import imshow
import random
import numpy as np
```

### <a name="configure-workspace"></a>Configuración del área de trabajo

Cree un objeto de área de trabajo desde el área de trabajo existente. `Workspace.from_config()` lee el archivo **aml_config/config.json** y carga los detalles en un objeto denominado `ws`.  En el resto del código de este tutorial se usa `ws`.

Cuando tenga un objeto del área de trabajo, especifique un nombre para el experimento, y cree y registre un directorio local con el área de trabajo. El historial de todas las ejecuciones se registra en el experimento especificado.

```python
ws = Workspace.from_config()
# project folder to save your local files
project_folder = './sample_projects/automl-local-classification'
# choose a name for the run history container in the workspace
experiment_name = 'automl-classifier'

import os

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Exploración de los datos

En el tutorial de aprendizaje inicial se usa una versión de alta resolución del conjunto de datos MNIST (28 x 28 píxeles).  Como el entrenamiento de ML automatizado requiere varias iteraciones, en este tutorial se usa una versión de las imágenes de menor resolución (8 x 8 píxeles) para mostrar los conceptos y reducir el tiempo necesario para las iteraciones.  

```python
from sklearn import datasets

digits = datasets.load_digits()

# Exclude the first 100 rows from training so that they can be used for test.
X_train = digits.data[100:,:]
y_train = digits.target[100:]
```

### <a name="display-some-sample-images"></a>Mostrar algunas imágenes de ejemplo

Cargue los datos en matrices `numpy`. A continuación, use `matplotlib` para trazar 30 imágenes aleatorias del conjunto de datos con sus etiquetas sobre ellas.

```python
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x = 2, y = -2, s = y_train[i], fontsize = 18)
    plt.imshow(X_train[i].reshape(8, 8), cmap = plt.cm.Greys)
plt.show()
```
Una muestra aleatoria de imágenes muestra:

![dígitos](./media/tutorial-auto-train-models/digits.png)


Ahora tiene los paquetes necesarios y los datos listos para el entrenamiento automático del modelo. 

## <a name="train-a-model"></a>Entrenamiento de un modelo

Para entrenar automáticamente un modelo, primero se definen valores de configuración del experimento y, a continuación, se ejecuta este último.


### <a name="define-settings"></a>Definición de la configuración

Defina la configuración del experimento y del modelo.

|Propiedad| Valor en este tutorial |DESCRIPCIÓN|
|----|----|---|
|**primary_metric**|ABC ponderada | Métrica que desea optimizar.|
|**max_time_sec**|12,000|Límite de tiempo en segundos para cada iteración|
|**iterations**|20|Número de iteraciones. En cada iteración, el modelo se entrena con datos con canalización específica|
|**n_cross_validations**|3|Número de divisiones de validación cruzada|
|**preprocess**|False| *True/False*. Permite al experimento realizar el procesamiento previo en la entrada.  Durante el procesamiento previo se administran los *datos ausentes* y se realizan *extracciones de características* comunes|
|**exit_score**|0.9985|El valor *double* indica el destino de *primary_metric*. Cuando se supera el destino, la ejecución finaliza|
|**blacklist_algos**|["kNN","LinearSVM"]|*Matriz* de *cadenas* que indica los algoritmos que pasar por alto.
|

```python
from azureml.train.automl import AutoMLConfig

##Local compute 
Automl_config = AutoMLConfig(task = 'classification',
                             primary_metric = 'AUC_weighted',
                             max_time_sec = 12000,
                             iterations = 20,
                             n_cross_validations = 3,
                             preprocess = False,
                             exit_score = 0.9985,
                             blacklist_algos = ['kNN','LinearSVM'],
                             X = X_train,
                             y = y_train,
                             path=project_folder)
```

### <a name="run-the-experiment"></a>Ejecución del experimento

Inicie el experimento para ejecutarlo localmente. Defina el destino del proceso como local y establezca la salida en true para ver el progreso en el experimento.


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(Automl_config, show_output=True)
```

Con el progreso de las iteraciones aparecen salidas como las siguientes, de línea en línea.  Verá una nueva línea cada **10-15 segundos**.

    Running locally
    Parent Run ID: AutoML_ca0c807b-b7bf-4809-a963-61c6feb73ea1
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             0      Normalizer extra trees                0:00:15.955367           0.988     0.988
             1      Normalizer extra trees                0:00:14.203088           0.952     0.988
             2      Normalizer lgbm_classifier            0:00:15.089057           0.994     0.994
             3      Normalizer SGD classifier             0:00:14.866700           0.500     0.994
             4      Normalizer SGD classifier             0:00:13.740577           0.983     0.994
             5      Normalizer DT                         0:00:13.879204           0.937     0.994
             6      Normalizer SGD classifier             0:00:13.379975           0.980     0.994
             7      Normalizer lgbm_classifier            0:00:15.953293           0.997     0.997
    Stopping criteria reached. Ending experiment.

## <a name="explore-the-results"></a>Exploración de los resultados

Explore los resultados del experimento con un widget de Jupyter o al examinar el historial del experimento.

### <a name="jupyter-widget"></a>Widget de Jupyter

Use el widget de cuaderno de Jupyter para ver un grafo y una tabla de todos los resultados.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

Esta es una imagen estática del widget.  En el cuaderno, puede usar la lista desplegable encima del grafo para ver un grafo de cada métrica disponible para cada iteración.

![tabla de widget](./media/tutorial-auto-train-models/table.png)
![trazado de widget](./media/tutorial-auto-train-models/graph.png)

### <a name="retrieve-all-iterations"></a>Recuperación de todas las iteraciones

Consulte el historial del experimento y las métricas individuales de cada iteración ejecutada.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```

En esta tabla se muestran los resultados:


<!-- hello world -->

<table><thead><tr>
        <th></th>
        <th>0</th>
        <th>1</th>
        <th>2</th>
        <th>3</th>
        <th>4</th>
        <th>5</th>
        <th>6</th>
        <th>7</th>
    </tr></thead>
<tbody>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row0" class="row_heading level0 row0" >AUC_macro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col0" class="data row0 col0" >0.988094</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col1" class="data row0 col1" >0.951981</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col2" class="data row0 col2" >0.993606</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col3" class="data row0 col3" >0,5</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col4" class="data row0 col4" >0.982724</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col5" class="data row0 col5" >0.936998</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col6" class="data row0 col6" >0.979978</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row0_col7" class="data row0 col7" >0.996639</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row1" class="row_heading level0 row1" >AUC_micro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col0" class="data row1 col0" >0.988104</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col1" class="data row1 col1" >0.948402</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col2" class="data row1 col2" >0.99413</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col3" class="data row1 col3" >0.463035</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col4" class="data row1 col4" >0.976078</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col5" class="data row1 col5" >0.945169</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col6" class="data row1 col6" >0.968913</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row1_col7" class="data row1 col7" >0.997027</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row2" class="row_heading level0 row2" >AUC_weighted</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col0" class="data row2 col0" >0.987943</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col1" class="data row2 col1" >0.952255</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col2" class="data row2 col2" >0.993513</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col3" class="data row2 col3" >0,5</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col4" class="data row2 col4" >0.982801</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col5" class="data row2 col5" >0.937292</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col6" class="data row2 col6" >0.979973</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row2_col7" class="data row2 col7" >0.99656</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row3" class="row_heading level0 row3" >AUC_weighted_max</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col0" class="data row3 col0" >0.987943</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col1" class="data row3 col1" >0.987943</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col2" class="data row3 col2" >0.993513</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col3" class="data row3 col3" >0.993513</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col4" class="data row3 col4" >0.993513</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col5" class="data row3 col5" >0.993513</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col6" class="data row3 col6" >0.993513</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row3_col7" class="data row3 col7" >0.99656</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row4" class="row_heading level0 row4" >accuracy</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col0" class="data row4 col0" >0.852093</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col1" class="data row4 col1" >0.666464</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col2" class="data row4 col2" >0.898057</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col3" class="data row4 col3" >0.0701284</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col4" class="data row4 col4" >0.832662</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col5" class="data row4 col5" >0.701827</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col6" class="data row4 col6" >0.83325</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row4_col7" class="data row4 col7" >0.925752</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row5" class="row_heading level0 row5" >average_precision_score_macro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col0" class="data row5 col0" >0.929167</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col1" class="data row5 col1" >0.786258</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col2" class="data row5 col2" >0.961497</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col3" class="data row5 col3" >0,1</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col4" class="data row5 col4" >0.917486</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col5" class="data row5 col5" >0.685547</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col6" class="data row5 col6" >0.906611</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row5_col7" class="data row5 col7" >0.977775</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row6" class="row_heading level0 row6" >average_precision_score_micro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col0" class="data row6 col0" >0.932596</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col1" class="data row6 col1" >0.728331</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col2" class="data row6 col2" >0.964138</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col3" class="data row6 col3" >0.0909031</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col4" class="data row6 col4" >0.880136</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col5" class="data row6 col5" >0.757538</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col6" class="data row6 col6" >0.859813</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row6_col7" class="data row6 col7" >0.980408</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row7" class="row_heading level0 row7" >average_precision_score_weighted</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col0" class="data row7 col0" >0.930681</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col1" class="data row7 col1" >0.788964</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col2" class="data row7 col2" >0.962007</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col3" class="data row7 col3" >0.102123</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col4" class="data row7 col4" >0.918785</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col5" class="data row7 col5" >0.692041</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col6" class="data row7 col6" >0.908293</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row7_col7" class="data row7 col7" >0.977699</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row8" class="row_heading level0 row8" >balanced_accuracy</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col0" class="data row8 col0" >0.917902</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col1" class="data row8 col1" >0.814509</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col2" class="data row8 col2" >0.94491</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col3" class="data row8 col3" >0,5</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col4" class="data row8 col4" >0.909248</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col5" class="data row8 col5" >0.833428</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col6" class="data row8 col6" >0.907412</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row8_col7" class="data row8 col7" >0.959351</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row9" class="row_heading level0 row9" >f1_score_macro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col0" class="data row9 col0" >0.850511</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col1" class="data row9 col1" >0.643116</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col2" class="data row9 col2" >0.899262</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col3" class="data row9 col3" >0.013092</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col4" class="data row9 col4" >0.825054</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col5" class="data row9 col5" >0.691712</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col6" class="data row9 col6" >0.819627</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row9_col7" class="data row9 col7" >0.926081</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row10" class="row_heading level0 row10" >f1_score_micro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col0" class="data row10 col0" >0.852093</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col1" class="data row10 col1" >0.666464</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col2" class="data row10 col2" >0.898057</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col3" class="data row10 col3" >0.0701284</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col4" class="data row10 col4" >0.832662</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col5" class="data row10 col5" >0.701827</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col6" class="data row10 col6" >0.83325</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row10_col7" class="data row10 col7" >0.925752</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row11" class="row_heading level0 row11" >f1_score_weighted</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col0" class="data row11 col0" >0.852134</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col1" class="data row11 col1" >0.646049</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col2" class="data row11 col2" >0.898705</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col3" class="data row11 col3" >0.00933691</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col4" class="data row11 col4" >0.830731</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col5" class="data row11 col5" >0.696538</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col6" class="data row11 col6" >0.824547</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row11_col7" class="data row11 col7" >0.925778</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row12" class="row_heading level0 row12" >log_loss</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col0" class="data row12 col0" >0.554364</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col1" class="data row12 col1" >1.15728</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col2" class="data row12 col2" >0.51741</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col3" class="data row12 col3" >2.30397</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col4" class="data row12 col4" >1.94009</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col5" class="data row12 col5" >1.57663</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col6" class="data row12 col6" >2.1848</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row12_col7" class="data row12 col7" >0.250725</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row13" class="row_heading level0 row13" >norm_macro_recall</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col0" class="data row13 col0" >0.835815</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col1" class="data row13 col1" >0.629003</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col2" class="data row13 col2" >0.890167</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col3" class="data row13 col3" >0</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col4" class="data row13 col4" >0.818755</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col5" class="data row13 col5" >0.666629</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col6" class="data row13 col6" >0.814739</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row13_col7" class="data row13 col7" >0.918851</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row14" class="row_heading level0 row14" >precision_score_macro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col0" class="data row14 col0" >0.855892</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col1" class="data row14 col1" >0.707715</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col2" class="data row14 col2" >0.90195</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col3" class="data row14 col3" >0.00701284</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col4" class="data row14 col4" >0.84882</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col5" class="data row14 col5" >0.729611</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col6" class="data row14 col6" >0.855384</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row14_col7" class="data row14 col7" >0.927881</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row15" class="row_heading level0 row15" >precision_score_micro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col0" class="data row15 col0" >0.852093</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col1" class="data row15 col1" >0.666464</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col2" class="data row15 col2" >0.898057</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col3" class="data row15 col3" >0.0701284</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col4" class="data row15 col4" >0.832662</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col5" class="data row15 col5" >0.701827</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col6" class="data row15 col6" >0.83325</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row15_col7" class="data row15 col7" >0.925752</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row16" class="row_heading level0 row16" >precision_score_weighted</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col0" class="data row16 col0" >0.859204</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col1" class="data row16 col1" >0.711918</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col2" class="data row16 col2" >0.903523</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col3" class="data row16 col3" >0.00500676</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col4" class="data row16 col4" >0.861209</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col5" class="data row16 col5" >0.737586</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col6" class="data row16 col6" >0.863524</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row16_col7" class="data row16 col7" >0.928403</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row17" class="row_heading level0 row17" >recall_score_macro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col0" class="data row17 col0" >0.852234</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col1" class="data row17 col1" >0.666102</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col2" class="data row17 col2" >0.901151</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col3" class="data row17 col3" >0,1</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col4" class="data row17 col4" >0.83688</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col5" class="data row17 col5" >0.699966</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col6" class="data row17 col6" >0.833265</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row17_col7" class="data row17 col7" >0.926966</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row18" class="row_heading level0 row18" >recall_score_micro</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col0" class="data row18 col0" >0.852093</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col1" class="data row18 col1" >0.666464</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col2" class="data row18 col2" >0.898057</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col3" class="data row18 col3" >0.0701284</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col4" class="data row18 col4" >0.832662</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col5" class="data row18 col5" >0.701827</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col6" class="data row18 col6" >0.83325</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row18_col7" class="data row18 col7" >0.925752</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row19" class="row_heading level0 row19" >recall_score_weighted</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col0" class="data row19 col0" >0.852093</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col1" class="data row19 col1" >0.666464</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col2" class="data row19 col2" >0.898057</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col3" class="data row19 col3" >0.0701284</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col4" class="data row19 col4" >0.832662</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col5" class="data row19 col5" >0.701827</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col6" class="data row19 col6" >0.83325</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row19_col7" class="data row19 col7" >0.925752</td> 
    </tr>    <tr> 
        <th id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180level0_row20" class="row_heading level0 row20" >weighted_accuracy</th> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col0" class="data row20 col0" >0.851054</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col1" class="data row20 col1" >0.66639</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col2" class="data row20 col2" >0.895428</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col3" class="data row20 col3" >0.049121</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col4" class="data row20 col4" >0.829247</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col5" class="data row20 col5" >0.702754</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col6" class="data row20 col6" >0.833464</td> 
        <td id="T_32497c5c_a5a9_11e8_a10f_c49ded1c6180row20_col7" class="data row20 col7" >0.924723</td> 
    </tr></tbody> 
</table> 
<!-- hello world -->

## <a name="register-the-best-model"></a>Registro del mejor modelo 

Use el objeto `local_run` para obtener el mejor modelo y regístrelo en el área de trabajo. 

```python
# find the run with the highest accuracy value.
best_run, fitted_model = local_run.get_output()

# register model in workspace
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model"></a>Prueba del mejor modelo

Use el modelo para predecir unos dígitos aleatorios.  Muestre el valor de la predicción y la imagen.  Se usa un color de fuente rojo e imagen inversa (blanco sobre negro) para resaltar los ejemplos clasificados incorrectamente.

Como la precisión del modelo es alta, es posible que deba ejecutar el siguiente código varias veces para que pueda ver un ejemplo de clasificación incorrecta.

```python
# find 30 random samples from test set
n = 30
X_test = digits.data[:100, :]
y_test = digits.target[:100]
sample_indices = np.random.permutation(X_test.shape[0])[0:n]
test_samples = X_test[sample_indices]


# predict using the  model
result = fitted_model.predict(test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x = 2, y = -2, s = result[i], fontsize = 18, color = font_color)
    plt.imshow(X_test[s].reshape(8, 8), cmap = clr_map)
    
    i = i + 1
plt.show()
```


![results](./media/tutorial-auto-train-models/results.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Pasos siguientes

En este tutorial del servicio Azure Machine Learning, ha usado Python para:

> [!div class="checklist"]
> * Configuración de su entorno de desarrollo
> * Acceder y examinar los datos
> * Entrenar con un clasificador automatizado en local con parámetros personalizados
> * Exploración de los resultados
> * Revisar los resultados del entrenamiento
> * Registro del mejor modelo

Más información sobre [cómo configurar las opciones para el aprendizaje automático](how-to-configure-auto-train.md) o [cómo usar el aprendizaje automático en un recurso remoto](how-to-auto-train-remote.md).  

