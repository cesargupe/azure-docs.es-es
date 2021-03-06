---
title: Solución de problemas de Azure Cloud Shell | Microsoft Docs
description: Solución de problemas de Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 089c623ff2c53a59c60c3fe1a53876c16a5353dd
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159029"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Solución de problemas y limitaciones de Azure Cloud Shell

Entre las resoluciones conocidas de problemas en Azure Cloud Shell se incluyen las siguientes:

## <a name="general-troubleshooting"></a>Solución general de problemas

### <a name="early-timeouts-in-firefox"></a>Tiempos de expiración tempranos en Firefox

- **Detalles**: Cloud Shell usa una conexión Websocket abierta para pasar la entrada/salida al explorador. Firefox tiene directivas preestablecidas que pueden cerrar la conexión WebSocket prematuramente, lo que genera tiempos de expiración tempranos en Cloud Shell.
- **Resolución**: abra FireFox y escriba "about:config" en el cuadro de dirección URL. Busque "network.websocket.timeout.ping.request" y cambie el valor de 0 a 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Deshabilitación de Cloud Shell en un entorno de red bloqueada

- **Detalles**: es posible que los administradores deseen deshabilitar el acceso a Cloud Shell para sus usuarios. Cloud Shell utiliza el acceso al dominio `ux.console.azure.com` (que se puede denegar), por lo que se detiene cualquier acceso a los puntos de entrada de Cloud Shell, incluido portal.azure.com, shell.azure.com, la extensión de la cuenta de Azure de Visual Studio Code y docs.microsoft.com.
- **Resolución**: restringir el acceso a `ux.console.azure.com` a través de la configuración de red a su entorno. El icono de Cloud Shell seguirá existiendo en portal.azure.com, pero no se conectará correctamente al servicio.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Cuadro de diálogo de Storage. Error: 403 RequestDisallowedByPolicy

- **Detalles**: al crear una cuenta de almacenamiento a través de Cloud Shell se produce un error debido a una directiva de Azure creada por el administrador. El mensaje de error incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolución**: póngase en contacto con el administrador de Azure para quitar o actualizar la directiva que deniega la creación del almacenamiento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Cuadro de diálogo de Storage. Error: 400 DisallowedOperation

- **Detalles**: cuando se usa una suscripción de Azure Active Directory, no puede crear almacenamiento.
- **Resolución**: use una suscripción de Azure capaz de crear recursos de almacenamiento. Las suscripciones de Azure AD no pueden crear recursos de Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Salida de terminal. Error: Failed to connect terminal: websocket cannot be established (Error al conectar con el terminal: no se puede establecer el websocket). Presione `Enter` para volver a conectarse.
- **Detalles**: Cloud Shell requiere la capacidad de establecer una conexión de websocket con la infraestructura de Cloud Shell.
- **Resolución**: Compruebe que ha configurado las opciones de red para permitir el envío de solicitudes https y websocket para dominios en *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Establezca la conexión de Cloud Shell para admitir el uso de TLS 1.2.
 - **Detalles**: para definir la versión de TLS para la conexión con Cloud Shell, debe establecer la configuración específica del explorador.
 - **Resolución**: vaya a la configuración de seguridad del explorador y seleccione la casilla junto a "Usar TLS 1.2".

## <a name="bash-troubleshooting"></a>Solución de problemas de Bash

### <a name="cannot-run-the-docker-daemon"></a>No se puede ejecutar el demonio de Docker

