---
title: Capaciteit van een Azure API Management-exemplaar | Microsoft Documenten
description: In dit artikel wordt uitgelegd wat de capaciteitsstatistiek is en hoe u weloverwogen beslissingen nemen of u een Azure API Management-exemplaar wilt schalen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: b6d949b50be348e72cedfc3710383308b04de106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336009"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capaciteit van een Azure API Management-exemplaar

**Capaciteit** is de belangrijkste [Azure Monitor-statistiek](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) voor het nemen van weloverwogen beslissingen of een API-beheerinstantie moet worden geschaald om meer belasting mogelijk te maken. De constructie is complex en legt bepaald gedrag op.

In dit artikel wordt uitgelegd wat de **capaciteit** is en hoe deze zich gedraagt. Het laat zien hoe u toegang krijgt tot **capaciteitsstatistieken** in de Azure-portal en stelt voor wanneer u overweegt uw API-beheerexemplaar te schalen of te upgraden.

> [!IMPORTANT]
> In dit artikel wordt besproken hoe u uw Azure API Management-exemplaar controleren en schalen op basis van de capaciteitsstatistiek. Het is echter even belangrijk om te begrijpen wat er gebeurt wanneer een afzonderlijke API Management-instantie daadwerkelijk zijn capaciteit heeft *bereikt.* Azure API Management past geen beperking op serviceniveau toe om een fysieke overbelasting van de exemplaren te voorkomen. Wanneer een instantie zijn fysieke capaciteit bereikt, gedraagt deze zich vergelijkbaar met een overbelaste webserver die geen binnenkomende aanvragen kan verwerken: latentie neemt toe, verbindingen worden verbroken, er zullen time-outfouten optreden, enz. Dit betekent dat API-clients bereid moeten zijn om deze mogelijkheid vergelijkbaar met die van elke andere externe service aan te pakken (bijvoorbeeld door een beleid voor nieuwe try toe te passen).

## <a name="prerequisites"></a>Vereisten

Als u de stappen uit dit artikel wilt volgen, moet u het volgende hebben:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een APIM-exemplaar. Zie [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Wat is capaciteit

![Metrische capaciteitswaarde](./media/api-management-capacity/capacity-ingredients.png)

**Capaciteit** is een indicator van belasting op een API-beheerexemplaar. Het weerspiegelt resources gebruik (CPU, geheugen) en netwerk wachtrij lengtes. CPU- en geheugengebruik onthult het verbruik van resources door:

+ API Management-gegevensplaneservices, zoals aanvraagverwerking, waaronder doorstuuraanvragen of het uitvoeren van een beleid.
+ API Management Management plane services, zoals beheeracties die worden toegepast via de Azure Portal of ARM, of load afkomstig van de [ontwikkelaarsportal.](api-management-howto-developer-portal.md)
+ Geselecteerde processen van het besturingssysteem, inclusief processen die kosten van TLS-handshakes op nieuwe verbindingen met zich meebrengen.

De totale **capaciteit** is een gemiddelde van zijn eigen waarden van elke eenheid van een API Management-instantie.

Hoewel de **capaciteitsstatistiek** is ontworpen om problemen met uw API-beheerinstantie te vergroten, zijn er gevallen waarin problemen niet worden weerspiegeld in wijzigingen in de **capaciteitsstatistiek.**

## <a name="capacity-metric-behavior"></a>Metriek gedrag capaciteit

Door de constructie kan de **capaciteit** in het echte leven worden beïnvloed door vele variabelen, bijvoorbeeld:

+ verbindingspatronen (nieuwe verbinding op een aanvraag versus hergebruik van de bestaande verbinding)
+ grootte van een verzoek en antwoord
+ beleid dat is geconfigureerd voor elke API of het aantal clients dat aanvragen verzendt.

Hoe complexer de bewerkingen op de aanvragen zijn, hoe hoger het **capaciteitsverbruik** zal zijn. Complexe transformatiebeleidsregels verbruiken bijvoorbeeld veel meer CPU dan een eenvoudige aanvraag doorsturen. Trage backend service reacties zal het ook verhogen.

> [!IMPORTANT]
> **Capaciteit** is geen directe maatstaf voor het aantal aanvragen dat wordt verwerkt.

![Metrische pieken voor capaciteit](./media/api-management-capacity/capacity-spikes.png)

**Capaciteit** kan ook spike met tussenpozen of groter zijn dan nul, zelfs als er geen aanvragen worden verwerkt. Het gebeurt vanwege systeem- of platformspecifieke acties en mag niet in aanmerking worden genomen bij de beslissing om een instantie te schalen.

Metric met een lage **capaciteit** betekent niet noodzakelijkerwijs dat uw API-beheerinstantie geen problemen ondervindt.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>De Azure Portal gebruiken om de capaciteit te onderzoeken
  
![Metrische capaciteitswaarde](./media/api-management-capacity/capacity-metric.png)  

1. Navigeer naar uw APIM-exemplaar in de [Azure-portal.](https://portal.azure.com/)
2. Selecteer **Metrische gegevens**.
3. Selecteer in de paarse sectie **capaciteitsstatistiek** uit beschikbare statistieken en laat de **standaardAvg-aggregatie** achter.

    > [!TIP]
    > U moet altijd kijken naar een **capaciteitsmetrische** uitsplitsing per locatie om verkeerde interpretaties te voorkomen.

4. Selecteer **locatie** voor splitsing van de statistiek op dimensie in de groene sectie.
5. Kies een gewenst tijdsbestek van de bovenste balk van de sectie.

    U een metrische waarschuwing instellen om u te laten weten wanneer er iets onverwachts gebeurt. Ontvang bijvoorbeeld meldingen wanneer uw APIM-exemplaar de verwachte piekcapaciteit meer dan 20 minuten heeft overschreden.

    >[!TIP]
    > U waarschuwingen configureren om u te laten weten wanneer de capaciteit van uw service bijna op is of azure monitor-functionaliteit voor automatisch schalen gebruiken om automatisch een Azure API Management-eenheid toe te voegen. Het schalen van de bewerking kan ongeveer 30 minuten duren, dus u moet uw regels dienovereenkomstig plannen.  
    > Alleen het schalen van de hoofdlocatie is toegestaan.

## <a name="use-capacity-for-scaling-decisions"></a>Capaciteit gebruiken voor het schalen van beslissingen

**Capaciteit** is de statistiek voor het nemen van beslissingen of een API-beheerinstantie moet worden geschaald om meer belasting mogelijk te maken. Overweeg het volgende:

+ Kijkend naar een lange termijn trend en gemiddelde.
+ Plotselinge pieken negeren die waarschijnlijk niet gerelateerd zijn aan een toename van de belasting (zie sectie 'Capaciteitsmetrisch gedrag' voor uitleg).
+ Upgraden of schalen van uw instantie, wanneer **de waarde**van de capaciteit gedurende een langere periode (bijvoorbeeld 30 minuten) hoger is dan 60% of 70%. Verschillende waarden kunnen beter werken voor uw service of scenario.

>[!TIP]  
> Als u uw verkeer vooraf inschatten, test u uw APIM-exemplaar op workloads die u verwacht. U de aanvraagbelasting voor uw tenant geleidelijk verhogen en controleren welke waarde van de capaciteitsstatistiek overeenkomt met uw piekbelasting. Volg de stappen uit de vorige sectie om Azure-portal te gebruiken om te begrijpen hoeveel capaciteit op een bepaald moment wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

[Een Azure API Management-serviceinstantie schalen of upgraden](upgrade-and-scale.md)