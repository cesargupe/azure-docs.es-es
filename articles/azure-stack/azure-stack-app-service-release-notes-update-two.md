---
title: Notas de la versión de App Service en la actualización 2 de Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 2 para App Service en Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: sethm
ms.openlocfilehash: 9f320f508fd45b6ad70b1357e873663796825621
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078807"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Notas de la versión de App Service en la actualización 2 de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras y correcciones de la actualización 2 de Azure App Service en Azure Stack y todos los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1804 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 2 de App Service en Azure Stack es **72.0.13698.10**

### <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Las nuevas implementaciones de Azure App Service en Azure Stack requieren ahora un [certificado comodín con tres firmantes](azure-stack-app-service-before-you-get-started.md#get-certificates) debido a las mejoras en la forma en que se trata SSO para Kudu en este momento en Azure App Service. El nuevo firmante es **\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>**
>
>

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 2 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - Se agregó .Net Framework 4.7.1
  - Versiones de **Node.JS** agregadas:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Versiones de **NPM** agregadas:
    - 5.6.0
  - Se actualizaron los componentes .Net Core para que sean consistentes con Azure App Service en la nube pública.
  - Kudu actualizado

- Característica Intercambio automático de ranuras de implementación habilitada: [configuración de intercambio automático](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Característica Testing in Production habilitada: [introducción a Testing in Production](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure Functions Proxies habilitados: [trabajo con Azure Functions Proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- Se agregó compatibilidad UX con la extensión de administración de App Service para:
  - Rotación de secretos
  - Rotación de certificados
  - Rotación de credenciales de sistema
  - Rotación de cadenas de conexión

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Para ello, vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:
 * Origen: Cualquiera
 * Intervalo de puertos de origen: *
 * Destino: Direcciones IP
 * Intervalo de direcciones IP de destino: intervalo de direcciones IP del servidor de archivos
 * Intervalo de puertos de destino: 445
 * Protocolo: TCP
 * Acción: Permitir
 * Prioridad: 700
 * Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1804](azure-stack-update-1804.md)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
