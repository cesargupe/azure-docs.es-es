---
title: Ejecución de proyectos de ciencia de datos - Azure Machine Learning | Microsoft Docs
description: Cómo un científico de datos puede hacer un seguimiento del progreso de un proyecto de ciencia de datos.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 32390b05d2ec258a68ed4f53135399675105a7e9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302092"
---
# <a name="track-progress-of-data-science-projects"></a>Seguimiento del progreso de proyectos de ciencia de datos

Los administradores de grupo de ciencia de datos, los responsables de equipo y de proyecto tienen que realizar el seguimiento del progreso de sus proyectos, qué trabajo se ha realizado en ellos y por quién, y lo que queda en la lista de tareas pendientes. 

## <a name="azure-devops-dashboards"></a>Paneles de Azure DevOps
Si usa Azure DevOps, puede crear paneles para hacer el seguimiento de las actividades y de los elementos de trabajo asociados a un proyecto ágil determinado. 

Para más información sobre cómo crear y personalizar los paneles y widgets en Azure DevOps, consulte los conjuntos de instrucciones siguientes:

- [Agregar y administrar paneles](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards).
- [Agregar widgets a un panel](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Panel de ejemplo

Este es un panel de ejemplo sencillo que se creó para hacer seguimiento de las actividades de sprint de un proyecto de ciencia de datos ágil, así como también la cantidad de confirmaciones en los repositorios asociados. En el panel en la **parte superior izquierda** se muestra:

- la cuenta regresiva del sprint actual, 
- la cantidad de confirmaciones de cada repositorio durante los últimos siete días,
- el elemento de trabajo para usuarios específicos. 

En el resto de los paneles se muestra el diagrama de flujo acumulado (CFD), la evolución y la evolución ascendente de un proyecto:

- **Parte inferior izquierda**: CFD de la cantidad de trabajo con un estado determinado, donde el estado aprobado aparece en gris, el estado confirmado aparece en azul y el estado listo, en verde.
- **Parte superior derecha**: gráfico de evolución del trabajo que falta por completar en contraposición con el tiempo restante.
- **Parte inferior derecha**: gráfico de evolución ascendente del trabajo que se completó en contraposición con la cantidad de trabajo total.

![dashboard](./media/track-progress/dashboard.png)

Para una descripción de cómo crear estos gráficos, consulte las guías de inicio rápido y los tutoriales que aparecen en [Paneles](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Pasos siguientes

También se proporcionan tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 
