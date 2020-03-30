---
title: Gegevensevaluatie
titleSuffix: ML Studio (classic) - Azure
description: Vier criteria waaraan uw gegevens moeten voldoen om klaar te zijn voor data science. Deze video heeft concrete voorbeelden om te helpen bij de basisgegevensevaluatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ccc422dfb3105fd1e12569a84a4ebfd22182b225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837805"
---
# <a name="is-your-data-ready-for-data-science"></a>Zijn gegevens gereed voor gegevenswetenschap?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Data Science for Beginners serie
Lees hoe u uw gegevens evalueren om ervoor te zorgen dat deze voldoen aan basiscriteria om klaar te zijn voor gegevenswetenschap.

Om het meeste uit de serie te halen, bekijk ze allemaal. [Ga naar de lijst met video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere video's in deze serie
*Data Science for Beginners* is een snelle introductie tot data science in vijf korte video's.

* Video 1: [De 5 vragen data science antwoorden](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Video 2: Zijn uw gegevens klaar voor data science?
* Video 3: [Stel een vraag die u beantwoorden met gegevens](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Video 4: [Voorspel een antwoord met een eenvoudig model](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sec)*
* Video 5: [Kopieer het werk van anderen om data science te doen](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcript: Zijn uw gegevens klaar voor data science?
Welkom bij "Zijn uw gegevens klaar voor data science?" de tweede video in de serie *Data Science voor beginners*.  

Voordat data science u de antwoorden kan geven die u wilt, moet u het een aantal hoogwaardige grondstoffen geven om mee te werken. Net als het maken van een pizza, hoe beter de ingrediënten die je begint met, hoe beter het eindproduct. 

## <a name="criteria-for-data"></a>Criteria voor gegevens
In data science zijn er bepaalde ingrediënten die samen getrokken moeten worden, waaronder:

* Relevante
* Verbonden
* Nauwkeurige
* Genoeg om mee te werken

## <a name="is-your-data-relevant"></a>Zijn uw gegevens relevant?
Dus het eerste ingrediënt - je hebt gegevens nodig die relevant zijn.

![Relevante gegevens versus irrelevante gegevens - gegevens evalueren](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Aan de linkerkant, de tafel presenteert het alcoholgehalte in het bloed van zeven mensen getest buiten een Boston bar, de Red Sox slaggemiddelde in hun laatste wedstrijd, en de prijs van melk in de dichtstbijzijnde supermarkt.

Dit zijn allemaal volkomen legitieme gegevens. Het is alleen fout is dat het niet relevant is. Er is geen duidelijk verband tussen deze nummers. Als iemand gaf je de huidige prijs van melk en de Red Sox slaggemiddelde, er is geen manier waarop je kon raden hun alcoholgehalte in het bloed.

Kijk nu naar de tafel aan de rechterkant. Deze keer werd de lichaamsmassa van elke persoon gemeten, evenals het aantal drankjes dat ze hebben gehad.  De getallen in elke rij zijn nu relevant voor elkaar. Als ik je mijn lichaamsmassa en het aantal Margarita's gaf dat ik heb gehad, zou je mijn alcoholgehalte kunnen raden.

## <a name="do-you-have-connected-data"></a>Heeft u verbonden gegevens?
Het volgende ingrediënt is verbonden gegevens.

![Verbonden gegevens versus losgekoppelde gegevens - gegevenscriteria, datagereed](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Hier is een aantal relevante gegevens over de kwaliteit van hamburgers: grill temperatuur, patty gewicht, en rating in de lokale food magazine. Maar let op de gaten in de tabel aan de linkerkant.

De meeste gegevenssets missen bepaalde waarden. Het is gebruikelijk om gaten als deze te hebben en er zijn manieren om om hen heen te werken. Maar als er te veel ontbreekt, beginnen je gegevens op Zwitserse kaas te lijken.

Als je kijkt naar de tabel aan de linkerkant, er is zo veel ontbrekende gegevens, is het moeilijk om te komen met enige vorm van relatie tussen grill temperatuur en patty gewicht. In dit voorbeeld worden losgekoppelde gegevens weergegeven.

De tabel aan de rechterkant is echter volledig en volledig - een voorbeeld van verbonden gegevens.

## <a name="is-your-data-accurate"></a>Zijn uw gegevens juist?
Het volgende ingrediënt is nauwkeurigheid. Hier zijn vier doelen om te raken.

![Nauwkeurige gegevens versus onjuiste gegevens - gegevenscriteria](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Kijk naar het doel rechtsboven. Er is een strakke groepering recht rond de stieren oog. Dat is natuurlijk juist. Vreemd genoeg, in de taal van data science, prestaties op het doel rechts onder het wordt ook beschouwd als nauwkeurig.

Als je het midden van deze pijlen in kaart bracht, zou je zien dat het heel dicht bij het oog van de stieren is. De pijlen zijn verspreid over het doel, dus ze worden beschouwd als onnauwkeurig, maar ze zijn gecentreerd rond de bulls eye, dus ze worden beschouwd als nauwkeurig.

Kijk nu eens naar het doel linksboven. Hier raken de pijlen heel dicht bij elkaar, een strakke groepering. Ze zijn precies, maar ze zijn onjuist omdat het centrum ver van het bulls eye is. De pijlen in het doel linksonder zijn zowel onnauwkeurig als onnauwkeurig. Deze boogschutter heeft meer oefening nodig.

## <a name="do-you-have-enough-data-to-work-with"></a>Heb je genoeg data om mee te werken?
Ten slotte is ingrediënt #4 voldoende gegevens.

![Heeft u genoeg gegevens voor analyse? Gegevensevaluatie](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Denk aan elk gegevenspunt in uw tabel als een penseelstreek in een schilderij. Als u slechts een paar van hen, kan het schilderij fuzzy - het is moeilijk te zeggen wat het is.

Als u wat meer penseelstreken toevoegt, begint uw schilderij een beetje scherper te worden.

Als je nauwelijks genoeg slagen hebt, zie je alleen genoeg om een aantal brede beslissingen te nemen. Is het ergens waar ik misschien wil komen? Het ziet er helder uit, dat ziet eruit als schoon water - ja, dat is waar ik ga op vakantie.

Naarmate u meer gegevens toevoegt, wordt het beeld duidelijker en u meer gedetailleerde beslissingen nemen. Nu u kijken naar de drie hotels op de linkeroever. U de architectonische kenmerken van de ene op de voorgrond. Je zou zelfs kiezen om te verblijven op de derde verdieping vanwege het uitzicht.

Met gegevens die relevant, verbonden, nauwkeurig en genoeg zijn, beschikt u over alle ingrediënten die nodig zijn om gegevenswetenschap van hoge kwaliteit te doen.

Bekijk de andere vier video's in *Data Science voor beginners* van Microsoft Azure Machine Learning Studio (klassiek).

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste data science experiment met Machine Learning Studio (klassiek)](create-experiment.md)
* [Een inleiding tot Machine Learning op Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
