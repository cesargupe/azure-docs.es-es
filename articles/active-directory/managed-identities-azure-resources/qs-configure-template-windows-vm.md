---
title: Cómo configurar identidades administradas para recursos de Azure en una VM de Azure mediante una plantilla
description: Instrucciones detalladas para configurar identidades administradas para recursos de Azure en una VM de Azure mediante una plantilla de Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 78920bfe000287daef7b4efcaa8339d599d6f57e
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578508"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Configurar identidades administradas para recursos de Azure en una VM de Azure mediante plantillas

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Las identidades administradas para los recursos de Azure proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, con la plantilla de implementación de Azure Resource Manager, aprenderá a usar las siguientes entidades administradas para operaciones de recursos de Azure en una VM de Azure:

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con el uso de la plantilla de implementación de Azure Resource Manager, consulte la [sección de información general](overview.md). **No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure

Al igual que con Azure Portal y los scripts, las plantillas de [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) proporcionan la capacidad de implementar recursos nuevos o modificados definidos por un grupo de recursos de Azure. Existen varias opciones para la edición e implementación de plantillas, tanto localmente como basadas en el portal, incluidas:

   - Usar una [plantilla personalizada de Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite crear una plantilla desde cero o basada en una común existente o [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar a partir de un grupo de recursos existente, exportando una plantilla de [la implementación original](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) o del [estado actual de la implementación](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar un [editor de JSON (por ejemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md) local y, a continuación, cargarla e implementarla con PowerShell o la CLI.
   - Usar el [proyecto del grupo de recursos de Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) de Visual Studio tanto para crear como para implementar una plantilla.  

Independientemente de la opción que elija, la sintaxis de la plantilla es la misma durante la implementación inicial y posteriores implementaciones. La habilitación de una identidad administrada asignada por el sistema o el usuario en una VM existente o nueva se realiza de la misma forma. Además, de forma predeterminada, Azure Resource Manager realiza una [actualización incremental](../../azure-resource-manager/deployment-modes.md) en las implementaciones.

## <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema

En esta sección, se habilita y deshabilita una identidad administrada asignada por el sistema con una plantilla de Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitación de una identidad administrada asignada por el sistema durante la creación de una VM o en una VM existente de Azure

Para habilitar una identidad administrada asignada por el sistema en una máquina virtual, la cuenta necesita la asignación de roles [Colaborador de la máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  No se requiere ninguna otra asignación de roles de directorio de Azure AD.

1. Independientemente de que inicie sesión localmente en Azure o mediante Azure Portal, use una cuenta que esté asociada a la suscripción de Azure que contiene la máquina virtual.

2. Para habilitar la identidad administrada asignada por el sistema, cargue la plantilla en un editor, busque el recurso de interés `Microsoft.Compute/virtualMachines` en la sección `resources` y agregue la propiedad `"identity"` en el mismo nivel que la propiedad `"type": "Microsoft.Compute/virtualMachines"`. Use la sintaxis siguiente:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

3. (Opcional) Agregue las identidades administradas de VM para la extensión de recursos de Azure como elemento `resources`. Este paso es opcional, ya que puede usar el punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens.  Use la sintaxis siguiente:

   >[!NOTE] 
   > En el ejemplo siguiente se da por hecho que se está implementando una extensión (`ManagedIdentityExtensionForWindows`) de máquina virtual Windows. También puede configurarlo para Linux utilizando `ManagedIdentityExtensionForLinux` en su lugar, para los elementos `"name"` y `"type"`. Está planeado que la extensión de máquina virtual quede en desuso en enero de 2019.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2018-06-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

4. Cuando haya terminado, deben agregarse las siguientes secciones a la sección `resource` de la plantilla para que se parezca a la siguiente:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Asignación de un rol a la identidad administrada asignada por el sistema de la VM

Después de haber habilitado la identidad administrada asignada por el sistema en la VM, es aconsejable concederle un rol con el acceso de **lector** al grupo de recursos en el que se creó.

Para asignar un rol a una identidad asignada por el sistema de la máquina virtual, la cuenta debe tener la asignación del rol [Administrador de acceso de usuario](/azure/role-based-access-control/built-in-roles#user-access-administrator).

1. Independientemente de que inicie sesión localmente en Azure o mediante Azure Portal, use una cuenta que esté asociada a la suscripción de Azure que contiene la máquina virtual.
 
2. Cargue la plantilla en un [editor](#azure-resource-manager-templates) y agregue la siguiente información para dar a la máquina virtual acceso de **lector** al grupo de recursos en el que se creó.  La estructura de la plantilla puede variar según el editor y el modelo de implementación que elija.
   
   Agregue lo siguiente bajo la sección `parameters`:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Agregue lo siguiente bajo la sección `variables`:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Agregue lo siguiente bajo la sección `resources`:

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Deshabilitación de una identidad administrada asignada por el sistema en una VM de Azure

Para eliminar una identidad administrada asignada por el sistema de una máquina virtual, la cuenta necesita la asignación de roles [Colaborador de la máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  No se requiere ninguna otra asignación de roles de directorio de Azure AD.

1. Independientemente de que inicie sesión localmente en Azure o mediante Azure Portal, use una cuenta que esté asociada a la suscripción de Azure que contiene la máquina virtual.

2. Cargue la plantilla en un [editor](#azure-resource-manager-templates) y busque el `Microsoft.Compute/virtualMachines`recurso de interés dentro de la sección `resources`. Si dispone de una VM que solo tenga una identidad administrada asignada por el sistema, puede deshabilitarla cambiando el tipo de identidad a `None`.  
   
   **Microsoft.Compute/virtualMachines versión 2018-06-01 de la API**

   Si la VM tiene identidades administradas asignadas tanto por el usuario como por el sistema, quite `SystemAssigned` del tipo de identidad y conserve `UserAssigned` junto con los valores de diccionario `userAssignedIdentities`.

   **Microsoft.Compute/virtualMachines versión 2018-06-01 de la API**
   
   Si `apiVersion` es `2017-12-01` y la VM tiene identidades administradas asignadas tanto por el usuario como por el sistema, quite `SystemAssigned` del tipo de identidad y conserve `UserAssigned` junto con la matriz `identityIds` de identidades administradas asignadas por el usuario.  
   
En el ejemplo siguiente se muestra cómo quitar una identidad administrada asignada por el sistema de una VM sin identidades administradas asignadas por el usuario:

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-managed-identity"></a>Identidad administrada asignada por el usuario

En esta sección, asignará una identidad administrada asignada por el usuario a una VM de Azure mediante la plantilla de Azure Resource Manager.

> [!Note]
> Para crear una identidad administrada asignada por el usuario mediante una plantilla de Azure Resource Manager, consulte [Create a user-assigned managed identity](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) (Crear una identidad administrada asignada por el usuario).

 ### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Asignación de una identidad administrada asignada por el usuario a una VM de Azure

Para asignar una identidad asignada por un usuario a una máquina virtual, la cuenta debe tener las asignaciones de roles [Colaborador de la máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) y [Operador de identidades administradas](/azure/role-based-access-control/built-in-roles#managed-identity-operator). No se requiere ninguna otra asignación de roles de directorio de Azure AD.

1. En el elemento `resources`, agregue la siguiente entrada para asignar una identidad administrada asignada por el usuario a la VM.  No olvide reemplazar `<USERASSIGNEDIDENTITY>` con el nombre de la identidad administrada asignada por el usuario que ha creado.

   **Microsoft.Compute/virtualMachines versión 2018-06-01 de la API**

   Si `apiVersion` es `2018-06-01`, las identidades administradas asignadas por el usuario se almacenan en el formato de diccionario `userAssignedIdentities` y el valor `<USERASSIGNEDIDENTITYNAME>` debe almacenarse en una variable definida en la sección `variables` de la plantilla.

   ```json
   {
       "apiVersion": "2018-06-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachines versión 2017-12-01 de la API**
    
   Si `apiVersion` es `2017-12-01`, las identidades administradas asignadas por el usuario se almacenan en la matriz `identityIds` y el valor `<USERASSIGNEDIDENTITYNAME>` debe almacenarse en una variable definida en la sección `variables` de la plantilla.
    
   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       

2. (Opcional) A continuación, en el elemento `resources`, agregue la siguiente entrada para asignar la extensión de identidad administrada a la máquina virtual (está planeado que quede en desuso en enero de 2019). Este paso es opcional, ya que puede usar el punto de conexión de identidad de Azure Instance Metadata Service (IMDS) para recuperar tokens. Use la sintaxis siguiente:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3. Cuando haya terminado, deben agregarse las siguientes secciones a la sección `resource` de la plantilla para que se parezca a la siguiente:
   
   **Microsoft.Compute/virtualMachines versión 2018-06-01 de la API**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
   **Microsoft.Compute/virtualMachines versión 2017-12-01 de la API**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Eliminación de una identidad administrada asignada por el usuario de una VM de Azure

Para eliminar una identidad asignada por el usuario de una máquina virtual, la cuenta necesita la asignación de roles [Colaborador de la máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor). No se requiere ninguna otra asignación de roles de directorio de Azure AD.

1. Independientemente de que inicie sesión localmente en Azure o mediante Azure Portal, use una cuenta que esté asociada a la suscripción de Azure que contiene la máquina virtual.

2. Cargue la plantilla en un [editor](#azure-resource-manager-templates) y busque el `Microsoft.Compute/virtualMachines`recurso de interés dentro de la sección `resources`. Si dispone de una máquina virtual que solo tenga una identidad administrada asignada por el usuario, puede deshabilitarla cambiando el tipo de identidad a `None`.
 
   En el ejemplo siguiente se muestra cómo quitar todas las identidades administradas asignadas por un usuario de una VM sin identidades administradas asignadas por el sistema:
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **Microsoft.Compute/virtualMachines versión 2018-06-01 de la API**
    
   Para quitar una identidad administrada asignada por un usuario único de una VM, elimínela del diccionario `useraAssignedIdentities`.

   Si tiene una identidad administrada asignada por el sistema, consérvela en el valor `type` de `identity`.
 
   **Microsoft.Compute/virtualMachines versión 2017-12-01 de la API**

   Para quitar una identidad administrada asignada por un usuario único de una máquina virtual, elimínela de la matriz `identityIds`.

   Si tiene una identidad administrada asignada por el sistema, consérvela en el valor `type` de `identity`.
   
## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre las identidades administradas de recursos de Azure](overview.md).

