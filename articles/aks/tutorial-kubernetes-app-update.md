---
title: 'Tutorial de Kubernetes en Azure: Actualización de una aplicación'
description: En este tutorial de Azure Kubernetes Service (AKS), aprenderá a actualizar la implementación de una aplicación existente a AKS con una nueva versión del código de la aplicación.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b2dd52fec112b879e072d3ac5598dd7978e68cbc
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918651"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Tutorial: Actualización de una aplicación en Azure Kubernetes Service (AKS)

Después de implementar una aplicación en Kubernetes, se puede actualizar especificando una nueva imagen de contenedor o la versión de la imagen. Si lo hace, la actualización se preconfigura para que solo una parte de la implementación se actualice simultáneamente. Esta actualización preconfigurada permite que la aplicación siga ejecutándose durante la actualización. También proporciona un mecanismo de reversión si se produce un error de implementación.

En este tutorial, la sección seis de siete, se actualiza la aplicación de ejemplo de Azure Vote. Aprenderá a:

> [!div class="checklist"]
> * Actualizar el código de la aplicación de front-end
> * Crear una imagen de contenedor actualizada
> * Insertar una imagen de contenedor en Azure Container Registry
> * Implementar la imagen de contenedor actualizada

## <a name="before-you-begin"></a>Antes de empezar

En los tutoriales anteriores se ha empaquetado una aplicación en una imagen de contenedor, esta se ha cargado en Azure Container Registry (ACR) y se ha creado un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes.

También se clonó un repositorio de aplicaciones que incluye el código fuente de la aplicación y un archivo de Docker Compose creado previamente que se usa en este tutorial. Confirme que ha creado un clon del repositorio y que ha cambiado los directorios en el repositorio clonado. Si no ha finalizado estos pasos y desea continuar, vuelva al [Tutorial 1: Creación de las imágenes de contenedor][aks-tutorial-prepare-app].

Para realizar este tutorial es necesario disponer de la versión 2.0.44, o superior, de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure][azure-cli-install].

## <a name="update-an-application"></a>Actualización de una aplicación

Vamos a realizar un cambio en la aplicación de ejemplo y, después, a actualizar la versión ya implementada en el clúster de AKS. El código fuente de la aplicación de ejemplo se puede encontrar en el directorio *azure-vote*. Abra el archivo *config_file.cfg* con un editor, como `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Cambie los valores de *VOTE1VALUE* y *VOTE2VALUE* a otros colores. El ejemplo siguiente muestra los valores de color actualizados:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Guarde y cierre el archivo.

## <a name="update-the-container-image"></a>Actualización de la imagen del contenedor

Para volver a crear la imagen del front-end y probar la aplicación actualizada, use [docker-compose][docker-compose]. El argumento `--build` se emplea para indicar a Docker Compose que vuelva a crear la imagen de la aplicación.

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Prueba de la aplicación de forma local

Para comprobar que la imagen de contenedor actualizada muestra los cambios, abra un explorador web local en http://localhost:8080.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

Los valores de color actualizados que se proporcionan en el archivo *config_file.cfg* se muestran en la aplicación en ejecución.

## <a name="tag-and-push-the-image"></a>Etiquetado e inserción de la imagen

Para usar correctamente la imagen actualizada, etiquete la imagen *azure-vote-front* con el nombre del servidor de inicio de sesión de seguridad del registro de ACR. Para obtener el nombre del servidor de inicio de sesión, use el comando [az acr list](/cli/azure/acr#az_acr_list):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use [docker tag][docker-tag] para etiquetar la imagen. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de ACR o el nombre de host del registro público y actualice la versión de la imagen a *: v2* como se muestra a continuación:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Ahora use [docker push][docker-push] para cargar la imagen en el registro. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de ACR. Si experimenta problemas al insertar el registro ACR, asegúrese de que ha ejecutado el comando [az acr login][az-acr-login].

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Implementación de la aplicación actualizada

Para garantizar el tiempo máximo de actividad, se deben ejecutar varias instancias de pod de la aplicación. Compruebe el número de instancias de front-end en ejecución con el comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Si no tiene varios pods de front-end, escale la implementación de *azure-vote-front*.

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Use el comando [kubectl set][kubectl-set] para actualizar la aplicación. Actualice `<acrLoginServer>` con el nombre de host o de servidor de inicio de sesión del registro del contenedor y especifique la versión de la aplicación *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Para supervisar la implementación, use el comando [kubectl get pod][kubectl-get]. A medida que se implementa la aplicación actualizada, sus pods se terminan y se vuelven a crear con la nueva imagen de contenedor.

```console
kubectl get pods
```

La salida del ejemplo siguiente muestra los pods que terminan y las nuevas instancias que se ejecutan a medida que progresa la implementación:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Prueba de la aplicación actualizada

Para ver la aplicación actualizada, obtenga primero la dirección IP externa del servicio `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Ahora abra un explorador web en la dirección IP.

![Imagen del clúster de Kubernetes en Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, actualiza una aplicación y se implanta esta actualización en un clúster de Kubernetes. Ha aprendido a:

> [!div class="checklist"]
> * Actualizar el código de la aplicación de front-end
> * Crear una imagen de contenedor actualizada
> * Insertar una imagen de contenedor en Azure Container Registry
> * Implementar la imagen de contenedor actualizada

Pase al siguiente tutorial para aprender a actualizar un clúster de AKS a una nueva versión de Kubernetes.

> [!div class="nextstepaction"]
> [Actualización de Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr#az_acr_login
[azure-cli-install]: /cli/azure/install-azure-cli
