---
title: Innamebewerkingen van Azure Data Explorer controleren met diagnostische logboeken
description: Meer informatie over het instellen van diagnostische logboeken voor Azure Data Explorer om de opnamebewerkingen te controleren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277429"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Innamebewerkingen van Azure Data Explorer controleren met diagnostische logboeken (voorbeeld)

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. Vervolgens neemt u gegevens in een tabel in een database in een tabel in, zodat u er query's tegen uitvoeren. [Diagnostische logboeken van Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) bevatten gegevens over de werking van Azure-resources. Azure Data Explorer gebruikt diagnostische logboeken voor inzichten over innamesuccessen en -fouten. U bedrijfslogboeken exporteren naar Azure Storage, Event Hub of Log Analytics om de opnamestatus te controleren. Logboeken van Azure Storage en Azure Event Hub kunnen worden doorgestuurd naar een tabel in uw Azure Data Explorer-cluster voor verdere analyse.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/)aan.
* Maak een [cluster en database](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Diagnostische logboeken instellen voor een Azure Data Explorer-cluster

Diagnostische logboeken kunnen worden gebruikt om de verzameling van de volgende logboekgegevens te configureren:
* Succesvolle opnamebewerkingen: deze logboeken bevatten informatie over succesvol voltooide opnamebewerkingen.
* Mislukte opnamebewerkingen: deze logboeken bevatten gedetailleerde informatie over mislukte opnamebewerkingen, inclusief foutgegevens. 

De gegevens worden vervolgens gearchiveerd naar een opslagaccount, gestreamd naar een gebeurtenishub of verzonden naar Log Analytics, volgens uw specificaties.

### <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Ga als volgt te werk om diagnostische logboeken in te schakelen:

1. Selecteer in de [Azure-portal](https://portal.azure.com)de Azure Data Explorer-clusterbron die u wilt controleren.
1. Selecteer **Diagnostische instellingen** onder **Controle**.
  
    ![Diagnostische logboeken toevoegen](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Selecteer **Diagnostische instelling toevoegen**.
1. Ga als een venster naar **de instellingen voor diagnostische gegevens:**
 
    ![Configuratie van diagnostische instellingen](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selecteer **Naam** voor uw diagnostische instelling.
    1. Selecteer een of meer doelen: een opslagaccount, gebeurtenishub of logboekanalyse.
    1. Selecteer logboeken die `SucceededIngestion` `FailedIngestion`moeten worden verzameld: of .
    1. Selecteer [statistieken](using-metrics.md#supported-azure-data-explorer-metrics) die moeten worden verzameld (optioneel).  
    1. Selecteer **Opslaan** om de nieuwe instellingen en statistieken voor diagnostische logboeken op te slaan.
    1. Maak een **nieuw ondersteuningsverzoek** in de Azure-portal om activering van diagnostische logboeken aan te vragen.

Nieuwe instellingen worden in een paar minuten ingesteld. Logboeken worden vervolgens weergegeven in het geconfigureerde archiefdoel (Opslagaccount, gebeurtenishub of logboekanalyse). 

## <a name="diagnostic-logs-schema"></a>Schema diagnostische logboeken

Alle [diagnostische logboeken van Azure Monitor delen een algemeen schema op het hoogste niveau](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Data Explorer heeft unieke eigenschappen voor hun eigen gebeurtenissen. Alle logboeken worden opgeslagen in een JSON-indeling.

### <a name="ingestion-logs-schema"></a>Schema voor innamelogboeken

Log JSON-tekenreeksen bevatten elementen in de volgende tabel:

|Name               |Beschrijving
|---                |---
|tijd               |Tijd van het rapport
|resourceId         |Azure Resource Manager-bron-id
|operationName      |Naam van de bewerking: 'MICROSOFT. KUSTO/CLUSTERS/INNAME/ACTIE"
|operationVersion   |Schemaversie: '1.0' 
|category           |Categorie van de bewerking. `SucceededIngestion` of `FailedIngestion`. Eigenschappen verschillen voor [een geslaagde bewerking](#successful-ingestion-operation-log) of [mislukte bewerking](#failed-ingestion-operation-log).
|properties         |Gedetailleerde informatie over de operatie.

#### <a name="successful-ingestion-operation-log"></a>Logboek van de innamebewerking

**Voorbeeld:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Eigenschappen van een diagnostisch logboek voor een geslaagde bewerking**

|Name               |Beschrijving
|---                |---
|succeededOn        |Tijd van inname voltooiing
|operationId        |Inname-id van Azure Data Explorer
|database           |Naam van de doeldatabase
|tabel              |Naam van de doeltabel
|ingestionSourceId  |ID van de innamegegevensbron
|ingestionSourcePath|Pad van de opnamegegevensbron of blob URI
|rootActivityId     |Activiteits-id

#### <a name="failed-ingestion-operation-log"></a>Logboek van mislukte opnamebewerking

**Voorbeeld:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Eigenschappen van een diagnostisch logboek voor mislukte bewerking**

|Name               |Beschrijving
|---                |---
|mislukt Op           |Tijd van inname voltooiing
|operationId        |Inname-id van Azure Data Explorer
|database           |Naam van de doeldatabase
|tabel              |Naam van de doeltabel
|ingestionSourceId  |ID van de innamegegevensbron
|ingestionSourcePath|Pad van de opnamegegevensbron of blob URI
|rootActivityId     |Activiteits-id
|Details            |Gedetailleerde beschrijving van de fout en foutbericht
|errorCode (errorCode)          |Foutcode 
|foutStatus      |`Permanent` of `Transient`. Het opnieuw proberen van een tijdelijke fout kan slagen.
|afkomstig vanFromUpdatePolicy|True als een fout afkomstig is van een updatebeleid
|shouldRetry        |True als opnieuw proberen kan slagen

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Gegevens over het innemen en querybewaking in Azure Data Explorer](ingest-data-no-code.md)
* [Metrische gegevens gebruiken om clusterstatus te bewaken](using-metrics.md)

