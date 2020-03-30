---
title: Algemene scenario’s Azure Data Catalog
description: Een overzicht van veelvoorkomende scenario's voor Azure Data Catalog, inclusief de registratie en detectie van waardevolle gegevensbronnen, het inschakelen van selfservice business intelligence en het vastleggen van bestaande kennis over gegevensbronnen en -processen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736464"
---
# <a name="azure-data-catalog-common-scenarios"></a>Algemene scenario’s Azure Data Catalog
In dit artikel worden veelvoorkomende scenario's gepresenteerd waarin Azure Data Catalog uw organisatie kan helpen meer waarde te halen uit de bestaande gegevensbronnen.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: Registratie van centrale gegevensbronnen
Organisaties hebben vaak veel waardevolle databronnen. Deze gegevensbronnen omvatten line-of-business, oltp-systemen (online transactieverwerking), gegevensmagazijnen en business intelligence/analytics-databases. Het aantal systemen, en de overlap tussen hen, neemt meestal in de loop van de tijd toe naarmate de bedrijfsbehoeften evolueren en het bedrijf zelf evolueert door bijvoorbeeld fusies en overnames.

Het kan moeilijk zijn voor leden van de organisatie om te weten waar ze de gegevens binnen deze gegevensbronnen kunnen vinden. Vragen als de volgende komen maar al te vaak voor:

* Van de drie HR-systemen die binnen het bedrijf worden gebruikt, welke moet ik gebruiken om dit soort rapport te maken?
* Waar moet ik naartoe om de gecertificeerde verkoopcijfers voor het boekjaar te krijgen dat net is afgelopen?
* Aan wie moet ik vragen of wat is het proces dat ik moet gebruiken om toegang te krijgen tot het datawarehouse?
* Ik weet niet of deze cijfers kloppen. Wie kan ik vragen om inzicht in hoe deze gegevens moeten worden gebruikt voordat ik dit dashboard met mijn team deel?

Voor deze en andere vragen kan Azure Data Catalog antwoorden geven. De centrale, hoogwaardige, DOOR IT beheerde gegevensbronnen die in verschillende organisaties worden gebruikt, zijn vaak het logische uitgangspunt voor het vullen van de catalogus. Hoewel elke gebruiker een gegevensbron kan registreren, helpt het starten van de catalogus met de gegevensbronnen die waarschijnlijk waarde bieden aan het grootste aantal gebruikers, de acceptatie en het gebruik van het systeem te stimuleren. 

Als u aan de slag gaat met Azure Data Catalog, kan het identificeren en registreren van belangrijke gegevensbronnen die door veel verschillende teams van gegevensconsumenten worden gebruikt, uw eerste stap naar succes zijn.

Dit scenario biedt ook een kans om de waardevolle gegevensbronnen te annoteren om ze gemakkelijker te begrijpen en toegankelijk te maken. Een belangrijk aspect van deze inspanning is om informatie op te nemen over hoe gebruikers toegang tot de gegevensbron kunnen vragen. Met Azure Data Catalog u het e-mailadres opgeven van de gebruiker of het team dat verantwoordelijk is voor het beheren van de toegang tot gegevensbronnen, koppelingen naar bestaande hulpprogramma's of documentatie of gratis tekst die het proces voor toegangsverzoek beschrijft. Deze informatie helpt leden die geregistreerde gegevensbronnen ontdekken, maar die nog geen machtigingen hebben om toegang te krijgen tot de gegevens om eenvoudig toegang te vragen met behulp van de processen die worden gedefinieerd en gecontroleerd door de eigenaars van de gegevensbron.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: Self-service business intelligence
Hoewel traditionele business-intelligence-oplossingen een onmisbaar onderdeel blijven van de datalandschappen van veel organisaties, is het veranderende tempo van het bedrijfsleven selfservice BI steeds belangrijker geworden. Door selfservice BI te gebruiken, kunnen informatiewerkers en analisten hun eigen rapporten, werkmappen en dashboards maken zonder afhankelijk te zijn van een centraal IT-team of te worden beperkt door de planning en beschikbaarheid van dat IT-team.

