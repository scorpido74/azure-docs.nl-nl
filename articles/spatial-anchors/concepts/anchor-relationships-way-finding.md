---
title: Verankerrelaties en manierzoeken
description: Meer informatie over het conceptuele model achter ankerrelaties. Leer ankers in een ruimte met elkaar te verbinden en de API in de buurt te gebruiken om een zoekscenario te realiseren.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270602"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Veranker relaties en bewegwijzering in Azure Spatial Anchors

Door ankerrelaties te gebruiken, u verbonden ankers in een ruimte maken en vervolgens vragen als deze stellen:

* Zijn er ankers in de buurt?
* Hoe ver zijn ze weg?

## <a name="examples"></a>Voorbeelden

U verbonden ankers gebruiken in dit soort gevallen:

* Een werknemer moet een taak voltooien waarbij verschillende locaties in een industriële fabriek worden bezocht. De fabriek heeft ruimtelijke ankers op elke locatie. Een HoloLens of mobiele app helpt de werknemer van de ene locatie naar de andere te leiden. De app vraagt eerst naar de ruimtelijke ankers in de buurt en leidt de werknemer vervolgens naar de volgende locatie. De app toont visueel de algemene richting en afstand tot de volgende locatie.

* Een museum creëert ruimtelijke ankers bij openbare tentoonstellingen. Samen vormen deze ankers een rondleiding van een uur door de essentiële openbare tentoonstellingen van het museum. Bij een openbare vertoning kunnen bezoekers de mixed reality-app van het museum openen op hun mobiele apparaat. Dan richten ze hun telefoon camera rond de ruimte om de algemene richting en afstand tot de andere openbare displays op de tour te zien. Als een gebruiker naar een openbaar beeldscherm loopt, werkt de app de algemene richting en afstand bij om de gebruiker te helpen begeleiden.

## <a name="set-up-way-finding"></a>Way-finding instellen

Een app die lijn-van-zicht richting en afstand tussen ankers gebruikt om begeleiding te bieden, maakt gebruik van *way-finding*. Way-finding is anders dan turn-by-turn navigatie. In turn-by-turn navigatie, gebruikers worden begeleid rond muren, door deuren, en tussen verdiepingen. Met bewegwijzering krijgt de gebruiker hints over de algemene richting van de bestemming. Maar gevolgtrekking of kennis van de ruimte helpt de gebruiker ook navigeren door de structuur naar de bestemming.

Om een manier-vinden ervaring op te bouwen, moet je eerst een ruimte voorbereiden voor de ervaring en een app ontwikkelen waarmee gebruikers zullen communiceren. Dit zijn de conceptuele stappen:

1. **Plan de ruimte**: Bepaal welke locaties binnen de ruimte deel uitmaken van de manier-finding ervaring. In onze scenario's kan de fabrieksbegeleider of de museumtourcoördinator beslissen welke locaties in de manier-vindervaring moeten worden opgenomen.
2. **Verankeringen verbinden**: Bezoek de gekozen locaties om ruimtelijke ankers te maken. U dit doen in een beheermodus van de app voor eindgebruikers of in een andere app. Je verbindt of koppelt elk anker aan de anderen. De service onderhoudt deze relaties.
3. **Start de eindgebruikerservaring:** Gebruikers voeren de app uit om een anker te vinden, dat zich op een van de gekozen locaties kan bevinden. Uw algehele ontwerp moet bepalen op welke locaties gebruikers de ervaring kunnen invoeren.
4. **Ankers in de buurt zoeken:** Nadat de gebruiker een anker heeft gevonden, kan de app ankers in de buurt aanvragen. Deze procedure retourneert een pose tussen het apparaat en deze ankers.
5. **Gids van de gebruiker**: De app kan de pose aan elk van deze ankers gebruiken om advies te geven over de algemene richting en afstand van de gebruiker. De camerafeed in de app kan bijvoorbeeld een pictogram en pijl weergeven om elke potentiële bestemming weer te geven, zoals de volgende afbeelding laat zien.
6. **De richtlijnen verfijnen:** terwijl de gebruiker loopt, kan de app periodiek een nieuwe pose berekenen tussen het apparaat en het doelanker. De app blijft de richtlijnen verfijnen die de gebruiker helpen op de bestemming aan te komen.

    ![Een voorbeeld van hoe een app richtlijnen voor het vinden van manieren kan weergeven](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ankers verbinden

Om een manier-vinden ervaring op te bouwen, moet je eerst de ankers in de gekozen locaties plaatsen. In deze sectie gaan we ervan uit dat de beheerder van de app dit werk al heeft voltooid.

### <a name="connect-anchors-in-a-single-session"></a>Ankers aansluiten in één sessie

Ga als contact op met ankers:

1. Loop naar de eerste locatie en maak Anchor A met behulp van een CloudSpatialAnchorSession.
2. Loop naar de tweede locatie. Het onderliggende MR/AR-platform volgt de beweging.
3. Maak Anchor B met dezelfde CloudSpatialAnchorSession. Ankers A en B zijn nu met elkaar verbonden. De dienst Ruimtelijke Ankers onderhoudt deze relatie.
4. Ga door met de procedure voor de resterende ankers.

### <a name="connect-anchors-in-multiple-sessions"></a>Ankers in meerdere sessies verbinden

U ruimtelijke ankers over meerdere sessies verbinden. Met deze methode u sommige ankers tegelijk maken en verbinden en later meer ankers maken en verbinden.

Ga als contact op met ankers over meerdere sessies:

1. De app maakt een aantal ankers in één CloudSpatialAnchorSession.
2. Op een ander moment lokaliseert de app een van deze ankers (bijvoorbeeld Anchor A) met behulp van een nieuwe CloudSpatialAnchorSession.
3. Loop naar een nieuwe locatie. Het onderliggende mixed-reality- of augmented reality-platform volgt de beweging.
4. Maak Anchor C met dezelfde CloudSpatialAnchorSession. Ankers A, B en C zijn nu verbonden. De dienst Ruimtelijke Ankers onderhoudt deze relatie.

U deze procedure voortzetten voor meer ankers en meer sessies in de loop van de tijd.

### <a name="verify-anchor-connections"></a>Ankerverbindingen verifiëren

De app kan controleren of twee ankers zijn verbonden door een query uit te geven voor ankers in de buurt. Wanneer het resultaat van de query het doelanker bevat, wordt de ankerverbinding geverifieerd. Als de ankers niet zijn verbonden, kan de app proberen ze opnieuw met elkaar te verbinden.

Hier volgen enkele redenen waarom ankers mogelijk geen verbinding maken:

* De onderliggende mixed-reality of augmented reality platform verloren tracking tijdens het proces van het aansluiten van ankers.
* Door een netwerkfout tijdens de communicatie met de dienst Ruimtelijke Ankers kon de ankerverbinding niet worden gehandhaafd.

### <a name="find-sample-code"></a>Voorbeeldcode zoeken

Zie [Voorbeeld-apps voor ruimtelijke ankers](https://github.com/Azure/azure-spatial-anchors-samples)voor voorbeelden die laat zien hoe u ankers verbinden en in de buurt query's maken.
