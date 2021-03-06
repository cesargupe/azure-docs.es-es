---
title: Instalación de Azure IoT Edge en Windows con contenedores de Windows | Microsoft Docs
description: Instrucciones de instalación de Azure IoT Edge en Windows con contenedores de Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: e6edc9d6e98c03b1a5847dc08bbaa3ad029aa673
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51565044"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Instalación del entorno de ejecución de Azure IoT Edge en Windows para usar con contenedores de Windows

El entorno de ejecución de Azure IoT Edge es lo que convierte a un dispositivo en un dispositivo IoT Edge. El entorno de ejecución se puede implementar en dispositivos tan pequeños como un Raspberry Pi o tan grandes como un servidor industrial. Una vez que un dispositivo está configurado con el entorno de ejecución de IoT Edge, puede empezar a implementar lógica de negocios en él desde la nube. 

Para más información acerca de cómo funciona el entorno de ejecución de IoT Edge y los componentes que se incluyen, consulte [Información del entorno de ejecución de Azure IoT Edge y su arquitectura](iot-edge-runtime.md).

En este artículo se enumeran los pasos para instalar el entorno de ejecución de Azure IoT Edge con contenedores Windows en el sistema Windows x64 (AMD e Intel). 

Esta compatibilidad con Windows se encuentra actualmente en versión preliminar.

## <a name="supported-windows-versions"></a>Versiones de Windows admitidas
Se puede utilizar Azure IoT Edge con contenedores de Windows con:
  * Windows 10/IoT Enterprise/IoT Core con la actualización de abril de 2018 (compilación 17134).
  * Windows Server 1803

Para más información acerca de qué sistemas operativos se admiten actualmente, consulte [Compatibilidad de Azure IoT Edge](support.md#operating-systems).

## <a name="install-the-container-runtime"></a>Instalación del entorno de ejecución del contenedor 

>[!NOTE]
>Para la instalación del motor de contenedor en Windows IoT Core, siga los pasos descritos en el artículo sobre el [aprovisionamiento de un dispositivo de IoT Core](how-to-install-iot-core.md) y, después, continúe con las instrucciones siguientes.

Azure IoT Edge se basa en un entorno de ejecución de contenedor [compatible con OCI](https://www.opencontainers.org/) (por ejemplo, Docker). Se puede usar [Docker para Windows](https://www.docker.com/docker-windows) para desarrollo y pruebas. 

Configure Docker para Windows [para utilizar los contenedores de Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Instalación del demonio de seguridad de Azure IoT Edge

>[!NOTE]
>Los paquetes de software de Azure IoT Edge están sujetos a los términos de licencia ubicados en los paquetes (en el directorio LICENSE). Lea los términos de licencia antes de usar el paquete. La instalación y uso del paquete constituye la aceptación de estos términos. Si no acepta los términos de licencia, no utilice el paquete.

Un único dispositivo de IoT Edge se puede aprovisionar manualmente mediante una cadena de conexiones de dispositivo proporcionada por IoT Hub. O bien, puede usar el servicio Device Provisioning para aprovisionar dispositivos automáticamente, lo que resulta útil cuando tiene muchos dispositivos para aprovisionar. Según la elección de aprovisionamiento, elija el script de instalación adecuado. 

### <a name="install-and-manually-provision"></a>Instalación y aprovisionamiento manual

1. Siga los pasos de [Registro de un nuevo dispositivo Azure IoT Edge desde Azure Portal](how-to-register-device-portal.md) para registrar el dispositivo y recuperar la cadena de conexión de dicho dispositivo. 

2. En el dispositivo IoT Edge, ejecute PowerShell como administrador. 

3. Descargue e instale el entorno de ejecución de IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. Cuando se le pida **DeviceConnectionString**, proporcione la cadena de conexión que recuperó de IoT Hub. No incluya comillas para la cadena de conexión. 

### <a name="install-and-automatically-provision"></a>Instalación y aprovisionamiento automático

1. Siga los pasos de [Creación y aprovisionamiento de un dispositivo TPM Edge simulado en Windows](how-to-auto-provision-simulated-device-windows.md) para configurar el servicio Device Provisioning y recuperar su **identificador de ámbito**, simular un dispositivo TPM y recuperar su **identificador de registro**; luego, cree una inscripción individual. Una vez que el dispositivo esté registrado en IoT Hub, continúe con la instalación.  

   >[!TIP]
   >Mantenga abierta la ventana que se está ejecutando el simulador de TPM durante la instalación y prueba. 

2. En el dispositivo IoT Edge, ejecute PowerShell como administrador. 

3. Descargue e instale el entorno de ejecución de IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. Cuando se le solicite, proporcione **ScopeID** y **RegistrationID** para su servicio de aprovisionamiento y el dispositivo. 

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Puede comprobar el estado del servicio IoT Edge mediante: 

```powershell
Get-Service iotedge
```

El examen de los registros de servicio de los últimos 5 minutos con:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Y, enumere los módulos en ejecución con:

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>Recomendaciones y sugerencias

Si la red tiene un servidor proxy, siga los pasos descritos en [Configure your IoT Edge device to communicate through a proxy server](how-to-configure-proxy-support.md) (Configuración del dispositivo IoT Edge para comunicarse mediante un servidor proxy) para instalar e iniciar el entorno de ejecución de Azure IoT Edge.

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene un dispositivo IoT Edge aprovisionado con el entorno de ejecución instalado, puede [implementar módulos de IoT Edge](how-to-deploy-modules-portal.md).

Si tiene problemas con la instalación correcta del entorno de ejecución de Edge, consulte la página de [solución de problemas](troubleshoot.md).
