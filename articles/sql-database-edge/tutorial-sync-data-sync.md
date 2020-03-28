---
title: Gegevens van Azure SQL Database Edge synchroniseren met SQL Data Sync | Microsoft Documenten
description: Meer informatie over het synchroniseren van gegevens van Azure SQL Database Edge met Azure SQL Data Sync
keywords: sql-databaseedge, synchronisatiegegevens van sql-databaseedge, sql-databaseedge-gegevenssynchronisatie
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74384188"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Zelfstudie: Gegevens synchroniseren van SQL Database Edge naar Azure SQL Database met SQL Data Sync

In deze zelfstudie leert u hoe u een Azure SQL Data *Sync-groep* gebruikt om gegevens van Azure SQL Database Edge stapsgewijs te synchroniseren met Azure SQL SQL Database. SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens synchroniseren die u bidirectioneel selecteert in meerdere SQL-databases en SQL Server-exemplaren. Zie [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md)voor meer informatie over SQL Data Sync.

Omdat SQL Database Edge is gebouwd op de nieuwste versies van de [SQL Server Database Engine,](/sql/sql-server/sql-server-technical-documentation/)kan elk gegevenssynchronisatiemechanisme dat van toepassing is op een on-premises SQL Server-instantie ook worden gebruikt om gegevens te synchroniseren met of vanuit een SQL Database Edge-instantie die op een edge-apparaat wordt uitgevoerd.

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie vereist een Windows-computer die is geconfigureerd met de [Data Sync Agent voor Azure SQL Data Sync.](../sql-database/sql-database-data-sync-agent.md)

## <a name="before-you-begin"></a>Voordat u begint

* Een Azure SQL-database maken. Zie [Eén database maken in Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)voor informatie over het maken van een Azure SQL-database met behulp van de Azure-portal.

* Maak de tabellen en andere noodzakelijke objecten in uw Azure SQL Database-implementatie.

* Maak de benodigde tabellen en objecten in uw Azure SQL Database Edge-implementatie. Zie [SQL Database DAC-pakketten gebruiken met SQL Database Edge](stream-analytics.md)voor meer informatie.

* Registreer de instantie Azure SQL Database Edge met de Data Sync Agent voor Azure SQL Data Sync. Zie [Een on-premises SQL Server-database toevoegen](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)voor meer informatie.

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Gegevens synchroniseren tussen een Azure SQL-database en SQL Database Edge

Het instellen van synchronisatie tussen een Azure SQL-database en een SQL Database Edge-instantie met SQL Data Sync omvat drie belangrijke stappen:  

1. Gebruik de Azure-portal om een synchronisatiegroep te maken. Zie [Een synchronisatiegroep maken](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)voor meer informatie. U één *hubdatabase* gebruiken om meerdere synchronisatiegroepen te maken om gegevens uit verschillende SQL Database Edge-exemplaren te synchroniseren met een of meer SQL-databases in Azure.

2. Synchronisatieleden toevoegen aan de synchronisatiegroep. Zie [Synchronisatieleden toevoegen voor](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)meer informatie .

3. Stel de synchronisatiegroep in om de tabellen te selecteren die deel uitmaken van de synchronisatie. Zie [Een synchronisatiegroep configureren](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)voor meer informatie .

Nadat u de vorige stappen hebt voltooid, hebt u een synchronisatiegroep met een Azure SQL-database en een SQL Database Edge-instantie.

Zie de volgende artikelen voor meer informatie over SQL Data Sync:

* [Gegevenssynchronisatieagent voor Azure SQL-gegevenssynchronisatie](../sql-database/sql-database-data-sync-agent.md)

* [Aanbevolen procedures](../sql-database/sql-database-best-practices-data-sync.md) en [problemen met Azure SQL Data Sync oplossen](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL-gegevenssynchronisatie met Azure Monitor-logboeken bewaken](../sql-database/sql-database-sync-monitor-oms.md)

* [Het synchronisatieschema bijwerken met Transact-SQL](../sql-database/sql-database-update-sync-schema.md) of [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [Gebruik PowerShell om te synchroniseren tussen Azure SQL Database en Azure SQL Database Edge.](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md) Vervang in deze `OnPremiseServer` zelfstudie de databasegegevens door de Azure SQL Database Edge-details.
