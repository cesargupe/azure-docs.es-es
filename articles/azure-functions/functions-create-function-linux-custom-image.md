---
title: Creación de una función en Linux con una imagen personalizada (versión preliminar) | Microsoft Docs
description: Aprenda a crear funciones de Azure que se ejecutan en una imagen de Linux personalizada.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: de15d1f8c268e80ac1659c53a141ec39cc6d3cb8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564816"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Creación de una función en Linux con una imagen personalizada (versión preliminar)

Azure Functions le permite hospedar sus funciones en Linux en su propio contenedor personalizado. También puede [hospedarlas en un contenedor de Azure App Service predeterminado](functions-create-first-azure-function-azure-cli-linux.md). Esta funcionalidad está actualmente en versión preliminar y requiere [el entorno de ejecución Functions 2.0](functions-versions.md).

En este tutorial, aprenderá a implementar funciones en Azure como una imagen de Docker personalizada. Este patrón es útil cuando debe personalizar la imagen de contenedor integrada de App Service. Es posible que quiera usar una imagen personalizada cuando las funciones necesiten una versión de idioma determinada o requieran una dependencia o configuración específica que no se proporciona en la imagen integrada.

En este tutorial se explica cómo usar Azure Functions Core Tools para crear una función en una imagen de Linux personalizada. Esta imagen se publica en una aplicación de función de Azure, que se creó mediante la CLI de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una aplicación de función y un archivo Dockerfile mediante Core Tools.
> * Crear una imagen personalizada con Docker
> * Publicar una imagen personalizada en un registro de contenedor
> * Cree una cuenta de Azure Storage.
> * Crear un plan de App Service de Linux
> * Implementar una aplicación de función desde Docker Hub
> * Agregar la configuración de aplicación a la aplicación de función

Los pasos siguientes se admiten en equipos Mac, Windows o Linux.  

## <a name="prerequisites"></a>Requisitos previos

Antes de ejecutar este ejemplo, debe tener lo siguiente:

* Instale [la versión 2.x de Azure Core Tools](functions-run-local.md#v2).

* Instale la [CLI de Azure]( /cli/azure/install-azure-cli). En este artículo se necesita la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión que tiene.  
También puede usar [Azure Cloud Shell](https://shell.azure.com/bash).

* Una suscripción de Azure activa.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Creación del proyecto de aplicación de función local

Ejecute el comando siguiente desde la línea de comandos para crear un proyecto de aplicación de función en la carpeta `MyFunctionProj` del directorio local actual.

```bash
func init MyFunctionProj --docker
```

Al incluir la opción `--docker`, se genera un archivo Dockerfile para el proyecto. Dicho archivo se usa para crear un contenedor personalizado en el que se ejecuta el proyecto. La imagen base que se usa depende del lenguaje del runtime de trabajo que se elija.  

Cuando se le solicite, elija un runtime de trabajo en los siguientes lenguajes:

* `dotnet`: crea un proyecto de biblioteca de clases .NET (.csproj).
* `node`: crea un proyecto JavaScript.

Cuando se ejecute el comando, verá una salida similar a la siguiente:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Use el siguiente comando para ir a la nueva carpeta del proyecto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Compilación de la imagen desde el archivo de Docker

Eche un vistazo al archivo _Dockerfile_ en la carpeta raíz del proyecto. Este archivo describe el entorno que es necesario para ejecutar la aplicación de función en Linux. El ejemplo siguiente es un archivo Dockerfile que crea un contenedor que ejecuta una aplicación de función en el runtime de trabajo de JavaScript (Node.js): 

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Al hospedar una imagen en un registro de contenedor privado, debe agregar la configuración de conexión a la aplicación de función mediante variables **ENV** en el Dockerfile. Como en este tutorial no se puede garantizar que se vaya a usar un registro privado, la configuración de conexión se [agrega después de la implementación mediante la CLI de Azure](#configure-the-function-app) como procedimiento recomendado de seguridad.

### <a name="run-the-build-command"></a>Ejecute el comando `build`.
En la carpeta raíz, ejecute el comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) y especifique un nombre, `mydockerimage`, y una etiqueta, `v1.0.0`. Reemplace `<docker-id>` por el identificador de su cuenta de Docker Hub. Este comando compila la imagen de Docker del contenedor.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Cuando el comando se ejecuta, verá una salida similar a la siguiente, que en este caso es para un runtime de trabajo de JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Prueba de la imagen de manera local
Compruebe que la imagen que ha creado funciona mediante la ejecución de la imagen de Docker en un contenedor local. Emita el comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) y transmítale el nombre y la etiqueta de la imagen. Asegúrese de especificar el puerto con el argumento `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Una vez que la imagen personalizada ya se ejecuta en un contenedor de Docker local, compruebe que la aplicación de función y el contenedor funcionan correctamente; para ello, vaya a <http://localhost:8080>.

![Prueba de la aplicación de función de manera local](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Opcionalmente, puede volver a probar la función, esta vez en el contenedor local, con la dirección URL siguiente:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Después de haber comprobado la aplicación de función en el contenedor, detenga la ejecución. Ahora, puede insertar la imagen personalizada en su cuenta de Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Inserción de la imagen personalizada en Docker Hub

Un registro es una aplicación que hospeda imágenes y proporciona servicios de imágenes y servicios de contenedores. Para compartir la imagen, debe insertarla en un registro. Docker Hub es un registro para imágenes de Docker que le permite hospedar sus propios repositorios, ya sean públicos o privados.

Antes de poder insertar una imagen, debe iniciar sesión en Docker Hub mediante el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Reemplace `<docker-id>` por el nombre de cuenta y escriba la contraseña en la consola en el símbolo del sistema. Para ver otras opciones de contraseña de Docker Hub, consulte la [documentación del comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Un mensaje de inicio de sesión correcto confirmará que inició sesión. Cuando haya iniciado sesión, inserte la imagen en Docker Hub mediante el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Examine la salida del comando para comprobar que la inserción se realizó correctamente.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Ahora, puede usar esta imagen como origen de implementación para una nueva aplicación de función en Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

Actualmente, los planes de consumo no admiten el hospedaje de Functions en Linux. Tiene que hospedar las aplicaciones de contenedor de Linux en un plan de App Service de Linux. Para obtener más información sobre el hospedaje, consulte [Comparación de los planes de hospedaje de Azure Functions](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Creación e implementación de la imagen personalizada

La aplicación de función hospeda la ejecución de las funciones. Cree una aplicación de función a partir de una imagen de Docker Hub mediante el comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

En el siguiente comando, sustituya un nombre de aplicación de función único donde vea el marcador de posición `<app_name>` y el nombre de la cuenta de almacenamiento para `<storage_name>`. El `<app_name>` se usa como el dominio DNS predeterminado para la Function App y, por ello, el nombre debe ser único en todas las aplicaciones de Azure. Al igual que antes, `<docker-id>` es el nombre de cuenta de Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Después de que se haya creado la Function App, la CLI de Azure muestra información similar al siguiente ejemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

El parámetro _deployment-container-image-name_ indica la imagen hospedada en Docker Hub que se usará para crear la aplicación de función.

## <a name="configure-the-function-app"></a>Configuración de la Function App

La función necesita la cadena de conexión para conectarse a la cuenta de almacenamiento predeterminada. Al publicar la imagen personalizada en una cuenta de contenedor privada, debe establecer los valores de la aplicación como variables de entorno en Dockerfile mediante la [instrucción ENV](https://docs.docker.com/engine/reference/builder/#env), o algo similar.

En este caso, `<storage_account>` es el nombre de la cuenta de almacenamiento que ha creado. Obtenga la cadena de conexión con el comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Agregue esta configuración de aplicación a la Function App con el comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Si el contenedor es privado, tendría que establecer también la siguiente configuración de aplicación  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Tendrá que detener y, a continuación, iniciar la aplicación de función para que se tomen estos valores

Ahora puede probar cómo se ejecutan las funciones en Linux en Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una aplicación de función y un archivo Dockerfile mediante Core Tools.
> * Crear una imagen personalizada con Docker
> * Publicar una imagen personalizada en un registro de contenedor
> * Cree una cuenta de Azure Storage.
> * Crear un plan de App Service de Linux
> * Implementar una aplicación de función desde Docker Hub
> * Agregar la configuración de aplicación a la aplicación de función

Aprenda a habilitar la funcionalidad de integración continua integrada en la plataforma central de App Service. Puede configurar la aplicación de función para que el contenedor se vuelva a implementar cuando actualice su imagen en Docker Hub.

> [!div class="nextstepaction"] 
> [Implementación continua con Web App for Containers](../app-service/containers/app-service-linux-ci-cd.md)
