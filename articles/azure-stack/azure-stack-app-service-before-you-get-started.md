---
title: Antes de implementar App Service en Azure Stack | Microsoft Docs
description: Pasos necesarios para implementar App Service en Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: 4f669d44582c47cc6c7c090627f957288fee0f1a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615881"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Antes de empezar a trabajar con App Service en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Antes de implementar Azure App Service en Azure Stack, debe completar los pasos de los requisitos previos de este artículo.

> [!IMPORTANT]
> Aplique la actualización 1809 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.4.

## <a name="download-the-installer-and-helper-scripts"></a>Descarga de los scripts de la aplicación auxiliar y el instalador

1. Descargue los [scripts de la aplicación auxiliar para la implementación de App Service en Azure Stack](https://aka.ms/appsvconmashelpers).
2. Descargue el [instalador de App Service en Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extraiga los archivos del archivo ZIP de scripts de la aplicación auxiliar. Se extraen los siguientes archivos y carpetas:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - Carpeta Modules
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Distribuya la extensión de script personalizado desde Marketplace

Azure App Service en Azure Stack requiere v1.9.0 de extensión de script personalizado.  La extensión tiene que [distribuirse desde Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) antes de iniciar la implementación o actualización de Azure App Service en Azure Stack

## <a name="high-availability"></a>Alta disponibilidad

La actualización 1802 de Azure Stack agregó compatibilidad con los dominios de error. Las nuevas implementaciones de Azure App Service en Azure Stack se distribuirán entre los dominios de error y proporcionarán tolerancia a errores.

Para las implementaciones existentes de Azure App Service en Azure Stack, que se implementaron antes de la actualización 1802, consulte el artículo [Redistribución de un proveedor de recursos de App Service entre dominios de error](azure-stack-app-service-fault-domain-update.md).

Además, implemente el servidor de archivos necesarios y las instancias de SQL Server en una configuración de alta disponibilidad.

## <a name="get-certificates"></a>Obtención de certificados

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Certificado raíz de Azure Resource Manager para Azure Stack

Abra una sesión de PowerShell con privilegios elevados en un equipo que pueda alcanzar el punto de conexión con privilegios en el sistema Azure Stack integrado o el host del Kit de desarrollo de Azure Stack.

Ejecute el script *Get-AzureStackRootCert.ps1* desde la carpeta en la que extrajo los scripts de la aplicación auxiliar. La carpeta donde se crea el certificado raíz del script es la misma donde se encuentra el script que App Service necesita para crear certificados.

Al ejecutar el siguiente comando de PowerShell, tendrá que proporcionar el punto de conexión con privilegios y las credenciales para AzureStack\CloudAdmin.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Parámetros de script Get-AzureStackRootCert.ps1

| Parámetro | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Obligatorio | AzS-ERCS01 | Punto de conexión con privilegios |
| CloudAdminCredential | Obligatorio | AzureStack\CloudAdmin | Credenciales de cuenta de dominio para administradores de nube de Azure Stack |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certificados necesarios para la implementación de ASDK de Azure App Service

El script *Create-AppServiceCerts.ps1* funciona con la entidad de certificación de Azure Stack para crear los cuatro certificados que necesita App Service.

| Nombre de archivo | Uso |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | Certificado SSL predeterminado de App Service |
| api.appservice.local.azurestack.external.pfx | Certificado SSL de API de App Service |
| ftp.appservice.local.azurestack.external.pfx | Certificado SSL del publicador de App Service |
| sso.appservice.local.azurestack.external.pfx | Certificado de aplicación de identidad de App Service |

Para crear los certificados, siga estos pasos:

1. Inicie sesión en el host del Kit de desarrollo de Azure Stack con la cuenta AzureStack\AzureStackAdmin.
2. Abra una sesión de PowerShell con privilegios elevados.
3. Ejecute el script *Create-AppServiceCerts.ps1* desde la carpeta en la que extrajo los scripts de la aplicación auxiliar. Este script crea cuatro certificados en la misma carpeta que el script que App Service necesita para la creación de certificados.
4. Escriba una contraseña para proteger los archivos .pfx y anótela. Deberá escribirla en App Service en el instalador de Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>Parámetros de script Create-AppServiceCerts.ps1

| Parámetro | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| pfxPassword | Obligatorio | Null | Contraseña que ayuda a proteger la clave privada del certificado |
| DomainName | Obligatorio | local.azurestack.external | Región y sufijo de dominio de Azure Stack |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Certificados necesarios para una implementación de producción en Azure Stack de Azure App Service

Para ejecutar el proveedor de recursos en producción, debe proporcionar los siguientes certificados:

- Certificado de dominio predeterminado
- Certificado de API
- Certificado de publicador
- Certificado de identidad

#### <a name="default-domain-certificate"></a>Certificado de dominio predeterminado

El certificado de dominio predeterminado se coloca en el rol de Front-End. Las aplicaciones del usuario lo utilizan para una solicitud de dominio comodín o predeterminado para Azure App Service. El certificado también se utiliza para operaciones de control de código fuente (Kudu).

El certificado debe estar en formato .pfx y debe ser un certificado comodín con tres firmantes. Este requisito permite que el dominio predeterminado y el punto de conexión de SCM para las operaciones de control de código fuente se realicen mediante un certificado.

| Formato | Ejemplo |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>Certificado de API

El certificado de la API se coloca en el rol de administración. El proveedor de recursos lo usa para ayudar a proteger las llamadas a la API. El certificado para publicar debe contener un firmante que coincida con la entrada DNS de la API.

| Formato | Ejemplo |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>Certificado de publicador

El certificado para el rol de publicador protege el tráfico FTPS de los propietarios de aplicaciones cuando cargan contenido. El certificado para publicar debe contener un firmante que coincida con la entrada DNS de FTPS.

| Formato | Ejemplo |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Certificado de identidad

El certificado para la aplicación de identidad permite:

- La integración entre Azure Active Directory (Azure AD) o el directorio de Active Directory Federation Services (AD FS), Azure Stack y App Service para admitir la integración con el proveedor de recursos de proceso.
- Escenarios de inicio de sesión único para herramientas de desarrollo avanzadas de Azure App Service en Azure Stack.

El certificado de identidad debe contener un firmante que coincida con el siguiente formato.

| Formato | Ejemplo |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>Virtual network

> [!NOTE]
> La creación previa de una red virtual personalizada es opcional, ya que Azure App Service en Azure Stack puede crear la red virtual necesaria, pero después tendrá que comunicarse con SQL y el servidor de archivos utilizando direcciones IP públicas.

Azure App Service en Azure Stack le permite implementar el proveedor de recursos en una red virtual existente o crear una red virtual como parte de la implementación. El uso de una red virtual existente permite utilizar IP internas para conectarse al servidor de archivos y al servidor SQL Server que Azure App Service necesita en Azure Stack. La red virtual debe configurarse con el intervalo de direcciones y las subredes siguientes antes de instalar Azure App Service en Azure Stack:

Virtual Network - /16

Subredes

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>Preparación del servidor de archivos

Azure App Service necesita utilizar un servidor de archivos. Para las implementaciones de producción, se debe configurar el servidor de archivos para tener alta disponibilidad y ser capaz de administrar los errores.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Plantilla de inicio rápido para el servidor de archivos para las implementaciones de Azure App Service en ASDK.

Para su uso solo con las implementaciones del kit de desarrollo de Azure Stack, puede usar la [plantilla de implementación de Azure Resource Manager de ejemplo](https://aka.ms/appsvconmasdkfstemplate) para implementar un servidor de archivos de nodo único configurado. El servidor de archivos de nodo único estará en un grupo de trabajo.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Plantilla de inicio rápido para un servidor de archivos de alta disponibilidad y SQL Server

Hay ahora disponible una [plantilla de inicio rápido de arquitectura de referencia](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha), que implementará el servidor de archivos, SQL Server, con compatibilidad con la infraestructura de Active Directory en una red virtual configurada para admitir una implementación de alta disponibilidad de Azure App Service en Azure Stack.  

### <a name="steps-to-deploy-a-custom-file-server"></a>Pasos para implementar un servidor de archivos personalizado

>[!IMPORTANT]
> Si decide implementar App Service en una instancia de Virtual Network, el servidor de archivos debe implementarse en una subred independiente de App Service.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Aprovisionar grupos y cuentas en Active Directory

1. Crear los siguientes grupos de seguridad global de Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Crear las siguientes cuentas de Active Directory como cuentas de servicio:

   - FileShareOwner
   - FileShareUser

   Como procedimiento recomendado de seguridad, los usuarios de estas cuentas (y para todos los roles de web) deben ser únicos y tener contraseñas y nombres de usuario seguros. Establezca las contraseñas con las condiciones siguientes:

   - Habilitar **La contraseña nunca expira**.
   - Habilitar **El usuario no puede cambiar la contraseña**.
   - Deshabilitar **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**.

3. Agregue las cuentas a las pertenencias a grupos como sigue:

   - Agregar **FileShareOwner** al grupo **FileShareOwners**.
   - Agregar **FileShareUser** al grupo **FileShareUsers**.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Aprovisionar grupos y cuentas en un grupo de trabajo

>[!NOTE]
> Cuando esté configurando un servidor de archivos, ejecute los siguientes comandos en el **símbolo del sistema de administrador**. <br>***No use PowerShell.***

Cuando se usa la plantilla de Azure Resource Manager, los usuarios ya están creados.

1. Ejecute los comandos siguientes para crear las cuentas FileShareOwner y FileShareUser. Reemplazar `<password>` por sus propios valores.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Establezca las contraseñas de las cuentas para que no expiren nunca al ejecutar los siguientes comandos WMIC:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Cree los grupos locales FileShareUsers y FileShareOwners y agrégueles las cuentas en el primer paso:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Aprovisionar el recurso compartido de contenido

El recurso compartido de contenido incluye el contenido del sitio web del inquilino. El procedimiento para aprovisionar el recurso compartido de contenido en un único servidor de archivos es el mismo para los entornos de Active Directory y del grupo de trabajo. Pero es diferente para un clúster de conmutación por error en Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Aprovisionamiento del recurso compartido de contenido en un único servidor de archivos (Active Directory o grupo de trabajo)

En un único servidor de archivos, ejecute los siguientes comandos en un símbolo del sistema con privilegios elevados. Reemplace el valor de `C:\WebSites` por las rutas de acceso correspondientes de su entorno.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Configurar el control de acceso a los recursos compartidos

Ejecute los comandos siguientes en un símbolo del sistema con privilegios elevados en el servidor de archivos o en el nodo del clúster de conmutación por error, que es el propietario actual del recurso del clúster. Reemplace los valores en cursiva por valores específicos de su entorno.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Grupo de trabajo

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>Preparación de la instancia de SQL Server

Para que Azure App Service en Azure Stack hospede y mida bases de datos, debe preparar una instancia de SQL Server para almacenar las bases de datos de App Service.

Para implementaciones del Kit de desarrollo de Azure Stack, puede usar SQL Server Express 2014 SP2 o versiones posteriores.

Para entornos de producción y para ofrecer alta disponibilidad, debe usar una versión completa de SQL Server 2014 SP2 o versiones posteriores, habilitar la autenticación de modo mixto y realizar la implementación en una [configuración de alta disponibilidad](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

La instancia de SQL Server para Azure App Service en Azure Stack debe ser accesible desde todos los roles de App Service. SQL Server se puede implementar en la suscripción del proveedor predeterminada en Azure Stack. O bien, puede usar la infraestructura existente en su organización (siempre y cuando haya conectividad con Azure Stack). Si va a usar una imagen de Azure Marketplace, no olvide configurar el firewall adecuadamente.

>[!NOTE]
> Varias imágenes de la máquina virtual IaaS de SQL están disponibles a través de la característica Administración de Marketplace. Asegúrese de descargar siempre la versión más reciente de la extensión IaaS de SQL antes de implementar una máquina virtual mediante un elemento de Marketplace. Las imágenes de SQL son las mismas que las máquinas virtuales de SQL disponibles en Azure. Para las máquinas virtuales de SQL creadas a partir de estas imágenes, la extensión de IaaS y las correspondientes mejoras del portal proporcionan características como las funcionalidades de copia de seguridad y de revisión automática.
>
Para cualquiera de los roles de SQL Server, puede utilizar una instancia predeterminada o una instancia con nombre. Si usa una instancia con nombre, asegúrese de iniciar manualmente el servicio SQL Server Browser y abrir el puerto 1434.

>[!IMPORTANT]
> Si decide implementar App Service en una instancia de Virtual Network, SQL Server debe implementarse en una subred independiente de App Service y del servidor de archivos.
>

## <a name="create-an-azure-active-directory-application"></a>Creación de una aplicación de Azure Active Directory

Configuración de una entidad de servicio de Azure AD para admitir las siguientes operaciones:

- Integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo.
- Inicio de sesión único para las herramientas avanzadas del desarrollador y el portal de Azure Functions.

Estos pasos se aplican solo a entornos de Azure Stack protegidos con Azure AD.

Los administradores deben configurar SSO para:

- Habilitar las herramientas avanzadas de desarrollador en App Service (Kudu).
- Habilitar el uso de la experiencia del portal de Azure Functions.

Siga estos pasos:

1. Abra una instancia de PowerShell como azurestack\AzureStackAdmin.
2. Vaya a la ubicación de los scripts descargados y extraídos en el [paso de requisitos previos](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).
4. Ejecute el script **Create-AADIdentityApp.ps1**. Cuando se le pida, escriba el identificador del inquilino de Azure AD que esté usando para la implementación de Azure Stack. Por ejemplo, escriba **myazurestack.onmicrosoft.com**.
5. En la ventana **Credencial**, escriba la cuenta y la contraseña de administrador del servicio de Azure AD. Seleccione **Aceptar**.
6. Escriba la contraseña y la ruta de acceso del archivo del [certificado creado anteriormente](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). El certificado creado para este paso de forma predeterminada es **sso.appservice.local.azurestack.external.pfx**.
7. El script crea una nueva aplicación en la instancia de Azure AD del inquilino. Tome nota del identificador de la aplicación que se devuelve en la salida de PowerShell. Necesitará esta información durante la instalación.
8. Abra una nueva ventana del explorador e inicie sesión en [Azure Portal](https://portal.azure.com) como administrador del servicio Azure Active Directory.
9. Abra el proveedor de recursos de Azure AD.
10. Seleccione **Registros de aplicaciones**.
11. Busque el identificador de la aplicación devuelto como parte del paso 7. Se muestra una aplicación de App Service.
12. Seleccione **Aplicación** en la lista.
13. Seleccione **Configuración**.
14. Seleccione **Permisos necesarios** > **Conceder permisos** > **Sí**.

```PowerShell
    Create-AADIdentityApp.ps1
```

| Parámetro | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| DirectoryTenantName | Obligatorio | Null | Identificador de inquilino de Azure AD. Proporcione el GUID o una cadena. Un ejemplo es myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Obligatorio | Null | Punto de conexión de Azure Resource Manager del administrador. Por ejemplo, adminmanagement.local.azurestack.external. |
| TenantARMEndpoint | Obligatorio | Null | Punto de conexión de Azure Resource Manager del inquilino. Por ejemplo, management.local.azurestack.external. |
| AzureStackAdminCredential | Obligatorio | Null | Credenciales del administrador del servicio de Azure AD. |
| CertificateFilePath | Obligatorio | Null | **Ruta de acceso completa** del archivo de certificado de la aplicación de identidad generado anteriormente. |
| CertificatePassword | Obligatorio | Null | Contraseña que ayuda a proteger la clave privada del certificado. |
| Entorno | Opcional | AzureCloud | El nombre del entorno en la nube admitido en el que está disponible el servicio Graph de Azure Active Directory de destino.  Valores permitidos: 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Creación de una aplicación de Servicios de federación de Active Directory

Para entornos de Azure Stack protegidos por AD FS, debe configurar una entidad de servicio de AD FS para admitir las siguientes operaciones:

- Integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo.
- Inicio de sesión único para las herramientas avanzadas del desarrollador y el portal de Azure Functions.

Los administradores deben configurar SSO para:

- Configurar una entidad de servicio para la integración del conjunto de escalado de máquinas virtuales en los niveles de trabajo.
- Habilitar las herramientas avanzadas de desarrollador en App Service (Kudu).
- Habilitar el uso de la experiencia del portal de Azure Functions.

Siga estos pasos:

1. Abra una instancia de PowerShell como azurestack\AzureStackAdmin.
2. Vaya a la ubicación de los scripts descargados y extraídos en el [paso de requisitos previos](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts).
3. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).
4. Ejecute el script **Create-ADFSIdentityApp.ps1**.
5. En la ventana **Credencial**, escriba la cuenta y la contraseña de administrador de la nube de AD FS. Seleccione **Aceptar**.
6. Proporcione la ruta de acceso del archivo y la contraseña del [certificado creado anteriormente](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack). El certificado creado para este paso de forma predeterminada es **sso.appservice.local.azurestack.external.pfx**.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| Parámetro | Obligatorio u opcional | Valor predeterminado | DESCRIPCIÓN |
| --- | --- | --- | --- |
| AdminArmEndpoint | Obligatorio | Null | Punto de conexión de Azure Resource Manager del administrador. Por ejemplo, adminmanagement.local.azurestack.external. |
| PrivilegedEndpoint | Obligatorio | Null | Punto de conexión de acceso con privilegios. Por ejemplo, AzS-ERCS01. |
| CloudAdminCredential | Obligatorio | Null | Credencial de cuenta de dominio de administración en la nube de Azure Stack. Por ejemplo, Azurestack\CloudAdmin. |
| CertificateFilePath | Obligatorio | Null | **Ruta de acceso completa** al archivo PFX del certificado de la aplicación de identidad. |
| CertificatePassword | Obligatorio | Null | Contraseña que ayuda a proteger la clave privada del certificado. |

## <a name="next-steps"></a>Pasos siguientes

[Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md)
