---
title: Levering en opnieuw proberen - Azure Event Grid IoT Edge | Microsoft Documenten
description: Levering en opnieuw proberen in Gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841709"
---
# <a name="delivery-and-retry"></a>Leveren en opnieuw proberen

Event Grid biedt duurzame levering. Het probeert om elk bericht minstens één keer voor elk het bijpassende abonnement onmiddellijk te leveren. Als het eindpunt van een abonnee de ontvangst van een gebeurtenis niet bevestigt of als er een storing optreedt, worden gebeurtenisrastersopnieuw geleverd op basis van een vast **schema voor nieuwe pogingen** en een nieuw **gebruiksbeleid**.  Standaard levert de module Gebeurtenisraster één gebeurtenis tegelijk aan de abonnee. De payload is echter een array met een enkele gebeurtenis. U de module meer dan één gebeurtenis tegelijk laten leveren door de functie uitvoerbatching in te schakelen. Zie [uitvoerbatching](delivery-output-batching.md)voor meer informatie over deze functie.  

> [!IMPORTANT]
>Er is geen persistentieondersteuning voor gebeurtenisgegevens. Dit betekent dat u gebeurtenissen die nog niet zijn geleverd, opnieuw implementeren of opnieuw starten.

## <a name="retry-schedule"></a>Schema opnieuw proberen

Event Grid wacht tot 60 seconden op een antwoord na het verzenden van een bericht. Als het eindpunt van de abonnee het antwoord niet ackt, wordt het bericht in een van onze back-off wachtrijen geplaatst voor volgende nieuwe pogingen.

Er zijn twee vooraf geconfigureerde back-off wachtrijen die bepalen welk schema een nieuwe poging zal worden geprobeerd. Dit zijn:

| Planning | Beschrijving |
| ---------| ------------ |
| 1 minuut | Berichten die hier terechtkomen, worden elke minuut geprobeerd.
| 10 minuten | Berichten die hier terechtkomen, worden elke 10e minuut geprobeerd.

### <a name="how-it-works"></a>Hoe werkt het?

1. Bericht komt binnen in de module Gebeurtenisraster. Er wordt geprobeerd om het onmiddellijk te leveren.
1. Als de levering mislukt, wordt het bericht in de wachtrij van 1 minuut geplaatst en na een minuut opnieuw geprobeerd.
1. Als de levering blijft mislukken, wordt het bericht in de wachtrij van 10 minuten geplaatst en elke 10 minuten opnieuw geprobeerd.
1. Leveringen worden geprobeerd totdat de beleidslimieten succesvol zijn bereikt of opnieuw worden geprobeerd.

## <a name="retry-policy-limits"></a>Beleidslimieten opnieuw proberen

Er zijn twee configuraties die het beleid voor opnieuw proberen bepalen. Dit zijn:

* Maximum aantal pogingen
* Event time-to-live (TTL)

Een gebeurtenis wordt verwijderd als een van de grenzen van het nieuwe beleid wordt bereikt. Het nieuwe schema zelf is beschreven in de sectie Opnieuw proberen schema. Configuratie van deze limieten kan worden gedaan voor alle abonnees of per abonnementsbasis. De volgende sectie beschrijft elk is verder detail.

## <a name="configuring-defaults-for-all-subscribers"></a>Standaardinstellingen configureren voor alle abonnees

Er zijn twee `brokers__defaultMaxDeliveryAttempts` `broker__defaultEventTimeToLiveInSeconds` eigenschappen: en die kunnen worden geconfigureerd als onderdeel van de implementatie van gebeurtenisraster, waarmee de standaardinstellingen voor het opnieuw proberen van beleid voor alle abonnees worden geregeld.

| Naam van eigenschap | Beschrijving |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Maximaal aantal pogingen om een gebeurtenis te leveren. Standaardwaarde: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Gebeurtenis TTL in seconden waarna een gebeurtenis wordt verwijderd als deze niet wordt geleverd. Standaardwaarde: **7200** seconden

## <a name="configuring-defaults-per-subscriber"></a>Standaardwaarden per abonnee configureren

U ook beleidslimieten per abonnement opnieuw proberen.
Zie onze [API-documentatie](api.md) voor informatie over het configureren van standaardinstellingen per abonnee. Standaardinstellingen op abonnementsniveau overschrijven de configuraties op moduleniveau.

## <a name="examples"></a>Voorbeelden

In het volgende voorbeeld wordt het beleid voor opnieuw proberen ingesteld in de module Gebeurtenisraster met maxNumberOfAttempts = 3 en Gebeurtenis TTL van 30 minuten

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
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

In het volgende voorbeeld wordt een webhaakabonnement ingesteld met maxNumberOfAttempts = 3 en Event TTL van 30 minuten

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