In selfservice BI-scenario's combineren gebruikers vaak gegevens uit meerdere bronnen, waarvan er vele mogelijk niet eerder zijn gebruikt voor BI en analyse. Hoewel sommige van deze gegevensbronnen al bekend zijn, kan het een uitdaging zijn om te ontdekken wat u moet doen om potentiële gegevensbronnen voor een bepaalde taak te lokaliseren en te evalueren.

Traditioneel is dit detectieproces een handmatig proces: analisten gebruiken hun peer-netwerkverbindingen om anderen te identificeren die werken met de gevraagde gegevens. Nadat een gegevensbron is gevonden en gebruikt, herhaalt het proces zich opnieuw voor elke volgende self-service BI-inspanning, waarbij meerdere gebruikers een redundant handmatig detectieproces uitvoeren.

Met Azure Data Catalog kan uw organisatie deze cyclus van inspanning doorbreken. Na het ontdekken van een gegevensbron op traditionele wijze, kan een analist deze registreren om het in de toekomst gemakkelijker te ontdekken door andere gebruikers. Hoewel de analist meer waarde kan toevoegen door de geregistreerde gegevensactiva te annoteren, hoeft deze annotatie niet tegelijkertijd met registratie plaats te vinden. Gebruikers kunnen na verloop van tijd bijdragen, zoals hun schema's toelaten, geleidelijk waarde toevoegen aan de gegevensbronnen die in de catalogus zijn geregistreerd.

Deze organische groei van de catalogusinhoud is een natuurlijke aanvulling op de vooraf registratie van centrale gegevensbronnen. Het vooraf vullen van de catalogus met gegevens die veel gebruikers nodig hebben, kan een motivator zijn voor het eerste gebruik en de ontdekking. Het inschakelen van gebruikers om extra bronnen te registreren en te annoteren kan een manier zijn om hen en andere leden van de organisatie betrokken te houden.

Het is vermeldenswaard dat, hoewel dit scenario zich specifiek richt op self-service BI, dezelfde patronen en uitdagingen ook van toepassing zijn op grootschalige corporate BI-projecten. Door gegevenscatalogus te gebruiken, kan uw organisatie elke inspanning verbeteren die een handmatig proces van gegevensbrondetectie inhoudt.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: Kennis van stammen vastleggen
Hoe weet u welke gegevens u nodig hebt om uw werk te doen en waar u die gegevens vinden?

Als je al een tijdje in je werk bent, weet je het waarschijnlijk gewoon. Je hebt een geleidelijk leerproces doorlopen en in de loop der tijd zijn de gegevensbronnen die essentieel zijn voor je dagelijkse werk, geleerd.

Hoe weet die persoon welke gegevens nodig zijn voor de taak en waar hij deze kan vinden wanneer een nieuwe werknemer zich bij uw team aansluit?

De kans is groot dat de nieuwe persoon naar je toe komt met deze vragen.

Deze voortdurende overdracht van tribale kennis maakt deel uit van het data-source detectieproces in grote en kleine organisaties. Meer senior en ervaren teamleden hebben opgebouwd kennis door de jaren heen, en nieuwere teamleden hebben geleerd om hen te vragen wanneer ze vragen hebben. De meest vitale informatie bestaat vaak alleen in de hoofden van een paar belangrijke mensen, en wanneer die mensen op vakantie zijn of het team verlaten, lijdt de organisatie.

Gegevensexperts doen gewoonlijk hun best om hun kennis te documenteren en te delen via e-mail of in Word-documenten op een SharePoint-site van een team. Hoewel deze aanpak waardevol kan zijn, introduceert het een nieuw ontdekkingsprobleem: hoe weten mensen welke documentatie er bestaat en waar ze deze kunnen vinden?

Met Azure Data Catalog heeft uw organisatie één centrale locatie voor het opslaan en delen van deze tribale kennis en voor het gemakkelijk vindbaar maken ervan. In Data Catalog kunnen uw gegevensexperts gegevensassets rechtstreeks annoteren en koppelingen naar bestaande documentatie leveren. Wanneer organisatieleden de catalogus gebruiken om een gegevensbron te ontdekken, vinden ze niet alleen de bron zelf, maar ook de kennis die voorheen alleen bestond in de hoofden van de experts van uw organisatie.
