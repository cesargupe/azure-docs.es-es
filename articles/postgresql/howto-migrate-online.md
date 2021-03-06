---
title: Migración con tiempo de inactividad mínimo a Azure Database for PostgreSQL
description: En este artículo se describe cómo realizar una migración con tiempo de inactividad mínimo de una base de datos de PostgreSQL a una base de datos de Azure Database for PostgreSQL mediante Azure Database Migration Service.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 80e5d30677735b35d90fda6288d7bf6f2ea4aa1b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093840"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migración con tiempo de inactividad mínimo a Azure Database for PostgreSQL
Puede realizar migraciones de PostgreSQL a Azure Database for PostgreSQL con un tiempo de inactividad mínimo mediante la **funcionalidad de sincronización continua** recién introducida para el [servicio Azure Database Migration Service](https://aka.ms/get-dms) (DMS). Esta funcionalidad limita la cantidad de tiempo de inactividad en el que incurre la aplicación.

## <a name="overview"></a>Información general
DMS realiza una carga inicial de la base de datos local en Azure Database for PostgreSQL y, a continuación, realiza la sincronización continua de todas las transacciones nuevas de Azure mientras la aplicación se siga ejecutando. Una vez que los datos se ponen al día en el destino de Azure, puede detener la aplicación por un breve período de tiempo (tiempo de inactividad mínimo), esperar al último lote de datos (desde el momento en que detiene la aplicación hasta el momento en que la aplicación deja de estar disponible efectivamente para aceptar cualquier tráfico nuevo) para ponerse al día en el destino y, a continuación, actualizar la cadena de conexión para que apunte a Azure. Cuando haya terminado, la aplicación estará funcionando en Azure.

![Sincronización continua con Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

La migración de DMS de orígenes de PostgreSQL está actualmente en versión preliminar. Si desea probar el servicio de migración para cargas de trabajo de PostgreSQL, regístrese en la [página de la versión preliminar](https://aka.ms/dms-preview) de Azure DMS para mostrar su interés. Sus comentarios son muy valiosos para ayudar a mejorar aún más el servicio.

## <a name="next-steps"></a>Pasos siguientes
- Vea el vídeo sobre [modernización de aplicaciones con Microsoft Azure](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102), que contiene una demostración que muestra cómo migrar aplicaciones de PostgreSQL a Azure Database for PostgreSQL.
- Vea el tutorial [Migración de PostgreSQL a Azure Database for PostgreSQL en línea mediante DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online).
