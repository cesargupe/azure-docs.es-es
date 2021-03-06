---
title: Protección de un dominio administrado con Azure Active Directory Domain Services | Microsoft Docs
description: Protección de un dominio administrado
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 1dea3def00423ecf092562aa47c76258316194a9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159067"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Protección de un dominio administrado con Azure AD Domain Services
Este artículo sirve de ayuda para proteger un dominio administrado. Puede desactivar el uso de conjuntos de cifrado débil y deshabilitar la sincronización de códigos hash de credenciales NTLM.

## <a name="install-the-required-powershell-modules"></a>Instalación de los módulos de PowerShell necesarios

### <a name="install-and-configure-azure-ad-powershell"></a>Instalación y configuración de Azure AD PowerShell
Siga las instrucciones que aparecen en el artículo para [instalar el módulo de Azure AD PowerShell y conectarse a Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instale y configure Azure PowerShell.
Siga las instrucciones que aparecen en el artículo para [instalar el módulo de Azure PowerShell y conectarse a la suscripción de Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Deshabilitación de la sincronización de hash de credenciales NTLM y de conjuntos de cifrado débil
Use el siguiente script de PowerShell para:
1. Deshabilitar la compatibilidad con NTLM v1 en el dominio administrado.
2. Deshabilitar la sincronización de códigos hash de contraseñas NTLM desde su instancia de Active Directory local.
3. Deshabilitar TLS v1 en el dominio administrado.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Pasos siguientes
* [Sincronización de Azure AD Domain Services](active-directory-ds-synchronization.md)
