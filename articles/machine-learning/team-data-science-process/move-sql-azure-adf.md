---
title: SQL Server-gegevens naar SQL Azure met Azure Data Factory - Team Data Science-proces
description: Stel een ADF-pijplijn in die twee gegevensmigratieactiviteiten samenstelt die gegevens dagelijks verplaatsen tussen databases on-premises en in de cloud.
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
ms.openlocfilehash: 8f696f1c6c414cd9db082e79e0f34c56156e1ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722489"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Gegevens verplaatsen van een on-premises SQL-server naar SQL Azure met Azure Data Factory

In dit artikel ziet u hoe u gegevens van een on-premises SQL Server-database naar een SQL Azure-database via Azure Blob Storage verplaatsen met behulp van de Azure Data Factory (ADF): deze methode is een ondersteunde legacy-benadering die de voordelen heeft van een gerepliceerde versiekopie, hoewel [we voorstellen om op onze pagina gegevensmigratie te kijken naar de nieuwste opties.](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)

Zie Gegevens verplaatsen naar een Azure SQL Database voor [een](move-sql-azure.md)tabel met verschillende opties voor het verplaatsen van gegevens naar een Azure SQL-database.

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Inleiding: Wat is ADF en wanneer moet het worden gebruikt om gegevens te migreren?
Azure Data Factory is een volledig beheerde cloudgebaseerde data-integratieservice die de beweging en transformatie van gegevens orkestreert en automatiseert. Het belangrijkste concept in het ADF-model is pijplijn. Een pijplijn is een logische groepering van activiteiten, die elk de acties definieert die moeten worden uitgevoerd op de gegevens in gegevenssets. Gekoppelde services worden gebruikt om de informatie te definiëren die nodig is voor Data Factory om verbinding te maken met de gegevensbronnen.

Met ADF kunnen bestaande gegevensverwerkingsservices worden samengesteld in gegevenspijplijnen die in de cloud beschikbaar zijn en worden beheerd. Deze gegevenspijplijnen kunnen worden gepland om gegevens in te nemen, voor te bereiden, te transformeren, te analyseren en te publiceren, en ADF beheert en orkestreert de complexe gegevens- en verwerkingsafhankelijkheden. Oplossingen kunnen snel worden gebouwd en geïmplementeerd in de cloud, waarbij een groeiend aantal on-premises en clouddatabronnen met elkaar wordt verbonden.

Overweeg adf te gebruiken:

* wanneer gegevens voortdurend moeten worden gemigreerd in een hybride scenario dat toegang heeft tot zowel on-premises als cloudresources
* wanneer de gegevens transformaties nodig hebben of bedrijfslogica eraan worden toegevoegd wanneer deze worden gemigreerd.

ADF maakt het plannen en monitoren van taken mogelijk met behulp van eenvoudige JSON-scripts die de beweging van gegevens periodiek beheren. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen. Zie de documentatie in [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/)voor meer informatie over ADF.

## <a name="the-scenario"></a><a name="scenario"></a>Het scenario
We hebben een ADF-pijplijn opgezet die twee gegevensmigratieactiviteiten samenstelt. Samen verplaatsen ze dagelijks gegevens tussen een on-premises SQL Database en een Azure SQL Database in de cloud. De twee activiteiten zijn:

* gegevens uit een on-premises SQL Server-database naar een Azure Blob Storage-account kopiëren
* gegevens uit het Azure Blob Storage-account kopiëren naar een Azure SQL-database.

