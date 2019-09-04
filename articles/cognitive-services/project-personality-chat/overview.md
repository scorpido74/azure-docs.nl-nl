---
title: Wat is Project Personality Chat?
titlesuffix: Azure Cognitive Services
description: Dit artikel geeft een overzicht van Azure Project Personality Chat, een op de cloud gebaseerde API voor het verbeteren van de gespreksmogelijkheden van uw bot.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nitinme
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: 101f87230ee64e7f281f337189e18d09d93abed3
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257783"
---
# <a name="what-is-project-personality-chat"></a>Wat is Project Personality Chat?

Project Personality Chat verbetert de gespreksvaardigheden van uw bot door een eenvoudig gesprek te voeren in overeenstemming met een specifiek gekozen persoonlijkheid. Personality Chat maakt gebruik van intentieclassificaties om veelvoorkomende intenties in eenvoudige gesprekken te identificeren en antwoorden te genereren die consistent zijn met een bepaalde persoonlijkheid. Op basis van de intentie en de betrouwbaarheidsscores kiest de bot het beste antwoord uit een gecureerde termenbank of worden er in realtime antwoorden gegenereerd met behulp van diepe neurale netwerken (DNN's). U kunt kiezen uit drie standaardpersoonlijkheden (persona). Het personamodel retourneert antwoorden die het meest overeenkomen met de gekozen persoonlijkheid.

Er is een aanpasbare set met termen beschikbaar voor gebruik in eenvoudige gesprekken. Deze set kan gemakkelijk worden geïntegreerd met behulp van de Microsoft Bot Framework SDK. Met deze SDK bespaart u de tijd en kosten die u anders kwijt bent aan het helemaal zelf samenstellen van een termenbank.

## <a name="getting-started-with-project-personality-chat"></a>Aan de slag met Project Personality Chat

U kunt naar de pagina met praktijklabs van Project Personality Chat gaan en chatten met de beschikbare demo. U kunt daar ook een aanvraag voor vroege toegang indienen wanneer de service beschikbaar is.
Op dit moment kunt u de aanpasbare bibliotheek met alleen gecureerde inhoud ook integreren in uw bots via de Microsoft Bot Framework SDK. <br>
[Voorbeelden: Personality Chat integreren in een bot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Bibliotheek van Personality Chat uitproberen](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Antwoorden genereren met behulp van neurale netwerken

Personality Chat maakt gebruik van deep learning-modellen om algemene gesprekspatronen te leren en geschikte antwoorden te genereren. Het model voor het genereren van antwoorden is een RNN, wat staat voor Recurrent Neural Network, ook wel terugkerend neuraal netwerk genoemd. Dit model wordt getraind door middel van miljoenen gesprekken, waarbij het model patronen van menselijke interactie begrijpt en leert. Aan de hand van de geleerde patronen van zinsopbouw worden nieuwe query's gevormd en antwoorden gegenereerd. Bij het genereren van het nieuwe antwoord doorzoekt het model een 'woordenlijst voor schrijven" van woorden en worden de n-beste woorden geselecteerd voor het antwoord. Met behulp van beam-zoeken wordt er doorgezocht naar de n-beste tweede woorden voor elk van de gegenereerde eerste woorden. Een antwoord wordt als volledig beschouwd wanneer het model het zogenaamde EOS-woord kiest, wat staat voor 'End Of Sentence' ofwel einde van zin. Als op deze manier alle antwoorden zijn verzameld, worden de n-beste antwoorden gekozen op basis van de waarschijnlijkheidsscore voor het volledige antwoord.

Het model leert om contextueel juiste antwoorden te genereren die de juiste 'structuur' hebben. Een vraag zoals “Do you want to talk now?” geeft een goede indicatie van de structuur van een geschikt antwoord: dit zal waarschijnlijk beginnen met een variant van "yes" of "nee" en het voornaamwoord is waarschijnlijk 'I'. Een antwoord met 'nee' zal eerder een beleefde toelichting bevatten, enzovoort.

Het systeem probeert een taalmodel te leren voor de basisstructuur van een gesprek. Deze structuur moet rekening houden met het gegeven dat antwoorden gedeeltelijk worden beïnvloed door externe beperkingen zoals onderwerp en persoonlijkheid.  Aangezien deze beperkingen worden geleerd op basis van brede patronen, zijn ze geschikt voor (maar niet beperkt tot) toepassingen voor eenvoudige gesprekken.

![Sequentie-sequentiemodel voor het genereren van een antwoord](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Persoonlijkheid modelleren

 Bij ieder gegevensgestuurd gespreksmodel bestaat de uitdaging uit het consistent presenteren van een coherente 'persoonlijkheid' of 'persona'. PERSONA wordt gedefinieerd als het karakter dat wordt ervaren tijdens gespreksinteracties. Een persona kan worden gezien als opgebouwd uit elementen van identiteit, taalgedrag en stijl van interactie. In de huidige versie van Personality Chat ligt de nadruk op taalgedrag en stijl van interactie.

Dit model vertegenwoordigt elke afzonderlijke spreker als een vector of embedding. Het codeert informatie van de spreker die van invloed is op de inhoud en stijl van zijn of haar antwoorden. Het model leert vervolgens sprekervoorstellingen op basis van gespreksinhoud afkomstig van verschillende sprekers. De sprekers met soortgelijke antwoorden hebben meestal vergelijkbare embeddings, en bezetten bij elkaar gelegen posities in de vectorruimte. Op deze manier dragen de trainingsgegevens van bij elkaar gelegen sprekers in de vectorruimte bij aan het verbeteren van de mogelijkheid van het sprekermodel om antwoorden te genereren. Het model clustert sprekers die soortgelijke voorstellingen hebben in de vectorruimte om zo een persona-cluster te vormen, met een 'persona-id'.

In het geval van de standaardpersona worden attributen en gecureerde antwoorden gebruikt om het best overeenkomende cluster met sprekers te vinden. Dit cluster wordt vervolgens gekozen als de persona-id voor elk van de standaardpersonaliteiten. Verdere aanpassing is mogelijk voor elk type persoonlijkheid aan de hand van een set bot-/merkantwoorden. Er worden vervolgens gesprekken tot stand gebracht die nauwkeurig de persona van dat individu nabootsen, bijvoorbeeld door taalkundige reacties en andere belangrijke kenmerken.

![Persona gemodelleerd met behulp van sprekerclusters](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Intenties in eenvoudige gesprekken begrijpen

Personality Chat gebruikt intentieclassificaties om een reactie te geven op intenties in eenvoudige gesprekken. Deze classificaties vormen geen belemmering voor taken of informatiequery's. Een algemene chatclassificatie bepaalt of de query de intentie heeft van een eenvoudig gesprek of een uitwisseling van tekst op een nog lager niveau. Dit gebeurt aan de hand van lexicale en semantische classificaties en het combineren van hun scores. Deze intenties worden getraind met behulp van gespreksgegevens (als voorbeelden van positieve intentie) en zoek-/taakquery's (als voorbeelden van negatieve intentie).

Andere classificaties van subintenties worden gebruikt voor de identificatie van de subklassen van eenvoudige gesprekken om de typen eenvoudige gesprekken te beperken waarop de service reageert. Denk hierbij aan begroetingen, complimenten en meningen. Deze deep learning-classificaties converteren met behulp van CDSSM (Convolutional Deep Structure Semantic Model) alle query's naar vectoren. Ze worden getraind met tienduizenden gecureerde query's voor de subintenties. De classificatie koppelt vervolgens een query aan bestaande, geïdentificeerde intentieklassen door de beste match in de vectorruimte te vinden.

Er zijn verschillende controles ingebouwd om ongunstig antwoorden te voorkomen dat. Hiervoor wordt voortgeborduurd op kennis van intelligente agents zoals Zo. De standaardinstelling is dat Personality Chat alleen reageert op herkende intenties van gebruikers. U kunt uitproberen of Project Personality Chat geschikt is voor uw situatie. Uw feedback wordt zeer op prijs gesteld als u van mening bent dat ergens meer training vereist is.
