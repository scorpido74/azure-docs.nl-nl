---
title: Verklarende woordenlijst
description: Woorden lijst met termen Azure SQL Database
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76705782"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Woorden lijst met termen Azure SQL Database

|Context|Termijn|Meer informatie|
|:---|:---|:---|
|Azure-service|Azure SQL Database of SQL Database|[De Azure SQL Database-Service](sql-database-technical-overview.md)|
|Aankoop model|Op DTU gebaseerd inkoop model|[Op DTU gebaseerd inkoop model](sql-database-service-tiers-dtu.md)|
||Aankoopmodel op basis van vCore|[Aankoopmodel op basis van vCore](sql-database-service-tiers-vcore.md)|
|Implementatie optie |Individuele database|[Individuele databases](sql-database-single-database.md)|
||Elastische pool|[Elastische pool](sql-database-elastic-pool.md)|
||Beheerd exemplaar|[Beheerd exemplaar](sql-database-managed-instance.md)|
|Servicelaag|Basic, Standard, Premium, Algemeen, grootschalige, Bedrijfskritiek|Zie [Single Data Base en elastische pool](sql-database-service-tiers-vcore.md#service-tiers) en [Managed instance](sql-database-managed-instance.md#managed-instance-service-tiers)voor service lagen in het vCore-model. Zie [DTU-model](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers)voor service lagen in het DTU-model.|
|Compute-laag|Serverloze compute|[Serverloze compute](sql-database-service-tiers-vcore.md#compute-tiers)
||Ingerichte compute|[Ingerichte compute](sql-database-service-tiers-vcore.md#compute-tiers)
|Compute genereren|GEN5, M-serie, Fsv2-serie|[Hardware gegenereerd](sql-database-service-tiers-vcore.md#hardware-generations)
|Server entiteit|SQL Database Server of database server|[Databaseserver](sql-database-servers.md)|
||Managed instance server, Managed instance server of instance server SQL Database|[Beheerd exemplaar](sql-database-managed-instance.md)|
|Resourcetype|vCore|Er is een CPU-kern gegeven aan de reken resource voor één data base, elastische pool of een beheerd exemplaar.|
||Berekenings grootte en opslag hoeveelheid|De berekenings grootte is de maximale hoeveelheid CPU, geheugen en andere niet-opslag gerelateerde resources die beschikbaar zijn voor één data base, elastische pool of een beheerd exemplaar.  Opslag grootte is de maximale hoeveelheid opslag ruimte die beschikbaar is voor één data base, een elastische pool of een beheerd exemplaar.  Zie [VCore single data bases](sql-database-vcore-resource-limits-single-databases.md), [VCore elastisch Pools](sql-database-vcore-resource-limits-elastic-pools.md) and [Managed instances](sql-database-managed-instance-resource-limits.md)(Engelstalig) voor meer informatie over de grootte van het VCore-model.  Zie voor de grootte opties in het DTU-model [DTU single data bases](sql-database-dtu-resource-limits-single-databases.md) en [DTU elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md).
