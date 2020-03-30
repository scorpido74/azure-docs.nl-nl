---
title: Gegevens transformeren met U-SQL-script - Azure
description: Meer informatie over het verwerken of transformeren van gegevens door U-SQL-scripts uit te voeren op de Compute Service van Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927908"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Gegevens transformeren door U-SQL-scripts uit te voeren in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-usql-activity.md)
> * [Versie 2 (huidige versie)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [U-SQL-activiteit in V2](../transform-data-using-data-lake-analytics.md)als u de huidige versie van de gegevensfabriekservice gebruikt.

Een pijplijn in een Azure-gegevensfabriek verwerkt gegevens in gekoppelde opslagservices met behulp van gekoppelde compute-services. Het bevat een reeks activiteiten waarbij elke activiteit een specifieke verwerkingsbewerking uitvoert. In dit artikel wordt de **Data Lake Analytics U-SQL-activiteit** beschreven waarmee een **U-SQL-script** wordt uitgevoerd op een **gegevensgekoppelde** computerservice van Azure Data Lake Analytics. 

Maak een Azure Data Lake Analytics-account voordat u een pijplijn maakt met een Data Lake Analytics U-SQL-activiteit. Zie [Aan de slag met Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)voor meer informatie over Azure Data Lake Analytics.

Bekijk de [zelfstudie Van uw eerste pijplijn bouwen](data-factory-build-your-first-pipeline.md) voor gedetailleerde stappen voor het maken van een gegevensfabriek, gekoppelde services, gegevenssets en een pijplijn. Gebruik JSON-fragmenten met Data Factory Editor of Visual Studio of Azure PowerShell om entiteiten in gegevensfabriek te maken.

## <a name="supported-authentication-types"></a>Ondersteunde verificatietypen
U-SQL-activiteit ondersteunt onderstaande verificatietypen ten opzichte van Data Lake Analytics:
* Verificatie van service-principal
* Verificatie van gebruikersreferenties (OAuth) 

We raden u aan serviceprincipal-verificatie te gebruiken, vooral voor een geplande U-SQL-uitvoering. Tokenverloopgedrag kan optreden met verificatie van gebruikersreferenties. Zie de sectie [Gekoppelde serviceeigenschappen](#azure-data-lake-analytics-linked-service) voor configuratiedetails.

## <a name="azure-data-lake-analytics-linked-service"></a>Gekoppelde Azure Data Lake Analytics-service
U maakt een **azure Data Lake Analytics-gekoppelde** service om een Azure Data Lake Analytics-computeservice te koppelen aan een Azure-gegevensfabriek. De Data Lake Analytics U-SQL-activiteit in de pijplijn verwijst naar deze gekoppelde service. 

In de volgende tabel vindt u beschrijvingen voor de algemene eigenschappen die worden gebruikt in de JSON-definitie. U verder kiezen tussen serviceprincipal en verificatie van gebruikersreferenties.

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| **Type** |De eigenschap type moet worden ingesteld op: **AzureDataLakeAnalytics**. |Ja |
| **Accountnaam** |De naam van het Azure Data Lake Analytics-account. |Ja |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI. |Nee |
| **abonnementId** |Azure-abonnements-id |Nee (Als dit niet is opgegeven, wordt het abonnement van de gegevensfabriek gebruikt). |
| **resourceGroupName** |Naam van Azure-resourcegroep |Nee (Als deze niet is opgegeven, wordt de resourcegroep van de gegevensfabriek gebruikt). |

### <a name="service-principal-authentication-recommended"></a>Serviceprincipal-verificatie (aanbevolen)
Als u servicehoofdverificatie wilt gebruiken, registreert u een toepassingsentiteit in Azure Active Directory (Azure AD) en verleent u deze toegang tot De Gegevenslake store. Zie [Service-to-service-verificatie voor](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)gedetailleerde stappen . Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:
* Toepassings-id
* Toepassingssleutel 
* Tenant-id

Gebruik serviceprincipal-verificatie door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **servicePrincipalId** | Geef de client-id van de toepassing op. | Ja |
| **servicePrincipalKey** | Geef de sleutel van de toepassing op. | Ja |
| **Huurder** | Geef de tenantgegevens op (domeinnaam of tenant-id) waaronder uw toepassing zich bevindt. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. | Ja |

**Voorbeeld: Serviceprincipal-verificatie**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Verificatie van gebruikersreferenties
U ook verificatie van gebruikersreferenties voor Data Lake Analytics gebruiken door de volgende eigenschappen op te geven:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| **Vergunning** | Klik op de knop **Autoriseren** in de editor voor gegevensfabrieksonderdelen en voer uw referentie in die de automatisch gegenereerde autorisatie-URL aan deze eigenschap toewijst. | Ja |
| **Sessionid** | OAuth-sessie-ID van de OAuth-autorisatiesessie. Elke sessie-ID is uniek en kan slechts één keer worden gebruikt. Deze instelling wordt automatisch gegenereerd wanneer u de Data Factory Editor gebruikt. | Ja |

**Voorbeeld: Verificatie van gebruikersreferenties**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Token vervaldatum
De autorisatiecode die u hebt gegenereerd met de knop **Autoriseren,** verloopt na enige tijd. Zie de volgende tabel voor de vervaldatums voor verschillende typen gebruikersaccounts. Mogelijk ziet u het volgende foutbericht wanneer het **verificatietoken verloopt:** fout bij de bewerking van referenties: invalid_grant - AADSTS70002: Fout bij het valideren van referenties. AADSTS70008: De verstrekte toegangssubsidie is verlopen of ingetrokken. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlatie-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

| Gebruikerstype | Verloopt na |
|:--- |:--- |
| Gebruikersaccounts DIE NIET worden@hotmail.combeheerd @live.comdoor Azure Active Directory ( , enz.) |12 uur |
| Gebruikersaccounts beheerd door Azure Active Directory (AAD) |14 dagen na de laatste slice run. <br/><br/>90 dagen, als een segment op basis van OAuth-gebaseerde gekoppelde service ten minste eenmaal per 14 dagen wordt uitgevoerd. |

Als u deze fout wilt voorkomen/oplost, moet u opnieuw de knop **Autoriseren opnieuw autoriseren** wanneer het **token verloopt** en de gekoppelde service opnieuw implementeren. U ook als volgt waarden voor **sessieId-** en **autorisatie-eigenschappen** programmatisch genereren met behulp van code:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Zie [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)en [AuthorizationSessionGetResponse Class-onderwerpen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) voor meer informatie over de klassen Gegevensfabriek die in de code worden gebruikt. Voeg een verwijzing toe naar: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll voor de klasse WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-activiteit
In het volgende JSON-fragment wordt een pijplijn gedefinieerd met een Data Lake Analytics U-SQL-activiteit. De activiteitsdefinitie heeft een verwijzing naar de gekoppelde Azure Data Lake Analytics-service die u eerder hebt gemaakt.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

In de volgende tabel worden namen en beschrijvingen beschreven van eigenschappen die specifiek zijn voor deze activiteit. 

| Eigenschap            | Beschrijving                              | Vereist                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | De eigenschap type moet zijn ingesteld op **DataLakeAnalyticsU-SQL**. | Ja                                      |
| linkedServiceName   | Verwijzing naar de Azure Data Lake Analytics die is geregistreerd als gekoppelde service in Data Factory | Ja                                      |
| scriptPath          | Pad naar map die het U-SQL-script bevat. De naam van het bestand is hoofdlettergevoelig. | Nee (als u script gebruikt)                   |
| scriptLinkedService | Gekoppelde service die de opslag met het script koppelt aan de gegevensfabriek | Nee (als u script gebruikt)                   |
| uit              | Geef inline script op in plaats van scriptPath en scriptLinkedService op te geven. Bijvoorbeeld: `"script": "CREATE DATABASE test"`. | Nee (als u scriptPath en scriptLinkedService gebruikt) |
| mateParallelïsme | Het maximum aantal knooppunten dat tegelijkertijd wordt gebruikt om de taak uit te voeren. | Nee                                       |
| priority            | Hiermee bepaalt u welke taken van alle taken die in de wachtrij staan, als eerste moeten worden geselecteerd. Hoe lager het getal, hoe hoger de prioriteit. | Nee                                       |
| parameters          | Parameters voor het U-SQL-script          | Nee                                       |
| runtimeVersie      | Runtime-versie van de U-SQL-engine om te gebruiken | Nee                                       |
| compilatieMode     | <p>Compilatiemodus van U-SQL. Moet een van deze waarden:</p> <ul><li>**Semantisch:** Voer alleen semantische controles en noodzakelijke geestelijke gezondheid controles uit.</li><li>**Volledig:** Voer de volledige compilatie uit, inclusief syntaxiscontrole, optimalisatie, codegeneratie, enz.</li><li>**SingleBox:** Voer de volledige compilatie uit, met de instelling TargetType in SingleBox.</li></ul><p>Als u geen waarde voor deze eigenschap opgeeft, bepaalt de server de optimale compilatiemodus. </p> | Nee                                       |

Zie [SearchLogProcessing.txt Script Definition](#sample-u-sql-script) voor de scriptdefinitie. 

## <a name="sample-input-and-output-datasets"></a>Gegevensseten voor invoer en uitvoer van een voorbeeld
### <a name="input-dataset"></a>Invoergegevensset
In dit voorbeeld bevinden de invoergegevens zich in een Azure Data Lake Store (SearchLog.tsv-bestand in de map datalake/invoer). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Uitvoergegevensset
In dit voorbeeld worden de uitvoergegevens die door het U-SQL-script worden geproduceerd, opgeslagen in een Azure Data Lake Store (datalake/outputmap). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Voorbeeld van gekoppelde service in de Lake Store
Hier is de definitie van de voorbeeldazure data lake store gekoppelde service die wordt gebruikt door de invoer/ uitvoer gegevenssets. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Zie Gegevens verplaatsen van en naar het Azure [Data Lake Store-artikel](data-factory-azure-datalake-connector.md) voor beschrijvingen van JSON-eigenschappen. 

## <a name="sample-u-sql-script"></a>Voorbeeld van U-SQL-script

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
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

De waarden voor ** \@in-** en ** \@uitcheckparameters** in het U-SQL-script worden dynamisch doorADF doorgegeven met behulp van de sectie 'parameters'. Zie de sectie 'parameters' in de pijplijndefinitie.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

In dit geval worden invoerbestanden nog steeds opgehaald uit de map /datalake/input en worden uitvoerbestanden gegenereerd in de map /datalake/output. De bestandsnamen zijn dynamisch op basis van de begintijd van het segment.  


