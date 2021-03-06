---
title: archivo de inclusión
description: archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c212bda3b59037f99139e02ee6adc63b0084cbe5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305621"
---
## <a name="prepare-your-repository"></a>Preparación del repositorio

Para obtener compilaciones automáticas del servidor de compilación de Kudu para App Service, asegúrese de que la raíz del repositorio tiene los archivos correctos del proyecto.

| Tiempo de ejecución | Archivos del directorio raíz |
|-|-|
| ASP.NET (solo Windows) | _*.sln_, _*.csproj_ o _default.aspx_ |
| ASP.NET Core | _*.sln_ o _*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con un script de inicio |
| Python (solo Windows) | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| Trabajos web | _\<nombre_de_trabajo>/run.\<extensión>_ en _App\_Data/jobs/continuous_ (para WebJobs continuos) o _App\_Data/jobs/triggered_ (para WebJobs desencadenados). Para más información, consulte la [documentación de WebJobs de Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Consulte [Implementación continua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Para personalizar la implementación puede incluir un archivo _.deployment_ en la raíz del repositorio. Para más información, consulte el artículo sobre la [personalización de las implementaciones](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) y el artículo sobre el [script de implementación personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Si desarrolla en Visual Studio, deje que [Visual Studio cree un repositorio en su lugar](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). El proyecto está listo inmediatamente para su implementación mediante Git.
>
>

