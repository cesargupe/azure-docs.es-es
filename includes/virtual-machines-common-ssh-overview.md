---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506403"
---
## <a name="overview-of-ssh-and-keys"></a>Información general sobre SSH y sus claves

SSH es un protocolo de conexión cifrada que permite inicios de sesión seguros a través de conexiones no seguras. SSH es el protocolo de conexión predeterminado de las máquinas virtuales Linux hospedadas en Azure. Aunque el propio SSH proporciona una conexión cifrada, el uso de contraseñas con conexiones SSH deja la máquina virtual vulnerable a ataques por fuerza bruta o que se puedan averiguar las contraseñas. Existe un método más seguro, que es el preferido, para conectarse a una máquina virtual mediante SSH y consiste en utilizar un par con una clave pública y otra privada, que también se denominan *claves SSH*. 

* La *clave pública* se coloca en la máquina virtual Linux, o en cualquier otro servicio que se desee usar con una criptografía de clave pública.

* La *clave privada* del sistema local es utilizada por un cliente SSH para verificar la identidad al conectarse a la máquina virtual Linux. Esta clave se debe proteger, por lo que no se debe compartir.

En función de las directivas de seguridad de la organización, puede volver a usar un par de claves públicas y privadas único para obtener acceso a varias máquinas virtuales y servicios de Azure. No es necesario usar ningún par de claves independiente para cada máquina virtual o servicio al que se quiera acceder. 

La clave pública se puede compartir con cualquiera. Sin embargo, solo usted (o su infraestructura de seguridad local) debe tener la clave privada.