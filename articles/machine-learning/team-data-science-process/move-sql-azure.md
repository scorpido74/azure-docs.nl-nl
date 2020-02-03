---
title: Gegevens verplaatsen naar een Azure SQL Database - Team Data Science Process
description: Verplaats gegevens uit platte bestanden (CSV-of TSV-indelingen) of van gegevens die zijn opgeslagen in een on-premises SQL Server naar een Azure SQL Database.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722455"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Gegevens voor Azure Machine Learning verplaatsen naar een Azure SQL-database

In dit artikel vindt u een overzicht van de opties voor het verplaatsen van gegevens uit platte bestanden (CSV-of TSV-indelingen) of van gegevens die zijn opgeslagen in een on-premises SQL Server naar een Azure SQL Database. Deze taken voor het verplaatsen van gegevens naar de cloud maken deel uit van het Team Data Science Process.

Zie voor een onderwerp waarin de opties voor het verplaatsen van gegevens naar een on-premises SQL Server voor Machine Learning, [gegevens verplaatsen naar SQL Server op een virtuele machine van Azure](move-sql-server-virtual-machine.md).

De volgende tabel geeft een overzicht van de opties voor het verplaatsen van gegevens naar een Azure SQL Database.

| <b>Bron</b> | <b>DOEL: Azure SQL Database</b> |
| --- | --- |
| <b>Plat bestand (CSV of TSV Format teren)</b> |[SQL-query bulksgewijs invoegen](#bulk-insert-sql-query) |
| <b>On-premises SQL Server</b> |1.[exporteren naar een plat bestand](#export-flat-file)<br> 2. [SQL database wizard Migratie](#insert-tables-bcp)<br> 3. [back-up en herstel van data base](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Vereisten
De procedures die hier wordt beschreven, vereist dat u hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslag account**. U kunt een Azure storage-account gebruiken voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL database**. Als u een Azure SQL Database moet instellen, kunt u aan de [slag met Microsoft Azure SQL database](../../sql-database/sql-database-get-started.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL database.
* **Azure PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor instructies.

**Gegevens**: de migratie processen worden gedemonstreerd met behulp van de [NYC taxi-gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/). De NYC taxi-gegevensset bevat informatie over reis gegevens en beurzen en is beschikbaar in Azure Blob-opslag: [NYC taxi-gegevens](https://www.andresmh.com/nyctaxitrips/). Een voor beeld en een beschrijving van deze bestanden zijn te vinden in de beschrijving van de [NYC taxi trips](sql-walkthrough.md#dataset)van de verzameling.

U kunt aanpassen van de procedures die hier wordt beschreven aan een set van uw eigen gegevens of volg de stappen beschreven met behulp van de NYC Taxi-gegevensset. Als u de NYC taxi-gegevensset wilt uploaden naar uw on-premises SQL Server-Data Base, volgt u de procedure die wordt beschreven in [gegevens bulksgewijs importeren in SQL Server-Data Base](sql-walkthrough.md#dbload). Deze instructies zijn voor een SQL-Server op een Azure-Machine, maar de procedure voor het uploaden naar de on-premises SQL-Server is hetzelfde.

## <a name="file-to-azure-sql-database"></a>Gegevens verplaatsen van een bron met een plat bestand naar een Azure SQL Database
Gegevens in platte bestanden (CSV of TSV) kunnen worden verplaatst naar een Azure SQL Database met behulp van een Bulk Insert SQL-query.

### <a name="bulk-insert-sql-query"></a>SQL-query bulksgewijs invoegen
De stappen voor de procedure met behulp van de bulksgewijze insert SQL-query zijn vergelijkbaar met de instructies voor het verplaatsen van gegevens van een platte bestands bron naar SQL Server op een virtuele machine van Azure. Zie [BULKSGEWIJZE SQL-query invoegen](move-sql-server-virtual-machine.md#insert-tables-bulkquery)voor meer informatie.

## <a name="sql-on-prem-to-sazure-sql-database"></a>Gegevens verplaatsen van on-premises SQL Server naar een Azure SQL Database
Als de bron gegevens worden opgeslagen in een on-premises SQL Server, zijn er verschillende mogelijkheden om de gegevens naar een Azure SQL Database te verplaatsen:

1. [Exporteren naar plat bestand](#export-flat-file)
2. [Wizard Migratie SQL Database](#insert-tables-bcp)
3. [Back-up en herstel van data base](#db-migration)
4. [Azure Data Factory](#adf)

De stappen voor de eerste drie zijn vergelijkbaar met die secties in [gegevens verplaatsen naar SQL Server op een virtuele machine van Azure](move-sql-server-virtual-machine.md) die dezelfde procedures beslaan. Koppelingen naar de toepasselijke rubrieken in dit onderwerp vindt u in de volgende instructies.

### <a name="export-flat-file"></a>Exporteren naar plat bestand
De stappen voor het exporteren naar een plat bestand zijn vergelijkbaar met die voor het [exporteren naar een plat bestand](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Wizard Migratie SQL Database
De stappen voor het gebruik van de wizard SQL Database Migration zijn vergelijkbaar met die van de instructies in de [wizard SQL database migratie](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Back-up en herstel van data base
De stappen voor het gebruik van back-up en herstel van de Data Base zijn vergelijkbaar met die in de [back-up en herstel bewerking van de data base](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Meer informatie over het verplaatsen van gegevens naar een Azure SQL Database met Azure Data Factory (ADF) in dit onderwerp, [Verplaats gegevens van een on-premises SQL-Server naar SQL Azure met Azure Data Factory](move-sql-azure-adf.md). In dit onderwerp wordt beschreven hoe u ADF gebruikt om gegevens van een on-premises SQL Server Data Base te verplaatsen naar een Azure SQL Database via Azure Blob Storage.

U kunt ADF gebruiken wanneer gegevens voortdurend moeten worden gemigreerd met hybride on-premises en Cloud bronnen.  ADF helpt ook wanneer de gegevens trans formaties nodig hebben of nieuwe bedrijfs logica nodig heeft tijdens de migratie. ADF kunt u de planning en controle van taken met behulp van eenvoudige JSON-scripts waarmee de verplaatsing van gegevens op periodieke basis kunt beheren. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen.
