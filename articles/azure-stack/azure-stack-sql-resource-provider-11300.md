---
title: Notas de la versión del proveedor de recursos SQL de Azure Stack 1.1.30.0 | Microsoft Docs
description: Obtenga información sobre las novedades de la actualización del proveedor de recursos SQL de Azure Stack más reciente, incluidos los problemas conocidos y dónde descargarla.
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
ms.date: 11/14/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: d2dda25c63a6e4a73205b9e4a830a211d025b3ed
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688282"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Notas de la versión del proveedor de recursos SQL 1.1.30.0

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras y los problemas conocidos de la versión del proveedor de recursos SQL 1.1.30.0.

## <a name="build-reference"></a>Referencia de compilación
Descargue el archivo binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack. La versión mínima de Azure Stack necesaria para instalar esta versión del proveedor de recursos SQL se muestra a continuación:

> |Versión mínima de Azure Stack|Versión del proveedor de recursos SQL|
> |-----|-----|
> |Versión 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Aplique la actualización de Azure Stack compatible mínima para el sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar la versión más reciente del proveedor de recursos SQL.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Esta versión del proveedor de recursos SQL de Azure Stack incluye las siguientes mejoras y correcciones:

- **Telemetría habilitada para las implementaciones del proveedor de recursos SQL**. La colección de telemetría se ha habilitado para las implementaciones del proveedor de recursos SQL. La telemetría recopilada incluye la implementación del proveedor de recursos, las horas de inicio y fin, el estado de salida, los mensajes de salida y los detalles del error (si procede).

- **Actualización de cifrado TLS 1.2**. Se ha habilitado la compatibilidad solo de TLS 1.2 para la comunicación del proveedor de recursos con los componentes internos de Azure Stack. 

### <a name="fixes"></a>Correcciones

- **Compatibilidad del proveedor de recursos SQL con PowerShell de Azure Stack**. El proveedor de recursos SQL se ha actualizado para funcionar con el perfil de PowerShell de Azure Stack 2018-03-01-hybrid y para proporcionar compatibilidad con AzureRM 1.3.0 y versiones posteriores.

- **Hoja de cambio de contraseña de inicio de sesión SQL**. Se ha corregido un problema que impedía cambiar la contraseña en la hoja de cambio de contraseña. Se han quitado los vínculos de las notificaciones de cambio de contraseña.

- **Actualización de la hoja de configuración del servidor de hospedaje SQL**. Se ha corregido un problema en que la hoja de configuración estaba titulada incorrectamente como "Password".

## <a name="known-issues"></a>Problemas conocidos 

- **Las SKU de SQL pueden tardar hasta una hora en estar visibles en el portal**. Las SKU recién creadas pueden tardar hasta una hora en estar visible para su uso al crear nuevas bases de datos SQL. 

    **Solución alternativa**: ninguna.

- **Inicios de sesión SQL reutilizados**. Al intentar crear un nuevo inicio de sesión SQL con el mismo nombre de usuario que un inicio de sesión existente en la misma suscripción, se reutilizará el mismo inicio de sesión y la contraseña existente. 

    **Solución alternativa**: utilizar diferentes nombres de usuario al crear nuevos inicios de sesión en la misma suscripción o crear inicios de sesión con el mismo nombre de usuario en diferentes suscripciones.

- **Los inicios de sesión SQL compartidos causan incoherencia de datos**. Si se comparte un inicio de sesión SQL para varias bases de datos SQL en la misma suscripción, cambiar la contraseña de inicio de sesión provocará una incoherencia de datos.

    **Solución alternativa**: usar siempre inicios de sesión diferentes para distintas bases de datos en la misma suscripción.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure Stack
Consulte la documentación de las [notas de la versión de Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Pasos siguientes
[Obtener más información sobre el proveedor de recursos SQL](azure-stack-sql-resource-provider.md).

[Preparar la implementación del proveedor de recursos SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Actualizar el proveedor de recursos SQL desde una versión anterior](azure-stack-sql-resource-provider-update.md). 