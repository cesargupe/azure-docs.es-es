---
title: Acerca de VPN Gateway para Azure Stack | Microsoft Docs
description: Obtenga información y configure las puertas de enlace VPN que se utilizan con Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: d94b7f7f2f032278f71053e5de8bb2935af01f3f
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300156"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Acerca de VPN Gateway para Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Antes de poder enviar tráfico de red entre una red virtual de Azure y un sitio local, es preciso crear una puerta de enlace de red virtual para la red virtual.

Una puerta de enlace VPN es un tipo de puerta de enlace de red virtual que envía tráfico cifrado a través de una conexión pública. Puede usar las puertas de enlace de VPN para enviar tráfico de forma segura entre una red virtual en Azure Stack y una red virtual en Azure. También puede enviar tráfico de forma segura entre una red virtual y otra red conectada a un dispositivo VPN.

Al crear una puerta de enlace de red virtual, debe especificar el tipo de puerta de enlace que desea crear. Azure Stack admite un tipo de puerta de enlace de red virtual: el tipo **Vpn**.

Cada red virtual solo puede tener dos puertas de enlace de red virtual, pero solo una de cada tipo. Según la configuración que elija, puede crear varias conexiones a una única instancia de VPN Gateway. Un ejemplo de esto es la configuración de conexión multisitio.

Antes de crear y configurar instancias de VPN Gateway para Azure Stack, revise las [consideraciones para los servicios de red de Azure Stack](user/azure-stack-network-differences.md) para información sobre las diferencias de las configuraciones de Azure Stack y Azure.

>[!NOTE]
>En Azure, el rendimiento de ancho de banda de la SKU de VPN Gateway que elija debe dividirse entre todas las conexiones que están conectadas a la puerta de enlace. Pero en Azure Stack, el valor de ancho de banda de la SKU de VPN Gateway se aplica a cada recurso de conexión que está conectado a él.
>
> Por ejemplo:
> * En Azure, la SKU de VPN Gateway de nivel Básico puede alojar aproximadamente 100 Mbps de rendimiento agregado. Si crea dos conexiones a una instancia de VPN Gateway y una de ellas usa 50 Mbps de ancho de banda, la otra dispone de 50 Mbps.
> * En Azure Stack, a *cada* conexión a una SKU de VPN Gateway de nivel Básico se le asignan 100 Mbps de rendimiento.

## <a name="configuring-a-vpn-gateway"></a>Configuración de una instancia de VPN Gateway

Una conexión de puerta de enlace de VPN se basa en varios recursos con una configuración específica. La mayoría de los recursos puede configurarse de manera independiente, pero en algunos casos deben estar configurados según un orden determinado.

### <a name="settings"></a>Configuración

La configuración que ha elegido para cada recurso es fundamental para crear una conexión correcta.

Para más información acerca de los recursos individuales y la configuración de VPN Gateway, consulte [VPN gateway configuration settings for Azure Stack](azure-stack-vpn-gateway-settings.md) (Acerca de la configuración de VPN Gateway para Azure Stack). Este artículo permitirá entender:

* Tipos de puerta de enlace, tipos de VPN y tipos de conexión.
* Subredes de puerta de enlace, puertas de enlace de red locales y otras configuraciones de recursos que quiera considerar.

### <a name="deployment-tools"></a>Herramientas de implementación

Puede crear y configurar recursos mediante una herramienta de configuración, como Azure Portal. Más adelante, puede usar otra herramienta, como PowerShell, para configurar recursos adicionales o para modificar los existentes cuando sea necesario. Actualmente, no se pueden configurar todos los recursos ni establecer todas las configuraciones de recurso en Azure Portal. Las instrucciones de los artículos para cada topología de configuración indican cuándo se necesita una herramienta de configuración específica.

## <a name="connection-topology-diagrams"></a>Diagramas de topologías de conexión

Es importante saber que hay distintas configuraciones disponibles para las conexiones de VPN Gateway. Es preciso determinar qué configuración es la que mejor se adapta a sus necesidades. En las secciones siguientes, puede ver diagramas de topología e información sobre las conexiones de puerta de enlace de VPN siguientes:

