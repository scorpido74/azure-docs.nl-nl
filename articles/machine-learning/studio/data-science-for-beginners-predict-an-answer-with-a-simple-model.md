---
title: Antwoorden voorspellen met regressiemodellen
titleSuffix: ML Studio (classic) - Azure
description: Hoe maak je een eenvoudig regressiemodel om een prijs te voorspellen in Data Science for Beginners video 4. Bevat een lineaire regressie met doelgegevens.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 6ad9c7912eee6c3f5ec55b9cd7ab340bc79c9db7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837775"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Met een eenvoudig model een antwoord voorspellen
## <a name="video-4-data-science-for-beginners-series"></a>Video 4: Data Science for Beginners serie
Leer hoe je een eenvoudig regressiemodel maakt om de prijs van een diamant te voorspellen in Data Science for Beginners video 4. We tekenen een regressiemodel met doelgegevens.

Om het meeste uit de serie te halen, bekijk ze allemaal. [Ga naar de lijst met video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere video's in deze serie
*Data Science for Beginners* is een snelle introductie tot data science in vijf korte video's.

* Video 1: [De 5 vragen data science antwoorden](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Video 2: [Zijn uw gegevens klaar voor data science?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sec)*
* Video 3: [Stel een vraag die u beantwoorden met gegevens](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Video 4: Een antwoord voorspellen met een eenvoudig model
* Video 5: [Kopieer het werk van anderen om data science te doen](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Transcript: Voorspel een antwoord met een eenvoudig model
Welkom bij de vierde video in de "Data Science for Beginners" serie. In deze bouwen we een eenvoudig model en maken we een voorspelling.

Een *model* is een vereenvoudigd verhaal over onze gegevens. Ik zal je laten zien wat ik bedoel.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Relevante, nauwkeurige, verbonden, voldoende gegevens verzamelen
Stel dat ik een diamant wil kopen. Ik heb een ring die behoorde tot mijn grootmoeder met een instelling voor een 1,35 karaat diamant, en ik wil een idee krijgen van hoeveel het zal kosten. Ik neem een kladblok en pen in de juwelierszaak, en ik schrijf de prijs van alle diamanten in de zaak en hoeveel ze wegen in karaat. Te beginnen met de eerste diamant - het is 1,01 karaat en 7.366 dollar.

Nu ga ik door en doe dit voor alle andere diamanten in de winkel.

![Kolommen van diamantgegevens](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Merk op dat onze lijst twee kolommen heeft. Elke kolom heeft een ander attribuut - gewicht in karaat en prijs - en elke rij is een enkel gegevenspunt dat een enkele diamant vertegenwoordigt.

We hebben hier een kleine dataset gemaakt, een tabel. Merk op dat het voldoet aan onze criteria voor kwaliteit:

* De gegevens zijn **relevant** - gewicht is zeker gerelateerd aan de prijs
* Het is **juist** - we dubbel gecontroleerd de prijzen die we opschrijven
* Het is **verbonden** - er zijn geen lege spaties in een van deze kolommen
* En, zoals we zullen zien, het is **genoeg** gegevens om onze vraag te beantwoorden

## <a name="ask-a-sharp-question"></a>Stel een scherpe vraag
Nu stellen we onze vraag op een scherpe manier: "Hoeveel kost het om een 1,35 karaats diamant te kopen?"

Onze lijst heeft geen 1,35 karaats diamant erin, dus we zullen de rest van onze gegevens moeten gebruiken om een antwoord op de vraag te krijgen.

## <a name="plot-the-existing-data"></a>De bestaande gegevens in kaart brengen
Het eerste wat we doen is een horizontale nummerlijn tekenen, een as genoemd, om de gewichten in kaart te brengen. Het bereik van de gewichten is 0 tot 2, dus we trekken een lijn die dat bereik dekt en zet teken voor elke halve karaat.

Vervolgens tekenen we een verticale as om de prijs vast te leggen en deze aan te sluiten op de horizontale gewichtsas. Dit zal in eenheden van dollars zijn. Nu hebben we een reeks coördinatenassen.

![Gewichts- en prijsassen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

We nemen deze gegevens nu en veranderen het in een *verstrooiingsplot.* Dit is een geweldige manier om numerieke gegevenssets te visualiseren.

Voor het eerste gegevenspunt kijken we naar een verticale lijn bij 1,01 karaat. Dan, we eyeball een horizontale lijn op 7.366 dollar. Waar ze elkaar ontmoeten, trekken we een stip. Dit vertegenwoordigt onze eerste diamant.

Nu gaan we door elke diamant op deze lijst en doen hetzelfde. Als we klaar zijn, is dit wat we krijgen: een heleboel stippen, een voor elke diamant.

![Spreidingsplot](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Het model door de gegevenspunten tekenen
Als je naar de stippen en scheel kijkt, ziet de collectie eruit als een dikke, vage lijn. We kunnen onze marker nemen en er een rechte lijn doorheen trekken.

Door een lijn te tekenen, hebben we een *model*gemaakt. Denk aan dit als het nemen van de echte wereld en het maken van een simplistische cartoon versie van. Nu is de cartoon is verkeerd - de lijn gaat niet door alle gegevens punten. Maar, het is een nuttige vereenvoudiging.

![Lineaire regressielijn](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Het feit dat alle stippen niet precies door de lijn gaan is OK. Data wetenschappers verklaren dit door te zeggen dat er het model - dat is de lijn - en dan elke stip heeft wat *ruis* of *variantie* in verband met het. Er is de onderliggende perfecte relatie, en dan is er de zanderige, echte wereld die ruis en onzekerheid toevoegt.

Omdat we proberen om de vraag te beantwoorden *regression* *Hoeveel?* En omdat we een rechte lijn gebruiken, is het een *lineaire regressie.*

## <a name="use-the-model-to-find-the-answer"></a>Het model gebruiken om het antwoord te vinden
Nu hebben we een model en we stellen het onze vraag: Hoeveel kost een 1,35 karaatdiamant?

Om onze vraag te beantwoorden, we eyeball 1,35 karaat en trekken een verticale lijn. Waar het de modellijn overschrijdt, kijken we naar een horizontale lijn naar de dollaras. Het komt uit op 10.000. Boem! Dat is het antwoord: Een 1,35 karaat diamant kost ongeveer $ 10.000.

![Vind het antwoord op het model](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Een betrouwbaarheidsinterval maken
Het is natuurlijk om je af te vragen hoe nauwkeurig deze voorspelling is. Het is handig om te weten of de 1,35 karaat diamant zal zeer dicht bij $ 10.000, of een stuk hoger of lager. Om dit uit te zoeken, laten we een envelop rond de regressielijn tekenen die de meeste punten bevat. Deze envelop heet ons *vertrouwen interval*: We zijn er vrij zeker van dat de prijzen vallen binnen deze envelop, omdat in het verleden de meeste van hen hebben. We kunnen nog twee horizontale lijnen tekenen van waaruit de 1,35 karaatlijn de bovenkant en de onderkant van die envelop kruist.

![Betrouwbaarheidsinterval](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kunnen we iets zeggen over ons betrouwbaarheidsinterval: We kunnen vol vertrouwen zeggen dat de prijs van een 1,35 karaats diamant ongeveer $ 10.000 is - maar het kan zo laag zijn als $ 8.000 en het kan zo hoog zijn als $ 12.000.

## <a name="were-done-with-no-math-or-computers"></a>We zijn klaar, zonder wiskunde of computers.
We deden waar data scientists voor betaald worden, en we deden het gewoon door te tekenen:

* We stelden een vraag die we konden beantwoorden met gegevens
* We bouwden een *model* met behulp van *lineaire regressie*
* We maakten een *voorspelling,* compleet met een *betrouwbaarheidsinterval*

En we gebruikten geen wiskunde of computers om het te doen.

Nu als we meer informatie hadden gehad, zoals...

* de snede van de diamant
* kleurvariaties (hoe dicht de diamant is bij wit)
* het aantal insluitsels in de diamant

... dan zouden we meer kolommen hebben gehad. In dat geval wordt wiskunde nuttig. Als u meer dan twee kolommen hebt, is het moeilijk om punten op papier te tekenen. De wiskunde u passen die lijn of dat vlak om uw gegevens heel mooi.

Ook, als in plaats van slechts een handvol diamanten, hadden we tweeduizend of twee miljoen, dan kun je dat werk veel sneller doen met een computer.

Vandaag hebben we gesproken over hoe lineaire regressie te doen, en we maakten een voorspelling met behulp van gegevens.

Bekijk de andere video's in "Data Science for Beginners" van Microsoft Azure Machine Learning Studio (klassiek).

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste data science experiment met Machine Learning Studio (klassiek)](create-experiment.md)
* [Een inleiding tot Machine Learning op Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
