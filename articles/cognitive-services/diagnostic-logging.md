---
title: Diagnostische logboeken
titleSuffix: Azure Cognitive Services
description: Deze handleiding biedt stapsgewijze instructies om diagnostische logboekregistratie voor een Azure Cognitive Service in te schakelen. Deze logboeken bieden uitgebreide, frequente gegevens over de werking van een resource die worden gebruikt voor het identificeren en debuggen van problemen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827899"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Diagnostische logboekregistratie inschakelen voor Azure Cognitive Services

Deze handleiding biedt stapsgewijze instructies om diagnostische logboekregistratie voor een Azure Cognitive Service in te schakelen. Deze logboeken bieden uitgebreide, frequente gegevens over de werking van een resource die worden gebruikt voor het identificeren en debuggen van problemen. Voordat u verdergaat, moet u een Azure-account hebben met een abonnement op ten minste één cognitieve service, zoals [Bing Web Search,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview) [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)of [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Vereisten

Als u diagnostische logboekregistratie wilt inschakelen, moet u uw logboekgegevens ergens opslaan. Deze zelfstudie maakt gebruik van Azure Storage en Log Analytics.

* [Azure-opslag](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - Behoudt diagnostische logboeken voor beleidscontrole, statische analyse of back-up. Het opslagaccount hoeft niet in hetzelfde abonnement te zitten als de logboeken voor het uitzenden van resources, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang tot beide abonnementen heeft.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) - Een flexibele tool voor zoeken en analyseren van logboeken die het mogelijk maakt om ruwe logboeken te analyseren die worden gegenereerd door een Azure-bron.

> [!NOTE]
> Er zijn extra configuratieopties beschikbaar. Zie [Logboekgegevens verzamelen en gebruiken van uw Azure-bronnen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)voor meer informatie.

## <a name="enable-diagnostic-log-collection"></a>Diagnostische logboekverzameling inschakelen  

Laten we beginnen met het inschakelen van diagnostische logboekregistratie met behulp van de Azure-portal.

> [!NOTE]
> Als u deze functie wilt inschakelen met PowerShell of azure cli, gebruikt u de instructies in [Logboekgegevens verzamelen en gebruiken van uw Azure-bronnen.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

1. Ga naar Azure Portal. Zoek en selecteer vervolgens een resource voor Cognitive Services. Bijvoorbeeld uw abonnement op Bing Web Search.   
2. Zoek vervolgens in het navigatiemenu aan de linkerkant **Controle** en selecteer **Diagnostische instellingen**. Dit scherm bevat alle eerder gemaakte diagnostische instellingen voor deze bron.
3. Als er een eerder gemaakte bron is die u wilt gebruiken, u deze nu selecteren. Selecteer anders **+ Diagnostische instelling toevoegen**.
4. Voer een naam in voor de instelling. Selecteer vervolgens **Archiveren naar een opslagaccount** en **Verzenden naar logboekanalyse**.
5. Wanneer u wordt gevraagd om te configureren, selecteert u de opslagaccount en DES-werkruimte die u wilt gebruiken om u diagnostische logboeken op te slaan. **Opmerking:** Als u geen opslagaccount of OMS-werkruimte hebt, volgt u de aanwijzingen om er een te maken.
6. Selecteer **Audit,** **RequestResponse**en **AllMetrics**. Stel vervolgens de bewaartermijn in voor uw diagnostische logboekgegevens. Als een bewaarbeleid op nul is ingesteld, worden gebeurtenissen voor die logboekcategorie voor onbepaalde tijd opgeslagen.
7. Klik op **Opslaan**.

Het kan tot twee uur duren voordat logboekgegevens beschikbaar zijn voor query's en analyses. Dus maak je geen zorgen als je niet meteen iets ziet.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Diagnostische gegevens uit Azure Storage weergeven en exporteren

Azure Storage is een robuuste oplossing voor objectopslag die is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. In deze sectie leert u uw opslagaccount op te vragen voor totale transacties over een tijdsbestek van 30 dagen en de gegevens te exporteren naar excel.

1. Zoek in de Azure-portal de Azure Storage-bron die u in de laatste sectie hebt gemaakt.
2. Zoek in het navigatiemenu aan de linkerkant **de zoeking controle** en selecteer **Metrische gegevens**.
3. Gebruik de beschikbare vervolgkeuzeklassen om uw query te configureren. Laten we in dit voorbeeld het tijdsbereik instellen op **Laatste 30 dagen** en de statistiek op **Transactie**.
4. Wanneer de query is voltooid, ziet u een visualisatie van de transactie in de afgelopen 30 dagen. Als u deze gegevens wilt exporteren, gebruikt u de knop **Exporteren naar Excel** boven aan de pagina.

Meer informatie over wat u doen met diagnostische gegevens in [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Logboeken bekijken in Log Analytics

Volg deze instructies om logboekanalysegegevens voor uw bron te verkennen.

1. Zoek en selecteer Log **Analytics** in het navigatiemenu aan de linkerkant in de Azure-portal.
2. Zoek en selecteer de resource die u hebt gemaakt bij het inschakelen van diagnoses.
3. Zoek en selecteer **Logboeken**onder **Algemeen**. Vanaf deze pagina u query's uitvoeren op uw logboeken.

### <a name="sample-queries"></a>Voorbeeldquery's

Hier zijn een paar eenvoudige Kusto-query's die u gebruiken om uw loggegevens te verkennen.

Voer deze query uit voor alle diagnostische logboeken van Azure Cognitive Services voor een bepaalde periode:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Voer deze query uit om de 10 meest recente logboeken te bekijken:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Voer deze query uit naar groepsbewerkingen op **resource:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Voer deze query uit om de gemiddelde tijd te vinden die nodig is om een bewerking uit te voeren:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Voer deze query uit om het volume van bewerkingen in de loop van de tijd te bekijken die zijn gesplitst door OperationName met tellingen die voor elke tiens worden opgeslagen.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Volgende stappen

* Lees zowel het [overzicht van statistieken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) in Microsoft Azure als Overzicht van azure diagnostic [logs-artikelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) als de statistieken en logboekcategorieën die worden ondersteund door de verschillende Azure-services.
* Lees deze artikelen voor meer informatie over gebeurtenishubs:
  * [Wat is Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Aan de slag met Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Lees [Downloadstatistieken en diagnostische logboeken uit Azure Storage.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)
* Lees [Logboekzoekopdrachten begrijpen in Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
