---
title: Gegevens verplaatsen naar een Azure SQL-database - Team Data Science-proces
description: Gegevens verplaatsen van platte bestanden (CSV- of TSV-indelingen) of van gegevens die zijn opgeslagen in een on-premises SQL Server naar een Azure SQL-database.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722455"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Gegevens voor Azure Machine Learning verplaatsen naar een Azure SQL-database

In dit artikel worden de opties beschreven voor het verplaatsen van gegevens van platte bestanden (CSV- of TSV-indelingen) of van gegevens die zijn opgeslagen in een on-premises SQL Server naar een Azure SQL-database. Deze taken voor het verplaatsen van gegevens naar de cloud maken deel uit van het Team Data Science Process.

Zie [Gegevens verplaatsen naar SQL Server op een virtuele Azure-machine](move-sql-server-virtual-machine.md)voor een onderwerp dat de opties voor het verplaatsen van gegevens naar een on-premises SQL Server voor Machine Learning beschrijft.

In de volgende tabel worden de opties voor het verplaatsen van gegevens naar een Azure SQL-database samengevat.

| <b>Bron</b> | <b>BESTEMMING: Azure SQL-database</b> |
| --- | --- |
| <b>Vlak bestand (CSV of TSV opgemaakt)</b> |[SQL-query voor bulkinvoegen](#bulk-insert-sql-query) |
| <b>On-premises SQL Server</b> |1.[Exporteren naar plat bestand](#export-flat-file)<br> Wizard [SQL-databasemigratie](#insert-tables-bcp)<br> 3. [Database back-up en herstellen](#db-migration)<br> 4. [Azure-gegevensfabriek](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Vereisten
De hier beschreven procedures vereisen dat u:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslagaccount**. U gebruikt een Azure-opslagaccount voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL Database**. Als u een Azure SQL-database moet instellen, biedt [aan de slag met Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL Database.
* Azure **PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie Azure [PowerShell installeren en configureren](/powershell/azure/overview)voor instructies.

**Gegevens**: De migratieprocessen worden gedemonstreerd met behulp van de [NYC Taxi-gegevensset.](https://chriswhong.com/open-data/foil_nyc_taxi/) De NYC Taxi-gegevensset bevat informatie over reisgegevens en beurzen en is beschikbaar op Azure blob-opslag: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Een voorbeeld en beschrijving van deze bestanden zijn opgenomen in [NYC Taxi Trips Dataset Beschrijving](sql-walkthrough.md#dataset).

U de hier beschreven procedures aanpassen aan een set van uw eigen gegevens of de stappen volgen zoals beschreven met behulp van de NYC Taxi-gegevensset. Als u de nyc-taxi-gegevensset wilt uploaden naar uw on-premises SQL Server-database, volgt u de procedure die is beschreven in [Bulk Import Data in SQL Server Database.](sql-walkthrough.md#dbload) Deze instructies zijn voor een SQL Server op een Azure Virtual Machine, maar de procedure voor het uploaden naar de on-premises SQL Server is hetzelfde.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>Gegevens verplaatsen van een platte bestandsbron naar een Azure SQL-database
Gegevens in platte bestanden (CSV of TSV-indeling) kunnen worden verplaatst naar een Azure SQL-database met behulp van een Bulk Insert SQL Query.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>SQL-query voor bulkinvoegen
De stappen voor de procedure met de SQL Query voor bulkinvoegen zijn vergelijkbaar met de aanwijzingen voor het verplaatsen van gegevens van een platte bestandsbron naar SQL Server op een Azure VM. Zie Sql [Query voor bulk invoegen voor](move-sql-server-virtual-machine.md#insert-tables-bulkquery)meer informatie .

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>Gegevens verplaatsen van on-premises SQL Server naar een Azure SQL-database
Als de brongegevens zijn opgeslagen in een on-premises SQL Server, zijn er verschillende mogelijkheden om de gegevens naar een Azure SQL-database te verplaatsen:

1. [Exporteren naar vlak bestand](#export-flat-file)
2. [Wizard SQL-databasemigratie](#insert-tables-bcp)
3. [Database back-ups maken en herstellen](#db-migration)
4. [Azure-gegevensfabriek](#adf)

De stappen voor de eerste drie zijn vergelijkbaar met die secties in [Move-gegevens naar SQL Server op een virtuele Azure-machine](move-sql-server-virtual-machine.md) die dezelfde procedures dekken. Links naar de juiste secties in dat onderwerp worden in de volgende instructies gegeven.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exporteren naar vlak bestand
De stappen voor deze export naar een vlak bestand zijn vergelijkbaar met die richtingen die worden behandeld in [Exporteren naar plat bestand](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Wizard SQL-databasemigratie
De stappen voor het gebruik van de wizard SQL-databasemigratie zijn vergelijkbaar met de aanwijzingen die worden weergegeven in de [wizard SQL-databasemigratie](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Database back-ups maken en herstellen
De stappen voor het gebruik van databaseback-up en herstel zijn vergelijkbaar met de aanwijzingen in [Databaseback-up en herstel.](move-sql-server-virtual-machine.md#sql-backup)

### <a name="azure-data-factory"></a><a name="adf"></a>Azure-gegevensfabriek
Lees in dit onderwerp hoe u gegevens verplaatst naar een Azure SQL-database met Azure Data Factory (ADF) en [gegevens verplaatsen van een on-premises SQL-server naar SQL Azure met Azure Data Factory.](move-sql-azure-adf.md) In dit onderwerp wordt uitgelegd hoe u ADF gebruiken om gegevens van een on-premises SQL Server-database naar een Azure SQL-database via Azure Blob Storage te verplaatsen.

Overweeg ADF te gebruiken wanneer gegevens voortdurend moeten worden gemigreerd met hybride on-premises en cloudbronnen.  ADF helpt ook wanneer de gegevens transformaties nodig hebben of nieuwe bedrijfslogica nodig heeft tijdens migratie. ADF maakt het plannen en monitoren van taken mogelijk met behulp van eenvoudige JSON-scripts die de beweging van gegevens periodiek beheren. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen.
