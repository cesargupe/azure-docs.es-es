---
title: Composición del módulo de Azure IoT Edge | Microsoft Docs
description: Obtenga información sobre cómo un manifiesto de implementación declara qué módulos se deben implementar, cómo implementarlos y cómo crear rutas de mensajes entre ellos.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3201e8509e7c63bb0d9b607d26292bd85e2b605d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569240"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>Obtenga información sobre cómo implementar módulos y establecer rutas en IoT Edge

Cada dispositivo IoT Edge ejecuta al menos dos módulos: $edgeAgent y $edgeHub, que constituyen el tiempo de ejecución de IoT Edge. Además, cualquier dispositivo IoT Edge puede ejecutar varios módulos para llevar a cabo cualquier número de procesos. Si implementa todos estos módulos en un dispositivo a la vez, necesita una manera de declarar qué módulos se incluyen y cómo interactúan entre sí. 

El *manifiesto de implementación* es un documento JSON que describe lo siguiente:

* La configuración del agente de Edge, que incluye la imagen del contenedor para cada módulo, las credenciales para acceder a los registros de contenedor privados y las instrucciones de cómo debe crearse y administrarse cada módulo.
* La configuración del centro de Edge, lo que incluye cómo fluyen los mensajes entre los módulos y finalmente a IoT Hub.
* Opcionalmente, las propiedades deseadas de los módulos gemelos.

Todos los dispositivos IoT Edge deben configurarse con un manifiesto de implementación. Si no, una instancia de IoT Edge en tiempo de ejecución recién instalada notificará un código de error hasta que se configuren con un manifiesto válido. 

En los tutoriales de Azure IoT Edge, creará un manifiesto de implementación a través de un asistente del portal de Azure IoT Edge. También puede aplicar un manifiesto de implementación mediante programación con REST o el SDK del servicio IoT Hub. Para más información, consulte el artículo [Descripción de las implementaciones de IoT Edge](module-deployment-monitoring.md).

## <a name="create-a-deployment-manifest"></a>Creación de un manifiesto de implementación

A nivel general, el manifiesto de implementación configura las propiedades deseadas de un módulo gemelo en los módulos de IoT Edge implementados en un dispositivo de IoT Edge. Dos de estos módulos siempre existen: `$edgeAgent`, y `$edgeHub`.

Un manifiesto de implementación que contiene solo la instancia en tiempo de ejecución de IoT Edge (agente y centro) es válido.

El manifiesto sigue esta estructura:

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>Configuración de módulos

Se debe indicar al entorno de ejecución de IoT Edge cómo instalar los módulos de la implementación. La información de configuración y administración de todos los módulos va en el interior de las propiedades deseadas de **$edgeAgent**. Esta información incluye los parámetros de configuración para el propio agente de Edge. 

Para ver una lista completa de propiedades que pueden o deben incluirse, consulte [Properties of the Edge agent and Edge hub](module-edgeagent-edgehub.md) (Propiedades del agente de Edge y el centro de Edge).

Las propiedades de $edgeAgent siguen esta estructura:

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>Declaración de rutas

El centro de Edge proporciona una manera de enrutar de forma declarativa los mensajes entre los módulos y entre los módulos e IoT Hub. El centro de Edge administra toda la comunicación, por lo que la información de la ruta va dentro de las propiedades deseadas de **$edgeHub**. Puede tener varias rutas dentro de la misma implementación.

Las rutas se declaran en las propiedades deseadas de **$edgeHub** con la sintaxis siguiente:

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

Cada ruta necesita un origen y un receptor, pero la condición es una parte opcional que puede usar para filtrar los mensajes. 


### <a name="source"></a>Origen
El origen especifica de dónde proceden los mensajes. Puede ser cualquiera de los siguientes valores:

