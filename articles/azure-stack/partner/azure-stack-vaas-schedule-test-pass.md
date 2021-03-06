---
title: Uso del portal de validación como servicio de Azure Stack para programar la primera prueba | Microsoft Docs
description: Use el portal de validación como servicio de Azure Stack para programar la primera prueba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: How to
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1fbda6c0351287b9bc7574d100c0862b172a0aed
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650974"
---
# <a name="schedule-your-first-test"></a>Programación de la primera prueba

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Programe una prueba en el portal de validación como servicio (VaaS) para su solución de Azure Stack. Una solución VaaS representa una solución de Azure Stack con una lista de materiales de hardware en particular. Puede programar una prueba para comprobar que el hardware puede ejecutar Azure Stack.

Para comprobar la solución, cree el flujo de trabajo para una prueba. Un flujo de trabajo VaaS funciona dentro del contexto de una solución VaaS. Representa una serie de conjuntos de pruebas que actúan sobre la funcionalidad de implementación de Azure Stack en su hardware. Agregue los parámetros del entorno de su solución y seleccione una o varias pruebas para que se ejecuten en su solución.

Aunque el flujo de trabajo de prueba superada se puede usar para ejecutar cualquier prueba proporcionada por VaaS, incluidas las pruebas de los flujos de trabajo de validación, los resultados del flujo de trabajo de pruebas superadas no se consideran *oficiales*. Para obtener información acerca de los flujos de trabajo de validación oficiales, consulte [Flujos de trabajo](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Requisitos previos

Antes de seguir este tutorial de inicio rápido, debe finalizar los siguientes elementos:

- [Set up your Validation as a Service resources](azure-stack-vaas-set-up-resources.md) (Configuración de los recursos de validación como servicio)
- [Implementación del agente local](azure-stack-vaas-local-agent.md) (recomendado)
- [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md) (recomendado)

## <a name="start-a-workflow"></a>Inicio de un flujo de trabajo

![Inicio de sesión en el portal de VaaS](media/vaas_portalsignin.png)

Inicie sesión en el portal, seleccione o cree una solución y, luego, seleccione la solución.

1. Inicie sesión en el [portal de VaaS](https://azurestackvalidation.com).
2. Escriba el nombre de una solución existente o seleccione **Nueva solución** para crear una solución. Para obtener instrucciones, consulte [Creación de una solución en el portal de VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Seleccione **Iniciar** en el icono **Pruebas superadas**.

## <a name="specify-parameters"></a>Especificación de parámetros

![Texto alternativo](media/vaas_test_pass_parameters.png)

Defina el flujo de trabajo para su solución. El flujo de trabajo tiene los pasos de proceso usados para probar su solución.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Seleccione **Siguiente** para seleccionar las pruebas que va a programar.

## <a name="select-tests-to-run"></a>Selección de las pruebas que se ejecutarán

Elija las pruebas que le gustaría ejecutar en el flujo de trabajo.

1. Seleccione las pruebas que quiere ejecutar en el flujo de trabajo.

    Si quiere invalidar los parámetros comunes (es decir, los parámetros proporcionados en la sección anterior) para cualquier prueba, seleccione el vínculo **Editar** a un lado para especificar valores nuevos.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]
1. Seleccione **Siguiente** para revisar el flujo de trabajo.

## <a name="review-and-submit"></a>Revisión y envío

Revise, cree y, luego, programe el flujo de trabajo.

1. Revise la información que se muestra.

    El servicio crea el flujo de trabajo con la información proporcionada y se programarán las pruebas seleccionadas.

    Si algo parece incorrecto, use el botón **Anterior** para ir a una sección anterior.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md)
