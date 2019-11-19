---
title: Azure Data Explorer opname bewerkingen bewaken met Diagnostische logboeken
description: Meer informatie over het instellen van Diagnostische logboeken voor Azure Data Explorer om opname bewerkingen te bewaken.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 13f86f0156299619d8bf8d92eb92bbcf8b4cb76c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173801"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Azure Data Explorer opname bewerkingen bewaken met Diagnostische logboeken (preview)

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. Vervolgens neemt u gegevens op in een tabel in een Data Base, zodat u er query's op kunt uitvoeren. [Azure monitor Diagnostische logboeken](/azure/azure-monitor/platform/diagnostic-logs-overview) bevatten gegevens over de werking van Azure-resources. Azure Data Explorer gebruikt Diagnostische logboeken voor inzichten in geslaagde opname en storingen. U kunt bewerkings logboeken exporteren naar Azure Storage, Event hub of Log Analytics om opname status te bewaken. Logboeken van Azure Storage en Azure Event hub kunnen worden doorgestuurd naar een tabel in uw Azure Data Explorer-cluster voor verdere analyse.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/).
* Een [cluster en data base](create-cluster-database-portal.md)maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Diagnostische logboeken instellen voor een Azure Data Explorer-cluster

Diagnostische logboeken kunnen worden gebruikt om de verzameling van de volgende logboek gegevens te configureren:
* Geslaagde opname bewerkingen: deze logboeken bevatten informatie over het volt ooien van opname bewerkingen.
* Mislukte opname bewerkingen: deze logboeken bevatten gedetailleerde informatie over mislukte opname bewerkingen, waaronder fout Details. 

De gegevens worden vervolgens gearchiveerd in een opslag account, gestreamd naar een event hub of verzonden naar Log Analytics, conform uw specificaties.

### <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Voer de volgende stappen uit om Diagnostische logboeken in te scha kelen:

1. Selecteer in de [Azure Portal](https://portal.azure.com)de Azure Data Explorer-cluster resource die u wilt bewaken.
1. Onder **bewaking**, selecteer **diagnostische instellingen**.
  
    ![Diagnostische logboeken toevoegen](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Selecteer **Diagnostische instelling toevoegen**.
1. In het venster **Diagnostische instellingen** :
 
    ![Configuratie van diagnostische instellingen](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selecteer een **naam** voor de diagnostische instelling.
    1. Selecteer een of meer doelen: een opslag account, Event hub of Log Analytics.
    1. Selecteer de logboeken die moeten worden verzameld: `SucceededIngestion` of `FailedIngestion`.
    1. Selecteer de [metrische gegevens](using-metrics.md) die moeten worden verzameld (optioneel).   
    1. Selecteer **Opslaan** om de nieuwe instellingen en metrische gegevens van de diagnostische logboeken op te slaan.
    1. Maak een **nieuwe ondersteunings aanvraag** in de Azure Portal om de activering van Diagnostische logboeken aan te vragen.

Nieuwe instellingen worden in een paar minuten ingesteld. De logboeken worden vervolgens weer gegeven in het geconfigureerde archief doel (opslag account, Event hub of Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Diagnostische logboeken schema

Alle [Azure monitor Diagnostische logboeken delen een gemeen schappelijk schema op het hoogste niveau](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Data Explorer heeft unieke eigenschappen voor hun eigen gebeurtenissen. Alle logboeken worden opgeslagen in een JSON-indeling.

### <a name="ingestion-logs-schema"></a>Schema opname logboeken

JSON-teken reeksen in het logboek bevatten elementen die in de volgende tabel worden weer gegeven:

|Naam               |Beschrijving
|---                |---
|tijd               |Tijd van het rapport
|resourceId         |Resource-ID Azure Resource Manager
|operationName      |De naam van de bewerking: ' micro soft. KUSTO/CLUSTERS/OPNAME/ACTIE
|operationVersion   |Schema versie: ' 1,0 ' 
|category           |De categorie van de bewerking. `SucceededIngestion` of `FailedIngestion`. Eigenschappen wijken af van een [geslaagde bewerking](#successful-ingestion-operation-log) of [mislukte bewerking](#failed-ingestion-operation-log).
|properties         |Gedetailleerde informatie over de bewerking.

#### <a name="successful-ingestion-operation-log"></a>Geslaagd opname bewerkings logboek

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
**Eigenschappen van een geslaagde bewerking diagnostisch logboek**

|Naam               |Beschrijving
|---                |---
|succeededOn        |Voltooiings tijd van opname
|operationId        |Bewerkings-ID van Azure Data Explorer opname
|database           |De naam van de doel database
|table              |De naam van de doel tabel
|ingestionSourceId  |ID van de opname gegevens bron
|ingestionSourcePath|Pad van de opname gegevens bron of BLOB-URI
|rootActivityId     |Activiteit-id

#### <a name="failed-ingestion-operation-log"></a>Mislukt opname logboek

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

**Eigenschappen van een mislukte bewerking diagnose logboek**

|Naam               |Beschrijving
|---                |---
|failedOn           |Voltooiings tijd van opname
|operationId        |Bewerkings-ID van Azure Data Explorer opname
|database           |De naam van de doel database
|table              |De naam van de doel tabel
|ingestionSourceId  |ID van de opname gegevens bron
|ingestionSourcePath|Pad van de opname gegevens bron of BLOB-URI
|rootActivityId     |Activiteit-id
|details informatie            |Gedetailleerde beschrijving van de fout en het fout bericht
|Code          |Foutcode 
|failureStatus      |`Permanent` of `Transient`. Het opnieuw proberen van een tijdelijke fout is mislukt.
|originatesFromUpdatePolicy|Waar als de fout is opgetreden vanuit een update beleid
|shouldRetry        |Waar als de nieuwe poging slaagt

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: gegevens opnemen en controleren in azure Data Explorer](ingest-data-no-code.md)
* [Metrische gegevens gebruiken voor het bewaken van de cluster status](using-metrics.md)

