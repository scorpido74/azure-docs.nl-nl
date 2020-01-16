---
title: SQL Server-gegevens naar SQL Azure met Azure Data Factory - Team Data Science Process
description: Instellen van een ADF-pijplijn waarmee stelt het bericht op twee activiteiten van de gegevens migreren die samen gegevens dagelijks tussen databases on-premises en in de cloud verplaatsen.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b64aa6c0e6e0e3bf449d44996df3223b12a69923
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982414"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Gegevens verplaatsen van een on-premises SQL server naar SQL Azure met Azure Data Factory

In dit artikel wordt beschreven hoe u gegevens verplaatst van een on-premises SQL Server Data Base naar een SQL Azure-data base via Azure Blob Storage met behulp van de Azure Data Factory (ADF): deze methode is een ondersteunde verouderde benadering die de voor delen van een gerepliceerde staging-kopie biedt, maar [we raden u aan om onze Datamigration-pagina te bekijken voor de nieuwste opties](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Zie voor een tabel met een overzicht van verschillende opties voor het verplaatsen van gegevens naar een Azure SQL Database, [gegevens verplaatsen naar een Azure SQL Database voor Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Inleiding: Wat is er ADF en wanneer moet deze worden gebruikt om gegevens te migreren?
Azure Data Factory is een volledig beheerde cloud-gebaseerde service voor gegevensintegratie die wordt georganiseerd en de verplaatsing en transformatie van gegevens worden geautomatiseerd. Het belangrijkste concept in het model ADF is de pijplijn. Een pijplijn is een logische groepering van activiteiten, die elk de acties die worden uitgevoerd op de gegevens die zijn opgenomen in gegevenssets definieert. Gekoppelde services worden gebruikt om de informatie die nodig zijn voor Data Factory verbinding maken met de gegevensbronnen die worden gedefinieerd.

Met ADF, kunnen bestaande services voor gegevensverwerking bestaan in gegevenspijplijnen die maximaal beschikbaar en beheerd in de cloud. Deze gegevens-pipelines voor het opnemen, voorbereiden, transformeren, analyseren en publiceren van gegevens kunnen worden gepland en ADF beheert en orkestreert de complexe gegevens en de verwerking van afhankelijkheden. Oplossingen kunnen snel worden gebouwd en geïmplementeerd in de cloud, verbinding te maken van een groeiend aantal on-premises en cloudgegevensbronnen.

Overweeg het gebruik van ADF:

* Wanneer gegevens moeten voortdurend worden gemigreerd een hybride scenario die toegang heeft tot zowel on-premises en cloudbronnen
* Wanneer de gegevens is uitgevoerd of moet worden gewijzigd of toegevoegd als gemigreerd bedrijfslogica hebt.

ADF kunt u de planning en controle van taken met behulp van eenvoudige JSON-scripts waarmee de verplaatsing van gegevens op periodieke basis kunt beheren. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen. Voor meer informatie over ADF, Raadpleeg de documentatie bij [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Het Scenario
We instellen een ADF-pijplijn waarmee twee gegevens migratieactiviteiten stelt het bericht. Samen wordt gegevens dagelijks verplaatsen tussen een on-premises SQL-database en een Azure SQL Database in de cloud. De twee activiteiten zijn:

* gegevens kopiëren van een on-premises SQL Server-database naar een Azure Blob Storage-account
* gegevens kopiëren van de Azure Blob Storage-account naar een Azure SQL Database.

> [!NOTE]
> De stappen die hier zijn aangepast van de meer gedetailleerde zelfstudie die door het team ADF weergegeven: [gegevens kopiëren van een on-premises SQL Server-database naar Azure Blob-opslag](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) verwijzingen naar de relevante secties van dit onderwerp vindt u bij van toepassing.
>
>

## <a name="prereqs"></a>Vereisten
In deze zelfstudie wordt ervan uitgegaan dat u hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure storage-account**. U kunt een Azure storage-account gebruiken voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL Database**. Als u een Azure SQL Database moet instellen, bevat het onderwerp aan de [slag met Microsoft Azure SQL database](../../sql-database/sql-database-get-started.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL database.
* Geïnstalleerd en geconfigureerd **Azure PowerShell** lokaal. Zie voor instructies [hoe u Azure PowerShell installeren en configureren](/powershell/azure/overview).

> [!NOTE]
> Deze procedure maakt gebruik van de [Azure-portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> De gegevens uploaden naar uw on-premises SQL-Server
We gebruiken de [NYC Taxi gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/) ter illustratie van het migratieproces. De gegevensset NYC over taxi's is beschikbaar, zoals beschreven in dit item, op Azure blob-opslag [NYC Taxi gegevens](https://www.andresmh.com/nyctaxitrips/). De gegevens heeft twee bestanden, het bestand trip_data.csv, dat de details van de fietstocht bevat, en het bestand trip_far.csv details van het tarief voor elke reis betaald bevat. Een voorbeeld en een beschrijving van deze bestanden zijn beschikbaar in [NYC Taxi Trips gegevensset beschrijving](sql-walkthrough.md#dataset).

U kunt aanpassen van de procedure die hier beschikbaar zijn op een set van uw eigen gegevens of volg de stappen beschreven met behulp van de NYC Taxi-gegevensset. Als u wilt uploaden de gegevensset NYC over taxi's in uw on-premises SQL Server-database, volgt u de procedure wordt beschreven in [bulksgewijs importeren van gegevens in SQL Server-Database](sql-walkthrough.md#dbload). Deze instructies zijn voor een SQL-Server op een Azure-Machine, maar de procedure voor het uploaden naar de on-premises SQL-Server is hetzelfde.

## <a name="create-adf"></a> Maak een Azure Data Factory
De instructies voor het maken van een nieuwe Azure Data Factory en een resourcegroep in de [Azure-portal](https://portal.azure.com/) vindt u [maken van een Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Naam van het nieuwe exemplaar van de ADF *adfdsp* en de naam van de resourcegroep gemaakt *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installeren en configureren van Azure Data Factory Integration Runtime
De Integration Runtime is een klant beheerde gegevensintegratie-infrastructuur door Azure Data Factory gebruikt om u te bieden mogelijkheden voor gegevensintegratie in verschillende netwerkomgevingen. Deze runtime heette voorheen 'Data Management Gateway'.

Als u wilt instellen, [volgt u de instructies voor het maken van een pijp lijn](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Maak gekoppelde services verbinding maken met de gegevensbronnen
De informatie die nodig zijn voor Azure Data Factory verbinding maken met een Gegevensresource definieert de gekoppelde service. We hebben drie resources in dit scenario waarvoor gekoppelde services nodig zijn:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL-database

De stapsgewijze procedure voor het maken van gekoppelde services vindt u in [gekoppelde services](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Definiëren en maken van tabellen op te geven hoe u toegang tot de gegevenssets
Tabellen die de structuur, de locatie en de beschikbaarheid van de gegevenssets met de volgende procedures op basis van een script opgeven maken. JSON-bestanden worden gebruikt voor het definiëren van de tabellen. Zie voor meer informatie over de structuur van deze bestanden [gegevenssets](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> U dient te worden uitgevoerd de `Add-AzureAccount` cmdlet alvorens de [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet om te bevestigen dat het juiste Azure-abonnement is geselecteerd voor de uitvoering van de opdracht. Zie voor documentatie van deze cmdlet [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

De definities van JSON-indeling in de tabellen gebruiken de volgende namen:

* de **tabelnaam** in de on-premises SQL server is *nyctaxi_data*
* de **containernaam** in Azure Blob Storage-account is *containername*

Drie tabeldefinities die nodig zijn voor deze ADF-pijplijn:

1. [On-premises SQL-tabel](#adf-table-onprem-sql)
2. [BLOB-tabel](#adf-table-blob-store)
3. [SQL Azure-tabel](#adf-table-azure-sql)

> [!NOTE]
> Deze procedures gebruikt Azure PowerShell om te definiëren en maken van de ADF-activiteiten. Maar deze taken kunnen ook worden bereikt met behulp van de Azure portal. Zie voor meer informatie, [gegevenssets](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>On-premises SQL-tabel
De definitie van de tabel voor de on-premises SQL Server is opgegeven in de volgende JSON-bestand:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

De kolomnamen zijn niet opgenomen in hier. U kunt subplan selecteren op de kolomnamen door ze hier (voor meer informatie de [ADF documentatie](../../data-factory/copy-activity-overview.md) onderwerp.

Kopieer de JSON-definitie van de tabel in een bestand met de naam *onpremtabledef.json* -bestand en sla deze op een bekende locatie (hier ervan uitgegaan dat *C:\temp\onpremtabledef.json*). De tabel in ADF maken met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Blobtabel
De definitie voor de tabel voor de locatie van de uitvoer-blob is in de volgende (Hiermee worden de opgenomen gegevens van on-premises naar Azure-blob):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopieer de JSON-definitie van de tabel in een bestand met de naam *bloboutputtabledef.json* -bestand en sla deze op een bekende locatie (hier ervan uitgegaan dat *C:\temp\bloboutputtabledef.json*). De tabel in ADF maken met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure-tabel
Definitie voor de tabel voor de SQL Azure-uitvoer is in de volgende (dit schema wijst de gegevens die afkomstig zijn van de blob):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopieer de JSON-definitie van de tabel in een bestand met de naam *AzureSqlTable.json* -bestand en sla deze op een bekende locatie (hier ervan uitgegaan dat *C:\temp\AzureSqlTable.json*). De tabel in ADF maken met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Definiëren en de pijplijn te maken
Geef de activiteiten die deel uitmaken van de pijplijn en de pijplijn maken met de volgende procedures op basis van een script. Een JSON-bestand wordt gebruikt om de pijplijn-eigenschappen te definiëren.

* Het script wordt ervan uitgegaan dat de **pijplijnnaam** is *AMLDSProcessPipeline*.
* Let ook op dat we de periodiciteit van de pijplijn worden uitgevoerd op dagelijkse basis en gebruiken van de standaard-uitvoeringstijd voor de taak (12: 00 uur UTC) ingesteld.

> [!NOTE]
> Azure PowerShell de volgende procedures gebruiken om te definiëren en maken van de ADF-pijplijn. Maar deze taak kan ook worden bereikt met behulp van de Azure portal. Zie voor meer informatie, [pijplijn maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

De tabeldefinities die u eerder hebt gebruikt, wordt de pijplijndefinitie van de voor de ADF als volgt opgegeven:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopieer deze JSON-definitie van de pijplijn in een bestand met de naam *pipelinedef.json* -bestand en sla deze op een bekende locatie (hier ervan uitgegaan dat *C:\temp\pipelinedef.json*). Maak de pijplijn in ADF met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>De pijplijn starten
De pijplijn kan nu worden uitgevoerd met de volgende opdracht uit:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

De *startdate* en *enddate* parameterwaarden moeten worden vervangen door de werkelijke datums tussen wie u wilt dat de pijplijn om uit te voeren.

Zodra de pijplijn wordt uitgevoerd, zou het mogelijk om te zien van de gegevens weergegeven in de container voor de blob, één bestand per dag is geselecteerd.

Houd er rekening mee dat we de functionaliteit die stapsgewijs door ADF pipe gegevens niet hebben gemaakt. Zie voor meer informatie over hoe u deze en andere mogelijkheden van ADF doet de [ADF documentatie](https://azure.microsoft.com/services/data-factory/).
