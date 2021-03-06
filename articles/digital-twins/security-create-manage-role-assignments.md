---
title: Descripción de la conectividad y la autenticación de dispositivos con Azure Digital Twins | Microsoft Docs
description: Uso de Azure Digital Twins para conectar y autenticar dispositivos
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: f032e3ebf6a10411057cd6d41df0cad6248f328b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636244"
---
# <a name="create-and-manage-role-assignments"></a>Creación y administración de asignaciones de roles

Azure Digital Twins usa el control de acceso basado en rol ([RBAC](./security-role-based-access-control.md)) para administrar el acceso a los recursos.

Cada asignación de roles incluye:

* **Identificador de objeto**: un identificador de Azure Active Directory, el identificador de objeto de la entidad de servicio o el nombre de dominio
* **Tipo de identificador de objeto**
* **Identificador de definición de roles**
* **Ruta de acceso al espacio**
* **Identificador de inquilino**: en la mayoría de casos, un identificador de inquilino de Azure Active Directory

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="role-definition-identifiers"></a>Identificadores de definición de roles

En la siguiente tabla se muestra lo que se puede obtener al consultar las API de sistema o de roles.

| **Rol** | **Identificador** |
| --- | --- |
| Administrador del espacio | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrador de usuarios| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrador de dispositivos | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrador de claves | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrador de tokens | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Usuario | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Especialista de soporte técnico | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalador de dispositivos | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Dispositivo de puerta de enlace | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>ObjectIdTypes admitidos

Los `ObjectIdTypes` admitidos:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Crear una asignación de rol

```plaintext
HTTP POST YOUR_MANAGEMENT_API_URL/roleassignments
```

| **Nombre** | **Obligatorio** | **Tipo** | **Descripción** |
| --- | --- | --- | --- |
| roleId| SÍ |string | Identificador de definición de rol. Busque las definiciones de roles y sus identificadores consultando la API de sistema. |
| objectId | SÍ |string | Identificador de objeto para la asignación de roles que debe tener el formato en función del tipo asociado. Para el ObjectIdType `DomainName`, ObjectId debe comenzar con el carácter `“@”`. |
| objectIdType | SÍ |string | Tipo de asignación de roles. Debe ser una de las siguientes filas de la tabla. |
| tenantId | Varía | string |Identificador de inquilino. No se permite para los ObjectIdType `DeviceId` y `TenantId`. Obligatorio para los ObjectIdType `UserId` y `ServicePrincipalId`. Opcional para el ObjectIdType DomainName. |
| path* | SÍ | string |Ruta de acceso completa al objeto `Space`. Un ejemplo es `/{Guid}/{Guid}`. Si un identificador necesita la asignación de roles para todo el grafo, especifique `"/"`. Este carácter designa la raíz, pero se desaconseja su uso. Siga siempre el principio de privilegio mínimo. |

## <a name="sample-configuration"></a>Configuración de ejemplo

En este ejemplo, un usuario necesita permiso del administrador para acceder a una planta del espacio del inquilino.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

En este ejemplo, una aplicación que ejecuta simulaciones de dispositivos y sensores en escenarios de prueba.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Todos los usuarios que forman parte de un dominio reciben acceso de lectura para los usuarios, sensores y espacios. Este acceso incluye sus correspondientes objetos relacionados.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Use GET para obtener una asignación de roles.

```plaintext
HTTP GET YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| **Nombre** | **In** | **Obligatorio** |    **Tipo** |  **Descripción** |
| --- | --- | --- | --- | --- |
| YOUR_PATH | Ruta de acceso | True | string |    Ruta de acceso completa al espacio |

Use DELETE para eliminar una asignación de roles.

```plaintext
HTTP DELETE YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ID
```

| **Nombre** | **In** | **Obligatorio** | **Tipo** | **Descripción** |
| --- | --- | --- | --- | --- |
| YOUR_ROLE_ID | Ruta de acceso | True | string | Identificador de la asignación de roles |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la seguridad de Azure Digital Twins, consulte el artículo sobre la [autenticación en las API](./security-authenticating-apis.md).
