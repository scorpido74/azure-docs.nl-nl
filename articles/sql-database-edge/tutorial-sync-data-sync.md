---
title: Gegevens van Azure SQL Database rand synchroniseren met behulp van SQL Data Sync | Microsoft Docs
description: Meer informatie over het synchroniseren van gegevens tussen Azure SQL Database Edge met Azure SQL Data Sync
keywords: SQL data base Edge, gegevens synchroniseren van SQL data base Edge, SQL data base EDGE Data Sync
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501314"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Zelf studie: gegevens van SQL Database Edge synchroniseren met Azure SQL Database met behulp van SQL Data Sync

In deze zelf studie leert u hoe u SQL Data Sync *synchronisatie groep* kunt gebruiken om gegevens op basis van Azure SQL database Edge te synchroniseren met Azure SQL database. SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u in twee richtingen selecteert, kunt synchroniseren in meerdere SQL-data bases en SQL Server exemplaren. Zie voor meer informatie over Azure SQL Data Sync [azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Omdat Azure SQL Database Edge is gebaseerd op de nieuwste versies van de [Microsoft SQL server data base-engine](/sql/sql-server/sql-server-technical-documentation/), kan alle gegevens synchronisatie mechanismen die van toepassing zijn op een on-premises SQL Server instantie ook worden gebruikt voor het synchroniseren van gegevens van of naar een SQL database rand exemplaar dat wordt uitgevoerd op een edge-apparaat.

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie is een Windows-computer vereist die is geconfigureerd met de [Azure SQL Data Sync-agent](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Voordat u begint

* Maak een Azure SQL Database. Zie [een afzonderlijke data base maken in Azure SQL database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)voor meer informatie over het maken van een Azure SQL database met behulp van Azure Portal.

* Maak de tabellen en andere benodigde objecten in uw Azure SQL Database-implementatie.

* Maak de benodigde tabellen en objecten in uw Azure SQL Database Edge-implementatie. Zie [SQL database DAC-pakketten gebruiken met SQL database Edge](stream-analytics.md)voor meer informatie.

* Registreer het Azure SQL Database Edge-exemplaar met de Azure SQL Data Sync-agent. Zie [een on-premises SQL Server-Data Base toevoegen](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)voor meer informatie.

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Gegevens synchroniseren tussen een Azure SQL Database en SQL Database rand

Het instellen van synchronisatie tussen een Azure SQL Database en een SQL Database rand exemplaar met behulp van SQL Data Sync omvat drie belang rijke stappen.  

1. Gebruik Azure Portal om een synchronisatie groep te maken. Als u de synchronisatie groep wilt maken, raadpleegt u [synchronisatie groep maken met behulp van Azure Portal](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Dezelfde *hub* -data base kan worden gebruikt om meerdere verschillende synchronisatie groepen te maken om gegevens van verschillende SQL database Edge-instanties te synchroniseren met een of meer SQL-data bases in Azure.

2. Voeg synchronisatie leden toe aan de synchronisatie groep. Als u leden aan de synchronisatie groep wilt toevoegen, raadpleegt u [leden toevoegen aan SQL Data Sync groep](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configureer de synchronisatie groep om de tabellen te selecteren die deel zullen uitmaken van deze synchronisatie. Zie [synchronisatie groep configureren](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)voor meer informatie over het configureren van de synchronisatie groep.

Nadat u de bovenstaande stappen hebt voltooid, hebt u een synchronisatie groep die een Azure SQL Database bevat en een SQL Database Edge-exemplaar.

Voor meer informatie over SQL Data Sync, gezien de volgende artikelen:

* [Data Sync-agent voor Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Aanbevolen procedures](../sql-database/sql-database-best-practices-data-sync.md) voor [het oplossen van problemen met Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL Data Sync controleren met Azure Monitor-logboeken](../sql-database/sql-database-sync-monitor-oms.md)

* [Het synchronisatie schema bijwerken met Transact-SQL](../sql-database/sql-database-update-sync-schema.md) of [Power shell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [Power shell gebruiken om te synchroniseren tussen Azure SQL database en Azure SQL database Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). In de zelf studie vervangt u de details van de *OnPremiseServer* -data base door de details van de Azure SQL database rand.
