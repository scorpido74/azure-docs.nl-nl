---
title: Batching uitvoer in Azure Event Grid IoT Edge | Microsoft Documenten
description: Batching uitvoer in gebeurtenisraster op IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841744"
---
# <a name="output-batching"></a>Batchverwerking van uitvoer

Event Grid heeft ondersteuning om meer dan één gebeurtenis in één leveringsaanvraag te leveren. Deze functie maakt het mogelijk om de totale leveringsdoorvoer te verhogen zonder de HTTP-kosten per aanvraag te betalen. Batching is standaard uitgeschakeld en kan per abonnement worden ingeschakeld.

> [!WARNING]
> De maximaal toegestane duur om elke leveringsaanvraag te verwerken, verandert niet, hoewel de abonneecode mogelijk meer werk per batched request moet doen. De time-out van de levering is standaard 60 seconden.

## <a name="batching-policy"></a>Batching-beleid

Het batchgedrag van Event Grid kan per abonnee worden aangepast door de volgende twee instellingen aan te passen:

* Maximale gebeurtenissen per batch

  Met deze instelling stelt u een bovengrens in voor het aantal gebeurtenissen dat kan worden toegevoegd aan een batched delivery request.

* Voorkeursbatchgrootte in Kilobytes

  Deze knop wordt gebruikt om het maximum aantal kilobytes dat per leveringsaanvraag kan worden verzonden verder te regelen

## <a name="batching-behavior"></a>Batching-gedrag

* Alles of geen

  Event Grid werkt met alles-of-geen semantiek. Het ondersteunt geen gedeeltelijk succes van een batchlevering. Abonnees moeten voorzichtig zijn om alleen te vragen voor zo veel gebeurtenissen per partij als ze redelijkerwijs kunnen verwerken in 60 seconden.

* Optimistische batching

  De instellingen voor batchingbeleid zijn geen strikte grenzen aan het batchgedrag en worden op basis van de beste inspanning gerespecteerd. Bij lage gebeurtenistarieven ziet u vaak dat de batchgrootte kleiner is dan de gevraagde maximale gebeurtenissen per batch.

* Standaard is ingesteld op UIT

  Gebeurtenisraster voegt standaard slechts één gebeurtenis toe aan elke leveringsaanvraag. De manier om batching in te schakelen is om een van de eerder genoemde instellingen in het artikel in het evenement abonnement JSON.

* Standaardwaarden

  Het is niet nodig om zowel de instellingen (Maximale gebeurtenissen per batch als de grootte van de geschatte batch in kilobytes) op te geven bij het maken van een gebeurtenisabonnement. Als er slechts één instelling is ingesteld, gebruikt Gebeurtenisraster (configureerbare) standaardwaarden. Zie de volgende secties voor de standaardwaarden en hoe u deze overschrijven.

## <a name="turn-on-output-batching"></a>Uitvoerbatching inschakelen

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Maximum toegestane waarden configureren

Met de volgende instellingen voor implementatietijd bepalen u de maximale waarde die is toegestaan bij het maken van een gebeurtenisabonnement.

| Naam van eigenschap | Beschrijving |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Maximale waarde toegestaan `PreferredBatchSizeInKilobytes` voor de knop. Standaard `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Maximale waarde toegestaan `MaxEventsPerBatch` voor de knop. Standaard `50`.

## <a name="configuring-runtime-default-values"></a>Standaardwaarden voor runtime configureren

Met de volgende instellingen voor implementatietijd bepalen de standaardwaarde runtime van elke knop wanneer deze niet is opgegeven in het gebeurtenisabonnement. Om dit te herhalen, moet ten minste één knop op het gebeurtenisabonnement worden ingesteld om batchgedrag in te schakelen.

| Naam van eigenschap | Beschrijving |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximale leveringsaanvraaggrootte `MaxEventsPerBatch` wanneer alleen is opgegeven. Standaard `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Maximum aantal gebeurtenissen dat aan een `MaxBatchSizeInBytes` batch moet worden toegevoegd wanneer alleen is opgegeven. Standaard `10`.
