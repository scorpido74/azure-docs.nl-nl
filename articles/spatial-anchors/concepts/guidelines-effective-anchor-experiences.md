---
title: Richtlijnen voor effectieve ankerervaringen
description: Richtlijnen en overwegingen om ankers effectief te maken en te lokaliseren met behulp van Azure Spatial Anchors.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270526"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Een effectieve ankerervaring creëren met Azure Spatial Anchors

Dit artikel bevat richtlijnen en overwegingen om u te helpen bij het effectief maken en lokaliseren van ankers met behulp van ruimtelijke ankers.

## <a name="good-anchors"></a>Goede ankers

Ruimtelijke ankers helpt u bij het creëren van goede ankers. Het is belangrijk om tijd te investeren in het opleiden of begeleiden van gebruikers in uw gebruikerservaring (UX) om goede ankers te creëren. Door te investeren in het creëren van goede ankers aan de voorkant, helpt u eindgebruikers om betrouwbaar ankers te vinden:

- Op verschillende apparaten.
- Op verschillende momenten.
- In verschillende lichtomstandigheden.
- Vanuit de gewenste perspectieven binnen de ruimte.

## <a name="static-and-dynamic-locations"></a>Statische en dynamische locaties

Onderdeel van het ontwerpen van de ankerervaring is het kiezen van de locaties. Worden de locaties statisch en gedefinieerd door een beheerder van de ruimte? Of zullen ze dynamisch zijn en gedefinieerd door de gebruiker?

Een winkelmanager wil misschien een statische winkelervaring om gebruikers te verleiden om te bezoeken. Maar de ontwikkelaar van een mixed-reality bordspel zou waarschijnlijk willen laten gebruikers kiezen waar te spelen.

Voor statische locaties u beheerders leren om tijd te besteden aan het samenstellen van de ruimte met goede ankers.

Voor dynamische locaties moet u nadenken over hoe u gebruikers in uw UX lesgeeft of begeleidt om goede ankers te maken.

## <a name="stable-visual-features"></a>Stabiele visuele kenmerken

Visuele trackingsystemen die worden gebruikt op mixed-reality- en augmented reality-apparaten vertrouwen op visuele kenmerken van de omgeving. Om de meest betrouwbare ervaring te krijgen:

- *Maak* wel ankers op locaties met stabiele visuele functies (dat wil zeggen functies die niet vaak veranderen).

- *Maak geen* ankers op grote lege oppervlakken die geen onderscheidende kenmerken hebben.

- *Maak geen* ankers op sterk reflecterende materialen.

- *Maak geen* ankers op oppervlakken waar het patroon zich herhaalt, zoals tapijt of behang.

