---
title: Stel een vraag die gegevens kunnen beantwoorden
titleSuffix: ML Studio (classic) - Azure
description: Leer hoe je een scherpe data science vraag formuleert in Data Science for Beginners video 3. Bevat een vergelijking van classificatie- en regressievragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 26837337b49d79a26404fd6709b036f6907720f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838844"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Stel een vraag die u met gegevens kunt beantwoorden
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Data Science for Beginners serie
Leer hoe je een data science probleem formuleert in een vraag in Data Science for Beginners video 3. Deze video bevat een vergelijking van vragen voor classificatie- en regressiealgoritmen.

Om het meeste uit de serie te halen, bekijk ze allemaal. [Ga naar de lijst met video's](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere video's in deze serie
*Data Science for Beginners* is een snelle introductie tot data science in vijf korte video's.

* Video 1: [De 5 vragen data science antwoorden](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Video 2: [Zijn uw gegevens klaar voor data science?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 sec)*
* Video 3: Stel een vraag die u beantwoorden met gegevens
* Video 4: [Voorspel een antwoord met een eenvoudig model](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sec)*
* Video 5: [Kopieer het werk van anderen om data science te doen](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transcript: Stel een vraag die u beantwoorden met gegevens
Welkom bij de derde video in de serie "Data Science for Beginners."  

In deze krijg je een aantal tips voor het formuleren van een vraag die je beantwoorden met gegevens.

Je zou meer uit deze video kunnen halen, als je eerst de twee eerdere video's in deze serie bekijkt: "De 5 vragen die data science kan beantwoorden" en "Is je gegevens klaar voor data science?"

## <a name="ask-a-sharp-question"></a>Stel een scherpe vraag
We hebben gesproken over hoe data science is het proces van het gebruik van namen (ook wel categorieën of labels) en nummers om een antwoord op een vraag te voorspellen. Maar het kan niet zomaar een vraag zijn; het moet een *scherpe vraag zijn.*

Een vage vraag hoeft niet beantwoord te worden met een naam of een nummer. Een scherpe vraag moet.

Stel je voor dat je een magische lamp hebt gevonden met een geest die elke vraag die je stelt naar waarheid zal beantwoorden. Maar het is een ondeugende geest, die zal proberen om hun antwoord zo vaag en verwarrend als ze kunnen wegkomen met. U wilt pin ze neer met een vraag zo luchtdicht dat ze niet kunnen helpen, maar vertel je wat je wilt weten.

Als je een vage vraag te stellen, zoals "Wat gaat er gebeuren met mijn voorraad?", de geest zou kunnen antwoorden, "De prijs zal veranderen". Dat is een waarheidsgetrouw antwoord, maar het is niet erg behulpzaam.

Maar als je een scherpe vraag te stellen, zoals "Wat zal mijn voorraad de verkoopprijs volgende week?", de geest kan het niet helpen, maar geven u een specifiek antwoord en voorspellen van een verkoopprijs.

## <a name="examples-of-your-answer-target-data"></a>Voorbeelden van uw antwoord: Doelgegevens
Zodra u uw vraag formuleert, controleert u of u voorbeelden van het antwoord in uw gegevens hebt.

Als onze vraag is : "Wat zal mijn voorraad de verkoopprijs volgende week?" dan moeten we ervoor zorgen dat onze gegevens de geschiedenis van de aandelenkoers bevatten.

Als onze vraag is: "Welke auto in mijn vloot gaat eerst falen?" dan moeten we ervoor zorgen dat onze gegevens informatie bevatten over eerdere fouten.

![Doelgegevens - voorbeelden van uw antwoord. Formuleer een data science vraag.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Deze voorbeelden van antwoorden worden een doel genoemd. Een doel is wat we proberen te voorspellen over toekomstige gegevenspunten, of het nu een categorie of een getal is.

Als u geen doelgegevens hebt, moet u er een paar krijgen. Je je vraag niet beantwoorden zonder.

## <a name="reformulate-your-question"></a>Herformuleer uw vraag
Soms u uw vraag herformuleren om een nuttiger antwoord te krijgen.

De vraag "Is dit gegevenspunt A of B?" voorspelt de categorie (of naam of label) van iets. Om het te beantwoorden, gebruiken we een *classificatiealgoritme.*

De vraag "Hoeveel?" of "Hoeveel?" voorspelt een bedrag. Om het te beantwoorden gebruiken we een *regressie algoritme*.

Om te zien hoe we deze kunnen transformeren, laten we eens kijken naar de vraag: "Welk nieuwsverhaal is het meest interessant voor deze lezer?" Het vraagt om een voorspelling van een enkele keuze uit vele mogelijkheden - met andere woorden "Is dit A of B of C of D?" - en zou gebruik maken van een classificatiealgoritme.

Maar, deze vraag kan gemakkelijker te beantwoorden als je het herformuleren als "Hoe interessant is elk verhaal op deze lijst aan deze lezer?" Nu u elk artikel een numerieke score geven, en dan is het gemakkelijk om het artikel met de hoogste score te identificeren. Dit is een herschikking van de classificatievraag in een regressievraag of Hoeveel?

![Herformuleer uw vraag. Classificatievraag versus regressievraag.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hoe je een vraag stelt is een aanwijzing welk algoritme je een antwoord kan geven.

U zult merken dat bepaalde families van algoritmen - zoals die in ons nieuwsverhaal voorbeeld - zijn nauw verwant. U uw vraag herformuleren om het algoritme te gebruiken dat u het meest nuttige antwoord geeft.

Maar, het belangrijkste, stel die scherpe vraag - de vraag die je beantwoorden met gegevens. En zorg ervoor dat u de juiste gegevens hebt om deze te beantwoorden.

We hebben gesproken over een aantal basisprincipes voor het stellen van een vraag die u beantwoorden met gegevens.

Bekijk de andere video's in "Data Science for Beginners" van Microsoft Azure Machine Learning Studio (klassiek).

## <a name="next-steps"></a>Volgende stappen
* [Probeer een eerste data science experiment met Machine Learning Studio (klassiek)](create-experiment.md)
* [Een inleiding tot Machine Learning op Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
