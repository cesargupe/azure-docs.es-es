---
title: Introducción a Azure Key Vault | Microsoft Docs
description: Azure Key Vault es un servicio en la nube que funciona como un almacén de secretos seguro.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 07/17/2018
ms.author: barclayn
ms.openlocfilehash: aae7836448ff27b4c80d7bb53e108034ee52db1c
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586298"
---
# <a name="what-is-azure-key-vault"></a>¿Qué es Azure Key Vault?

Azure Key Vault ayuda a solucionar los problemas siguientes
- **Administración de secretos**: Azure Key Vault se puede utilizar para almacenar de forma segura y controlar estrechamente el acceso a los tokens, contraseñas, certificados, claves de API y otros secretos
- **Administración de claves**: también se puede usar Azure Key Vault como una solución de administración de claves. Azure Key Vault facilita la creación y control de las claves de cifrado utilizadas para cifrar los datos. 
- **Administración de certificados**: Azure Key Vault también es un servicio que le permite aprovisionar, administrar e implementar fácilmente certificados públicos y privados de la Capa de sockets seguros y de Seguridad de la capa de transporte (SSL/TLS) para su uso con Azure y sus recursos internos conectados. 
- **Almacenamiento de secretos con copia de seguridad realizada por los módulos de seguridad de hardware**: las claves y secretos se pueden proteger mediante software o mediante dispositivos HSM validados FIPS 140-2 de nivel 2

## <a name="why-use-azure-key-vault"></a>Motivos para usar Azure Key Vault

### <a name="centralize-application-secrets"></a>Centralización de secretos de aplicación

La centralización del almacenamiento de los secretos de aplicación le permite controlar su distribución. Key Vault reduce en gran medida las posibilidades de que se puedan filtrar por accidente los secretos. Cuando usan Key Vault, los desarrolladores de aplicaciones ya no tienen que almacenar la información de seguridad en su aplicación. Esto elimina la necesidad de hacer que esta información sea parte del código. Por ejemplo, puede que una aplicación necesite conectarse a una base de datos. En lugar de almacenar la cadena de conexión en los códigos de la aplicación, almacénela de forma segura en Key Vault.

Las aplicaciones pueden acceder de forma segura a la información que necesitan mediante identificadores URI que les permiten recuperar las versiones específicas de un secreto después de haber almacenado la clave o secreto de una aplicación en Azure Key Vault. Esto sucede sin tener que escribir código personalizado para proteger la información del secreto.

### <a name="securely-store-secrets-and-keys"></a>Almacenamiento seguro de secretos y claves

Los secretos y las claves se protegen mediante Azure, para lo que se usan algoritmos estándar del sector, longitudes de clave y módulos de seguridad de hardware (HSM). Los módulos de seguridad de hardware se validan mediante los Estándares federales de procesamiento de información (FIPS) 140-2 de nivel 2.

El acceso a un almacén de claves requiere una autorización y autenticación correctas antes de que un autor de llamada (usuario o aplicación) pueda obtener acceso. La autenticación establece la identidad del autor de la llamada, mientras que la autorización determina las operaciones que puede realizar.

La autenticación se realiza a través de Azure Active Directory. La autorización puede realizarse mediante el control de acceso basado en rol (RBAC) o la directiva de acceso de Key Vault. Se usa el control de acceso basado en rol cuando se trata de la administración de los almacenes y la directiva de acceso de Key Vault cuando se intenta acceder a los datos almacenados en un almacén.

Las instancias de Azure Key Vault se pueden proteger mediante software o con módulos de seguridad de hardware (HSM). En aquellos casos en los que necesita obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Microsoft utiliza los módulos de seguridad de hardware de Thales. Puede usar las herramientas de Thales para mover una clave desde el módulo de seguridad de hardware a Azure Key Vault.

Además, Azure Key Vault está diseñado de modo que Microsoft no pueda ver ni extraer sus datos.

### <a name="monitor-access-and-use"></a>Supervisión del acceso y uso

Una vez que ha creado un par de almacenes de claves, querrá supervisar cómo y cuándo se accede a sus claves y secretos. Para ello, puede habilitar el registro para Key Vault. Puede configurar Azure Key Vault para:

- Archivar en una cuenta de almacenamiento.
- Transmitir a un centro de eventos.
- Enviar los registros a Log Analytics.

Tiene el control sobre los registros y puede protegerlos de forma segura restringiendo el acceso y, además, puede eliminar los registros que ya no necesita.

### <a name="simplified-administration-of-application-secrets"></a>Administración simplificada de secretos de aplicación

Al almacenar datos importantes, debe realizar varios pasos. Se debe proteger la información de seguridad, esta debe seguir un ciclo de vida y debe tener alta disponibilidad. Azure Key Vault simplifica mucho este proceso mediante:

- La eliminación de la necesidad de poseer conocimientos internos sobre los módulos de seguridad de hardware (HSM).
- El escalado vertical en un breve plazo de tiempo para adaptarse a los picos de uso de la organización.
- La replicación del contenido de una instancia de Key Vault de una región en una región secundaria. Esto garantiza la alta disponibilidad y elimina la necesidad de intervención del administrador para desencadenar la conmutación por error.
- La disposición de opciones estándar de administración de Azure a través del portal, la CLI de Azure y PowerShell.
- La automatización de determinadas tareas de los certificados que adquiere de entidades de certificación públicas, como la inscripción y la renovación.

Además, Azure Key Vault le permite segregar los secretos de aplicación. Las aplicaciones solo pueden acceder al almacén para el que tengan permiso, y se puede limitar el acceso para realizar únicamente operaciones específicas. Puede crear una instancia de Azure Key Vault por aplicación y restringir los secretos almacenados en un almacén de claves a una aplicación y a un equipo de desarrolladores concretos.

### <a name="integrate-with-other-azure-services"></a>Integración con otros servicios de Azure

Como almacén seguro de Azure, Key Vault se ha usado para simplificar escenarios con [Azure Disk Encryption](../security/azure-security-disk-encryption.md), la funcionalidad [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) de SQL Server y Azure SQL Database, y [Azure Web Apps]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). Key Vault se puede integrar con cuentas de almacenamiento e instancias de Event Hubs y Log Analytics.

## <a name="next-steps"></a>Pasos siguientes

- [Guía de inicio rápido: Creación de una instancia de Azure Key Vault mediante la CLI](quick-create-cli.md)
- [Configuración de una aplicación web de Azure para que lea un secreto desde el almacén de claves](tutorial-web-application-keyvault.md)
