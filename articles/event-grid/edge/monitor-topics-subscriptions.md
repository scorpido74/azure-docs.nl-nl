---
title: Onderwerpen en gebeurtenis abonnementen bewaken-Azure Event Grid IoT Edge | Microsoft Docs
description: Onderwerpen en gebeurtenis abonnementen bewaken
author: femila
ms.author: femila
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d4fbc5232722bfb08bde9be51d44e8e8d7514570
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554362"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Onderwerpen en gebeurtenis abonnementen bewaken

Event Grid op Edge stelt een aantal metrische gegevens voor onderwerpen en gebeurtenis abonnementen in de [Prometheus Exposition-indeling](https://prometheus.io/docs/instrumenting/exposition_formats/). In dit artikel vindt u een beschrijving van de beschik bare metrische gegevens en hoe u deze kunt inschakelen.

## <a name="enable-metrics"></a>Metrische gegevens inschakelen

Configureer de module voor het verzenden van metrische gegevens door de `metrics__reporterType` omgevings variabele in te stellen op `prometheus` in de opties voor het maken van een container:

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

Metrische gegevens zijn beschikbaar via `5888/metrics` de module voor http en `4438/metrics` voor HTTPS. Bijvoorbeeld `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` voor http. Op dit moment kan een metrische module het eind punt pollen om metrische gegevens te verzamelen, zoals in deze [voorbeeld architectuur](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Beschikbare metrische gegevens

In zowel onderwerpen als gebeurtenis abonnementen worden metrische gegevens gegeven om u inzicht te geven in de levering van gebeurtenissen en de prestaties van de module.

### <a name="topic-metrics"></a>Metrische gegevens over het onderwerp

| Metrisch | Beschrijving |
| ------ | ----------- |
| EventsReceived | Aantal gebeurtenissen dat is gepubliceerd naar het onderwerp
| UnmatchedEvents | Het aantal gebeurtenissen dat is gepubliceerd naar het onderwerp dat niet overeenkomt met een gebeurtenis abonnement en wordt verwijderd
| SuccessRequests | Aantal inkomende uitgifte aanvragen dat is ontvangen door het onderwerp
| SystemErrorRequests | Het aantal inkomende publicatie aanvragen dat is mislukt vanwege een interne systeem fout
| UserErrorRequests | Aantal op inkomende uitgifte aanvragen is mislukt vanwege een gebruikers fout zoals een onjuist gevormde JSON
| SuccessRequestLatencyMs | De latentie van de antwoord aanvraag publiceren in milliseconden


### <a name="event-subscription-metrics"></a>Metrische gegevens van gebeurtenis abonnementen

| Metrisch | Beschrijving |
| ------ | ----------- |
| DeliverySuccessCounts | Aantal gebeurtenissen dat is bezorgd bij het geconfigureerde eind punt
| DeliveryFailureCounts | Aantal gebeurtenissen dat niet kan worden bezorgd bij het geconfigureerde eind punt
| DeliverySuccessLatencyMs | Latentie van geslaagde gebeurtenissen in milliseconden
| DeliveryFailureLatencyMs | Latentie van bezorgings fouten van gebeurtenissen in milliseconden
| SystemDelayForFirstAttemptMs | Systeem vertraging van gebeurtenissen vóór eerste bezorg poging in milliseconden
| DeliveryAttemptsCount | Aantal pogingen voor gebeurtenis verzendingen-geslaagd en mislukt
| ExpiredCounts | Aantal gebeurtenissen dat is verlopen en niet is bezorgd bij het geconfigureerde eind punt