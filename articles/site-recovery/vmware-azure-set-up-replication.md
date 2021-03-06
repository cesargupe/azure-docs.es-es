---
title: Configuración y administración de directivas de replicación para la recuperación ante desastres de VMware en Azure con Azure Site Recovery | Microsoft Docs
description: Se describe cómo configurar opciones de replicación para la recuperación ante desastres de VMware en Azure con Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: fd987097c2ca7b1e7509a1a0e63905c36ec8fec8
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212903"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Configuración y administración de directivas de replicación para la recuperación ante desastres de VMware en Azure
En este artículo se describe cómo configurar una directiva de replicación cuando se replican máquinas virtuales de VMware en Azure mediante [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Para crear una directiva

1. Seleccione **Administrar** > **Infraestructura de Site Recovery**.
1. En **For VMware and Physical machines** (Para máquinas de VMware y físicas), seleccione **Directivas de replicación**. 
1. Haga clic en **+Directiva de replicación** y especifique el nombre de la directiva.
1. En **Umbral de RPO**, especifique el límite de RPO. Se generan alertas cuando la replicación continua supera este límite.
1. En **Retención de punto de recuperación**, especifique (en horas) la duración del período de retención para cada punto de recuperación. Las máquinas protegidas se pueden recuperar en cualquier punto dentro de un período de retención. Se admite una retención de hasta 24 horas para máquinas replicadas en Premium Storage. Se admite hasta 72 horas para un almacenamiento estándar.
1. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan las instantáneas coherentes con la aplicación.
1. Haga clic en **OK**. La directiva debe demorar entre 30 y 60 segundos en crearse.

Cuando se crea una directiva de replicación, se crea automáticamente una directiva de replicación para la conmutación por recuperación, con el sufijo "failback". Una vez creada la directiva, se puede editar seleccionándola > **Editar configuración**.

## <a name="associate-a-configuration-server"></a>Asociación de un servidor de configuración 

Asocie la directiva de replicación con el servidor de configuración local.

1. Haga clic en **Asociar** y seleccione el servidor de configuración.

    ![Asociación de servidor de configuración](./media/vmware-azure-set-up-replication/associate1.png)

1. Haga clic en **OK**. El servidor de configuración debería demorar entre uno y dos minutos en asociarse.

    ![Asociación del servidor de configuración](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Desasociación o eliminación de una directiva de replicación
1. Elija la directiva de replicación.
    a. Para desasociar la directiva del servidor de configuración, asegúrese de que no haya máquinas replicadas que estén utilizando la directiva. Después, haga clic en **Desasociar**.
    b. Para eliminar la directiva, asegúrese de que no esté asociada con un servidor de configuración. Después, haga clic en **Eliminar**. Tardará entre 30 y 60 segundos en eliminarla.
1. Haga clic en **OK**.