| Origen | DESCRIPCIÓN |
| ------ | ----------- |
| `/*` | Todos los mensajes de dispositivo a nube desde cualquier dispositivo o módulo. |
| `/messages/*` | Cualquier mensaje de dispositivo a nube que envíe un dispositivo o un módulo con alguna salida o sin ninguna. |
| `/messages/modules/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo con alguna salida o sin ninguna. |
| `/messages/modules/{moduleId}/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo concreto sin ninguna salida. |
| `/messages/modules/{moduleId}/outputs/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo concreto con alguna salida. |
| `/messages/modules/{moduleId}/outputs/{output}` | Cualquier mensaje de dispositivo a nube que envíe un módulo concreto con una salida específica. |

### <a name="condition"></a>Condición
La condición es opcional en una declaración de ruta. Si desea pasar todos los mensajes desde el receptor al origen, simplemente omita la cláusula **WHERE** por completo. O bien, puede usar el [lenguaje de consulta de IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md) para filtrar por determinados mensajes o tipos de mensajes que cumplen la condición.

Los mensajes que pasan entre módulos con IoT Edge tienen el mismo formato que los mensajes que pasan entre los dispositivos y Azure IoT Hub. Todos los mensajes tienen el formato JSON y tienen los parámetros **systemProperties**, **appProperties** y **body**. 

Puede generar consultas en función de los tres parámetros con la sintaxis siguiente: 

* Propiedades del sistema: `$<propertyName>` o `{$<propertyName>}`
* Propiedades de la aplicación: `<propertyName>`
* Propiedades del cuerpo: `$body.<propertyName>` 

Para obtener ejemplos sobre cómo crear consultas para las propiedades de mensaje, consulte [Expresiones de consulta de rutas de mensajes de dispositivo a la nube](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

Un ejemplo que es específico de IoT Edge es cuando desea filtrar los mensajes recibidos en un dispositivo de puerta de enlace desde un dispositivo hoja. Los mensajes que proceden de los módulos contienen una propiedad del sistema denominada **connectionModuleId**. Así pues, si desea enrutar los mensajes desde dispositivos hoja directamente a IoT Hub, utilice la siguiente ruta para excluir mensajes de módulo:

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>Receptor
El receptor define dónde se envían los mensajes. Puede ser cualquiera de los siguientes valores:

| Receptor | DESCRIPCIÓN |
| ---- | ----------- |
| `$upstream` | Envía el mensaje a IoT Hub. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Envía el mensaje a la entrada `{input}` del módulo`{moduleId}`. |

IoT Edge proporciona garantías de por lo menos una vez. El centro de Edge almacena mensajes localmente en caso de que una ruta no pueda entregar el mensaje a su receptor. Por ejemplo, si el centro de Edge no se puede conectar a IoT Hub, o el módulo de destino no está conectado.

El centro de Edge almacena los mensajes durante el tiempo especificado en la propiedad `storeAndForwardConfiguration.timeToLiveSecs` de las [propiedades deseadas del centro de Edge](module-edgeagent-edgehub.md).

## <a name="define-or-update-desired-properties"></a>Definición o actualización de las propiedades deseadas 

El manifiesto de implementación puede especificar propiedades deseadas para el módulo gemelo de cada módulo implementado en el dispositivo IoT Edge. Cuando se especifican las propiedades deseadas en el manifiesto de implementación, sobrescriben a las que estén presentes en el módulo gemelo.

Si no se especifican las propiedades deseadas del módulo gemelo en el manifiesto de implementación, IoT Hub no modificará el módulo y se podrán establecer las propiedades deseadas mediante programación.

Los módulos gemelos se modifican con los mismos mecanismos que los dispositivos gemelos. Para más información vea la [guía para desarrolladores de módulos gemelos](../iot-hub/iot-hub-devguide-module-twins.md).   

## <a name="deployment-manifest-example"></a>Ejemplo de manifiesto de implementación

Esto es un ejemplo de documento JSON de manifiesto de implementación.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "{password}",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para ver una lista completa de propiedades que pueden o deben incluirse en $edgeAgent y $edgeHub, consulte [Properties of the Edge agent and Edge hub](module-edgeagent-edgehub.md) (Propiedades del agente de Edge y el centro de Edge).

* Ahora que sabe cómo se usan los módulos de IoT Hub, [descubra los requisitos y las herramientas para desarrollar módulos de IoT Edge](module-development.md).
