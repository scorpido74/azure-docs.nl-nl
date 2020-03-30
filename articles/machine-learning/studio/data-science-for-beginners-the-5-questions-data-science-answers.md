---
title: Wetenschappelijke gegevens voor beginners
titleSuffix: ML Studio (classic) - Azure
description: Data Science for Beginners leert basisconcepten in 5 korte video's, te beginnen met De 5 Vragen Data Science Answers. Van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: af0611e121228a587e159bd4e6529c807132d16d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204440"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Gegevenswetenschap voor beginners, video 1: de 5 vragen waarop gegevenswetenschap antwoord geeft
Krijg een snelle introductie tot data science van *Data Science for Beginners* in vijf korte video's van een top data scientist. Deze video's zijn eenvoudig, maar nuttig, of je nu geïnteresseerd bent in het doen van data science of je werkt met data scientists.

Deze eerste video gaat over de soorten vragen die data science kan beantwoorden. Om het meeste uit de serie te halen, bekijk ze allemaal. [Ga naar de lijst met video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere video's in deze serie
*Data Science for Beginners* is een snelle introductie tot data science die in totaal ongeveer 25 minuten duurt. Bekijk alle vijf de video's:

* Video 1: De 5 vragen data science antwoorden
* Video 2: [Zijn uw gegevens klaar voor data science?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sec)*
* Video 3: [Stel een vraag die u beantwoorden met gegevens](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Video 4: [Voorspel een antwoord met een eenvoudig model](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sec)*
* Video 5: [Kopieer het werk van anderen om data science te doen](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transcript: De 5 vragen data science antwoorden
Hallo! Welkom bij de videoserie *Data Science voor beginners.*

Data Science kan intimiderend zijn, dus ik zal hier de basis introduceren zonder vergelijkingen of computer programmeerjargon.

In deze eerste video, zullen we praten over "De 5 vragen data science antwoorden."

Data Science gebruikt getallen en namen (ook wel categorieën of labels genoemd) om antwoorden op vragen te voorspellen.

Het kan u verrassen, maar *er zijn slechts vijf vragen die data science antwoorden:*

* Is dit A of B?
* Is dit raar?
* Hoeveel – of – Hoeveel?
* Hoe is dit georganiseerd?
* Wat moet ik verder nog doen?

Elk van deze vragen wordt beantwoord door een aparte familie van machine learning-methoden, algoritmen genaamd.

Het is handig om na te denken over een algoritme als een recept en uw gegevens als de ingrediënten. Een algoritme vertelt hoe je de gegevens combineren en mixen om een antwoord te krijgen. Computers zijn als een blender. Ze doen het grootste deel van het harde werk van het algoritme voor u en ze doen het vrij snel.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Vraag 1: Is dit A of B? gebruikt classificatiealgoritmen
Laten we beginnen met de vraag: Is dit A of B?

![Classificatiealgoritmen: Is dit A of B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Deze familie van algoritmen wordt classificatie van twee klassen genoemd.

Het is handig voor elke vraag die slechts twee mogelijke antwoorden heeft.

Bijvoorbeeld:

* Zal deze band falen in de komende 1.000 mijl: Ja of nee?
* Dat brengt in meer klanten: een $ 5 coupon of een 25% korting?

Deze vraag kan ook worden herformuleerd om meer dan twee opties op te nemen: Is dit A of B of C of D, enz.?  Dit wordt multiclass classificatie genoemd en het is handig als u meerdere - of enkele duizenden - mogelijke antwoorden hebt. Classificatie van meerdere klassen kiest de meest waarschijnlijke classificatie.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Vraag 2: Is dit raar? maakt gebruik van anomaliedetectiealgoritmen
De volgende vraag die data science kan beantwoorden is: Is dit raar? Deze vraag wordt beantwoord door een familie van algoritmen genaamd anomalie detectie.

![Anomalie Detectie algoritmen: Is dit raar?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Als u een creditcard hebt, hebt u al geprofiteerd van anomaliedetectie. Uw creditcardmaatschappij analyseert uw aankooppatronen, zodat ze u kunnen waarschuwen voor mogelijke fraude. Kosten die "raar" zijn, kunnen een aankoop zijn in een winkel waar u normaal gesproken niet winkelt of een ongewoon prijzig artikel koopt.

Deze vraag kan nuttig zijn op veel manieren. Bijvoorbeeld:

* Als u een auto met drukmeters hebt, wilt u misschien weten: Is deze drukmeterlezing normaal?
* Als u het internet in de gaten houdt, wilt u weten: Is dit bericht van het internet typisch?

Anomaliedetectie markeert onverwachte of ongebruikelijke gebeurtenissen of gedragingen. Het geeft aanwijzingen waar te zoeken naar problemen.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Vraag 3: Hoeveel? of Hoeveel? gebruikt regressiealgoritmen
Machine learning kan ook het antwoord op Hoeveel voorspellen? of Hoeveel? De algoritmefamilie die deze vraag beantwoordt, wordt regressie genoemd.

![Regressiealgoritmen: Hoeveel? of Hoeveel?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regressiealgoritmen maken numerieke voorspellingen, zoals:

* Wat zal de temperatuur volgende week dinsdag zijn?  
* Wat zal mijn omzet in het vierde kwartaal zijn?

Ze helpen bij het beantwoorden van elke vraag die vraagt om een nummer.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Vraag 4: Hoe is dit georganiseerd? maakt gebruik van clusteralgoritmes
Nu zijn de laatste twee vragen een beetje meer gevorderd.

Soms wilt u de structuur van een gegevensset begrijpen - Hoe is dit georganiseerd? Voor deze vraag hebt u geen voorbeelden waarvan u al de resultaten kent.

Er zijn een heleboel manieren om te plagen uit de structuur van de gegevens. Een benadering is clustering. Het scheidt gegevens in natuurlijke "klonten", voor een eenvoudigere interpretatie. Met clustering is er niet één goed antwoord.

![Clustering algoritmen: Hoe is dit georganiseerd?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Veelvoorkomende voorbeelden van clustervragen zijn:

* Welke kijkers houden van dezelfde soorten films?
* Welke printermodellen mislukken op dezelfde manier?

Door te begrijpen hoe gegevens worden georganiseerd, u gedrag en gebeurtenissen beter begrijpen en voorspellen.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Vraag 5: Wat moet ik nu doen? maakt gebruik van reinforcement learning-algoritmen
De laatste vraag - Wat moet ik nu doen? – maakt gebruik van een familie van algoritmen genaamd versterking leren.

Reinforcement leren werd geïnspireerd door hoe de hersenen van ratten en mensen reageren op straf en beloningen. Deze algoritmen leren van resultaten en beslissen over de volgende actie.

Typisch, versterking leren is een goede pasvorm voor geautomatiseerde systemen die veel kleine beslissingen te maken zonder menselijke begeleiding.

![Reinforcement Learning algoritmen: Wat moet ik nu doen?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Vragen die het beantwoordt gaan altijd over welke actie moet worden ondernomen - meestal door een machine of een robot. Een aantal voorbeelden:

* Als ik een temperatuurregeling systeem voor een huis: Pas de temperatuur of laat het waar het is?  
* Als ik een zelfrijdende auto ben: Bij een geel licht, remmen of versnellen?  
* Voor een robotvacuüm: Blijven stofzuigen, of terug naar het laadstation?

Reinforcement learning-algoritmen verzamelen gegevens terwijl ze gaan en leren van vallen en opstaan.

Dus dat is het - De 5 vragen die data science kan beantwoorden.

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste data science experiment met Machine Learning Studio (klassiek)](create-experiment.md)
* [Een inleiding tot Machine Learning op Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