![Voorbeelden van een goede omgeving voor ankers en een slechte omgeving voor ankers](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Verschillende kijkperspectieven

Bij het maken van een anker, na te denken over de mensen die later zal proberen om het anker te lokaliseren.

Denk bijvoorbeeld aan een anker in het midden van een kamer met twee deuren. U wilt waarschijnlijk gebruikers toestaan om de kamer binnen te komen vanuit beide deuren. Bij het maken van het anker moet u de positie van beide deuren scannen. U wijzigt perspectieven om omgevingsgegevens rond het anker vast te leggen, zodat gebruikers het anker vanuit beide deuren kunnen vinden.

In het algemeen, bij het maken van een anker, scannen vanuit het perspectief van de mensen die zullen proberen om het te lokaliseren. Dus als je virtuele inhoud op een buitensculptuur plaatst, is het zinvol om rond de sculptuur te lopen terwijl je het scant, terwijl je het anker maakt. Als uw anker zich in de hoek van een kamer bevindt, is er maar één richting om het te benaderen. Bij het maken van dit anker u het alleen vanuit dit perspectief scannen.

## <a name="multiple-anchors"></a>Meerdere ankers

Verlichting kan een verschil maken in de visuele functies die een app detecteert. Ankers gemaakt in sterk natuurlijk licht kan moeilijk te vinden in kunstlicht, en vice versa.

Als u dit probleem hebt, kan het helpen om twee ankers te maken. Maak op dezelfde plek het ene anker in het daglicht en het andere in kunstlicht. Uw app kan vervolgens vragen stellen voor beide ankers. Wanneer een van beide ankers zich bevindt, heeft de app een pose voor het anker.

Ook in omgevingen waar de visuele functies veranderen omdat de meeste objecten bewegen, kunnen meerdere ankers helpen. Wanneer een anker te moeilijk te vinden is vanwege belangrijke veranderingen in de omgeving, u het anker vervangen door een nieuw anker. U dit bijvoorbeeld doen in een winkel waar de lay-out om de paar maanden wordt vernieuwd.

## <a name="targets-and-rooms"></a>Doelen en kamers

In veel gevallen is een anker een toegangspunt tot de ervaring van uw app. U wilt deze stap snel en betrouwbaar doorlopen, zodat gebruikers uw ervaring kunnen invoeren. Tijd besteden aan hoe gebruikers uw ankers vinden is een belangrijke ontwerpstap. Het is nuttig om na te denken over het vinden van ankers in termen van twee brede scenario's: *doelen* en *kamers.*

### <a name="targets"></a>Doelen

In het doelscenario is de locatie van een anker bekend. In een fictieve mixed-reality-app plaatst één gebruiker bijvoorbeeld een virtueel canvas aan de muur. Ze instrueert de andere gebruikers in de kamer om hun apparaten op dezelfde plaats op de muur te wijzen om het anker te lokaliseren en de ervaring te beginnen.

Een ander voorbeeld van een doelscenario kan een teken in een coffeeshop zijn met de tekst 'Scannen naar deals'. De coffeeshop heeft hier een anker geplaatst. Als gebruikers het bord scannen, lokaliseren ze het anker en gaan ze de augmented reality-ervaring in om aanbiedingen op koffie te vinden.

In het doelscenario kunnen foto's helpen. Als u gebruikers een foto van het beoogde doel op hun apparaat laat zien, kunnen ze snel identificeren wat ze in de echte wereld moeten scannen. U uw gebruikers bijvoorbeeld helpen binnen het algemene gebied van een bedoeld doel te komen met behulp van GPS. Wanneer de gebruiker arriveert, toont uw app een foto van het doel. De gebruiker kijkt rond de ruimte, vindt het doel en scant naar het anker.

![Illustratie van een anker, dat een foto van het doel op het mobiele apparaat van een gebruiker toont](./media/start-here-edit.png)

### <a name="rooms"></a>Kamers

In het kamerscenario gaan gebruikers een ruimte in omdat ze weten dat er ergens een anker is. Gebruikers scannen de ruimte met hun apparaat en vinden snel het anker.

Deze ervaring vereist meestal dat u goed samengestelde ankers maakt, zoals besproken in verschillende kijkperspectieven. Als u de ruimte vanuit vele perspectieven hebt gescand toen u het anker hebt gemaakt, kunnen gebruikers bijna overal scannen wanneer ze het proberen te vinden.

![Illustratie van hoe een gebruiker een ruimte kan scannen om een anker te vinden](./media/scan-room.png)

In wezen besteedt u meer tijd aan het scannen van de ruimte wanneer u het anker maakt, zodat latere gebruikers het anker snel kunnen scannen en lokaliseren. Terwijl u uw ervaring creëert, moet u rekening houden met deze belangrijke afweging.

Het voorbeeld van de mixed-reality schilderij app die we eerder besproken werkt niet goed als een kamer scenario. Hier wil de gebruiker die het anker plaatst dat anderen snel deelnemen aan de ervaring. De gebruikers willen niet wachten om de ervaring te starten totdat de kamer goed is gescand. Omdat alle gebruikers precies weten waar ze hun apparaat moeten richten om de ankers te vinden, werkt dit voorbeeld beter als doelscenario.

## <a name="anchor-location"></a>Ankerlocatie

Visuele trackingsystemen vertrouwen op de visuele functies in een omgeving. Hoe meer visuele functies een scan bevat, hoe groter de kans op het vinden van een anker.

Volg de algemene richtlijnen in deze sectie om een UX te bouwen die een nuttige scan van de omgeving stimuleert.

Ten eerste, als de gebruiker niet binnen een paar seconden een anker vindt, moet de app gebruikers aanmoedigen om het apparaat te verplaatsen om meer perspectieven vast te leggen. De app kan gebruikers ook aanmoedigen om zich in de omgeving te verplaatsen om vanuit meer perspectieven naar het anker te scannen. Hoe meer functieperspectieven het apparaat ziet, hoe beter.

Voor doelscenario's vraagt u de gebruiker om het doel te verplaatsen om het doel vanuit verschillende perspectieven te bekijken. Met andere woorden, vraag de gebruiker om het doel vast te leggen vanuit nieuwe perspectieven totdat het anker is gevestigd.

Voor kamerscenario's vraagt u de gebruiker om de ruimte langzaam te scannen. Vraag de gebruiker bijvoorbeeld om 180 graden of zelfs 360 graden van de kamer vast te leggen. Of vraag de gebruiker om de kamer vanuit een nieuw perspectief te bekijken.

De meest zinvolle methode is om te scannen in de kamer. Een scan in de kamer legt meer visuele kenmerken van de omgeving vast dan bijvoorbeeld een scan van een nabijgelegen muur. Een scan van een nabijgelegen muur zal niet zo veel nuttige visuele kenmerken van de omgeving vast te leggen.

Het is niet handig om het apparaat herhaaldelijk van links naar rechts te verplaatsen wanneer u op zoek bent naar een anker. Dit legt gewoon dezelfde punten vanuit hetzelfde perspectief.

## <a name="experience-tests"></a>Ervaringstests

In dit artikel hebben we algemene richtlijnen besproken. Met Ruimtelijke ankers schrijf je apps die communiceren met de echte wereld. Daarom moet u tijd besteden aan het testen van de ankerscenario's van uw app in echte omgevingen. Dit geldt vooral voor omgevingen die vertegenwoordigen waar u verwacht dat uw gebruikers de app gebruiken.
