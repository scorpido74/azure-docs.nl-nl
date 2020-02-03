---
title: SQL Server-gegevens naar SQL Azure met Azure Data Factory - Team Data Science Process
description: Instellen van een ADF-pijplijn waarmee stelt het bericht op twee activiteiten van de gegevens migreren die samen gegevens dagelijks tussen databases on-premises en in de cloud verplaatsen.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722489"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Gegevens verplaatsen van een on-premises SQL server naar SQL Azure met Azure Data Factory

In dit artikel wordt beschreven hoe u gegevens van een on-premises SQL Server Data Base verplaatst naar een SQL Azure-data base via Azure Blob Storage met behulp van de Azure Data Factory (ADF): deze methode is een ondersteunde verouderde benadering die de voor delen van een gerepliceerde staging-kopie biedt, maar [we raden u aan de pagina gegevens migratie te bekijken voor de nieuwste opties](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Zie [gegevens verplaatsen naar een Azure SQL database voor Azure machine learning](move-sql-azure.md)voor een tabel met een overzicht van verschillende opties voor het verplaatsen van gegevens naar een Azure SQL database.

## <a name="intro"></a>Inleiding: wat is ADF en wanneer moet het worden gebruikt voor het migreren van gegevens?
Azure Data Factory is een volledig beheerde cloud-gebaseerde service voor gegevensintegratie die wordt georganiseerd en de verplaatsing en transformatie van gegevens worden geautomatiseerd. Het belangrijkste concept in het model ADF is de pijplijn. Een pijplijn is een logische groepering van activiteiten, die elk de acties die worden uitgevoerd op de gegevens die zijn opgenomen in gegevenssets definieert. Gekoppelde services worden gebruikt om de informatie die nodig zijn voor Data Factory verbinding maken met de gegevensbronnen die worden gedefinieerd.

Met ADF, kunnen bestaande services voor gegevensverwerking bestaan in gegevenspijplijnen die maximaal beschikbaar en beheerd in de cloud. Deze gegevens-pipelines voor het opnemen, voorbereiden, transformeren, analyseren en publiceren van gegevens kunnen worden gepland en ADF beheert en orkestreert de complexe gegevens en de verwerking van afhankelijkheden. Oplossingen kunnen snel worden gebouwd en geïmplementeerd in de cloud, verbinding te maken van een groeiend aantal on-premises en cloudgegevensbronnen.

Overweeg het gebruik van ADF:

* Wanneer gegevens moeten voortdurend worden gemigreerd een hybride scenario die toegang heeft tot zowel on-premises en cloudbronnen
* Wanneer de gegevens trans formaties of bedrijfs logica moeten bevatten wanneer ze worden gemigreerd.

ADF kunt u de planning en controle van taken met behulp van eenvoudige JSON-scripts waarmee de verplaatsing van gegevens op periodieke basis kunt beheren. ADF heeft ook andere mogelijkheden, zoals ondersteuning voor complexe bewerkingen. Zie de documentatie op [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/)voor meer informatie over ADF.

## <a name="scenario"></a>Het scenario
We instellen een ADF-pijplijn waarmee twee gegevens migratieactiviteiten stelt het bericht. Samen verplaatsen ze gegevens dagelijks over een on-premises SQL Database en een Azure SQL Database in de Cloud. De twee activiteiten zijn:

* gegevens kopiëren van een on-premises SQL Server-database naar een Azure Blob Storage-account
* gegevens kopiëren van de Azure Blob Storage-account naar een Azure SQL Database.

> [!NOTE]
> De stappen die hier worden weer gegeven, zijn aangepast aan de gedetailleerde zelf studie van het ADF-team: [gegevens kopiëren van een on-premises SQL Server Data Base naar Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) -verwijzingen naar de relevante secties van dat onderwerp worden indien van toepassing.
>
>

## <a name="prereqs"></a>Vereisten
In deze zelfstudie wordt ervan uitgegaan dat u hebt:

* Een **Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/).
* Een **Azure-opslag account**. U kunt een Azure storage-account gebruiken voor het opslaan van de gegevens in deze zelfstudie. Zie het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) als u geen account Azure-opslagaccount hebt. Nadat u het opslagaccount hebt gemaakt, moet u de accountsleutel ophalen die wordt gebruikt voor toegang tot de opslag. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md).
* Toegang tot een **Azure SQL database**. Als u een Azure SQL Database moet instellen, bevat het onderwerp aan de [slag met Microsoft Azure SQL database](../../sql-database/sql-database-get-started.md) informatie over het inrichten van een nieuw exemplaar van een Azure SQL database.
* **Azure PowerShell** lokaal geïnstalleerd en geconfigureerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor instructies.

