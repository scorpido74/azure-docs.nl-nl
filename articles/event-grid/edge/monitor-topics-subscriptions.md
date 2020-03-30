---
title: Onderwerpen en gebeurtenisabonnementen bewaken - Azure Event Grid IoT Edge | Microsoft Documenten
description: Onderwerpen en gebeurtenisabonnementen bewaken
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086674"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Onderwerpen en gebeurtenisabonnementen bewaken

Event Grid on Edge onthult een aantal statistieken voor onderwerpen en gebeurtenisabonnementen in de [Prometheus-expositieindeling.](https://prometheus.io/docs/instrumenting/exposition_formats/) In dit artikel worden de beschikbare statistieken beschreven en hoe u deze inschakelen.

## <a name="enable-metrics"></a>Statistieken inschakelen

Configureer de module om `metrics__reporterType` statistieken uit `prometheus` te zenden door de omgevingsvariabele in te stellen op in de opties voor het maken van containers:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

Metrics zullen beschikbaar `5888/metrics` zijn op van `4438/metrics` de module voor http en voor https. Bijvoorbeeld `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` voor http. Op dit punt kan een metrische module het eindpunt peilen om statistieken te verzamelen zoals in deze [voorbeeldarchitectuur](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Beschikbare metrische gegevens

Zowel onderwerpen als gebeurtenisabonnementen geven statistieken om u inzicht te geven in de levering van gebeurtenissen en de prestaties van modules.

### <a name="topic-metrics"></a>Onderwerpstatistieken

| Gegevens | Beschrijving |
| ------ | ----------- |
| EvenementenOntvangen | Aantal evenementen gepubliceerd in het onderwerp
| Ongeëvenaarde gebeurtenissen | Aantal gebeurtenissen dat is gepubliceerd in het onderwerp dat niet overeenkomt met een gebeurtenisabonnement en wordt verwijderd
| SuccesVerzoeken | Aantal binnenkomende publicatieverzoeken die door het onderwerp zijn ontvangen
| SystemErrorRequests | Aantal inkomende publicatieaanvragen is mislukt als gevolg van een interne systeemfout
| UserErrorRequests | Nummer op binnenkomende publicatieaanvragen is mislukt als gevolg van gebruikersfouten zoals misvormde JSON
| SuccesRequestLatencyMs | Latentie van aanvragen publiceren in milliseconden


### <a name="event-subscription-metrics"></a>Statistieken voor evenementabonnementen

| Gegevens | Beschrijving |
| ------ | ----------- |
| DeliverySuccessCounts | Aantal gebeurtenissen dat is geleverd aan het geconfigureerde eindpunt
| Aantal storingen bij bezorging | Aantal gebeurtenissen dat niet naar het geconfigureerde eindpunt is geleverd
| DeliverySuccessLatencyMs | Latentie van gebeurtenissen die in milliseconden met succes zijn geleverd
| DeliveryFailureLatencyMs | Latentie van foutbezorgingsfouten in milliseconden
| SysteemvertragingForFirstAttemptms | Systeemvertraging van gebeurtenissen vóór eerste leveringspoging in milliseconden
| Aantal deliveryattempts | Aantal pogingen tot het leveren van evenementen - succes en mislukking
| Verlopen tellingen | Aantal gebeurtenissen dat is verlopen en niet is geleverd aan het geconfigureerde eindpunt