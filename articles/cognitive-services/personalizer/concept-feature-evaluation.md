---
title: Functie-evaluatie - Personalizer
titleSuffix: Azure Cognitive Services
description: Wanneer u een evaluatie uitvoert in uw Personalizer-bron vanuit de Azure-portal, biedt Personalizer informatie over welke functies van context en acties het model beïnvloeden.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242419"
---
# <a name="feature-evaluation"></a>Functie-evaluatie

Wanneer u een evaluatie uitvoert in uw Personalizer-bron vanuit de [Azure-portal,](https://portal.azure.com)biedt Personalizer informatie over welke functies van context en acties het model beïnvloeden. 

Dit is handig om:

* Stel je extra functies voor die je zou kunnen gebruiken, om inspiratie op te doen uit welke functies belangrijker zijn in het model.
* Bekijk welke functies niet belangrijk zijn en verwijder ze mogelijk of analyseer mogelijk verder wat het gebruik kan beïnvloeden.
* Geef advies aan redactie- of curatieteams over nieuwe inhoud of producten die het waard zijn om in de catalogus te worden opgenomen.
* Veelvoorkomende problemen en fouten oplossen die zich voordoen bij het verzenden van functies naar Personalizer.

De belangrijkste kenmerken hebben sterkere gewichten in het model. Omdat deze functies een sterker gewicht hebben, zijn ze meestal aanwezig wanneer Personalizer hogere beloningen behaalt.

## <a name="getting-feature-importance-evaluation"></a>Evaluatie van functiebelang

Als u de resultaten van het functiebelang wilt bekijken, moet u een evaluatie uitvoeren. De evaluatie maakt door de mens leesbare functielabels op basis van de functienamen die tijdens de evaluatieperiode zijn waargenomen.

De resulterende informatie over functiebelang vertegenwoordigt het huidige Personalizer online model. De evaluatie analyseert functie belang van het model opgeslagen op de einddatum van de evaluatieperiode, na het ondergaan van alle training gedaan tijdens de evaluatie, met het huidige online leerbeleid. 

De resultaten van het functiebelang vertegenwoordigen geen ander beleid en modellen die tijdens de evaluatie zijn getest of gemaakt.  De evaluatie bevat geen functies die na het einde van de evaluatieperiode naar Personalizer zijn verzonden.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Evaluatie van het functiebelang interpreteren

Personalizer evalueert functies door het creëren van "groepen" van functies die vergelijkbaar belang hebben. Men kan zeggen dat de ene groep over het algemeen belangrijker is dan andere, maar binnen de groep is het ordenen van functies alfabetisch.

Informatie over elke functie bevat:

* Of de functie afkomstig is van Context of Acties.
* Functiesleutel en -waarde.

Een app voor het bestellen van een ijssalon kan bijvoorbeeld 'Context.Weer:Warm' als een zeer belangrijke functie zien.

Personalizer geeft correlaties weer van functies die, wanneer ze samen in aanmerking worden genomen, hogere beloningen opleveren.

U bijvoorbeeld 'Context.Weer:Warm *met* Action.MenuItem:IceCream' en 'Context.Weer:Koud *met* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Acties die u uitvoeren op basis van functie-evaluatie

### <a name="imagine-additional-features-you-could-use"></a>Stel je extra functies voor die je zou kunnen gebruiken

Laat u inspireren door de belangrijkste functies in het model. Als u bijvoorbeeld 'Context.MobileBattery:Low' ziet in een mobiele video-app, denkt u dat het type verbinding ervoor kan kiezen dat klanten er ook voor kiezen om de ene videoclip boven de andere te zien en vervolgens functies over het type connectiviteit en bandbreedte toe te voegen aan uw app.

### <a name="see-what-features-are-not-important"></a>Bekijk welke functies niet belangrijk zijn

Verwijder mogelijk onbelangrijke functies of analyseer verder wat het gebruik kan beïnvloeden. Functies kunnen laag scoren om vele redenen. Men zou kunnen zijn dat echt de functie geen invloed heeft op het gedrag van gebruikers. Maar het kan ook betekenen dat de functie niet duidelijk is voor de gebruiker. 

Een videosite kan bijvoorbeeld zien dat "Action.VideoResolution=4k" een functie van weinig belang is, die het onderzoek van gebruikers tegenspreekt. De oorzaak kan zijn dat de toepassing niet eens de videoresolutie vermeldt of weergeeft, zodat gebruikers hun gedrag niet zouden veranderen op basis van deze resolutie.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Leiding geven aan redactie- of curatieteams

Geef richtlijnen over nieuwe inhoud of producten die het waard zijn om in de catalogus te brengen. Personalizer is ontworpen om een hulpmiddel te zijn dat het menselijk inzicht en teams vergroot. Een manier om dit te doen is door het verstrekken van informatie aan redactionele groepen over wat is het over producten, artikelen of inhoud die gedrag drijft. Het scenario voor videotoepassingen kan bijvoorbeeld aantonen dat er een belangrijke functie is genaamd 'Action.VideoEntities.Cat:true', waardoor de redactie wordt gevraagd om meer kattenvideo's in te voeren.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Veelvoorkomende problemen en fouten oplossen

Veelvoorkomende problemen en fouten kunnen worden opgelost door uw toepassingscode te wijzigen, zodat deze geen ongepaste of onjuist opgemaakte functies naar Personalizer stuurt. 

Veelvoorkomende fouten bij het verzenden van functies zijn onder andere:

* Het verzenden van persoonlijk identificeerbare informatie (PII). PII die specifiek is voor één persoon (zoals naam, telefoonnummer, creditcardnummers, IP-adressen) mag niet worden gebruikt met Personalizer. Als uw toepassing gebruikers moet volgen, gebruikt u een niet-identificerende UUID of een ander gebruikersnaamnummer. In de meeste scenario's is dit ook problematisch.
* Bij grote aantallen gebruikers is het onwaarschijnlijk dat de interactie van elke gebruiker meer weegt dan alle interactie van de bevolking, dus het verzenden van gebruikers-id's (zelfs als niet-PII) zal waarschijnlijk meer ruis dan waarde toevoegen aan het model.
* Datumtijdvelden verzenden als nauwkeurige tijdstempels in plaats van prestatiegerichte tijdwaarden. Functies zoals Context.TimeStamp.Day=Monday of "Context.TimeStamp.Hour"="13" is handiger. Er zullen hoogstens 7 of 24 eigenschapwaarden voor elk zijn. Maar "Context.TimeStamp":"1985-04-12T23:20:50.52Z" is zo nauwkeurig dat er geen manier zal zijn om ervan te leren omdat het nooit meer zal gebeuren.

## <a name="next-steps"></a>Volgende stappen

Begrijp [schaalbaarheid en prestaties](concepts-scalability-performance.md) met Personalizer.

