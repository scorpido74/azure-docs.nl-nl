---
title: Verklarende woordenlijst
description: Azure SQL Database-woordenlijst met termen
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 733901d38703e02ab7dbe811b0f80a1dfedf03d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705782"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database-woordenlijst met termen

|Context|Termijn|Meer informatie|
|:---|:---|:---|
|Azure-service|Azure SQL Database of SQL Database|[De Azure SQL Database-service](sql-database-technical-overview.md)|
|Inkoopmodel|DTU-gebaseerd inkoopmodel|[DTU-gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md)|
||Aankoopmodel op basis van vCore|[Aankoopmodel op basis van vCore](sql-database-service-tiers-vcore.md)|
|Implementatie, optie |Individuele database|[Individuele databases](sql-database-single-database.md)|
||Elastische pool|[Elastische pool](sql-database-elastic-pool.md)|
||Beheerd exemplaar|[Beheerd exemplaar](sql-database-managed-instance.md)|
|Servicelaag|Basic, Standard, Premium, General Purpose, Hyperscale, Business Critical|Zie [één database en elastische groep en](sql-database-service-tiers-vcore.md#service-tiers) [beheerde instantie](sql-database-managed-instance.md#managed-instance-service-tiers)voor servicelagen in het vCore-model. Zie [DTU-model](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers)voor servicelagen in het DTU-model.|
|Rekenlaag|Serverloze compute|[Serverloze compute](sql-database-service-tiers-vcore.md#compute-tiers)
||Ingerichte rekenkracht|[Ingerichte rekenkracht](sql-database-service-tiers-vcore.md#compute-tiers)
|Compute generatie|Gen5, M-serie, Fsv2-serie|[Hardware generaties](sql-database-service-tiers-vcore.md#hardware-generations)
|Serverentiteit|SQL Database-server of databaseserver|[Databaseserver](sql-database-servers.md)|
||SQL Database managed instance server, managed instance server, or instance server|[Beheerd exemplaar](sql-database-managed-instance.md)|
|Resourcetype|vCore|Een CPU-kern die wordt geleverd aan de compute resource voor één database, elastische pool of beheerde instantie.|
||Rekengrootte en opslagbedrag|Rekengrootte is de maximale hoeveelheid CPU, geheugen en andere niet-opslaggerelateerde resources die beschikbaar zijn voor één database, elastische groep of beheerde instantie.  Opslaggrootte is de maximale hoeveelheid opslagruimte die beschikbaar is voor één database, elastische groep of beheerde instantie.  Zie [vCore-databases,](sql-database-vcore-resource-limits-single-databases.md) [vCore-elastische pools](sql-database-vcore-resource-limits-elastic-pools.md) en [beheerde exemplaren](sql-database-managed-instance-resource-limits.md)voor grootteopties in het vcore-model .  Zie [DTU-databases](sql-database-dtu-resource-limits-single-databases.md) en [DTU-elastische pools](sql-database-dtu-resource-limits-elastic-pools.md)voor grootteopties in het DTU-model.