* Modelo de implementación disponible
* Herramientas de configuración disponibles
* Vínculos que llevan directamente a un artículo, si lo hay

Los diagramas y las descripciones de las siguientes secciones pueden ayudarle a seleccionar la topología de conexión que mejor se ajuste a sus requisitos. Los diagramas muestran las principales topologías de referencia, pero también se pueden crear configuraciones más complejas con los diagramas como guía.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Sitio a sitio y multisitio (túnel VPN de IPsec/IKE)

### <a name="site-to-site"></a>De sitio a sitio

Una conexión de puerta de enlace de VPN de sitio a sitio (S2S) es una conexión a través de un túnel VPN IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN que esté ubicado en el entorno local y tenga asignada una dirección IP pública. Este dispositivo no puede estar detrás de un NAT. Se pueden usar conexiones S2S para las configuraciones híbridas y entre locales.

![Ejemplo de configuración de una conexión VPN de sitio a sitio](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multisitio

Este tipo de conexión es una variación de la conexión de sitio a sitio. Puede crear más de una conexión VPN desde la puerta de enlace de red virtual, normalmente conectándose a varios sitios locales. Cuando trabaje con varias conexiones, debe usar una VPN de tipo RouteBased (conocida como puerta de enlace dinámica al trabajar con redes virtuales clásicas). Como cada red virtual solo puede tener una puerta de enlace de red virtual, todas las conexiones a través de la puerta de enlace comparten el ancho de banda disponible. A menudo, esto se llama conexión "multisitio".

![Ejemplo de conexión multisitio de Azure VPN Gateway](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>SKU de puerta de enlace

Al crear una puerta de enlace de red virtual para Azure Stack, especifique la SKU de la puerta de enlace que desea usar. Se admiten las siguientes SKU de VPN Gateway:

* Básica
* Estándar
* HighPerformance

Cuando se selecciona una SKU de puerta de enlace superior, como Estándar con respecto a Básica o HighPerformance con respecto a Estándar o Básica, se asignan más CPU y ancho de banda de red a la puerta de enlace. Como consecuencia, la puerta de enlace puede admitir un mayor rendimiento de red a la red virtual.

Azure Stack no admite la SKU de puerta de enlace Ultrarrendimiento, que se utiliza exclusivamente con Express Route.

Tenga en cuenta la siguiente información cuando seleccione la SKU:

* Azure Stack no admite puertas de enlace basadas en directivas.
* El Protocolo de puerta de enlace de borde (BGP) no se admite en la SKU de nivel Básico.
* Las configuraciones de la coexistencia de ExpressRoute-VPN Gateway no se admiten en Azure Stack.
* Las conexiones de VPN Gateway S2S activo/activo solo pueden configurarse en la SKU HighPerformance.

## <a name="estimated-aggregate-throughput-by-sku"></a>Rendimiento agregado estimado por SKU

En la tabla siguiente se muestran los tipos de puerta de enlace y el rendimiento agregado estimado por SKU de puerta de enlace.

|   | Rendimiento de VPN Gateway *(1)* | Túneles IPsec máx. de VPN Gateway *(2)* |
|-------|-------|-------|
|**SKU Básica** ***(3)***    | 100 Mbps  | 10    |
|**SKU estándar**       | 100 Mbps  | 10    |
|**SKU de alto rendimiento** | 200 Mbps    | 5 |

**Notas de la tabla:**

*Nota (1)*: El rendimiento de la VPN no está garantizado para las conexiones entre locales a través de Internet. Es el valor máximo posible del rendimiento.  
*Nota (2)*: El número de túneles máximo es el total por cada implementación de Azure Stack para TODAS las suscripciones.  
*Nota (3)*: el enrutamiento de BGP no es compatible con la SKU Básica.

## <a name="next-steps"></a>Pasos siguientes

[Valores de la configuración de una puerta de enlace VPN para Azure Stack](azure-stack-vpn-gateway-settings.md)