> [!NOTE]
> Deze procedure maakt gebruik van de [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a>De gegevens uploaden naar uw on-premises SQL Server
We gebruiken de [NYC taxi-gegevensset](https://chriswhong.com/open-data/foil_nyc_taxi/) om het migratie proces te demonstreren. De NYC taxi-gegevensset is beschikbaar, zoals vermeld in dat bericht, op Azure Blob Storage [NYC taxi-gegevens](https://www.andresmh.com/nyctaxitrips/). De gegevens heeft twee bestanden, het bestand trip_data.csv, dat de details van de fietstocht bevat, en het bestand trip_far.csv details van het tarief voor elke reis betaald bevat. Een voor beeld en een beschrijving van deze bestanden zijn te vinden in de beschrijving van de [NYC taxi trips](sql-walkthrough.md#dataset)van de verzameling.

U kunt aanpassen van de procedure die hier beschikbaar zijn op een set van uw eigen gegevens of volg de stappen beschreven met behulp van de NYC Taxi-gegevensset. Als u de NYC taxi-gegevensset wilt uploaden naar uw on-premises SQL Server-Data Base, volgt u de procedure die wordt beschreven in [gegevens bulksgewijs importeren in SQL Server-Data Base](sql-walkthrough.md#dbload). Deze instructies zijn voor een SQL-Server op een Azure-Machine, maar de procedure voor het uploaden naar de on-premises SQL-Server is hetzelfde.

## <a name="create-adf"></a>Een Azure Data Factory maken
De instructies voor het maken van een nieuwe Azure Data Factory en een resource groep in de [Azure Portal](https://portal.azure.com/) worden door gegeven [een Azure Data Factory maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Noem het nieuwe ADF-exemplaar *adfdsp* en geef de resource groep de naam *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installeren en configureren van Azure Data Factory Integration Runtime
De Integration Runtime is een door de klant beheerde infra structuur voor gegevens integratie die door Azure Data Factory wordt gebruikt om mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Deze runtime heette voorheen 'Data Management Gateway'.

Als u wilt instellen, [volgt u de instructies voor het maken van een pijp lijn](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Maak gekoppelde services om verbinding te maken met de gegevens bronnen
De informatie die nodig zijn voor Azure Data Factory verbinding maken met een Gegevensresource definieert de gekoppelde service. We hebben drie resources in dit scenario waarvoor gekoppelde services nodig zijn:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL Database

De stapsgewijze procedure voor het maken van gekoppelde services vindt u in [gekoppelde services maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Tabellen definiëren en maken om op te geven hoe toegang tot de gegevens sets moet worden gemaakt
Tabellen die de structuur, de locatie en de beschikbaarheid van de gegevenssets met de volgende procedures op basis van een script opgeven maken. JSON-bestanden worden gebruikt voor het definiëren van de tabellen. Zie [gegevens sets](../../data-factory/concepts-datasets-linked-services.md)voor meer informatie over de structuur van deze bestanden.

> [!NOTE]
> U moet de cmdlet `Add-AzureAccount` uitvoeren voordat u de cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) uitvoert om te bevestigen dat het juiste Azure-abonnement is geselecteerd voor het uitvoeren van de opdracht. Zie [add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0)voor documentatie van deze cmdlet.
>
>

De definities van JSON-indeling in de tabellen gebruiken de volgende namen:

* de **tabel naam** in de on-premises SQL server is *nyctaxi_data*
* de **container naam** in het Azure Blob Storage-account is *containerName*

Drie tabeldefinities die nodig zijn voor deze ADF-pijplijn:

1. [On-premises tabel van SQL](#adf-table-onprem-sql)
2. [BLOB-tabel](#adf-table-blob-store)
3. [SQL Azure tabel](#adf-table-azure-sql)

> [!NOTE]
> Deze procedures gebruikt Azure PowerShell om te definiëren en maken van de ADF-activiteiten. Maar deze taken kunnen ook worden bereikt met behulp van de Azure portal. Zie [gegevens sets maken](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)voor meer informatie.
>
>

### <a name="adf-table-onprem-sql"></a>On-premises tabel van SQL
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

De kolomnamen zijn niet opgenomen in hier. U kunt de kolom namen subselecteren door deze hier op te nemen (Zie het onderwerp [ADF-documentatie](../../data-factory/copy-activity-overview.md) voor meer informatie.

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *onpremtabledef. json* en sla het bestand op een bekende locatie op (dit wordt aangenomen dat het *C:\temp\onpremtabledef.json*is). De tabel in ADF maken met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>BLOB-tabel
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

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *bloboutputtabledef. json* en sla het bestand op een bekende locatie op (dit wordt aangenomen dat het *C:\temp\bloboutputtabledef.json*is). De tabel in ADF maken met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure tabel
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

Kopieer de JSON-definitie van de tabel naar een bestand met de naam *AzureSqlTable. json* en sla het bestand op een bekende locatie op (dit wordt aangenomen dat het *C:\temp\AzureSqlTable.json*is). De tabel in ADF maken met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>De pijp lijn definiëren en maken
Geef de activiteiten die deel uitmaken van de pijplijn en de pijplijn maken met de volgende procedures op basis van een script. Een JSON-bestand wordt gebruikt om de pijplijn-eigenschappen te definiëren.

* In het script wordt ervan uitgegaan dat de naam van de **pijp lijn** *AMLDSProcessPipeline*is.
* Let ook op dat we de periodiciteit van de pijplijn worden uitgevoerd op dagelijkse basis en gebruiken van de standaard-uitvoeringstijd voor de taak (12: 00 uur UTC) ingesteld.

> [!NOTE]
> Azure PowerShell de volgende procedures gebruiken om te definiëren en maken van de ADF-pijplijn. Maar deze taak kan ook worden bereikt met behulp van de Azure portal. Zie [Create pijp lijn](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)voor meer informatie.
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

Kopieer deze JSON-definitie van de pijp lijn naar een bestand met de naam *pipelinedef. json* en sla het bestand op een bekende locatie op (dit wordt aangenomen dat het *C:\temp\pipelinedef.json*is). Maak de pijplijn in ADF met de volgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>De pijp lijn starten
De pijplijn kan nu worden uitgevoerd met de volgende opdracht uit:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

De waarden van de para meter *start date* en *EndDate* moeten worden vervangen door de werkelijke datums waartussen u de pijp lijn wilt uitvoeren.

Zodra de pijplijn wordt uitgevoerd, zou het mogelijk om te zien van de gegevens weergegeven in de container voor de blob, één bestand per dag is geselecteerd.

We hebben de functionaliteit van ADF om gegevens stapsgewijs niet meer gebruikt. Raadpleeg de [ADF-documentatie](https://azure.microsoft.com/services/data-factory/)voor meer informatie over hoe u dit doet en andere mogelijkheden van ADF.
