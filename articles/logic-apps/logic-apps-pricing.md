---
title: 'Precios y facturación: Azure Logic Apps | Microsoft Docs'
description: Descubra cómo funcionan los precios y la facturación en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 5f9147035c07bbe4fb3f38b74025015e70dd87b3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159580"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de precios de Azure Logic Apps

Puede crear y ejecutar flujos de trabajo de integración escalable automatizados en la nube con Azure Logic Apps. Estos son los detalles sobre cómo funcionan los precios y facturación para Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de precios de consumo

Para las nuevas aplicaciones lógicas que cree mediante el servicio de Logic Apps "global" o público, paga solo lo que usa. Estas aplicaciones lógicas usan un plan de consumo y un modelo de precios, lo que significa que todas las ejecuciones de acciones realizadas por una aplicación lógica son medidas. Cada paso de una definición de aplicación lógica es una acción, que incluye desencadenadores, pasos del flujo de control, llamadas a acciones integradas y llamadas a conectores. Para más información, consulte [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de precios fijos

> [!NOTE]
> El entorno de servicio de integración se encuentra en *versión preliminar privada*. Para solicitar acceso, [cree su solicitud para unirse aquí](https://aka.ms/iseprivatepreview).

Para las nuevas aplicaciones lógicas que cree con un [*entorno del servicio de integración* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), que es una instancia de Logic Apps privada aislada que usa recursos dedicados, paga un precio mensual fijo por acciones integradas y conectores estándar etiquetados por ISE. El ISE incluye un conector Enterprise sin costo, mientras que los conectores Enterprise adicionales se cobran en función del precio de consumo Enterprise. Para más información, consulte [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Desencadenadores

Los desencadenadores son acciones especiales que crean una instancia de aplicación lógica cuando se produce un evento específico. Los desencadenadores actúan de formas distintas, que afectan al modo en que la aplicación lógica se mide.

* **Desencadenador de sondeo**: sondea continuamente un punto de conexión en busca de mensajes que satisfacen los criterios para crear una instancia de una aplicación lógica e iniciar el flujo de trabajo. Cada solicitud de sondeo cuenta como una ejecución y se mide, incluso cuando no se crea ninguna instancia de aplicación lógica. Para especificar el intervalo de sondeo, configure el desencadenador a través del Diseñador de aplicaciones lógicas.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Desencadenador de webhook**: este desencadenador espera a que un cliente envíe una solicitud a un punto de conexión determinado. Cada solicitud enviada al punto de conexión de webhook se considera una ejecución de acción. Por ejemplo, el desencadenador de solicitud y de webhook HTTP son ambos desencadenadores de webhook.

* **Desencadenador de periodicidad**: este desencadenador crea una instancia de la aplicación lógica según el intervalo de periodicidad configurado en el desencadenador. Por ejemplo, puede establecer un desencadenador de periodicidad que se ejecute cada tres días o según una programación más compleja.

Puede encontrar ejecuciones de desencadenadores en el panel Información general de la aplicación lógica, en la sección Historial de desencadenadores.

## <a name="actions"></a>Acciones

Las acciones integradas, como las que llaman a HTTP, Azure Functions o API Management, así como los pasos de flujo de control, se miden como acciones nativas y tienen sus tipos respectivos. Las acciones que llaman a [conectores](https://docs.microsoft.com/connectors) tienen el tipo "ApiConnection". Estos conectores se clasifican como standard o enterprise, y se miden con sus [precios][pricing] respectivos. Los conectores empresariales en *versión preliminar* se cobran como conectores estándar.

Todas las acciones ejecutadas correcta o incorrectamente se cuentan y se miden como ejecuciones de acción. Sin embargo, las acciones omitidas debido a condiciones que no se cumplieron o a acciones que no se ejecutaron porque la aplicación lógica terminó antes de que se completaran, no se cuentan como ejecuciones de acción. Las aplicaciones lógicas deshabilitadas no pueden crear instancias nuevas, por lo que no se cobran mientras estén deshabilitadas.

> [!NOTE]
> Después de deshabilitar una aplicación lógica, las instancias que se estén ejecutando pueden tardar un tiempo antes de detenerse por completo.

Las acciones que se ejecutan dentro de los bucles se cuentan por cada ciclo del bucle. Por ejemplo, una sola acción de un bucle "for each" que procesa una lista de 10 elementos se cuenta multiplicando el número de elementos de lista (10) por el número de acciones en el bucle (1) más uno para iniciar el bucle. Por lo tanto, en este ejemplo, el cálculo es (10 * 1) + 1, lo que da como resultado 11 ejecuciones de acción.

## <a name="integration-account-usage"></a>Uso de la cuenta de integración

En el uso basado en el consumo se incluye una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) en la que puede explorar, desarrollar y realizar pruebas de las características [B2B/EDI](logic-apps-enterprise-integration-b2b.md) y [procesamiento XML](logic-apps-enterprise-integration-xml.md) de Logic Apps sin costo adicional. Puede tener una cuenta de integración por región y almacenar hasta [números específicos de artefactos](../logic-apps/logic-apps-limits-and-config.md), como acuerdos y entidades EDI, mapas, esquemas, ensamblados, certificados y configuraciones por lote.

Azure Logic Apps también ofrece cuentas de integración básicas y estándar con Acuerdos de Nivel de Servicio de Logic Apps admitidos. Puede utilizar cuentas de integración básicas cuando desee utilizar solo el control de mensajes, o actuar como un partner de pequeña empresa que tenga una relación de partners comerciales con una entidad empresarial mayor. Las cuentas de integración estándar admiten relaciones más complejas entre empresas y permiten aumentar el número de entidades que se pueden administrar. Para obtener más información, consulte [Precios de Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Pasos siguientes

* [Más información acerca de Logic Apps][whatis]
* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

