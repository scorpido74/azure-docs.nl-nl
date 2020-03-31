---
title: Azure Functions geo-disaster recovery en hoge beschikbaarheid
description: Geografische regio's gebruiken voor redundantie en overfalen in Azure-functies.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080225"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Herstel van geo-disaster azure-functies

Wanneer hele Azure-regio's of datacenters downtime ervaren, is het van cruciaal belang dat compute in een andere regio wordt verwerkt.  In dit artikel worden enkele strategieën uitgelegd die u gebruiken om functies te implementeren om herstel na noodgevallen mogelijk te maken.

## <a name="basic-concepts"></a>Basisbegrippen

Azure-functies worden uitgevoerd in een specifieke regio.  Als u een hogere beschikbaarheid wilt krijgen, u dezelfde functies in meerdere regio's implementeren.  Wanneer u zich in meerdere regio's bevindt, u uw functies laten uitvoeren in het *actieve/actieve* patroon of *het actieve/passieve* patroon.  

* Actief/actief. Beide regio's zijn actief en ontvangen gebeurtenissen (dupliceren of rotatie). Actief/actief wordt aanbevolen voor HTTPS-functies in combinatie met Azure Front Door.
* Actief/passief. Eén regio is actief en ontvangt gebeurtenissen, terwijl een secundaire niet actief is.  Wanneer failover nodig is, wordt het secundaire gebied geactiveerd en neemt de verwerking over.  Dit wordt aanbevolen voor niet-HTTP-functies zoals Service Bus en Event Hubs.

Lees hoe u [apps in meerdere regio's uitvoert](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) voor meer informatie over implementaties met meerdere regio's.

## <a name="activeactive-for-https-functions"></a>Actief/actief voor HTTPS-functies

Om actieve/actieve implementaties van functies te bereiken, is een component nodig die de gebeurtenissen tussen beide regio's kan coördineren.  Voor HTTPS-functies wordt deze coördinatie uitgevoerd met [Azure Front Door](../frontdoor/front-door-overview.md).  Azure Front Door kan HTTPS-aanvragen routeren en round-robinen tussen meerdere regionale functies.  Het controleert ook periodiek de gezondheid van elk eindpunt.  Als een regionale functie niet meer reageert op statuscontroles, haalt Azure Front Door deze uit de rotatie en stuurt het alleen verkeer door naar gezonde functies.  

![Architectuur voor Azure Front Door en Functie](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Actief/actief voor niet-HTTPS-functies

U nog steeds actieve/actieve implementaties uitvoeren voor niet-HTTPS-functies.  U moet echter overwegen hoe de twee regio's met elkaar omgaan of met elkaar zullen coördineren.  Als u dezelfde functie-app in twee regio's hebt geïmplementeerd, die elk in dezelfde wachtrij voor servicebus worden geactiveerd, fungeren ze als concurrerende consumenten bij het de-wachtrij in de wachtrij wachtrijen.  Hoewel dit betekent dat elk bericht alleen wordt verwerkt door een van de instanties, betekent dit ook dat er nog steeds een enkel storingspunt is op de enkele servicebus.  Als u twee wachtrijen voor servicebussen implementeert (één in een primaire regio, één in een secundaire regio) en de twee functie-apps die naar de wachtrij in de regio zijn gericht, komt de uitdaging nu in de manier waarop de wachtrijberichten worden verdeeld over de twee regio's.  Vaak betekent dit dat elke uitgever probeert een bericht naar *beide* regio's te publiceren en dat elk bericht wordt verwerkt door beide actieve functie-apps.  Hoewel dit een actief/actief patroon creëert, creëert het andere uitdagingen rond duplicatie van compute en wanneer of hoe gegevens worden geconsolideerd.  Om deze redenen wordt het aanbevolen voor niet-HTTPS-triggers om het actieve/passieve patroon te gebruiken.

## <a name="activepassive-for-non-https-functions"></a>Actief/passief voor niet-HTTPS-functies

Actief/passief biedt een manier voor slechts één functie om elk bericht te verwerken, maar biedt een mechanisme om in geval van een ramp niet naar een secundaire regio te gaan.  Azure Functions werkt samen met [geoherstel](../service-bus-messaging/service-bus-geo-dr.md) van Azure Service Bus en [geoherstel van Azure Event Hubs.](../event-hubs/event-hubs-geo-dr.md)

Als voorbeeld Azure Event Hubs-triggers gebruiken, houdt het actieve/passieve patroon het volgende in:

* Azure Event Hub geïmplementeerd in zowel een primaire als een secundaire regio.
* Geo-ramp ingeschakeld om de primaire en secundaire GebeurtenisHub te koppelen.  Hierdoor wordt ook een 'alias' gemaakt die u gebruiken om verbinding te maken met gebeurtenishubs en over te schakelen van primair naar secundair zonder de verbindingsgegevens te wijzigen.
* Functie-apps geïmplementeerd in zowel een primaire als een secundaire regio.
* De functie-apps activeren op de *directe* (niet-alias) verbindingstekenreeks voor de respectievelijke gebeurtenishub. 
* Uitgevers van de gebeurtenishub moeten publiceren naar de tekenreeks aliasverbinding. 

![Actief-passieve voorbeeldarchitectuur](media/functions-geo-dr/active-passive.png)

Voordat de failover mislukt, worden uitgevers die naar de gedeelde alias verzenden, doorgestuurd naar de primaire gebeurtenishub.  De primaire functie-app luistert uitsluitend naar de primaire gebeurtenishub.  De secundaire functie-app is passief en inactief.  Zodra failover is gestart, worden uitgevers die naar de gedeelde alias verzenden, nu doorgestuurd naar de secundaire gebeurtenishub.  De secundaire functie-app wordt nu actief en start automatisch met activeren.  Effectieve failover naar een secundaire regio kan volledig vanuit de gebeurtenishub worden aangestuurd, waarbij de functies alleen actief worden wanneer de betreffende gebeurtenishub actief is.

Lees meer over informatie en overwegingen voor failover met [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) en [event hubs](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Volgende stappen

* [Azure-voordeur maken](../frontdoor/quickstart-create-front-door.md)
* [Gebeurtenishubs mislukken overwegingen](../event-hubs/event-hubs-geo-dr.md#considerations)
