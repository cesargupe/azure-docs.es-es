---
title: Notas de la versión de SDK de Azure para .NET 2.6
description: Notas de la versión de SDK de Azure para .NET 2.6
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 040eea1efd3abdbbfa65c24b2950ebece9d4acbc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258398"
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Notas de la versión de SDK de Azure para .NET 2.6
Este documento contiene las notas de la versión de SDK de Azure para la versión .NET 2.6. 

Con SDK de Azure 2.6 puede desarrollar aplicaciones de servicio en la nube (PaaS) destinadas a .NET 4.5.2 o a .NET 4.6, siempre que instale de manera manual .NET Framework de destino en el rol de Servicio en la nube. Consulte [Instalar .NET en un rol de Servicio en la nube](https://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Actualizaciones de Service Bus
* Event Hubs: 
  
  * Ahora permite el control de acceso dirigido cuando se envían eventos mediante la exposición de un extremo de publicador adicional para Event Hubs.
  * Se agrega una estabilidad y mejora adicional a la característica Event Hubs.
  * Mayor compatibilidad para el protocolo Amqp sobre WebSocket para mensajería y Event Hubs.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Actualizaciones de herramientas de HDInsight para Visual Studio
* **Mejora de IntelliSense**: sugerencia de metadatos remotos
  
    Las Herramientas de HDInsight para Visual Studio ahora son compatibles con la obtención de metadatos remotos cuando se edita el script de Hive. Por ejemplo, puede escribir **SELECT * FROM** y se mostrarán todos los nombres de las tablas. Además, los nombres de las columnas aparecerán después de especificar una tabla.
* **Compatibilidad con el emulador de HDInsight**
  
    Las Herramientas de HDInsight para Visual Studio ahora admiten conectarse al emulador de HDInsight, de manera que puede desarrollar los scripts de Hive de manera local, sin introducir coste alguno y, a continuación, ejecutar dichos scripts en los clústeres de HDInsight. 
  
    Para obtener más información, consulte [este manual](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Las Herramientas de HDInsight para Visual Studio admiten clústeres genéricos de Hadoop** (vista previa)
  
    Las Herramientas de HDInsight para Visual Studio ahora son compatibles con clústeres genéricos de Hadoop, por lo que puede usarlas para:
  
  * conectarse al clúster, 
  * escribir la consulta de Hive con compatibilidad de finalización automática/IntelliSense mejorada, 
  * ver todos los trabajos del clúster con una interfaz de usuario intuitiva. 
    
    Para obtener más información, consulte [este manual](https://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Actualizaciones de Caché en rol
* **Caché en rol** se actualizó para utilizar la versión 4.3 del **SDK de Microsoft Azure Storage**. Hasta ahora, **In-Role Cache** usaba la versión 1.7 del SDK de Azure Storage.
  
    Los clientes que utilizan el SDK de Azure 2.5 o anterior deberán actualizar al SDK de Azure 2.6 y cambiar a la versión nueva del SDK de Azure Storage. 
  
    En este momento la versión 2011-08-18 de Azure Storage está programada para que se elimine el 1 de agosto de 2016. Todas las migraciones de caché en rol de Azure SDK 2.5 o versión anterior a 2.6 deberán haberse realizado en ese momento. Para obtener más información sobre la retirada de Azure Storage versión 2011-08-18, consulte [Actualización de la eliminación de la versión del servicio Microsoft Azure Storage: extensión hasta 2016](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> El 30 de noviembre de 2016 se retirará Azure Managed Cache Service y la Caché en rol de Azure. Se recomienda que migre a Azure Redis Cache con vistas a prepararse para la mencionada retirada. Para obtener más información sobre las fechas y la guía de migración, consulte [¿Qué oferta de caché de Azure es adecuada para mí?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Herramientas de Azure App Service
En la versión 2.6 del SDK de Azure se actualizaron los siguientes elementos.

* Se mejoró la publicación en Azure para incluir Aplicaciones de API de Azure como destino de la implementación.
* Funcionalidad de aprovisionamiento de Aplicaciones de API para permitir usuarios con la funcionalidad de aprovisionamiento y creación de Aplicación de API.
* Explorador de servidores cambió para reflejar un nuevo nodo de App Service, con aplicaciones web, aplicaciones móviles y aplicaciones de API agrupadas por Grupo de recursos.
* Se agregó la funcionalidad de gesto del cliente de Aplicaciones de API de Azure a la mayoría de los proyectos de C# que permitirá la generación automática de Aplicaciones de API compatibles con Swagger en la suscripción de Azure de un usuario.
* Las herramientas de Aplicaciones de API y los nodos de App Service en el Explorador de servidores solo se encuentran disponibles en Visual Studio 2013. 

## <a name="azure-resource-manager-tools-updates"></a>Actualizaciones de las herramientas del Administrador de recursos de Azure
Las herramientas de Azure Resource Manager se actualizaron para incluir las plantillas para máquinas virtuales, redes y almacenamiento. La experiencia de edición de JSON se actualizó para incluir una nueva vista de esquema para plantillas y la capacidad de editar las plantillas mediante fragmentos de código JSON. Las plantillas implementadas desde Visual Studio usan un script de PowerShell proporcionadas con el proyecto, de manera tal que Visual Studio usará cualquier cambio realizado en el script.

## <a name="diagnostics-improvements-for-cloud-services"></a>Mejoras de diagnósticos para Cloud Services
El SDK de Azure 2.6 vuelve a incluir la compatibilidad para recopilar registros de diagnóstico en el emulador de proceso de Azure y transferirlos al almacenamiento de desarrollo. Todos los registros de diagnósticos (incluidos registros de seguimiento de aplicaciones, registros de seguimiento de eventos para Windows (ETW), contadores de rendimiento, registros de infraestructura y registros de eventos de Windows) que se generan cuando la aplicación se ejecuta en el emulador se pueden transferir al almacenamiento de desarrollo para comprobar que el registro de diagnósticos funciona en la máquina local. 

La cuenta de almacenamiento de diagnósticos ahora se puede especificar en el archivo de configuración de servicio (.cscfg), lo que facilita la utilización de distintas cuentas de almacenamiento de diagnósticos en diferentes entornos. Existen algunas diferencias importantes entre el funcionamiento de la cadena de conexión en el SDK de Azure 2.4 y el SDK de Azure 2.6. Para obtener más información sobre cómo usar la cadena de conexión del almacenamiento de diagnóstico y cómo afecta sus proyectos, consulte [Configuración de los diagnósticos para Azure Cloud Services](https://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Cambios drásticos
### <a name="azure-resource-manager-tools"></a>Herramientas del Administrador de recursos de Azure
* El tipo de proyecto **Proyectos de implementación de la nube** disponible en el SDK de Azure 2.5 cambió su nombre a **Grupo de recursos de Azure**.
* **Proyectos de implementación de la nube** creado en el SDK de Azure 2.5 se puede utilizar en la versión 2.6, pero implementar la plantilla desde Visual Studio generará un error. Sin embargo, la implementación con el script de PowerShell seguirá funcionando como anteriormente lo hacía.  Para obtener información sobre cómo usar los **Proyectos de implementación de la nube** en la versión 2.6, lea esta [publicación](https://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Problemas conocidos
* Recopilar registros de diagnósticos en el emulador requiere un sistema operativo de 64 bits. Los registros de diagnósticos no se recopilarán cuando se ejecuten en un sistema operativo de 32 bits. Esto no afecta ninguna otra funcionalidad del emulador. 
* La versión del SDK 2.6 de Azure del 29 de abril de 2015 presentaba dos problemas: 
  
  * No se podía cargar la aplicación universal en Visual Studio 2015 cuando el SDK 2.6 de Azure se instalaba en el equipo.
  * La depuración de un proyecto de servicios en la nube daba error en Visual Studio 2013 y Visual Studio 2015 y Visual Studio dejaba de responder y se bloqueaba al tiempo que mostraba un cuadro de diálogo con el mensaje "Configurando diagnósticos para el emulador".
    
    Se publicó una actualización del SDK 2.6 de Azure el 18 de mayo de 2015. La versión actualizada es 2.6.30508.1601, y contiene correcciones para los dos problemas descritos anteriormente. Puede identificar la compilación del SDK en el Panel de Control -> Programas y características -> Microsoft Azure Tools para Microsoft Visual Studio 2013 – v 2.6. La columna Versión mostrará el número de compilación.
    
    Si todavía sigue experimentando los problemas mencionados anteriormente, instale la versión más reciente del SDK 2.6 de Azure para [VS 2012](https://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](https://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o [VS 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Otras referencias
[Información de compatibilidad y retirada del SDK de Azure para .NET y API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

