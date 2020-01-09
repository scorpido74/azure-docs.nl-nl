---
title: Gegevens verplaatsen naar een Azure SQL Database - Team Data Science Process
description: Gegevens verplaatsen van platte bestanden (CSV-bestand of TSV-indeling) of van de gegevens die zijn opgeslagen in een on-premises SQL Server naar een Azure SQL-database.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/04/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 79ef5a6e4af3e0becc5dbeed9ea377e8ef0eb3d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432165"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Gegevens voor Azure Machine Learning verplaatsen naar een Azure SQL-database

In dit artikel bevat een overzicht van de opties voor het verplaatsen van gegevens uit platte bestanden (CSV-bestand of TSV indelingen) of gegevens die zijn opgeslagen in een on-premises SQL Server naar een Azure SQL-database. Deze taken voor het verplaatsen van gegevens naar de cloud maken deel uit van het Team Data Science Process.

Zie voor een onderwerp dat geeft een overzicht van de opties voor het verplaatsen van gegevens naar een on-premises SQL Server voor Machine Learning, [gegevens verplaatsen naar SQL Server op een virtuele machine van Azure](move-sql-server-virtual-machine.md).

De volgende tabel geeft een overzicht van de opties voor het verplaatsen van gegevens naar een Azure SQL Database.

| <b>BRON</b> | <b>BESTEMMING: Azure SQL Database</b> |
| --- | --- |
| <b>Plat bestand (CSV- of TSV geformatteerd)</b> |[Bulksgewijs invoegen SQL-Query](#bulk-insert-sql-query) |
| <b>On-premises SQL Server</b> |1.[exporteren naar platte bestanden](#export-flat-file)<br> 2. [SQL database wizard Migratie](#insert-tables-bcp)<br> 3. [back-up en herstel van data base](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Vereisten
De procedures die hier wordt beschreven, vereist dat u hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure storage-account**. U kunt een Azure storage-account gebruiken voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-quickstart-create-account.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL Database**. Als u een Azure SQL-Database moet instellen [aan de slag met Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) bevat informatie over hoe u een nieuw exemplaar van een Azure SQL-Database inricht.
* Geïnstalleerd en geconfigureerd **Azure PowerShell** lokaal. Zie voor instructies [hoe u Azure PowerShell installeren en configureren](/powershell/azure/overview).

**Gegevens**: de migratie-processen worden gedemonstreerd met behulp van de [NYC Taxi gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/). De NYC Taxi-gegevensset bevat informatie over de reisgegevens en beurzen en is beschikbaar in Azure blob-opslag: [NYC Taxi gegevens](https://www.andresmh.com/nyctaxitrips/). Een voorbeeld en een beschrijving van deze bestanden zijn beschikbaar in [NYC Taxi Trips gegevensset beschrijving](sql-walkthrough.md#dataset).

U kunt aanpassen van de procedures die hier wordt beschreven aan een set van uw eigen gegevens of volg de stappen beschreven met behulp van de NYC Taxi-gegevensset. Als u wilt uploaden de gegevensset NYC over taxi's in uw on-premises SQL Server-database, volgt u de procedure wordt beschreven in [bulksgewijs importeren van gegevens in SQL Server-Database](sql-walkthrough.md#dbload). Deze instructies zijn voor een SQL-Server op een Azure-Machine, maar de procedure voor het uploaden naar de on-premises SQL-Server is hetzelfde.

## <a name="file-to-azure-sql-database"></a> Gegevens van de bron van een plat bestand verplaatsen naar een Azure SQL database
Gegevens in platte bestanden (CSV-bestand of TSV opgemaakt) kan worden verplaatst naar een Azure SQL-database met behulp van een Bulk Insert SQL-Query.

### <a name="bulk-insert-sql-query"></a> Bulksgewijs invoegen SQL-Query
De stappen voor de procedure met behulp van de Bulk Insert SQL-Query zijn vergelijkbaar met die in de secties voor het verplaatsen van gegevens van een plat bestandsbron met SQL Server op een Azure VM besproken. Zie voor meer informatie, [bulksgewijs invoegen SQL-Query](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Gegevens te verplaatsen van on-premises SQL Server naar een Azure SQL database
Als de brongegevens is opgeslagen in een on-premises SQL Server, zijn er verschillende mogelijkheden om de gegevens te verplaatsen naar een Azure SQL database:

1. [Exporteren naar platte bestanden](#export-flat-file)
2. [SQL Database-migratiewizard](#insert-tables-bcp)
3. [Database back-en herstellen](#db-migration)
4. [Azure Data Factory](#adf)

De stappen voor de eerste drie zijn vergelijkbaar met de secties in [gegevens verplaatsen naar SQL Server op een virtuele machine van Azure](move-sql-server-virtual-machine.md) die betrekking hebben op deze dezelfde procedures. Koppelingen naar de toepasselijke rubrieken in dit onderwerp vindt u in de volgende instructies.

### <a name="export-flat-file"></a>Exporteren naar platte bestanden
De stappen om deze te exporteren naar een plat bestand zijn vergelijkbaar met die zijn inbegrepen [exporteren naar platte bestanden](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>SQL Database-migratiewizard
De stappen voor het gebruik van de SQL Database-migratiewizard zijn vergelijkbaar met die zijn inbegrepen [SQL Database-migratiewizard](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Database back-en herstellen
De stappen voor het gebruik van de database back-up en herstel zijn vergelijkbaar met die zijn inbegrepen [Database back-en herstellen van](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
De procedure voor het verplaatsen van gegevens naar een Azure SQL-database met Azure Data Factory (ADF) vindt u in het onderwerp [gegevens verplaatsen van een on-premises SQL server naar SQL Azure met Azure Data Factory](move-sql-azure-adf.md). In dit onderwerp wordt beschreven hoe gegevens uit een on-premises SQL Server-database verplaatsen naar een Azure SQL database via Azure Blob Storage met behulp van ADF.

Overweeg het gebruik van ADF wanneer gegevens moeten voortdurend worden gemigreerd in een hybride scenario die toegang heeft tot zowel on-premises en cloudresources en wanneer de gegevens wordt afgehandeld, of moet worden gewijzigd of bedrijfslogica toegevoegd wanneer die wordt gemigreerd. ADF kunt u de planning en controle van taken met behulp van eenvoudige JSON-scripts waarmee de verplaatsing van gegevens op periodieke basis kunt beheren. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen.
