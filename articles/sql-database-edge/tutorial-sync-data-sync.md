---
title: Gegevens van Azure SQL Database rand synchroniseren met behulp van SQL Data Sync | Microsoft Docs
description: Meer informatie over het synchroniseren van gegevens van Azure SQL Database Edge met behulp van Azure SQL Data Sync
keywords: SQL data base Edge, gegevens synchroniseren van SQL data base Edge, SQL data base EDGE Data Sync
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384188"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Zelf studie: gegevens van SQL Database Edge synchroniseren met Azure SQL Database met behulp van SQL Data Sync

In deze zelf studie leert u hoe u een Azure SQL Data Sync- *synchronisatie groep* kunt gebruiken om gegevens op basis van Azure SQL database Edge te synchroniseren met Azure SQL database. SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u bidirectioneel op meerdere SQL-databases en SQL Server-exemplaren selecteert worden gesynchroniseerd. Zie voor meer informatie over SQL Data Sync [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Omdat SQL Database Edge is gebaseerd op de nieuwste versies van de [SQL server data base-engine](/sql/sql-server/sql-server-technical-documentation/), kan alle gegevens synchronisatie mechanismen die van toepassing zijn op een on-premises SQL Server-exemplaar ook worden gebruikt om gegevens te synchroniseren van of van een SQL database Edge-instantie die wordt uitgevoerd op een edge-apparaat.

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie is een Windows-computer vereist die is geconfigureerd met de [Data Sync-agent voor Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Voordat u begint

* Een Azure SQL-database maken. Zie [een enkele data base maken in Azure SQL database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)voor meer informatie over het maken van een Azure-SQL database met behulp van de Azure Portal.

* Maak de tabellen en andere benodigde objecten in uw Azure SQL Database-implementatie.

* Maak de benodigde tabellen en objecten in uw Azure SQL Database Edge-implementatie. Zie [SQL database DAC-pakketten gebruiken met SQL database Edge](stream-analytics.md)voor meer informatie.

* Registreer het Azure SQL Database Edge-exemplaar met de Data Sync-agent voor Azure SQL Data Sync. Zie [een on-premises SQL Server-Data Base toevoegen](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)voor meer informatie.

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Gegevens synchroniseren tussen een Azure-SQL database en SQL Database Edge

Het instellen van de synchronisatie tussen een Azure SQL database en een SQL Database Edge-exemplaar met behulp van SQL Data Sync bestaat uit drie belang rijke stappen:  

1. Gebruik de Azure Portal om een synchronisatie groep te maken. Zie [een synchronisatie groep maken](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)voor meer informatie. U kunt één *hub* -Data Base gebruiken om meerdere synchronisatie groepen te maken om gegevens van verschillende SQL database Edge-instanties te synchroniseren met een of meer SQL-data bases in Azure.

2. Voeg synchronisatie leden toe aan de synchronisatie groep. Zie [synchronisatie leden toevoegen](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)voor meer informatie.

3. Stel de synchronisatie groep in om de tabellen te selecteren die deel zullen uitmaken van de synchronisatie. Zie [Configure a Sync Group (een synchronisatie groep configureren](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)) voor meer informatie.

Nadat u de voor gaande stappen hebt voltooid, hebt u een synchronisatie groep die een Azure-SQL database en een SQL Database Edge-exemplaar bevat.

Raadpleeg de volgende artikelen voor meer informatie over SQL Data Sync:

* [Data Sync-agent voor Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Aanbevolen procedures](../sql-database/sql-database-best-practices-data-sync.md) voor [het oplossen van problemen met Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL Data Sync controleren met Azure Monitor-logboeken](../sql-database/sql-database-sync-monitor-oms.md)

* [Het synchronisatie schema bijwerken met Transact-SQL](../sql-database/sql-database-update-sync-schema.md) of [Power shell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [Power shell gebruiken om te synchroniseren tussen Azure SQL database en Azure SQL database Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). In deze zelf studie vervangt u de `OnPremiseServer` database Details door de details van de Azure SQL Database rand.
