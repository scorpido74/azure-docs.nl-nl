---
title: Status van het databasemigratiescenario
titleSuffix: Azure Database Migration Service
description: Meer informatie over de status van de migratiescenario's die worden ondersteund door Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254917"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status van migratiescenario's die worden ondersteund door Azure Database Migration Service

Azure Database Migration Service is ontworpen om verschillende migratiescenario's (bron-/doelparen) te ondersteunen voor zowel offline (eenmalige) als online (continuous sync) migraties. De scenariodekking van Azure Database Migration Service wordt in de loop van de tijd uitgebreid. Er worden regelmatig nieuwe scenario's toegevoegd. In dit artikel worden migratiescenario's geïdentificeerd die momenteel worden ondersteund door azure databasemigratieservice en de status (privévoorbeeld, openbare preview of algemene beschikbaarheid) voor elk scenario.

## <a name="offline-versus-online-migrations"></a>Offline versus online migraties

Met Azure Database Migration Service u een offline of een online migratie doen. Bij *offline* migraties begint de uitvaltijd van toepassingen op hetmoment dat de migratie wordt gestart. Gebruik een *online* migratie om downtime te beperken tot de tijd die nodig is om te worden teruggebracht naar de nieuwe omgeving wanneer de migratie is voltooid. Het wordt aanbevolen om een offline migratie te testen om te bepalen of de downtime acceptabel is; zo niet, doe dan een online migratie.

## <a name="migration-scenario-status"></a>Status migratiescenario

De status van migratiescenario's die worden ondersteund door Azure Database Migration Service varieert met de tijd. Over het algemeen worden scenario's eerst vrijgegeven in **privévoorbeeld**. Deelnemen aan private preview vereist dat klanten een nominatie indienen via de [DMS Preview-site.](https://aka.ms/dms-preview) Na een privévoorbeeld wordt de scenariostatus gewijzigd in **openbare voorbeeld**. Gebruikers van Azure Database Migration Service kunnen migratiescenario's in een openbare preview rechtstreeks vanuit de gebruikersinterface uitproberen. Aanmelden is niet nodig.  Migratiescenario's in openbare preview zijn echter mogelijk niet in alle regio's beschikbaar en kunnen aanvullende wijzigingen ondergaan voordat de definitieve release wordt vrijgegeven. Na een openbare preview verandert de scenariostatus **in de algemene beschikbaarheid**. Algemene beschikbaarheid (GA) is de status van de definitieve release en de functionaliteit is volledig en toegankelijk voor alle gebruikers.

## <a name="migration-scenario-support"></a>Ondersteuning voor migratiescenario's

In de volgende tabellen wordt weergegeven welke migratiescenario's worden ondersteund bij het gebruik van Azure Database Migration Service.

> [!NOTE]
> Als een scenario dat hieronder wordt weergegeven, niet wordt weergegeven in de gebruikersinterface, neemt u contact op met de alias [Azure Database Migrations vragen](mailto:AskAzureDatabaseMigrations@service.microsoft.com) voor aanvullende informatie.

> [!IMPORTANT]
> Zie de [DMS Preview-site](https://aka.ms/dms-preview)als u alle scenario's wilt weergeven die momenteel worden ondersteund door Azure Database Migration Service in Private Preview.

### <a name="offline-one-time-migration-support"></a>Ondersteuning voor offline (eenmalige) migratie

In de volgende tabel ziet u ondersteuning voor Azure Database Migration Service voor offlinemigraties.

| Doel  | Bron | Ondersteuning | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL Database** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL-VM** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Algemene beschikbaarheid |
| **Azure DB voor MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB voor PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Ondersteuning voor online (continuous sync) migratie

In de volgende tabel ziet u ondersteuning voor Azure Database Migration Service voor onlinemigraties.

| Doel  | Bron | Ondersteuning | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL Database** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL | ✔ | Algemene beschikbaarheid |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Algemene beschikbaarheid |
|   | RDS SQL | ✔ | Algemene beschikbaarheid |
|   | Oracle | ✔ | Privévoorbeeld |
| **Azure SQL-VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Algemene beschikbaarheid |
| **Azure DB voor MySQL** | MySQL | ✔ | Algemene beschikbaarheid |
|   | RDS MySQL | ✔ | Algemene beschikbaarheid |
| **Azure DB voor PostgreSQL** | PostgreSQL | ✔ | Algemene beschikbaarheid |
|   | RDS PostgreSQL | ✔ | Algemene beschikbaarheid |
|   | Oracle | ✔ | Openbare preview |

## <a name="next-steps"></a>Volgende stappen

Zie het artikel Wat is de Azure Database [Migration Service](dms-overview.md)voor een overzicht van azure database migration service en regionale beschikbaarheid .
