---
title: Análisis sobre la base de conocimiento
titleSuffix: Azure Cognitive Services
description: QnA Maker almacena todos los registros de chat y otra telemetría, si ha habilitado Application Insights durante la creación del servicio QnA Maker. Ejecute las consultas de ejemplo para obtener los registros de chat de Application Insights.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: dc363a3ba0d809a3307a6803993bdf500da45f1b
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035426"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Obtener análisis en la base de conocimiento

QnA Maker almacena todos los registros de chat y otra telemetría, si ha habilitado Application Insights durante la [creación del servicio QnA Maker](./set-up-qnamaker-service-azure.md). Ejecute las consultas de ejemplo para obtener los registros de chat de Application Insights.

1. Vaya al recurso de Application Insights.

    ![Selección del recurso de Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Seleccione **Analytics**. Se abre una nueva ventana en la que puede consultar la telemetría de QnA Maker.

    ![Seleccione Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Pegue la consulta siguiente y ejecútela.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Seleccione **Ejecutar** para ejecutar la consulta.

    ![Ejecutar consulta](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Ejecución de consultas de otros análisis en la base de conocimiento de QnA Maker

### <a name="total-90-day-traffic"></a>Tráfico total de 90 días

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>Tráfico total de preguntas en un período de tiempo determinado

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Tráfico de usuario

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribución de latencia de preguntas

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administrar claves](./key-management.md)
