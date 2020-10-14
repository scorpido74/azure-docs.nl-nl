---
title: Prijsscenario's voor oproepen (spraak/video) en chatten
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over het prijsmodel voor Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4eec258cf642688c87b363ff467f1f368727a013
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761223"
---
# <a name="pricing-scenarios"></a>Prijsscenario's

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Prijzen voor Azure Communication Services zijn gebaseerd op een model voor betalen per gebruik zonder kosten vooraf. Er worden alleen kosten in rekening gebracht voor uw verbruik en het gebruik van de services.

## <a name="voicevideo-calling-and-screen-sharing"></a>Spraak-/video-oproepen en scherm delen

Met Azure Communication Services kunt u functionaliteit voor spraak-/video-oproepen en scherm delen aan uw toepassingen toevoegen. U kunt de ervaring insluiten in uw toepassingen met behulp van JavaScript-, Objective-C- (Apple), Java- (Android) of .NET-clientbibliotheken. Raadpleeg onze [volledige lijst met beschikbare clientbibliotheken](./sdk-options.md).

### <a name="pricing"></a>Prijzen

De kosten voor de services voor oproepen en scherm delen bedragen $0,004 per minuut per deelnemer voor groepsoproepen. Raadpleeg [deze pagina](./call-flows.md) voor uitleg over de verschillende mogelijke oproepstromen.

Voor elke deelnemer van de oproep worden kosten in rekening gebracht voor elke minuut die men verbonden is met de oproep. Dit is altijd zo, ongeacht of de gebruiker deelneemt aan een video- of spraakoproep of het scherm deelt.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Prijsvoorbeeld: Audio/video-groepsoproep met JS- en iOS-clientbibliotheken

Alice heeft een groepsoproep gemaakt met haar collega's, Bob en Charlie. Alice en Bob hebben JS-clientbibliotheken gebruikt, en Charlie iOS-clientbibliotheken.

- De oproep duurt in totaal 60 minuten.
- Alice en Bob hebben aan de hele oproep deelgenomen. Alice heeft haar video 5 minuten aan gehad en haar scherm 23 minuten gedeeld. Bob heeft zijn video gedurende de hele oproep (60 minuten) aan gehad en zijn scherm 12 minuten gedeeld.
- Charlie heeft de oproep na 43 minuten verlaten. Charlie heeft audio en video gebruikt gedurende de hele deelname (43 minuten).

**Kostenberekeningen**

- 2 deelnemers x 60 minuten x $0,004 per deelnemer per minuut = $0,48 [voor video en audio geldt hetzelfde tarief]
- 1 deelnemers x 43 minuten x $0,004 per deelnemer per minuut = $0,172 [voor video en audio geldt hetzelfde tarief]

**Totale kosten voor de groepsoproep**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Chat

Met Communication Services kunt u uw toepassing uitbreiden met de mogelijkheid om chatberichten tussen twee of meer gebruikers te verzenden en te ontvangen. Chat-clientbibliotheken zijn beschikbaar voor JavaScript, .NET, Python en Java. Raadpleeg [deze pagina voor meer informatie over clientbibliotheken](./sdk-options.md)

### <a name="price"></a>Prijs

Er wordt $0,0008 in rekening gebracht voor elk verzonden chatbericht.

### <a name="pricing-example-chat-between-two-users"></a>Prijsvoorbeeld: Chat tussen twee gebruikers 

Geeta start een chatthread met Emily om een update te delen en verzendt 5 berichten. De chat duurt 10 minuten, waarbij Geeta en Elsje ieder nog eens 15 berichten verzenden.

**Kostenberekeningen** 
- Aantal verzonden berichten (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Prijsvoorbeeld: Groepschat met meerdere gebruikers 

Charlie start een chatthread met zijn vrienden Casey en Jasmine om een vakantie te plannen. Ze chatten een tijdje, waarbij Charlie, Casey en Jasmine respectievelijk 20, 30 en 18 berichten verzenden. Ze realiseren zich dat hun vriendin Rose wellicht ook mee op vakantie zou willen, dus ze voegen haar toe aan de chatthread en delen de berichtgeschiedenis met haar. 

Rose ziet de berichten en mengt zich in de chat. Casey ontvangt ondertussen een oproep en besluit de conversatie te verlaten om later weer aan te haken. Charlie, Jasmine en Rose overleggen over de reisdatums en verzenden nog eens respectievelijk 30, 25 en 35 berichten.

**Kostenberekeningen** 

- Aantal verzonden berichten (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonie en sms

## <a name="price"></a>Prijs 

Telefoonservices worden per minuut berekend, en sms-berichten worden per bericht berekend. De prijzen zijn afhankelijk van het type en de locatie van het nummer dat u gebruikt, evenals de bestemming van uw oproepen en sms-berichten.

### <a name="telephone-calling"></a>Telefoonoproepen

Traditionele telefonische oproep (oproep die plaatsvindt via het openbare telefoonnetwerk) is beschikbaar met betalen per gebruik voor telefoonnummers in de Verenigde Staten. De prijs wordt per minuut berekend op basis van het type nummer dat wordt gebruikt en de bestemming van de oproep. De prijsinformatie voor de meest populaire belbestemmingen vindt u in de volgende tabel. Raadpleeg de [gedetailleerde prijslijst](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) voor een volledige lijst met bestemmingen.


#### <a name="united-states-calling-prices"></a>Prijzen voor bellen in de Verenigde Staten

De volgende prijzen zijn inclusief de vereiste communicatiebelastingen en -kosten tot en met 30 juni 2021:

|Type getal   |Om oproepen te plaatsen   |Om oproepen te ontvangen|
|--------------|-----------|------------|
|Lokaal     |Vanaf $0,013/min       |$0,0085/min        |
|Gratis |$0,013/min   |$0,0220/min |

#### <a name="other-calling-destinations"></a>Overige belbestemmingen

De volgende prijzen zijn inclusief de vereiste communicatiebelastingen en -kosten tot en met 30 juni 2021:

|Bellen naar   |Prijs per minuut|
|-----------|------------|
|Canada     |Vanaf $0,013/min   |
|Verenigd Koninkrijk     |Vanaf $0,015/min   |
|Duitsland     |Vanaf $0,015/min   |
|Frankrijk     |Vanaf $0,016/min   |


### <a name="sms"></a>Sms

Sms biedt betalen per gebruik. De prijs wordt per bericht in rekening gebracht op basis van de bestemming van het bericht. Berichten kunnen worden verzonden door gratis telefoonnummers naar telefoonnummers die zich binnen de Verenigde Staten bevinden. Houd er rekening mee dat lokale (geografische) telefoonnummers niet kunnen worden gebruikt voor het verzenden van sms-berichten.

De volgende prijzen zijn inclusief de vereiste communicatiebelastingen en -kosten tot en met 30 juni 2021:

|Land   |Berichten verzenden|Berichten ontvangen|
|-----------|------------|------------|
|Verenigde Staten (gratis)    |$0,0075/bericht   | $0,0075/bericht |