---
title: Supervisión de aplicaciones en Azure App Service | Microsoft Docs
description: Aprenda a supervisar aplicaciones en Azure App Service mediante Azure Portal.
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: byvinyal
ms.openlocfilehash: 9c58e5c64ea3689634d7afb4c5fef08c9b21798c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244379"
---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Supervisión de Aplicaciones en Azure App Service
[App Service](https://go.microsoft.com/fwlink/?LinkId=529714) proporciona una funcionalidad de supervisión integrada en [Azure Portal](https://portal.azure.com).
Azure Portal incluye la capacidad de revisar **cuotas** y **métricas** de una aplicación, así como el plan de App Service, configurar **alertas** e incluso **escalar** automáticamente en función de estas métricas.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Descripción de cuotas y métricas
### <a name="quotas"></a>Cuotas
Las aplicaciones hospedadas en App Service están sujetas a ciertos *límites* en lo que respecta a los recursos que pueden utilizar. Los límites se definen mediante el **plan de App Service** asociado a la aplicación.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Si la aplicación se hospeda en un plan **gratis** o **compartido**, los límites de los recursos que la aplicación puede usar vienen definidos por las **cuotas**.

Si la aplicación está hospedada en un plan **básico**, **estándar** o **premium**, los límites de los recursos que se pueden utilizar vendrán definidos por el **tamaño** (pequeño, mediano o grande) y el **recuento de instancias** (1, 2, 3...) del **plan de App Service**.

Las **cuotas** de las aplicaciones **gratis** o **compartidas** son:

* **CPU (Short)**
  * Cantidad de CPU permitida para esta aplicación en un intervalo de cinco minutos. Esta cuota se restablece cada cinco minutos.
* **CPU (Day)**
  * Cantidad total de CPU permitida para esta aplicación en un día. Esta cuota se restablece cada 24 horas a medianoche (UTC).
* **Memoria**
  * Cantidad total de memoria permitida para esta aplicación.
* **Ancho de banda**
  * Cantidad total de ancho de banda saliente permitido para esta aplicación en un día.
    Esta cuota se restablece cada 24 horas a medianoche (UTC).
* **Sistema de archivos**
  * Cantidad total de almacenamiento permitido.

La única cuota aplicable a las aplicaciones hospedadas en un plan **básico**, **estándar** o **premium** es la del **sistema de archivos**.

Para obtener más información sobre cuotas específicas, límites y características disponibles para las distintas SKU de App Service, consulte los [límites del servicio de suscripción de Azure](../azure-subscription-service-limits.md#app-service-limits).

#### <a name="quota-enforcement"></a>Aplicación de cuotas
Si una aplicación supera las cuotas **CPU (Short)** (CPU [corta]), **CPU (Day)** (CPU [diaria]) o **bandwidth** (ancho de banda), se detiene hasta que vuelva a establecerse la cuota. Durante este tiempo, todas las solicitudes entrantes provocan un error **HTTP 403**.
![][http403]

Si la cuota de **memoria** de la aplicación se supera, la aplicación se reinicia.

Si la cuota de **sistema de archivos** se supera, se produce un error en todas las operaciones de escritura, incluso al escribir en registros.

Las cuotas se pueden incrementar o quitar de la aplicación actualizando el plan de App Service.

### <a name="metrics"></a>Métricas
**Métricas** proporcionan información acerca de la aplicación o el comportamiento del plan de App Service.

Para una **aplicación**, estas son las métricas disponibles:

* **Tiempo de respuesta promedio**
  * Tiempo promedio en milisegundos necesario para que la aplicación atienda solicitudes.
* **Espacio de trabajo de memoria promedio**
  * Cantidad media de memoria en MiBs que utiliza la aplicación.
* **Tiempo de CPU**
  * Cantidad de CPU en segundos consumida por la aplicación. Para más información acerca de esta métrica, consulte [Tiempo de CPU y porcentaje de CPU](#cpu-time-vs-cpu-percentage).
* **Entrada de datos**
  * Cantidad de ancho de banda entrante consumido por la aplicación en MiBs.
* **Salida de datos**
  * Cantidad de ancho de banda saliente consumido por la aplicación en MiBs.
* **Http 2xx**
  * Cantidad total de solicitudes que devuelven un código de estado HTTP >= 200, pero < 300.
* **Http 3xx**
  * Cantidad total de solicitudes que devuelven un código de estado HTTP >= 300, pero < 400.
* **Http 401**
  * Cantidad total de solicitudes que devuelven el código de estado HTTP 401.
* **Http 403**
  * Cantidad total de solicitudes que devuelven el código de estado HTTP 403.
* **Http 404**
  * Cantidad total de solicitudes que devuelven el código de estado HTTP 404.
* **Http 406**
  * Cantidad total de solicitudes que devuelven el código de estado HTTP 406.
* **Http 4xx**
  * Cantidad total de solicitudes que devuelven un código de estado HTTP >= 400, pero < 500.
* **Errores de servidor HTTP**
  * Cantidad total de solicitudes que devuelven un código de estado HTTP >= 500, pero < 600.
* **Espacio de trabajo de memoria**
  * Cantidad actual de memoria utilizada por la aplicación en MiBs.
* **Solicitudes**
  * Número total de solicitudes, independientemente de su código de estado HTTP resultante.

Para un **plan de App Service**, estas son las métricas disponibles:

> [!NOTE]
> Las métricas de plan de App Service solo están disponibles para planes **Básico**, **Estándar** o **Premium**.
> 
> 

* **Porcentaje de CPU**
  * Uso promedio de CPU de todas las instancias del plan.
* **Porcentaje de memoria**
  * Uso promedio de memoria entre todas las instancias del plan.
* **Entrada de datos**
  * Uso promedio de ancho de banda entrante entre todas las instancias del plan.
* **Salida de datos**
  * Uso promedio de ancho de banda saliente entre todas las instancias del plan.
* **Longitud de la cola de disco**
  * Cantidad media de solicitudes de lectura y escritura en cola en Storage. Una longitud de la cola de disco elevada es un indicio de que una aplicación puede estar ralentizándose debido a una excesiva E/S de disco.
* **Longitud de la cola HTTP**
  * Promedio de solicitudes HTTP que estuvieron en cola antes de realizarse. Una longitud de la cola HTTP elevada o creciente indica que un plan está sobrecargado.

### <a name="cpu-time-vs-cpu-percentage"></a>Tiempo de CPU y porcentaje de CPU
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Hay dos métricas que reflejan el uso de CPU. **Tiempo de CPU** y **Porcentaje de CPU**

**Tiempo de CPU** es útil para las aplicaciones hospedadas en planes **gratis** o **compartidos**, ya que una de sus cuotas está definida en minutos de CPU utilizados por la aplicación.

**Porcentaje de CPU** es útil para las aplicaciones hospedadas en los planes de tipo **básico**, **estándar** y **premium** dado que se pueden escalar horizontalmente. Porcentaje de CPU es una buena indicación del uso general en todas las instancias.

## <a name="metrics-granularity-and-retention-policy"></a>Directiva de retención y granularidad de métricas
El servicio registra y agrega las métricas para una aplicación y el plan de App Service con las siguientes directivas de retención y granularidades:

* Las métricas de granularidad de **minuto** se conservan durante **30 horas**.
* Las métricas de granularidad de **hora** se conservan durante **30 días**.
* Las métricas de granularidad de **día** se conservan durante **30 días**.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Supervisión de cuotas y métricas en Azure Portal.
Puede revisar el estado de las distintas **cuotas** y **métricas** que afectan a una aplicación en [Azure Portal](https://portal.azure.com).

![][quotas]
Las **cuotas** se encuentran en Configuración &gt;**Cuotas**. La experiencia de usuario permite revisar: (1) el nombre de las cuotas, (2) su intervalo de restablecimiento, (3) su límite actual y (4) su valor actual.

![][metrics]
Se puede acceder a **Métricas** directamente desde la página de recursos. También puede personalizar el gráfico si (1) **hace clic** en él y selecciona (2) **Editar gráfico**.
Desde aquí puede cambiar (3) el **intervalo de tiempo**, (4) el **tipo de gráfico** y (5) las **métricas** que se muestran.  

Para más información acerca de las métricas, consulte [Supervisión de las métricas del servicio](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alertas y autoescala
Las métricas para una aplicación o plan de App Service pueden enlazarse con las alertas. Para más información sobre esto, consulte [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-alerts-portal.md).

Las aplicaciones de App Service hospedadas en planes de App Service Básico, Estándar o Premium admiten el **escalado automático**. El escalado automático le permite configurar reglas que supervisan las métricas del plan de App Service. Las reglas pueden aumentar o disminuir el recuento de instancias que proporcionan recursos adicionales según sea necesario. Las reglas también pueden ayudarle a ahorrar dinero cuando la aplicación se aprovisiona en exceso. Para más información acerca de la autoescala, consulte [Escalado](../monitoring-and-diagnostics/insights-how-to-scale.md) y [Procedimientos recomendados de escalado automático en Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-best-practices.md).

> [!NOTE]
> Si desea empezar a trabajar con Azure App Service antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba de App Service](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
