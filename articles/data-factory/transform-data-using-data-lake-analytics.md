---
title: Gegevens transformeren met U-SQL-script
description: Meer informatie over het verwerken of transformeren van gegevens door U-SQL-scripts uit te voeren op de Compute Service van Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 427b7fff7b8f76412d7bd9d63aeb64583637779c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418963"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Gegevens transformeren door U-SQL-scripts uit te voeren in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](v1/data-factory-usql-activity.md)
> * [Huidige versie](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een pijplijn in een Azure-gegevensfabriek verwerkt gegevens in gekoppelde opslagservices met behulp van gekoppelde compute-services. Het bevat een reeks activiteiten waarbij elke activiteit een specifieke verwerkingsbewerking uitvoert. In dit artikel wordt de **Data Lake Analytics U-SQL-activiteit** beschreven waarmee een **U-SQL-script** wordt uitgevoerd op een **gegevensgekoppelde** computerservice van Azure Data Lake Analytics. 

Maak een Azure Data Lake Analytics-account voordat u een pijplijn maakt met een Data Lake Analytics U-SQL-activiteit. Zie [Aan de slag met Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)voor meer informatie over Azure Data Lake Analytics.


## <a name="azure-data-lake-analytics-linked-service"></a>Gekoppelde Azure Data Lake Analytics-service
U maakt een **azure Data Lake Analytics-gekoppelde** service om een Azure Data Lake Analytics-computeservice te koppelen aan een Azure-gegevensfabriek. De Data Lake Analytics U-SQL-activiteit in de pijplijn verwijst naar deze gekoppelde service. 

In de volgende tabel vindt u beschrijvingen voor de algemene eigenschappen die worden gebruikt in de JSON-definitie. 

| Eigenschap                 | Beschrijving                              | Vereist                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Type**                 | De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. | Ja                                      |
| **Accountnaam**          | De naam van het Azure Data Lake Analytics-account.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | Nee                                       |
| **abonnementId**       | Azure-abonnements-ID                    | Nee                                       |
| **resourceGroupName**    | Naam van Azure-resourcegroep                | Nee                                       |

### <a name="service-principal-authentication"></a>Verificatie van service-principal
De gekoppelde Azure Data Lake Analytics-service vereist een serviceprincipal-verificatie om verbinding te maken met de Azure Data Lake Analytics-service. Als u servicehoofdverificatie wilt gebruiken, registreert u een toepassingsentiteit in Azure Active Directory (Azure AD) en verleent u deze toegang tot zowel de Data Lake Analytics als de Data Lake Store die wordt gebruikt. Zie [Service-to-service-verificatie voor](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)gedetailleerde stappen . Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

* Toepassings-id
* Toepassingssleutel 
* Tenant-id

Geef servicehoofdsom toestemming aan uw Azure Data Lake Anatlyics met behulp van de [wizard Gebruiker toevoegen](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Gebruik serviceprincipal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap                | Beschrijving                              | Vereist |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geef de client-id van de toepassing op.     | Ja      |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op.           | Ja      |
| **Huurder**              | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja      |

**Voorbeeld: Serviceprincipal-verificatie**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Zie [Gekoppelde services berekenen](compute-linked-services.md)voor meer informatie over de gekoppelde service.

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
In het volgende JSON-fragment wordt een pijplijn gedefinieerd met een Data Lake Analytics U-SQL-activiteit. De activiteitsdefinitie heeft een verwijzing naar de gekoppelde Azure Data Lake Analytics-service die u eerder hebt gemaakt. Als u een Data Lake Analytics U-SQL-script wilt uitvoeren, verzendt Data Factory het script dat u hebt opgegeven aan de Data Lake Analytics en worden de vereiste ingangen en uitvoer gedefinieerd in het script voor Data Lake Analytics om op te halen en uit te voeren. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

In de volgende tabel worden namen en beschrijvingen beschreven van eigenschappen die specifiek zijn voor deze activiteit. 

| Eigenschap            | Beschrijving                              | Vereist |
| :------------------ | :--------------------------------------- | :------- |
| name                | Naam van de activiteit in de pijplijn     | Ja      |
| description         | Tekst waarin wordt beschreven wat de activiteit doet.  | Nee       |
| type                | Voor Data Lake Analytics U-SQL-activiteit is het activiteitstype **DataLakeAnalyticsU-SQL**. | Ja      |
| linkedServiceName   | Gekoppelde service aan Azure Data Lake Analytics. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service.  |Ja       |
| scriptPath          | Pad naar map die het U-SQL-script bevat. De naam van het bestand is hoofdlettergevoelig. | Ja      |
| scriptLinkedService | Gekoppelde service die de **Azure Data Lake Store** of Azure **Storage** koppelt die het script bevat aan de gegevensfabriek | Ja      |
| mateParallelïsme | Het maximum aantal knooppunten dat tegelijkertijd wordt gebruikt om de taak uit te voeren. | Nee       |
| priority            | Hiermee bepaalt u welke taken van alle taken die in de wachtrij staan, als eerste moeten worden geselecteerd. Hoe lager het getal, hoe hoger de prioriteit. | Nee       |
| parameters          | Parameters om door te geven aan het U-SQL-script.    | Nee       |
| runtimeVersie      | Runtime-versie van de U-SQL-engine te gebruiken. | Nee       |
| compilatieMode     | <p>Compilatiemodus van U-SQL. Moet een van deze waarden: **Semantisch:** Voer alleen semantische controles en noodzakelijke saniteitcontroles uit, **Volledig:** Voer de volledige compilatie uit, inclusief syntaxiscontrole, optimalisatie, codegeneratie, enz., **SingleBox:** Voer de volledige compilatie uit, met de instelling TargetType naar SingleBox. Als u geen waarde voor deze eigenschap opgeeft, bepaalt de server de optimale compilatiemodus. | Nee |

Zie [SearchLogProcessing.txt](#sample-u-sql-script) voor de scriptdefinitie. 

## <a name="sample-u-sql-script"></a>Voorbeeld van U-SQL-script

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

In het bovenstaande scriptvoorbeeld wordt de invoer en uitvoer naar het script gedefinieerd in ** \@in-** en ** \@uitparameters.** De waarden voor ** \@in-** en ** \@uitcheckparameters** in het U-SQL-script worden dynamisch doorGegeven door Data Factory met behulp van de sectie 'parameters'. 

U andere eigenschappen opgeven, zoals mateParallelisme en prioriteit, evenals in uw pijplijndefinitie voor de taken die worden uitgevoerd op de Azure Data Lake Analytics-service.

## <a name="dynamic-parameters"></a>Dynamische parameters
In de definitie van de voorbeeldpijplijn worden in- en uitparameters toegewezen met hardgecodeerde waarden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Het is mogelijk om in plaats daarvan dynamische parameters te gebruiken. Bijvoorbeeld: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

In dit geval worden invoerbestanden nog steeds opgehaald uit de map /datalake/input en worden uitvoerbestanden gegenereerd in de map /datalake/output. De bestandsnamen zijn dynamisch op basis van de begintijd van het venster die wordt doorgegeven wanneer de pijplijn wordt geactiveerd.  

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen waarin wordt uitgelegd hoe u gegevens op andere manieren transformeren: 

* [Hive activiteit](transform-data-using-hadoop-hive.md)
* [Varkensactiviteit](transform-data-using-hadoop-pig.md)
* [Activiteit verkleinen](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activiteit](transform-data-using-hadoop-streaming.md)
* [Vonkactiviteit](transform-data-using-spark.md)
* [.NET aangepaste activiteit](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution-activiteit](transform-data-using-machine-learning.md)
* [Opgeslagen procedureactiviteit](transform-data-using-stored-procedure.md)