> [!NOTE]
> De hier weergegeven stappen zijn aangepast uit de meer gedetailleerde zelfstudie die door het ADF-team wordt verstrekt: [Gegevens kopiëren van een on-premises SQL Server-database naar Azure Blob-opslag](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) Verwijzingen naar de relevante secties van dat onderwerp worden indien nodig verstrekt.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Vereisten
Deze zelfstudie gaat ervan uit dat je hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslagaccount**. U gebruikt een Azure-opslagaccount voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL Database**. Als u een Azure SQL-database moet instellen, bevat het onderwerp [Aan de slag met Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL Database.
* Azure **PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie Azure [PowerShell installeren en configureren](/powershell/azure/overview)voor instructies.

> [!NOTE]
> Deze procedure maakt gebruik van de [Azure-portal](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-on-premises-sql-server"></a><a name="upload-data"></a>De gegevens uploaden naar uw on-premises SQL Server
We gebruiken de [NYC Taxi-gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/) om het migratieproces aan te tonen. De NYC Taxi-gegevensset is beschikbaar, zoals vermeld in dat bericht, op Azure blob-opslag [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). De gegevens hebben twee bestanden, het trip_data.csv-bestand, dat reisgegevens bevat, en het trip_far.csv-bestand, dat details bevat over het tarief dat voor elke reis is betaald. Een voorbeeld en beschrijving van deze bestanden zijn opgenomen in [NYC Taxi Trips Dataset Beschrijving](sql-walkthrough.md#dataset).

U de hier aangeboden procedure aanpassen aan een set van uw eigen gegevens of de stappen volgen zoals beschreven met behulp van de NYC Taxi-gegevensset. Als u de nyc-taxi-gegevensset wilt uploaden naar uw on-premises SQL Server-database, volgt u de procedure die is beschreven in [Bulk Import Data in SQL Server Database.](sql-walkthrough.md#dbload) Deze instructies zijn voor een SQL Server op een Azure Virtual Machine, maar de procedure voor het uploaden naar de on-premises SQL Server is hetzelfde.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>Een Azure-gegevensfabriek maken
De instructies voor het maken van een nieuwe Azure Data Factory en een brongroep in de [Azure-portal](https://portal.azure.com/) worden verstrekt [Een Azure Data Factory maken.](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory) Geef de naam van de nieuwe ADF-instantie *adfdsp* en geef de naam van de resourcegroep die *adfdsprg*heeft gemaakt .

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Runtime Azure Data Factory Integration Installeren en configureren
De Integratieruntime is een door de klant beheerde infrastructuur voor gegevensintegratie die wordt gebruikt door Azure Data Factory om gegevensintegratiemogelijkheden te bieden in verschillende netwerkomgevingen. Deze runtime heette voorheen "Data Management Gateway".

Volg de [instructies voor het maken van een pijplijn](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline) als u het instellen wilt instellen

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Gekoppelde services maken om verbinding te maken met de gegevensbronnen
Een gekoppelde service definieert de informatie die nodig is om verbinding te maken met een gegevensbron. We hebben drie middelen in dit scenario waarvoor gekoppelde diensten nodig zijn:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL Database

De stapsgewijze procedure voor het maken van gekoppelde services wordt aangeboden in [Gekoppelde services maken.](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Tabellen definiëren en maken om op te geven hoe u toegang krijgt tot de gegevenssets
Maak tabellen die de structuur, locatie en beschikbaarheid van de gegevenssets opgeven met de volgende scriptgebaseerde procedures. JSON-bestanden worden gebruikt om de tabellen te definiëren. Zie [Gegevenssets](../../data-factory/concepts-datasets-linked-services.md)voor meer informatie over de structuur van deze bestanden.

> [!NOTE]
> U moet `Add-AzureAccount` de cmdlet uitvoeren voordat u de cmdlet [Nieuw-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) uitvoert om te bevestigen dat het juiste Azure-abonnement is geselecteerd voor de opdrachtuitvoering. Zie [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0)voor documentatie van deze cmdlet .
>
>

De op JSON gebaseerde definities in de tabellen gebruiken de volgende namen:

* de **tabelnaam** in de on-premises SQL-server is *nyctaxi_data*
* de **containernaam** in het Azure Blob Storage-account is *containernaam*

Voor deze ADF-pijplijn zijn drie tabeldefinities nodig:

1. [SQL-on-premises tabel](#adf-table-onprem-sql)
2. [Blobtabel](#adf-table-blob-store)
3. [SQL Azure-tabel](#adf-table-azure-sql)

> [!NOTE]
> Deze procedures gebruiken Azure PowerShell om de ADF-activiteiten te definiëren en te maken. Maar deze taken kunnen ook worden uitgevoerd met behulp van de Azure-portal. Zie [Gegevenssets maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)voor meer informatie.
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SQL-on-premises tabel
De tabeldefinitie voor de on-premises SQL Server is opgegeven in het volgende JSON-bestand:

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

De kolomnamen zijn hier niet opgenomen. U de kolomnamen onderselecteren door ze hier op te nemen (voor meer informatie raadpleegt u het [ADF-documentatieonderwerp.](../../data-factory/copy-activity-overview.md)

Kopieer de JSON-definitie van de tabel naar een bestand genaamd *onpremtabledef.json-bestand* en sla het op een bekende locatie op (hier wordt verondersteld *C:\temp\onpremtabledef.json*te zijn). Maak de tabel in ADF met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Blobtabel
De definitie voor de tabel voor de locatie van de uitvoerblob bevindt zich in de volgende (hiermee worden de ingenomen gegevens van on-premises naar Azure blob toegewezen):

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

Kopieer de JSON-definitie van de tabel naar een bestand genaamd *bloboutputtabledef.json-bestand* en sla het op een bekende locatie op (hier wordt verondersteld *C:\temp\bloboutputtabledef.json*te zijn). Maak de tabel in ADF met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure-tabel
Definitie voor de tabel voor de SQL Azure-uitvoer bevindt zich in de volgende (in dit schema worden de gegevens van de blob toegewezen):

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

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *AzureSqlTable.json-bestand* en sla het op een bekende locatie op (hier wordt verondersteld *C:\temp\AzureSqlTable.json*te zijn). Maak de tabel in ADF met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>De pijplijn definiëren en maken
Geef de activiteiten op die bij de pijplijn horen en maak de pijplijn met de volgende scriptgebaseerde procedures. Een JSON-bestand wordt gebruikt om de pijplijneigenschappen te definiëren.

* Het script gaat ervan uit dat de **naam van** de pijplijn *AMLDSProcessPipeline*is.
* Houd er ook rekening mee dat we de periodiciteit van de pijplijn die dagelijks moet worden uitgevoerd en de standaarduitvoeringstijd voor de taak gebruiken (12 uur UTC).

> [!NOTE]
> De volgende procedures gebruiken Azure PowerShell om de ADF-pijplijn te definiëren en te maken. Maar deze taak kan ook worden uitgevoerd met behulp van de Azure-portal. Zie Pijplijn [maken voor](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)meer informatie.
>
>

Met behulp van de eerder verstrekte tabeldefinities wordt de pijplijndefinitie voor de ADF als volgt opgegeven:

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

Kopieer deze JSON-definitie van de pijplijn naar een bestand genaamd *pipelinedef.json-bestand* en sla deze op een bekende locatie op (hier wordt verondersteld *C:\temp\pipelinedef.json*te zijn). Maak de pijplijn in ADF met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>De pijplijn starten
De pijplijn kan nu worden uitgevoerd met de volgende opdracht:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

De *parameterwaarden begindatum* en *einddatum* moeten worden vervangen door de werkelijke datums waartussen u de pijplijn wilt uitvoeren.

Zodra de pijplijn wordt uitgevoerd, moet u de gegevens in de container die voor de blob zijn geselecteerd, één bestand per dag kunnen zien.

We hebben geen gebruik gemaakt van de functionaliteit die ADF biedt om gegevens stapsgewijs te pipeteren. Zie de [ADF-documentatie](https://azure.microsoft.com/services/data-factory/)voor meer informatie over hoe u dit en andere mogelijkheden van ADF doen.
