---
title: Configuración de los parámetros del servicio en Azure Database for MariaDB
description: En este artículo se describe cómo configurar los parámetros del servicio en Azure Database for MariaDB mediante la utilidad de la línea de comandos de la CLI de Azure.
services: mariadb
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 4e391ce56b31f35da67b3d975bbc1290c17cb139
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516624"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalización de los parámetros de configuración del servidor con la CLI de Azure
Puede enumerar, mostrar y actualizar los parámetros de configuración de un servidor de Azure Database for MariaDB con la CLI de Azure, la utilidad de la línea de comandos de Azure. En el nivel del servidor, se expone y se puede modificar un subconjunto de las opciones de configuración del motor.

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- [Un servidor de Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- La utilidad de línea de comandos [CLI de Azure](/cli/azure/install-azure-cli) o usar Azure Cloud Shell en el explorador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Lista de los parámetros de configuración del servidor de Azure Database for MariaDB
Para obtener una lista de todos los parámetros modificables en un servidor y sus valores, ejecute el comando [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list).

Puede enumerar los parámetros de configuración del servidor **mydemoserver.mariadb.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Para ver la definición de cada uno de los parámetros enumerados, consulte la sección de referencia de MariaDB en [Server System Variables](https://mariadb.com/kb/en/library/server-system-variables/) (Variables de sistema del servidor).

## <a name="show-server-configuration-parameter-details"></a>Presentación de los detalles de los parámetros de configuración del servidor
Para mostrar los detalles de un parámetro de configuración específico de un servidor, ejecute el comando [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show).

En este ejemplo se muestran detalles del parámetro de configuración **slow\_query\_log** del servidor **mydemoserver.mariadb.database.azure.com** en el grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificación de un valor de los parámetros de configuración del servidor
También puede modificar el valor de un determinado parámetro de configuración del servidor, lo que actualizará el valor de configuración subyacente del motor del servidor de MariaDB. Para actualizar la configuración, use el comando [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set). 

Para actualizar el parámetro de configuración **slow\_query\_log** del servidor **mydemoserver.mariadb.database.azure.com** en el grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Si desea restablecer el valor de un parámetro de configuración, omita el parámetro opcional `--value` y el servicio aplicará el valor predeterminado. Para el ejemplo anterior, sería:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Este código restablece la configuración **slow\_query\_log** en el valor predeterminado **Apagado**. 

## <a name="working-with-the-time-zone-parameter"></a>Trabajo con el parámetro de zona horaria

### <a name="populating-the-time-zone-tables"></a>Relleno de las tablas de la zona horaria

Las tablas de la zona horaria del servidor se pueden rellenar mediante una llamada al procedimiento almacenado `az_load_timezone` desde una herramienta como la línea de comandos de MariaDB o MariaDB Workbench.

> [!NOTE]
> Si ejecuta el comando `az_load_timezone` desde MariaDB Workbench, es posible que primero tenga que desactivar el modo de actualización segura mediante `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Para ver los valores de zonas horarias disponibles, ejecute el comando siguiente:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Establecimiento de la zona horaria de nivel global

La zona horaria de nivel global se puede establecer mediante el comando [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set).

El siguiente comando actualiza el parámetro de configuración **time\_zone** del servidor **mydemoserver.mariadb.database.azure.com** en el grupo de recursos **myresourcegroup** a **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Establecimiento de la zona horaria de nivel de sesión

La zona horaria de nivel de sesión se puede establecer mediante la ejecución del comando `SET time_zone` desde una herramienta como la línea de comandos de MariaDB o MariaDB Workbench. En el ejemplo siguiente se establece la zona horaria en **US/Pacific**.  

```sql
SET time_zone = 'US/Pacific';
```

Consulte en la documentación de MariaDB las [Funciones de fecha y hora](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Pasos siguientes

- Cómo configurar [parámetros del servidor en Azure Portal](howto-server-parameters.md)