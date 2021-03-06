---
title: Información sobre el descuento de Azure Reservations | Microsoft Docs
description: Aprenda cómo se aplica un descuento en la reserva a las instancias de SQL Database en ejecución.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: ee73cb3164ce59136dd268853b8caa967a6f42e9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393391"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Aprenda cómo se aplica un descuento en la reserva de Azure a las instancias de SQL Database

Después de comprar capacidad reservada en Azure SQL Database, el descuento en la reserva se aplica automáticamente a las instancias que coincidan con los atributos y la cantidad de la reserva. Una reserva cubre los costos de proceso de la instancia de SQL Database. Se le cobra por el software, el almacenamiento y la administración de redes según las tarifas normales. Puede cubrir los costos de licencia de las instancias de SQL Database con [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para las instancias reservadas de máquina virtual, consulte el artículo de [información sobre el descuento en las instancias reservadas de máquina virtual de Azure](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Descuento en la reserva aplicado a las instancias de SQL Database

 El descuento sobre la capacidad reservada de SQL Database se aplica a las instancias en ejecución por hora. La reserva que compra coincide con el uso de procesos que emiten las instancias de SQL Database en ejecución. Para las instancias de SQL Database que no se ejecutan durante una hora entera, la reserva se aplica automáticamente a otras instancias que coincidan con los atributos de reserva. El descuento se puede aplicar a instancias de SQL Database en ejecución simultáneamente. Si no tiene instancias de SQL Database que se ejecuten durante toda la hora que coincidan con los atributos de reserva, no obtendrá todas las ventajas del descuento en la reserva para esa hora.

En los ejemplos siguientes se muestra cómo se aplica el descuento en la capacidad reservada de SQL Database en función del número de núcleos adquiridos y su momento de ejecución.

- Escenario 1: compra capacidad reservada de SQL Database para una instancia de 8 núcleos. Ejecuta una instancia de SQL Database con 16 núcleos que coincide con el resto de los atributos de la reserva. Se le cobra el precio de pago por uso de 8 núcleos por el proceso de SQL Database. Obtiene el descuento en la reserva para una hora de uso de proceso para una instancia de SQL Database de 8 núcleos.

En el resto de estos ejemplos, supongamos que la capacidad reservada de SQL Database que compra es para una instancia de 16 núcleos y el resto de atributos de la reserva coinciden con las instancias de SQL Database en ejecución.

- Escenario 2: ejecuta dos instancias de SQL Database con 8 núcleos cada una durante una hora. Se aplica el descuento en la reserva para 16 núcleos al uso de proceso para ambas instancias de SQL Database de 8 núcleos.
- Escenario 3: ejecuta una instancia de SQL Database de 16 núcleos de las 13:00 a las 13:30. Ejecuta otra instancia de SQL Database de 16 núcleos de las 13:30 a las 14:00. Ambas están cubiertas por el descuento en la reserva.
- Escenario 4: ejecuta una instancia de SQL Database de 16 núcleos de las 13:00 a las 13:45. Ejecuta otra instancia de SQL Database de 16 núcleos de las 13:30 a las 14:00. Se le cobra el precio de pago por uso por el solapamiento de 15 minutos. El descuento en la reserva se aplica al uso de proceso por el resto del tiempo.

Para obtener información sobre la aplicación de Azure Reservations en informes de uso de facturación, consulte el artículo [Información sobre el uso de Azure Reservations](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de Azure Reservations, consulte los siguientes artículos:

- [¿Qué es Azure Reservations?](billing-save-compute-costs-reservations.md)
- [Pago por adelantado de máquinas virtuales con Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pago por adelantado por recursos de proceso de SQL Database con capacidad reservada de Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Administración de Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Información sobre el uso de reservas para suscripciones de pago por uso](billing-understand-reserved-instance-usage.md)
- [Información sobre el uso de reservas para la inscripción Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Información sobre el uso de reservas para suscripciones de CSP](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si tiene más preguntas, [póngase en contacto con el soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
