---
title: Diep gaande informatie versus machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over uitgebreide kennis versus machine learning en hoe beide concepten verband houden met kunst matige intelligentie. Grondige kennis kan worden toegepast in scenario's zoals fraude detectie, spraak-en gezichts herkenning, sentiment Analytics en time series-prognoses.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: cff31916f837141ae54f3c14dd125be6a92a5008
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035511"
---
# <a name="deep-learning-vs-machine-learning"></a>Diep gaande informatie versus machine learning

In dit artikel vindt u meer informatie over het vergelijken van diep gaande en machine learning. U leert hoe de twee concepten worden vergeleken en hoe ze in de bredere categorie van kunst matige intelligentie passen. In dit artikel wordt ook beschreven hoe diep leren kan worden toegepast op praktijk scenario's zoals fraude detectie, spraak-en gezichts herkenning, sentiment Analytics en time series-prognoses.

## <a name="deep-learning-machine-learning-and-ai"></a>Diep leren, machine learning en AI

![Relatie diagram: AI versus machine learning versus diep gaande lessen](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Houd rekening met de volgende definities voor een beter begrip van diep leren tegenover machine learning vs. AI

- **Diep leren** is een subset van machine learning die is gebaseerd op kunst matige Neural netwerken. Het _leer proces_ is _dieper_ omdat de structuur van kunst matige Neural netwerken uit meerdere invoer-, uitvoer-en verborgen lagen bestaat. Elke laag bevat eenheden die de invoer gegevens transformeren naar informatie die de volgende laag voor een bepaalde voorspellende taak kan gebruiken. Dankzij deze structuur kan een machine een eigen gegevens verwerking leren.

- **Machine learning** is een subset van kunst matige intelligentie die gebruikmaakt van technieken (zoals diepe learning) waarmee computers ervaring kunnen gebruiken om taken te verbeteren. Het _leer proces_ is gebaseerd op de volgende stappen:

   1. Gegevens in een algoritme invoeren. (In deze stap kunt u aanvullende informatie voor het model opgeven, bijvoorbeeld door het uitpakken van functies uit te voeren.)
   1. Gebruik deze gegevens om een model te trainen.
   1. Het model testen en implementeren.
   1. Gebruik het geïmplementeerde model om een geautomatiseerde, voorspellende taak uit te voeren. (Met andere woorden, roep en gebruik het geïmplementeerde model om de voor spellingen te ontvangen die door het model worden geretourneerd.)

- **Kunst matige intelligentie (AI)** is een techniek waarmee computers Human Intelligence kunnen nabootsen. Het bevat machine learning. 
 
Het is belang rijk om inzicht te krijgen in de relatie tussen AI, machine learning en diep gaande lessen. Machine learning is een manier om kunst matige intelligentie te krijgen. U kunt met behulp van machine learning en diepe Leer technieken computer systemen en-toepassingen bouwen die vaak aan Human Intelligence zijn gekoppeld. Deze taken omvatten visuele perceptie, spraak herkenning, besluit vorming en taal omzetting.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Technieken van diep leren versus machine learning 

Nu u een overzicht hebt van machine learning versus diepere lessen, gaan we de twee technieken vergelijken. In machine learning moet het algoritme worden aangegeven hoe u een nauw keurige voor spelling maakt door meer informatie te gebruiken (bijvoorbeeld door functie extractie uit te voeren). In het uitgebreid leren is het algoritme meer informatie over het maken van een nauw keurige voor spelling door de eigen gegevens verwerking, dankzij de kunst matige Neural-netwerk structuur.

De volgende tabel vergelijkt de twee technieken in meer detail:

| |Alle machine learning |Alleen diep gaande lessen|
|---|---|---|
|  **Aantal gegevens punten** | Kan kleine gegevens bedragen gebruiken die door gebruikers worden verschaft. | Vereist een grote hoeveelheid trainings gegevens om beknopte conclusies te maken. |
|  **Hardware-afhankelijkheden** | Kan werken op computers met een laag eind punt. Er is geen grote hoeveelheid reken kracht nodig. | Is afhankelijk van high-end-machines. Er wordt een groot aantal bewerkingen voor matrix vermenigvuldiging uitgevoerd. Met een GPU kunnen deze bewerkingen efficiënt worden geoptimaliseerd. |
|  **Parametrisatie-proces** | Vereist onderdelen om nauw keurig te worden geïdentificeerd en gemaakt door gebruikers. | Meer informatie over functies op hoog niveau van gegevens en maakt zelf nieuwe functies. |
|  **Leer benadering** | Verdeelt taken in kleine delen en combineert de ontvangen resultaten vervolgens in één conclusie. | Hiermee lost u het probleem op een end-to-end-basis op. |
|  **Uitvoerings tijd** | Neemt relatief weinig tijd in beslag om te trainen, variërend van een paar seconden tot enkele uren. | Er is een ongewoon lange tijd nodig om te trainen omdat een diepe leer algoritme veel para meters omvat. |
|  **Uitvoer** | De uitvoer is doorgaans een numerieke waarde, zoals een score of classificatie. | De uitvoer kan tekst, een score, een element of een geluid zijn. |

## <a name="deep-learning-use-cases"></a>Uitgebreide leer cases

Vanwege de kunst matige Neural-netwerk structuur worden er diep gaande kennissen van Excel bij het identificeren van patronen in ongestructureerde gegevens, zoals afbeeldingen, geluid, video en tekst. Om die reden is het snel om veel branches te transformeren, met inbegrip van gezondheids zorg, energie, financiën en Trans Port. Deze branches zijn nu bezig met het herzien van traditionele bedrijfs processen. 

Enkele van de meest voorkomende toepassingen voor diep gaande lessen worden beschreven in de volgende alinea's.

### <a name="named-entity-recognition"></a>Herkenning benoemde entiteit

Een voor deel van het gebruik van diepe-Learning netwerken is het herkennen van entiteits herkenning, een manier om bepaalde soorten informatie uit ongestructureerde en niet-gelabelde gegevens op te halen. Deze informatie kan mensen, plaatsen, bedrijven of dingen zijn. De informatie kan vervolgens worden opgeslagen in een Structured schema om een lijst met adressen te maken of om te fungeren als een bench Mark voor een identiteits validatie-engine.

### <a name="object-detection"></a>Objectdetectie

Grondige kennis is toegepast in veel object detectie-use cases. Object detectie bestaat uit twee onderdelen: afbeeldings classificatie en vervolgens afbeeldings lokalisatie. Met afbeeldings _classificatie_ worden de objecten van de afbeelding, zoals auto's of personen, geïdentificeerd. _Lokalisatie_ van de installatie kopie biedt de specifieke locatie van deze objecten. 

Object detectie wordt al gebruikt in branches zoals gaming, handels verkeer, toer isme en zelf gerichte auto's.

### <a name="image-caption-generation"></a>Bijschrift bij afbeelding genereren

Net als bij afbeeldings herkenning moet in afbeeldings bijschriften voor een bepaalde afbeelding een bijschrift worden gegenereerd waarin de inhoud van de installatie kopie wordt beschreven. Wanneer u objecten op Foto's kunt detecteren en labelen, is de volgende stap het omzetten van deze labels in beschrijvende, samenhangende zinnen. In het algemeen gebruiken systemen voor het ondertitelen van afbeeldingen zeer grote convolutional Neural-netwerken om objecten in de Foto's te detecteren en vervolgens een recurrent Neural-netwerk (RNN) te gebruiken om de labels in samenhangende zinnen te zetten.

### <a name="machine-translation"></a>Machinevertaling

Bij automatische vertaling worden woorden, zinsdelen of zinnen uit de ene taal gebruikt en worden ze automatisch omgezet in een andere taal. Automatische vertaling van de machine is al lange tijd in de buurt, maar het diepe leer heeft indrukwekkende resultaten in twee specifieke gebieden: automatische vertaling van tekst (en vertaling van spraak naar tekst) en automatische vertaling van afbeeldingen. 

Met de juiste gegevens transformatie kan een diep gaande netwerk tekst, audio en visuele signalen begrijpen. Machine vertalingen kunnen worden gebruikt om geluids fragmenten in grotere audio bestanden te identificeren en het gesp roken woord of de afbeelding als tekst te transcriberen.

### <a name="text-analytics"></a>Tekstanalyse

Een belang rijke taak van een diep gaande training is e-discovery. Bedrijven gebruiken tekst analyse die is gebaseerd op diep gaande informatie over het detecteren van insider-handel en naleving van wettelijke voor Schriften. Hedge funds gebruiken tekst analyse om in te zoomen op enorme document opslagplaatsen om inzicht te krijgen in toekomstige investerings prestaties en markt sentiment. De use-case voor tekst analyse op basis van diep gaande lessen draait rond de mogelijkheid om enorme hoeveel heden tekst gegevens te parseren en analyses of yield-aggregaties uit te voeren.

## <a name="artificial-neural-networks"></a>Kunst matige Neural netwerken

Kunst matige Neural netwerken worden gevormd door lagen van verbonden knoop punten. Uitgebreide leer modellen gebruiken Neural-netwerken met een groot aantal lagen. 

In de volgende secties worden de meest populaire kunst matige Neural-netwerk topologieën besproken.

### <a name="feedforward-neural-network"></a>Feedforward Neural-netwerk

Het Feedforward Neural-netwerk is het meest eenvoudige type kunst matig Neural netwerk. In een Feedforward-netwerk worden gegevens in slechts één richting van de invoer laag naar de uitvoer laag gezonden. Met Feedforward Neural-netwerken wordt een invoer getransformeerd door een reeks verborgen lagen te plaatsen. Elke laag bestaat uit een set neurons en elke laag is volledig verbonden met alle neurons in de laag. De laatste volledig verbonden laag (de uitvoer laag) vertegenwoordigt de gegenereerde voor spellingen.

### <a name="recurrent-neural-network"></a>Recurrent Neural-netwerk

Recurrente Neural Networks is een veelgebruikt Neural netwerk. In deze netwerken wordt de uitvoer van een laag opgeslagen en weer gegeven in de invoer laag om te helpen bij het voors pellen van het resultaat van de laag. Rehuidige Neural-netwerken hebben fantastische leer mogelijkheden. Ze worden veel gebruikt voor complexe taken, zoals het leren van hand schrift en het herkennen van taal.

### <a name="convolutional-neural-networks"></a>Convolutional Neural-netwerken

Een convolutional Neural-netwerk is een bijzonder effectief Neural netwerk en biedt een unieke architectuur. Lagen zijn ingedeeld in drie dimensies: breedte, hoogte en diepte. De neurons in de ene laag worden niet verbonden met alle neurons in de volgende laag, maar alleen voor een kleine regio van de neurons van de laag. De uiteindelijke uitvoer wordt gereduceerd tot één vector met waarschijnlijkheids scores, geordend op de diepte dimensie. 

Convolutional Neural Networks zijn gebruikt in gebieden zoals het herkennen en classificeren van afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u de technologie voor diepe leren gebruikt in [Azure machine learning](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri):

- [Hand matige cijfers classificeren met behulp van een tensor flow-model](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Geclassificeerde handgeschreven cijfers door gebruik te maken van een tensor flow Estimator en Keras](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Installatie kopieën classificeren met behulp van een Pytorch-model](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Hand matige cijfers classificeren met behulp van een ketting model](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)
