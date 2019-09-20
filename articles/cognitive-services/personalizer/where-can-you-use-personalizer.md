---
title: Scenario beoordeling-persoonlijker
titleSuffix: Azure Cognitive Services
description: Personaler kan in elke situatie worden toegepast, waarbij uw toepassing het juiste item, de actie of het product kan selecteren om de ervaring te verbeteren, betere bedrijfs resultaten te leveren of de productiviteit te verbeteren.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: diberry
ms.openlocfilehash: 246e76a0ab94624945723b500ef136e038ab40ec
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155232"
---
# <a name="where-can-you-use-personalizer"></a>Waar kunt u Personalizer gebruiken?

Gebruik Personaler in elke situatie waarbij uw toepassing het juiste item, de actie of het product moet selecteren dat moet worden weer gegeven om de ervaring beter te maken, betere bedrijfs resultaten te behaalt of de productiviteit te verbeteren. 

Personaler gebruikt machine learning om te selecteren welke actie u wilt weer geven voor de gebruiker. De selectie kan aanzienlijk variëren, afhankelijk van de hoeveelheid, de kwaliteit en de distributie van de gegevens die naar de service worden verzonden.

### <a name="checklist-for-applying-personalizer"></a>Controle lijst voor het Toep assen van persoonlijker


U kunt persoonlijker Toep assen in situaties waarin:

* U hebt een zakelijk of bruikbaar doel voor uw toepassing.
* U hebt een plaats in uw toepassing waar u een contextuele beslissing neemt om het doel van gebruikers te verbeteren.
* De beste keuze kan en moeten worden geleerd van het gedrag van de collectieve gebruiker en de totale belonings Score.
* Het gebruik van machine learning voor personalisatie is gebaseerd op de [verantwoordelijke richt lijnen](ethics-responsible-use.md) en keuzes die u hebt gekozen.
* De contextuele beslissing kan worden uitgedrukt als een classificatie van de beste optie (actie) van een beperkt aantal keuzes.
* Hoe goed de geclassificeerde keuze voor uw toepassing kan worden bepaald door een aspect van het gedrag van de gebruiker te meten en deze in een _[belonings Score](concept-rewards.md)_ af te drukken.
* De belonings Score brengt niet te veel congevonden of externe factoren in rekening. De duur van het experiment is laag genoeg om de belonings score te berekenen, terwijl deze nog steeds relevant is.
* U kunt de context voor de rang orde als een lijst van ten minste vijf [functies](concepts-features.md) die u denkt wilt gebruiken om de juiste keuze te maken en die geen persoons gegevens bevatten. (PII).
* U hebt informatie over elke inhouds keuze, _actie_, als een lijst met ten minste vijf [functies](concepts-features.md) die u kunt gebruiken om de juiste keuze te maken.
* Uw toepassing kan gegevens lang genoeg bewaren om een geschiedenis van ten minste 100.000 interacties te verzamelen.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Overwegingen voor machine learning voor het Toep assen van persoonlijker

Personaler is gebaseerd op educatief leren, een aanpak van machine learning die wordt geduurd door feedback die u geeft. 

Personaler leert het beste in situaties waarin:

* Er zijn voldoende gebeurtenissen om optimaal te kunnen voldoen aan de optimale persoonlijke instellingen als het probleem zich in de loop van de tijd (zoals voor keuren in nieuws of mode) bevindt. Personaler wordt aangepast aan doorlopende wijzigingen in de echte wereld, maar de resultaten zijn niet optimaal als er niet voldoende gebeurtenissen en gegevens zijn om te leren van het detecteren en afrekenen van nieuwe patronen. U moet een use-case kiezen die vaak voldoende optreedt. Overweeg voor het zoeken naar use cases die ten minste 500 keer per dag plaatsvinden.
* Context en acties hebben voldoende [functies](concepts-features.md) om leren te vergemakkelijken.
* Er zijn minder dan 50 acties om per oproep te rangschikken.
* Met de instellingen voor het bewaren van gegevens kan Personaler voldoende gegevens verzamelen om offline-evaluaties en optimalisatie van het beleid uit te voeren. Dit is meestal ten minste 50.000 gegevens punten.

## <a name="monitor-effectiveness-of-personalizer"></a>De effectiviteit van een persoonlijker bewaken

U kunt de effectiviteit van personalisatie regel matig controleren door [offline-evaluaties](concepts-offline-evaluation.md)uit te voeren.

## <a name="use-personalizer-with-recommendation-engines"></a>Personaler gebruiken met aanbevolen engines

Veel bedrijven gebruiken aanbevolen engines, marketing-en campagne hulpprogramma's, segmentering van een doel groep, clustering en het samen stellen van gezamenlijke filters, en andere middelen om producten van een grote catalogus aan klanten te aanbevelen.

De [github-opslag plaats van micro soft adviseert](https://github.com/Microsoft/Recommenders) voor beelden en aanbevolen procedures voor het bouwen van aanbevelings systemen, als Jupyter-notebooks. Hierin worden voor beelden gegeven van het voorbereiden van gegevens, het bouwen van modellen, het evalueren, afstemmen en inzet van de aanbevolen engines, voor veel gang bare benaderingen zoals xDeepFM, SAR, als, RBM'S, DKN.

Personaler kan samen werken met een aanbevelings engine wanneer deze aanwezig is.

* Aanbevelings engines nemen grote hoeveel heden items (bijvoorbeeld 500.000) en raden een subset (zoals de top 20) van honderden of duizenden opties aan.
* Personaler neemt een klein aantal acties met veel informatie over hen en rangschikt deze in realtime voor een bepaalde uitgebreide context, terwijl de meeste aanbevolen engines slechts enkele kenmerken van gebruikers, producten en hun interacties gebruiken.
* Personaler is ontworpen voor het autonoom verkennen van gebruikers voorkeuren, waardoor er betere resultaten worden verkregen waarbij inhoud snel wordt gewijzigd, zoals nieuws, live evenementen, inhoud van Live Community, inhoud met dagelijkse updates of seizoen inhoud.

Een veelgebruikte toepassing is het uitvoeren van de uitvoer van een aanbevelings Engine (bijvoorbeeld de top 20 van producten voor een bepaalde klant) en die als invoer acties voor persoonlijk gebruik.

## <a name="next-steps"></a>Volgende stappen

[Ethische & verantwoordelijk gebruik](ethics-responsible-use.md).