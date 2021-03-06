---
title: Planeamiento de la capacidad de proceso de Azure Stack | Microsoft Docs
description: Obtenga más información sobre el planeamiento de la capacidad de proceso para las implementaciones de Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.custom: mvc
ms.openlocfilehash: b98879483d35a91810c9e9ab5e0ac81151bde52f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369751"
---
# <a name="azure-stack-compute-capacity-planning"></a>Planeamiento de la capacidad de proceso de Azure Stack
Los [tamaños de máquinas virtuales admitidos en Azure Stack](.\user\azure-stack-vm-sizes.md) son un subconjunto de los admitidos por Azure. Azure impone límites de recursos junto con varios vectores para evitar el consumo excesivo de recursos (nivel de servicio y local del servidor). Sin la imposición de algunos límites sobre el consumo del inquilino, las experiencias de este se verán afectadas cuando otros inquilinos consuman recursos en exceso. Para la salida de redes de la máquina virtual, hay extremos de ancho de banda en Azure Stack que coinciden con las limitaciones de Azure. En el caso de los recursos de almacenamiento, los límites de IOPS de almacenamiento se han implementado en Azure Stack para evitar el consumo excesivo básico de recursos por parte de los inquilinos para el acceso de almacenamiento.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Selección de ubicación de la máquina virtual y aprovisionamiento en exceso del núcleo virtual en el núcleo físico
En Azure Stack, no hay ninguna manera de que un inquilino especifique un servidor determinado para usarlo para la selección de ubicación de la máquina virtual. La única consideración que se debe tener al colocar las máquinas virtuales es si hay suficiente memoria en el host para ese tipo de máquina virtual. Azure Stack no excede la asignación de memoria. Sin embargo, se permite una asignación excesiva del número de núcleos. Dado que los algoritmos de selección de ubicación no observan la relación de aprovisionamiento en exceso del núcleo virtual en el núcleo físico existente como un factor, cada host podría tener una relación diferente. 

En Azure, para conseguir la alta disponibilidad de un sistema de producción con varias máquinas virtuales, las máquinas virtuales se colocan en un conjunto de disponibilidad para que se distribuyan a varios dominios de error. Esto significa que las máquinas virtuales colocadas en un conjunto de disponibilidad se aíslan físicamente entre sí en bastidores para permitir la resistencia ante errores, tal como se muestra en el diagrama siguiente:

![Dominios de error y de actualización](media\azure-stack-capacity-planning\domains.png)


Si bien la infraestructura de Azure Stack es resistente ante errores, la tecnología subyacente (clústeres de conmutación por error) de todos modos tiene cierto tiempo de inactividad de las máquinas virtuales en un servidor físico que se ve afectado en la eventualidad de que se produzca un error de hardware. Actualmente, Azure Stack admite un conjunto de disponibilidad con un máximo de tres dominios de error para coherencia con Azure. Las máquinas virtuales colocadas en conjuntos de disponibilidad se aislarán físicamente entre sí al distribuirlas de la manera más uniforme que sea posible en varios dominios de error (nodos de Azure Stack). Si se produce un error de hardware, las máquinas virtuales del dominio de error que presente el error se reiniciarán en otros nodos, pero, si es posible, se mantendrán en dominios de error independientes de las otras máquinas virtuales que se encuentran en el mismo conjunto de disponibilidad. Cuando el hardware vuelva a estar en línea, las máquinas virtuales se volverán a equilibrar para mantener la alta disponibilidad.

Otro concepto que se usa en Azure para proporcionar una alta disponibilidad es en forma de dominios de actualización de conjuntos de disponibilidad. Un dominio de actualización es un grupo lógico de hardware subyacente que puede someterse a mantenimiento o reiniciarse al mismo tiempo. En Azure Stack, las máquinas virtuales se migran en vivo entre los otros hosts en línea del clúster antes de que se actualice su host subyacente. Como no hay tiempo de inactividad para el inquilino durante una actualización del host, la característica de dominio de actualización de Azure Stack solo existe para compatibilidad de plantilla con Azure.

## <a name="azure-stack-resiliency-resources"></a>Recursos de resistencia de Azure Stack
Para permitir la revisión y la actualización de un sistema integrado de Azure Stack, así como para ofrecer resistencia ante errores de hardware físico, una parte de la memoria total del servidor se reserva y no está disponible para la selección de ubicación de máquinas virtuales (VM) del inquilino.

Si se produce un error en un servidor, las máquinas virtuales hospedadas en el servidor en el que se produjo el error se reiniciarán en los servidores restantes que estén disponibles para proporcionar disponibilidad de máquinas virtuales. De forma similar, durante el proceso de revisión y actualización, todas las máquinas virtuales que se ejecutan en un servidor migrarán a otro servidor disponible. Este movimiento o administración de máquinas virtuales solo se puede lograr si hay capacidad reservada para permitir el reinicio o la migración que se produzca.

El siguiente cálculo da como resultado la memoria total disponible que se puede usar para la selección de ubicación de la máquina virtual del inquilino. Esta capacidad de memoria es para la totalidad de la unidad de escalado de Azure Stack.

  Memoria disponible para la selección de ubicación de la máquina virtual = memoria total del servidor – Reserva de resistencia – Sobrecarga de la infraestructura de Azure Stack<sup>1</sup>

  Reserva de resistencia = H + R * (N-1) + V * (N-2)

> Donde:
> - H = tamaño de la memoria de un solo servidor
> - N = tamaño de la unidad de escalado (número de servidores)
> - R = reserva del sistema operativo para la sobrecarga del sistema operativo<sup>2</sup>
> - V = máquina virtual más grande de la unidad de escalado

  <sup>1</sup>Sobrecarga de la infraestructura de Azure Stack = 208 GB

  <sup>2</sup>Reserva del sistema operativo para la sobrecarga = 15 % de la memoria del nodo. El valor de reserva del sistema operativo es un valor estimado y puede variar según la capacidad de memoria física del servidor y la sobrecarga general del sistema operativo.

El valor V, la máquina virtual más grande de la unidad de escalado, se basa dinámicamente en el tamaño más grande de memoria de la máquina virtual del inquilino. Por ejemplo, el valor de la máquina virtual más grande podría ser 7 GB o 112 GB, así como cualquier otro tamaño de memoria de la máquina virtual admitido en la solución Azure Stack.

El cálculo anterior es un valor estimado y está sujeto a cambios en función de la versión actual de Azure Stack. La capacidad de implementar servicios y máquinas virtuales del inquilino se basa en los detalles de la solución implementada. Este cálculo de ejemplo es una guía simplemente y no la respuesta absoluta de la capacidad de implementar máquinas virtuales.



## <a name="next-steps"></a>Pasos siguientes
[Planeamiento de la capacidad de almacenamiento](capacity-planning-storage.md)
