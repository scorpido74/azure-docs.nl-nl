---
title: Webactiviteit in Azure Data Factory
description: Ontdek hoe u Webactiviteit, een van de activiteiten met de controlestroom die door Data Factory wordt ondersteund, gebruiken om een REST-eindpunt uit een pijplijn aan te roepen.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: a5cdb24a80dcbd95e4ccc59dd55f4acb9ae18060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417892"
---
# <a name="web-activity-in-azure-data-factory"></a>Webactiviteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


De WebActivity kan worden gebruikt om een aangepast REST-eindpunt aan te roepen vanaf een Data Factory-pijplijn. U kunt gegevenssets en gekoppelde services doorgeven die moten worden verbruikt door en die toegankelijk zijn voor de activiteit.

> [!NOTE]
> Webactiviteit kan alleen openbaar zichtbare URL's aanroepen. Het wordt niet ondersteund voor URL's die worden gehost in een privé virtueel netwerk.

## <a name="syntax"></a>Syntaxis

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
name | Naam van de webactiviteit | Tekenreeks | Ja
type | Moet zijn ingesteld op **WebActiviteit**. | Tekenreeks | Ja
method | Rest API-methode voor het doeleindpunt. | Tekenreeks. <br/><br/>Ondersteunde typen: "GET", "POST", "PUT" | Ja
url | Doeleindpunt en -pad | Tekenreeks (of expressie met resultaatType van tekenreeks). De activiteit krijgt een time-out van 1 minuut met een fout als deze geen antwoord van het eindpunt ontvangt. | Ja
Headers | Kopteksten die naar de aanvraag worden verzonden. Bijvoorbeeld om de taal in te stellen `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`en te typen op een verzoek: . | Tekenreeks (of expressie met resultaatType van tekenreeks) | Ja, content-type header is vereist. `"headers":{ "Content-Type":"application/json"}`
body | Vertegenwoordigt de payload die naar het eindpunt wordt verzonden.  | Tekenreeks (of expressie met resultaatType van tekenreeks). <br/><br/>Zie het schema van de payload van het verzoek in de sectie [Payload-schema aanvragen.](#request-payload-schema) | Vereist voor POST/PUT-methoden.
verificatie | Verificatiemethode die wordt gebruikt voor het aanroepen van het eindpunt. Ondersteunde typen zijn 'Basic' of ClientCertificate. Zie [Sectie Verificatie](#authentication) voor meer informatie. Als verificatie niet vereist is, sluit u deze eigenschap uit. | Tekenreeks (of expressie met resultaatType van tekenreeks) | Nee
gegevenssets | Lijst met gegevenssets die naar het eindpunt zijn doorgegeven. | Array met gegevenssetverwijzingen. Kan een lege array zijn. | Ja
linkedServices | Lijst met gekoppelde services die zijn doorgegeven aan eindpunt. | Array met gekoppelde servicereferenties. Kan een lege array zijn. | Ja

> [!NOTE]
> REST-eindpunten die de webactiviteit aanroept, moeten een antwoord van het type JSON retourneren. De activiteit krijgt een time-out van 1 minuut met een fout als deze geen antwoord van het eindpunt ontvangt.

In de volgende tabel worden de vereisten voor JSON-inhoud weergegeven:

| Waardetype | Aanvraagbody | Hoofdtekst van de reactie |
|---|---|---|
|JSON-object | Ondersteund | Ondersteund |
|JSON-array | Ondersteund <br/>(Op dit moment werken JSON-arrays niet als gevolg van een bug. Er wordt een oplossing uitgevoerd.) | Niet ondersteund |
| JSON-waarde | Ondersteund | Niet ondersteund |
| Niet-JSON-type | Niet ondersteund | Niet ondersteund |
||||

## <a name="authentication"></a>Verificatie

Hieronder vindt u de ondersteunde verificatietypen in de webactiviteit.

### <a name="none"></a>Geen

Als verificatie niet vereist is, moet u de eigenschap 'verificatie' niet opnemen.

### <a name="basic"></a>Basic

Geef gebruikersnaam en wachtwoord op die u met de basisverificatie wilt gebruiken.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Clientcertificaat

Geef de basis64-gecodeerde inhoud van een PFX-bestand en het wachtwoord op.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Beheerde identiteit

Geef de resourceuri op waarvoor het toegangstoken wordt aangevraagd met de beheerde identiteit voor de gegevensfabriek. Als u de API voor `https://management.azure.com/`Azure Resource Management wilt aanroepen, gebruikt u . Zie de [overzichtspagina beheerde identiteiten voor Azure-bronnen voor](/azure/active-directory/managed-identities-azure-resources/overview)meer informatie over hoe beheerde identiteiten werken.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Als uw gegevensfabriek is geconfigureerd met een git-opslagplaats, moet u uw referenties opslaan in Azure Key Vault om basisverificatie of clientcertificaatverificatie te gebruiken. Azure Data Factory slaat geen wachtwoorden op in git.

## <a name="request-payload-schema"></a>Laadschema aanvragen
Wanneer u de POST/PUT-methode gebruikt, vertegenwoordigt de eigenschap van het lichaam de payload die naar het eindpunt wordt verzonden. U gekoppelde services en gegevenssets doorgeven als onderdeel van de payload. Hier is het schema voor de payload:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Voorbeeld
In dit voorbeeld roept de webactiviteit in de pijplijn een REST-eindpunt aan. Het geeft een Azure SQL-gekoppelde service en een Azure SQL-gegevensset door aan het eindpunt. Het REST-eindpunt gebruikt de Azure SQL-verbindingstekenreeks om verbinding te maken met de Azure SQL-server en retourneert de naam van de instantie SQL-server.

### <a name="pipeline-definition"></a>Pijplijndefinitie

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Parameterwaarden voor pijplijn

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Eindpuntcode voor webservice

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Volgende stappen
Bekijk andere controlestroomactiviteiten die worden ondersteund door Data Factory:

- [Activiteit uitvoeren van pijplijn](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