- **Detalles**: Cloud Shell utiliza un contenedor para hospedar el entorno de shell, lo que hace que no se permita la ejecución del demonio.
- **Resolución**: utilice la [máquina de Docker](https://docs.docker.com/machine/overview/), que se instala de forma predeterminada para administrar contenedores de Docker desde un host de Docker remoto.

## <a name="powershell-troubleshooting"></a>Solución de problemas de PowerShell

### <a name="gui-applications-are-not-supported"></a>No se admiten las aplicaciones con GUI

- **Detalles**: si un usuario inicia una aplicación con GUI, no se devuelve el símbolo del sistema. Por ejemplo, cuando se clona un repositorio GitHub privado que tiene habilitada la autenticación en dos fases, aparece un cuadro de diálogo para completar esta autenticación.
- **Resolución**: cierre y vuelva a abrir el shell.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solución de problemas de la administración remota de máquinas virtuales de Azure

- **Detalles**: debido a la configuración predeterminada del Firewall de Windows para WinRM, es posible que el usuario vea el error siguiente: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolución**: ejecute `Enable-AzureRmVMPSRemoting` para habilitar todos los aspectos de la comunicación remota de PowerShell en la máquina de destino.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` no actualiza el resultado en la unidad de Azure.

- **Detalles**: de forma predeterminada, para optimizar la experiencia del usuario, los resultados de `dir` se almacenan en caché en la unidad de Azure.
- **Resolución**: después de crear, actualizar o quitar un recurso de Azure, ejecute `dir -force` para actualizar los resultados en la unidad de Azure.

## <a name="general-limitations"></a>Limitaciones generales

Azure Cloud Shell tiene las limitaciones conocidas siguientes:

### <a name="system-state-and-persistence"></a>Persistencia y estado del sistema

La máquina que proporciona la sesión de Cloud Shell es temporal y se recicla después de que la sesión esté inactiva durante 20 minutos. Cloud Shell requiere montar un recurso compartido de archivos de Azure. Como resultado, la suscripción debe poder configurar los recursos de almacenamiento para tener acceso a Cloud Shell. Otras consideraciones:

- Con el almacenamiento montado, solo se conservan las modificaciones dentro del directorio `clouddrive`. En Bash, también se conserva el directorio `$HOME`.
- Solo se pueden montar recursos compartidos de archivos de Azure desde la [región asignada](persisting-shell-storage.md#mount-a-new-clouddrive).
  - En Bash, ejecute `env` para buscar la región establecida como `ACC_LOCATION`.
- Azure Files solo admite cuentas de almacenamiento con redundancia local o de almacenamiento con redundancia geográfica.

### <a name="browser-support"></a>Compatibilidad con exploradores

Cloud Shell admite las versiones más recientes de los siguientes exploradores:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari en modo privado no es compatible.

### <a name="copy-and-paste"></a>Copiar y pegar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Solo puede haber un shell activo para un usuario determinado

Los usuarios solo pueden iniciar un tipo de shell a la vez, ya sea **Bash** o **PowerShell**. Pero puede haber varias instancias de Bash o PowerShell en ejecución al mismo tiempo. Los cambios entre Bash y PowerShell reinician Cloud Shell, lo que finaliza las sesiones existentes.

### <a name="usage-limits"></a>Límites de uso

Cloud Shell está pensado para casos de uso interactivos. Por tanto, todas las sesiones que no sean de este tipo y que se prolonguen durante mucho tiempo se finalizarán sin previo aviso.

### <a name="user-permissions"></a>Permisos de usuario

Los permisos se establecen como usuarios normales sin acceso a sudo. No se conserva cualquier instalación fuera del directorio `$Home`.

## <a name="bash-limitations"></a>Limitaciones de Bash

### <a name="editing-bashrc"></a>Editar .bashrc

Tenga cuidado al editar .bashrc, ya que puede producir errores inesperados en Cloud Shell.

## <a name="powershell-limitations"></a>Limitaciones de PowerShell

### <a name="preview-version-of-azuread-module"></a>Versión preliminar del módulo de Azure AD

Actualmente, `AzureAD.Standard.Preview`, hay disponible una versión preliminar del módulo basado en .NET Standard. Este módulo proporciona la misma funcionalidad que `AzureAD`.

### <a name="sqlserver-module-functionality"></a>Funcionalidad del módulo `SqlServer`

El módulo `SqlServer` incluido en Cloud Shell solo es compatible con versiones preliminares de PowerShell Core. En concreto, `Invoke-SqlCmd` aún no está disponible.

### <a name="default-file-location-when-created-from-azure-drive"></a>Ubicación del archivo predeterminada cuando se crea a partir de la unidad de Azure

Con los cmdlets de PowerShell, los usuarios no pueden crear archivos en la unidad de Azure. Cuando los usuarios crean nuevos archivos con otras herramientas, como vim o nano, los archivos se guardan de forma predeterminada en `$HOME`.

### <a name="commands-that-create-gui-pop-ups-are-not-supported"></a>No se admiten los comandos que crean los elementos emergentes de GUI

Si el usuario ejecuta un comando que crearía un cuadro de diálogo de Windows, como `Connect-AzureAD` o `Connect-AzureRmAccount`, se ve un mensaje de error como el siguiente: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>La finalización con tabulación puede iniciar la excepción de PSReadline

Si EditMode de PSReadline del usuario se establece en Emacs, el usuario intenta mostrar todas las posibilidades mediante la finalización con tabulación y el tamaño de la ventana es demasiado pequeño para mostrarlas todas, PSReadline iniciará una excepción no controlada.

### <a name="large-gap-after-displaying-progress-bar"></a>Espacio grande después de mostrar la barra de progreso

Si un comando o una acción del usuario muestra una barra de progreso, como una finalización con tabulación mientras está en la unidad `Azure:`, puede que el cursor no se establezca correctamente y que aparezca un espacio donde antes aparecía la barra de progreso.

### <a name="random-characters-appear-inline"></a>Aparecen caracteres aleatorios insertados

Los códigos de secuencia de posición del cursor como, por ejemplo `5;13R`, pueden aparecer en la entrada del usuario. Los caracteres se pueden quitar manualmente.

## <a name="personal-data-in-cloud-shell"></a>Datos personales en Cloud Shell

Azure Cloud Shell se toma en serio sus datos, los datos capturados y almacenados por el servicio Azure Cloud Shell se usan para proporcionar los valores predeterminados de su experiencia, tales como el shell que ha utilizado más recientemente, el tamaño y el tipo de fuente que prefiere y los detalles de recursos compartidos de archivos que respaldan la unidad en la nube. En caso de que quiera exportar o eliminar estos datos, use las siguientes instrucciones.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportación
Para poder **exportar** la configuración de usuario, Cloud Shell guarda automáticamente datos tales como el shell, el tamaño y el tipo de fuente preferidos ejecutando los comandos siguientes.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Inicio de Azure Cloud Shell")](https://shell.azure.com)
2. Ejecute los siguientes comandos en Bash o PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Eliminar
Para poder **eliminar** la configuración de usuario, Cloud Shell guarda automáticamente datos tales como el shell, el tamaño y el tipo de fuente preferidos ejecutando los comandos siguientes. La próxima vez que inicie Cloud Shell se le pedirá que vuelva a incorporar un recurso compartido de archivos. 

>[!Note]
> Si elimina la configuración de usuario, el recurso compartido real de Azure Files no se eliminará. Vaya a Azure Files para completar esa acción.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Inicio de Azure Cloud Shell")](https://shell.azure.com)
2. Ejecute los siguientes comandos en Bash o PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
