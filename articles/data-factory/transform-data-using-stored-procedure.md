---
title: Gegevens transformeren met behulp van de activiteit Opgeslagen procedure
description: Hier wordt uitgelegd hoe sql-serveropgeslagen procedureactiviteit wordt gebruikt om een opgeslagen procedure in een Azure SQL Database/Data Warehouse aan te roepen vanuit een Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 57bf653aa3f421ae8897c4be661ceef589fcdc06
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418810"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Gegevens transformeren met de activiteit OPGESLAGEN SQL Server in Azure Data Factory
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-stored-proc-activity.md)
> * [Huidige versie](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U gebruikt gegevenstransformatieactiviteiten in een Data [Factory-pijplijn](concepts-pipelines-activities.md) om ruwe gegevens om te zetten en te verwerken in voorspellingen en inzichten. De activiteit Opgeslagen procedure is een van de transformatieactiviteiten die Data Factory ondersteunt. Dit artikel bouwt voort op het artikel [over transformatiegegevens,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten in Data Factory.

> [!NOTE]
> Als u nieuw bent in Azure Data Factory, leest u [Inleiding tot Azure Data Factory](introduction.md) en doet u de zelfstudie: [Zelfstudie: gegevens transformeren](tutorial-transform-data-spark-powershell.md) voordat u dit artikel leest. 

U de opgeslagen procedureactiviteit gebruiken om een opgeslagen procedure aan te roepen in een van de volgende gegevensarchieven in uw onderneming of op een virtuele Azure-machine (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-database.  Als u SQL Server gebruikt, installeert u self-hosted integration runtime op dezelfde machine die de database host of op een aparte machine die toegang heeft tot de database. Self-Hosted integration runtime is een component die on-premises/on Azure VM op een veilige en beheerde manier gegevensbronnen on-premises/on Azure VM verbindt met cloudservices. Zie Artikel Runtime voor [zelfgehoste integratie](create-self-hosted-integration-runtime.md) voor meer informatie.

> [!IMPORTANT]
> Wanneer u gegevens kopieert naar Azure SQL Database of SQL Server, u de **SqlSink** in kopieeractiviteit configureren om een opgeslagen procedure aan te roepen met behulp van de eigenschap **sqlWriterStoredProcedureName.** Zie volgende verbindingsartikelen: Azure SQL [Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md)voor meer informatie over de eigenschap. Een beroep doen op een opgeslagen procedure terwijl gegevens naar een Azure SQL Data Warehouse worden gekopieerd met behulp van een kopieeractiviteit, wordt niet ondersteund. U de opgeslagen procedureactiviteit echter gebruiken om een opgeslagen procedure in een SQL Data Warehouse aan te roepen. 
>
> Wanneer u gegevens kopieert uit Azure SQL Database of SQL Server of Azure SQL Data Warehouse, u **SqlSource** in kopieeractiviteit configureren om een opgeslagen procedure aan te roepen om gegevens uit de brondatabase te lezen met behulp van de eigenschap **sqlReaderStoredProcedureName.** Zie voor meer informatie de volgende verbindingsartikelen: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md), Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Syntaxisdetails
Hier is de JSON-indeling voor het definiëren van een activiteit opgeslagen procedure:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

In de volgende tabel worden de volgende JSON-eigenschappen beschreven:

| Eigenschap                  | Beschrijving                              | Vereist |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Naam van de activiteit                     | Ja      |
| description               | Tekst waarin wordt beschreven waarvoor de activiteit wordt gebruikt | Nee       |
| type                      | Voor opgeslagen procedureactiviteit is het activiteitstype **SqlServerStoredProcedure** | Ja      |
| linkedServiceName         | Verwijzing naar de **Azure SQL Database** of Azure SQL Data **Warehouse** of **SQL Server** die is geregistreerd als gekoppelde service in Data Factory. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service. | Ja      |
| storedProcedureName       | Geef de naam op van de opgeslagen procedure die u moet aanroepen. | Ja      |
| storedProcedureParameters | Geef de waarden op voor opgeslagen procedureparameters. Met `"param1": { "value": "param1Value","type":"param1Type" }` gebruiken om parameterwaarden en het type ervan door de gegevensbron te geven. Als u null voor een parameter `"param1": { "value": null }` moet doorgeven, gebruikt u (alle kleine letters). | Nee       |

## <a name="parameter-data-type-mapping"></a>Toewijzing van parametergegevenstype
Het gegevenstype dat u opgeeft voor de parameter is het type Azure Data Factory dat wordt toegewezen aan het gegevenstype in de gegevensbron die u gebruikt. U de gegevenstypetoewijzingen voor uw gegevensbron vinden in het connectorsgebied. Enkele voorbeelden zijn

| Gegevensbron          | Gegevenstypetoewijzing |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Foutgegevens

Wanneer een opgeslagen procedure mislukt en foutgegevens retourneert, u de foutgegevens niet rechtstreeks vastleggen in de activiteitsuitvoer. Data Factory pompt echter al zijn activiteiten naar Azure Monitor. Onder de gebeurtenissen die Data Factory pompt naar Azure Monitor, duwt het daar foutgegevens. U bijvoorbeeld e-mailwaarschuwingen van die gebeurtenissen instellen. Zie [Gegevensfabrieken waarschuwen en controleren met Azure Monitor](monitor-using-azure-monitor.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren: 

* [U-SQL-activiteit](transform-data-using-data-lake-analytics.md)
* [Hive Activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit kaart verminderen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Uitvoeringsactiviteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
