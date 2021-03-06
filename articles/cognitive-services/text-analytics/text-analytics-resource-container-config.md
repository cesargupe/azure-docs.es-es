---
title: Configuración de contenedores
titlesuffix: Text Analytics - Cognitive Services - Azure
description: Opciones de configuración de contenedores de Text Analytics.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 0f6b8fa27d2db45be2c677a52c53cff5847acf4a
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634929"
---
# <a name="configure-containers"></a>Configuración de contenedores

Text Analytics proporciona a cada contenedor un marco de configuración común, por lo que puede configurar y administrar fácilmente la configuración de almacenamiento, registro, telemetría y seguridad de los contenedores.

## <a name="configuration-settings"></a>Valores de configuración

Las opciones de configuración de los contenedores de Text Analytics son jerárquicas y todos los contenedores usan una jerarquía compartida, que se basa en la siguiente estructura de nivel superior:

* [ApiKey](#apikey-configuration-setting)
* [Application Insights](#applicationinsights-configuration-settings)
* [Autenticación](#authentication-configuration-settings)
* [Facturación](#billing-configuration-setting)
* [Eula](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [Registro](#logging-configuration-settings)
* [Mounts](#mounts-configuration-settings)

Puede usar [variables de entorno](#configuration-settings-as-environment-variables) o [argumentos de la línea de comandos](#configuration-settings-as-command-line-arguments) para especificar las opciones de configuración al crear una instancia de contenedores de Text Analytics.

Los valores de variable de entorno invalidan los valores de argumento de la línea de comandos, que a su vez invalidan los valores predeterminados para la imagen de contenedor. En otras palabras, si especifica valores diferentes en una variable de entorno y un argumento de la línea de comandos para la misma opción de configuración, tales como `Logging:Disk:LogLevel`, y, a continuación, crea una instancia de un contenedor, el valor de la variable de entorno se utiliza por el contenedor del que se creó una instancia.

### <a name="configuration-settings-as-environment-variables"></a>Opciones de configuración como variables de entorno

Puede usar la [sintaxis de variables de entorno de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) para especificar las opciones de configuración.

El contenedor lee las variables de entorno del usuario cuando se crea una instancia del contenedor. Si existe una variable de entorno, su valor invalida el valor predeterminado para la opción de configuración especificada. La ventaja de usar variables de entorno es que se pueden establecer varias opciones de configuración antes de crear instancias de contenedores y varios contenedores pueden usar automáticamente el mismo conjunto de opciones de configuración.

Por ejemplo, los siguientes comandos usan una variable de entorno para configurar el nivel de registro de consola en [LogLevel.Information](https://msdn.microsoft.com) y, a continuación, se crea una instancia de un contenedor de la imagen de contenedor de Análisis de sentimiento. El valor de la variable de entorno invalida la opción de configuración predeterminada.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Opciones de configuración como argumentos de la línea de comandos

Puede usar la [sintaxis de argumento de la línea de comandos de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) para especificar las opciones de configuración.

Puede especificar las opciones de configuración en el parámetro `ARGS` opcional del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) que se usa para crear una instancia de un contenedor a partir de una imagen de contenedor descargada. La ventaja de utilizar argumentos de la línea de comandos es que cada contenedor puede usar un conjunto de opciones de configuración diferente y personalizado.

Por ejemplo, los siguientes comandos crean una instancia de un contendedor a partir de la imagen de contenedor de Análisis de sentimiento y se configura el nivel de registro de consola en LogLevel.Information, lo que invalida la opción de configuración predeterminada.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Opción de configuración ApiKey

La opción de configuración `ApiKey` especifica la clave de configuración del recurso de Text Analytics en Azure que se usa para realizar un seguimiento de la información de facturación del contenedor. Debe especificar un valor para esta opción de configuración que debe ser una clave de configuración válida para el recurso de Text Analytics especificado para la opción de configuración [`Billing`](#billing-configuration-setting).

> [!IMPORTANT]
> Las opciones de configuración [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) y [`Eula`](#eula-configuration-setting) se usan en conjunto, y debe proporcionar los valores válidos para las tres; en caso contrario, no se inicia el contenedor. Para obtener más información sobre el uso de estas opciones de configuración para crear instancias de un contenedor, consulte [Facturación](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Opción de configuración ApplicationInsights

Los valores de configuración de la sección `ApplicationInsights` le permiten agregar la compatibilidad de telemetría de [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) al contenedor. Azure Application Insights proporciona una supervisión detallada del contenedor hasta el nivel de código. Puede supervisar fácilmente la disponibilidad, el rendimiento y el uso del contenedor. También puede identificar y diagnosticar errores en el contenedor rápidamente sin tener que esperar a que un usuario informe de ellos.

En la tabla siguiente se describen las opciones de configuración compatibles en la sección `ApplicationInsights`.

| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|------|-----------|-------------|
| `InstrumentationKey` | string | Clave de instrumentación de la instancia de Application Insights para la que se envían los datos de telemetría del contenedor. Para más información, consulte [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Opción de configuración Authentication

Las opciones de configuración `Authentication` proporcionan opciones de seguridad de Azure para el contenedor. Aunque las opciones de configuración de esta sección están disponibles para todos los contenedores de Text Analytics, la manera en que se usan los valores de configuración es específica de cada contenedor y es posible que los contenedores no usen esta sección.

En la tabla siguiente se describen las opciones de configuración compatibles en la sección `Authentication`.

| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|------|-----------|-------------|
| `ApiKey` | Cadena o matriz | Claves de suscripción de Azure que utilizada el contenedor para tener acceso a otros recursos de Azure, si el contenedor lo necesita.<br/> Si el contenedor usa más de una clave de suscripción, este valor se especifica como una matriz de cadenas; en caso contrario, se usa un valor de cadena para especificar una clave de suscripción única utilizada por el contenedor. |

## <a name="billing-configuration-setting"></a>Opción de configuración Billing

La opción de configuración `Billing` especifica el URI del punto de conexión del recurso de Text Analytics en Azure que se usa para realizar un seguimiento de la información de facturación del contenedor. Debe especificar un valor para esta opción de configuración y el valor debe ser un URI de punto de conexión válido para un recurso de Text Analytics en Azure.

> [!IMPORTANT]
> Las opciones de configuración [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) y [`Eula`](#eula-configuration-setting) se usan en conjunto, y debe proporcionar los valores válidos para las tres; en caso contrario, no se inicia el contenedor. Para obtener más información sobre el uso de estas opciones de configuración para crear instancias de un contenedor, consulte [Facturación](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="eula-configuration-setting"></a>Opción de configuración Eula

La opción de configuración `Eula` indica que ha aceptado la licencia del contenedor. Debe especificar un valor para esta opción de configuración y el valor debe establecerse en `accept`.

> [!IMPORTANT]
> Las opciones de configuración [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) y [`Eula`](#eula-configuration-setting) se usan en conjunto, y debe proporcionar los valores válidos para las tres; en caso contrario, no se inicia el contenedor. Para obtener más información sobre el uso de estas opciones de configuración para crear instancias de un contenedor, consulte [Facturación](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="fluentd-configuration-settings"></a>Opciones de configuración Fluentd

La sección `Fluentd` administra las opciones de configuración de [Fluentd](https://www.fluentd.org), un recopilador de datos de código abierto para el registro unificado. Los contenedores de Text Analytics incluyen un proveedor de registro de Fluentd que permite que el contenedor escriba el registro y, opcionalmente, datos de métrica en un servidor de Fluentd.

En la tabla siguiente se describen las opciones de configuración compatibles en la sección `Fluentd`.

| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|------|-----------|-------------|
| `Host` | Cadena | Dirección IP o nombre de host DNS del servidor de Fluentd. |
| `Port` | Entero | Puerto del servidor de Fluentd.<br/> El valor predeterminado es 24 224. |
| `HeartbeatMs` | Entero | Intervalo de latidos (en milisegundos). Si no se envía ningún tráfico de evento antes de que este intervalo expire, se envía un latido al servidor de Fluentd. El valor predeterminado es 60 000 milisegundos (1 minuto). |
| `SendBufferSize` | Entero | Espacio en búfer de red (en bytes) asignado para las operaciones de envío. El valor predeterminado es 32 768 bytes (32 kilobytes). |
| `TlsConnectionEstablishmentTimeoutMs` | Entero | Tiempo de expiración (en milisegundos) para establecer una conexión SSL/TLS con el servidor de Fluentd. El valor predeterminado es 10 000 milisegundos (10 segundos).<br/> Si `UseTLS` se establece en false, este valor se ignora. |
| `UseTLS` | boolean | Indica si el contenedor debe utilizar SSL/TLS para comunicarse con el servidor de Fluentd. El valor predeterminado es false. |

## <a name="logging-configuration-settings"></a>Opciones de configuración Logging

Las opciones de configuración `Logging` administran la compatibilidad con el registro de ASP.NET Core del contenedor. Puede usar los mismos valores y opciones de configuración para el contenedor que para una aplicación ASP.NET Core. Los siguientes proveedores de registro son compatibles con el contenedor de Text Analytics:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Proveedor de registro de `Console` de ASP.NET Core. Se admiten todos los valores predeterminados y las opciones de configuración de ASP.NET Core para este proveedor de registro.
* [Depuración](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Proveedor de registro de `Debug` de ASP.NET Core. Se admiten todos los valores predeterminados y las opciones de configuración de ASP.NET Core para este proveedor de registro.
* Disco  
  Proveedor de registro JSON. Este proveedor de registro escribe datos de registro para el montaje de salida.  
  El proveedor de registro `Disk` admite la configuración siguiente:  

  | NOMBRE | Tipo de datos | DESCRIPCIÓN |
  |------|-----------|-------------|
  | `Format` | string | Formato de salida de los archivos de registro.<br/> **Nota:** Este valor debe establecerse en `json` para habilitar el proveedor de registro. Si se especifica este valor sin especificar también un montaje de salida al crear una instancia de un contenedor, se produce un error. |
  | `MaxFileSize` | Entero | Tamaño máximo en megabytes (MB) de un archivo de registro. Cuando el tamaño del archivo de registro actual cumple este valor o lo supera, el proveedor de registro inicia un nuevo archivo de registro. Si se especifica -1, el tamaño del archivo de registro solo está limitado por el tamaño máximo de archivo, si existe, para el montaje de salida. El valor predeterminado es 1. |

Para obtener más información acerca de cómo configurar la compatibilidad con el registro de ASP.NET Core, consulte [Configuración del archivo de configuración](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Opciones de configuración Mounts

Los contenedores de Docker que proporcionan los contenedores de Text Analytics están diseñados para ser inmutables y sin estado. En otras palabras, los archivos creados dentro de un contenedor se almacenan en una capa grabable del contenedor, que solo se conserva mientras el contenedor se está ejecutando y a la que no se puede acceder fácilmente. Si el contenedor se detiene o elimina, los archivos creados dentro de él se destruyen.

Sin embargo, dado que son contenedores de Docker, puede usar las opciones de almacenamiento de Docker, como los montajes de enlace y volúmenes, para leer y escribir datos guardados fuera del contenedor, si lo admite el contenedor. Para obtener más información acerca de cómo especificar y administrar las opciones de almacenamiento de Docker, consulte [Manage data in Docker](https://docs.docker.com/storage/) (Administración de datos en Docker).

> [!NOTE]
> Normalmente, no tendrá que cambiar los valores de estas opciones de configuración. En su lugar, usará los valores especificados en estas opciones de configuración como destinos al especificar montajes de entrada y salida para el contenedor. Para obtener más información acerca de cómo especificar montajes de entrada y salida, consulte [Montajes de entrada y salida](#input-and-output-mounts).

En la tabla siguiente se describen las opciones de configuración compatibles en la sección `Mounts`.

| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|------|-----------|-------------|
| `Input` | string | Destino del montaje de entrada. El valor predeterminado es `/input`. |
| `Output` | string | Destino del montaje de salida. El valor predeterminado es `/output`. |

### <a name="input-and-output-mounts"></a>Montajes de entrada y salida

De forma predeterminada, cada contenedor puede admitir un *montaje de entrada*, desde el que el contenedor puede leer datos, y un *montaje de salida*, en el que el contenedor puede escribir datos. No es necesario que los contenedores admitan montajes de entrada o salida, sin embargo, cada contenedor puede utilizar tanto montajes de entrada como de salida para fines específicos de los contenedores, además de las opciones de registro admitidas por el contenedor de Text Analytics. En la siguiente tabla se incluye una lista con los montajes de entrada y salida admitidos para cada contenedor en contenedores de Text Analytics.

| Contenedor | Montaje de entrada | Montaje de salida |
|-----------|-------------|--------------|
|[Extracción de frases clave](#working-with-key-phrase-extraction) | No compatible | Opcional |
|[Detección de idioma](#working-with-language-detection) | No compatible | Opcional |
|[Análisis de sentimiento](#working-with-sentiment-analysis) | No compatible | Opcional |

Puede especificar un montaje de entrada y uno de salida mediante la especificación de la opción `--mount` del comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) que se usa para crear una instancia de un contenedor a partir de una imagen de contenedor descargada. De forma predeterminada, el montaje de entrada usa `/input` como destino, mientras que el montaje de salida usa `/output`. Se puede especificar cualquier opción de almacenamiento de Docker disponible para el host de contenedor de Docker en la opción `--mount`.

Por ejemplo, el comando siguiente define un montaje de enlace de Docker para la carpeta `D:\Output` en el equipo host como montaje de salida. A continuación, crea una instancia de un contenedor a partir de la imagen de contenedor de Análisis de sentimiento y guarda los archivos de registro en formato JSON para el montaje de salida.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```
