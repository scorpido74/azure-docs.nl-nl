---
title: Gegevens synchroniseren vanuit Azure SQL Edge (preview) met behulp van SQL Data Sync
description: Meer informatie over het synchroniseren van gegevens van Azure SQL Edge (preview) met behulp van Azure SQL Data Sync
keywords: SQL Edge, gegevens synchroniseren vanaf SQL Edge, SQL Edge-gegevenssynchronisatie
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6bcdfc3eb09b6c5ed4f8dde4d48a34bee8746e1e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593442"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Zelfstudie: Gegevens synchroniseren vanuit Azure SQL Edge naar Azure SQL Database met behulp van SQL Data Sync

In deze zelfstudie leert u hoe u een Azure SQL Data Sync-*synchronisatiegroep* kunt gebruiken om gegevens van de Azure SQL Edge incrementeel te synchroniseren naar Azure SQL Database. SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u in twee richtingen selecteert, kunt synchroniseren in meerdere SQL-databases en SQL Server-exemplaren. Zie [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md) voor meer informatie over SQL Data Sync.

Omdat SQL Edge is gebaseerd op de nieuwste versies van de [SQL Server Database-engine](/sql/sql-server/sql-server-technical-documentation/), kunnen alle gegevenssynchronisatiemechanismen die van toepassing zijn op een on-premises SQL Server-exemplaar ook worden gebruikt voor het synchroniseren van gegevens van of naar een SQL Edge-exemplaar dat wordt uitgevoerd op een edge-apparaat.

## <a name="prerequisites"></a>Vereisten

Bij deze zelfstudie is een Windows-computer vereist die is geconfigureerd met de [Data Sync-agent voor Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Voordat u begint

* Een Azure SQL-database maken. Zie [Een afzonderlijke database maken in Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal) voor meer informatie over het maken van een Azure SQL-database met behulp van de Azure-portal.

* De tabellen en andere benodigde objecten in uw Azure SQL Database-implementatie maken.

* De benodigde tabellen en objecten in uw Azure SQL Edge-implementatie maken. Zie [SQL Database DAC-pakketten gebruiken met SQL Edge](stream-analytics.md) voor meer informatie.

* De Azure SQL Edge-instantie registreren met de Data Sync-agent voor Azure SQL Data Sync. Zie [Een on-premises SQL Server-Data Base toevoegen](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem) voor meer informatie.

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Gegevens synchroniseren tussen een Azure SQL-database en SQL Edge

Het instellen van synchronisatie tussen een Azure SQL-database en een SQL Edge-exemplaar met behulp van SQL Data Sync omvat drie belangrijke stappen:  

1. Gebruik de Azure-portal om een synchronisatiegroep te maken. Zie [Een synchronisatiegroep maken](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group) voor meer informatie. U kunt één *hub*-database gebruiken om meerdere synchronisatiegroepen te maken om gegevens van verschillende SQL Edge-exemplaren te synchroniseren met een of meer SQL-databases in Azure.

2. Voeg synchronisatieleden toe aan de synchronisatiegroep. Zie [Synchronisatieleden toevoegen](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members) voor meer informatie.

3. Stel de synchronisatiegroep in om de tabellen te selecteren die deel zullen uitmaken van de synchronisatie. Zie [Een synchronisatiegroep configureren](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members) voor meer informatie.

Nadat u de voorgaande stappen hebt voltooid, hebt u een synchronisatiegroep die een Azure-SQL database en een SQL Edge-exemplaar bevat.

Zie de volgende artikelen voor meer informatie over SQL Data Sync:

* [Data Sync-agent voor Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Best practices](../sql-database/sql-database-best-practices-data-sync.md) en [Problemen met Azure SQL Data Sync oplossen](../sql-database/sql-database-troubleshoot-data-sync.md)

* [SQL Data Sync bewaken met Azure Monitor-logboeken](../sql-database/sql-database-sync-monitor-oms.md)

* [Het synchronisatieschema bijwerken met Transact-SQL](../sql-database/sql-database-update-sync-schema.md) of [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [PowerShell gebruiken om tussen Azure SQL-database en Azure SQL Edge te synchroniseren](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). In deze zelfstudie vervangt u de `OnPremiseServer` databasedetails door de details van Azure SQL Edge.
