---
title: Solucionar problemas de extensión de la máquina virtual de Azure Log Analytics | Microsoft Docs
description: Aquí se describen los síntomas, causas y soluciones de los problemas más comunes que surgen con la extensión de máquinas virtuales de Log Analytics para máquinas virtuales de Azure que se ejecutan en Windows y Linux.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: f1ca7abc867df25d37093cb777f35216b5ee5a30
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957988"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Solución de problemas de la extensión de máquina virtual de Log Analytics
En este artículo se proporciona ayuda y posibles soluciones para resolver los errores que puedan surgir con la extensión de máquinas virtuales de Log Analytics para aquellas máquinas virtuales que se ejecutan en Microsoft Azure para Windows y Linux.

Para comprobar el estado de la extensión, realice los pasos siguientes en Azure Portal.

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Máquinas virtuales**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Máquinas virtuales**.
3. En la lista de máquinas virtuales, busque y seleccione la que le interesa.
3. En la máquina virtual, haga clic en **Extensiones**.
4. En la lista, compruebe si la extensión de Log Analytics está habilitada.  Para Linux, el agente aparece como **OMSAgentforLinux** y para Windows, el agente aparece como **MicrosoftMonitoringAgent**.

   ![Vista de la extensión de máquina virtual](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Haga clic en la extensión para ver los detalles. 

   ![Detalles de la extensión de máquina virtual](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Solución de problemas con la extensión de máquinas virtuales de Azure en Windows.

Si la extensión de la máquina virtual *Microsoft Monitoring Agent* no se instala o no envía informes, puede realizar los pasos siguientes para solucionar el problema.

1. Siga los pasos de [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) para comprobar que el agente de máquina virtual de Azure está instalado y funciona correctamente.
   * También puede revisar el archivo de registro del agente de máquina virtual `C:\WindowsAzure\logs\WaAppAgent.log`
   * Si el registro no existe, el agente de máquina virtual no estará instalado.
   * [Instale el agente de máquina virtual de Azure.](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Confirme que se está ejecutando la tarea de latido de la extensión de Microsoft Monitoring Agent mediante los siguientes pasos:
   * Inicie sesión en la máquina virtual.
   * Abra el programador de tareas y busque la tarea `update_azureoperationalinsight_agent_heartbeat`.
   * Confirme que la tarea está habilitada y se ejecuta cada minuto.
   * Consulte el archivo de registro de latido en `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`.
3. Revise los archivos de registro de la extensión de máquina virtual de Microsoft Monitoring Agent en `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`.
4. Asegúrese de que la máquina virtual puede ejecutar scripts de PowerShell.
5. Asegúrese de que no se hayan modificado los permisos en C:\Windows\temp.
6. Consulte el estado de Microsoft Monitoring Agent, para lo que debe escribir lo siguiente en una ventana de PowerShell con privilegios elevados en la máquina virtual `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`.
7. Revise los archivos de registro de instalación de Microsoft Monitoring Agent en `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`.

Para más información, consulte [Solución de problemas de la extensión de máquina virtual de Microsoft Azure](../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Solución de problemas con la extensión de máquinas virtuales en Linux
[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 
Si la extensión de la máquina virtual del *agente de Log Analytics para Linux* no se instala o no envía informes, puede realizar los pasos siguientes para solucionar el problema.

1. Si el estado de la extensión es *desconocido*, compruebe si el agente de máquina virtual de Azure está instalado y funciona correctamente revisando el archivo de registro del agente de máquina virtual `/var/log/waagent.log`.
   * Si el registro no existe, el agente de máquina virtual no estará instalado.
   * [Instale el agente de máquina virtual de Azure en máquinas virtuales Linux.](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Para otros estados incorrectos, revise los archivos de registro de extensión de máquina virtual del agente de Log Analytics para Linux en `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` y `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`.
3. Si el estado de la extensión es correcto, pero no se están cargando datos, revise los archivos de registro del agente de Log Analytics para Linux en `/var/opt/microsoft/omsagent/log/omsagent.log`.

Para más información, consulte [Solución de problemas de la extensión de máquina virtual de Linux Azure](../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones adicionales para solucionar problemas relacionados con el agente de Log Analytics para Linux que se hospeda en equipos externos a Azure, vea [Troubleshoot Azure Log Analytics Linux Agent](log-analytics-agent-linux-support.md) (Solución de problemas del agente de Linux para Azure Log Analytics).  
