---
title: Diep gaande informatie versus machine learning
titleSuffix: Azure
description: Meer informatie over hoe diep leren is gekoppeld aan machine learning en kunst matige intelligentie. Grondige kennis wordt gebruikt in scenario's zoals fraude detectie, stem & gezichts herkenning, sentiment Analytics en time series-prognose.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: b024010583ba1c6e0ffdf663f7335011ce212bf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414586"
---
# <a name="deep-learning-vs-machine-learning"></a>Diep gaande informatie versus machine learning

In dit artikel vindt u meer informatie over het vergelijken van diep gaande en machine learning. U leert hoe de twee concepten worden vergeleken en hoe ze in de bredere categorie van kunst matige intelligentie passen. In dit artikel wordt ook beschreven hoe diep leren kan worden toegepast op praktijk scenario's zoals fraude detectie, spraak-en gezichts herkenning, sentiment Analytics en time series-prognoses.

## <a name="deep-learning-machine-learning-and-ai"></a>Diep leren, machine learning en AI

![Relatie diagram: AI versus machine learning versus diep gaande lessen](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Houd rekening met de volgende definities voor een beter begrip van diep leren en machine learning versus AI:

- **Diep leren** is een subset van machine learning die is gebaseerd op kunst matige Neural netwerken. Het _leer proces_ is _dieper_ omdat de structuur van kunst matige Neural netwerken uit meerdere invoer-, uitvoer-en verborgen lagen bestaat. Elke laag bevat eenheden die de invoer gegevens transformeren naar informatie die de volgende laag voor een bepaalde voorspellende taak kan gebruiken. Dankzij deze structuur kan een machine een eigen gegevens verwerking leren.

- **Machine learning** is een subset van kunst matige intelligentie die gebruikmaakt van technieken (zoals diepe learning) waarmee computers ervaring kunnen gebruiken om taken te verbeteren. Het _leer proces_ is gebaseerd op de volgende stappen:

   1. Gegevens in een algoritme invoeren. (In deze stap kunt u aanvullende informatie voor het model opgeven, bijvoorbeeld door het uitpakken van functies uit te voeren.)
   1. Gebruik deze gegevens om een model te trainen.
   1. Het model testen en implementeren.
   1. Gebruik het geïmplementeerde model om een geautomatiseerde, voorspellende taak uit te voeren. (Met andere woorden, roep en gebruik het geïmplementeerde model om de voor spellingen te ontvangen die door het model worden geretourneerd.)

- **Kunst matige intelligentie (AI)** is een techniek waarmee computers Human Intelligence kunnen nabootsen. Het bevat machine learning. 
 
Het is belang rijk om inzicht te krijgen in de relatie tussen AI, machine learning en diep gaande lessen. Machine learning is een manier om kunst matige intelligentie te krijgen. U kunt met behulp van machine learning en diepe Leer technieken computer systemen en-toepassingen bouwen die vaak aan Human Intelligence zijn gekoppeld. Deze taken omvatten afbeeldings herkenning, spraak herkenning en taal omzetting.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Technieken van diep leren versus machine learning 

Nu u een overzicht hebt van machine learning versus diepere lessen, gaan we de twee technieken vergelijken. In machine learning moet het algoritme worden aangegeven hoe u een nauw keurige voor spelling maakt door meer informatie te gebruiken (bijvoorbeeld door functie extractie uit te voeren). In het uitgebreid leren is het algoritme meer informatie over het maken van een nauw keurige voor spelling door de eigen gegevens verwerking, dankzij de kunst matige Neural-netwerk structuur.

De volgende tabel vergelijkt de twee technieken in meer detail:

| |Alle machine learning |Alleen diep gaande lessen|
|---|---|---|
|  **Aantal gegevens punten** | Kan kleine hoeveel heden gegevens gebruiken om voor spellingen te maken. | Er moet gebruik worden gemaakt van grote hoeveel heden trainings gegevens om voor spellingen te maken. |
|  **Hardware-afhankelijkheden** | Kan werken op computers met een laag eind punt. Er is geen grote hoeveelheid reken kracht nodig. | Is afhankelijk van high-end-machines. Er wordt een groot aantal bewerkingen voor matrix vermenigvuldiging uitgevoerd. Met een GPU kunnen deze bewerkingen efficiënt worden geoptimaliseerd. |
|  **Parametrisatie-proces** | Vereist onderdelen om nauw keurig te worden geïdentificeerd en gemaakt door gebruikers. | Meer informatie over functies op hoog niveau van gegevens en maakt zelf nieuwe functies. |
|  **Leer benadering** | Verdeelt het leer proces in kleinere stappen. Vervolgens worden de resultaten van elke Step Into één uitvoer gecombineerd. | Gaat door het leer proces door het probleem te verhelpen. |
|  **Uitvoeringstijd** | Neemt relatief weinig tijd in beslag om te trainen, variërend van een paar seconden tot enkele uren. | Het duurt doorgaans lang om te trainen omdat een diepe leer algoritme veel lagen omvat. |
|  **Uitvoer** | De uitvoer is doorgaans een numerieke waarde, zoals een score of classificatie. | De uitvoer kan meerdere indelingen hebben, zoals een tekst, een score of een geluid. |

## <a name="deep-learning-use-cases"></a>Uitgebreide leer cases

Vanwege de kunst matige Neural-netwerk structuur worden er diep gaande kennissen van Excel bij het identificeren van patronen in ongestructureerde gegevens, zoals afbeeldingen, geluid, video en tekst. Om die reden is het snel om veel branches te transformeren, met inbegrip van gezondheids zorg, energie, financiën en Trans Port. Deze branches zijn nu bezig met het herzien van traditionele bedrijfs processen. 

Enkele van de meest voorkomende toepassingen voor diep gaande lessen worden beschreven in de volgende alinea's.

### <a name="named-entity-recognition"></a>Herkenning benoemde entiteit

Benoemde entiteits herkenning is een diepe leer methode die een stuk tekst als invoer gebruikt en transformeert in een vooraf opgegeven klasse. Deze nieuwe informatie kan een post code, een datum, een product-ID zijn. De informatie kan vervolgens worden opgeslagen in een Structured schema om een lijst met adressen te maken of om te fungeren als een bench Mark voor een identiteits validatie-engine.

### <a name="object-detection"></a>Objectdetectie

Grondige kennis is toegepast in veel object detectie-use cases. Object detectie bestaat uit twee onderdelen: afbeeldings classificatie en vervolgens afbeeldings lokalisatie. Met afbeeldings _classificatie_ worden de objecten van de afbeelding, zoals auto's of personen, geïdentificeerd. _Lokalisatie_ van de installatie kopie biedt de specifieke locatie van deze objecten. 

Object detectie wordt al gebruikt in branches zoals gaming, handels verkeer, toer isme en zelf gerichte auto's.

### <a name="image-caption-generation"></a>Bijschrift bij afbeelding genereren

Net als bij afbeeldings herkenning moet in afbeeldings bijschriften voor een bepaalde afbeelding een bijschrift worden gegenereerd waarin de inhoud van de installatie kopie wordt beschreven. Wanneer u objecten op Foto's kunt detecteren en labelen, is de volgende stap het omzetten van deze labels in beschrijvende zinnen. 

Doorgaans gebruiken installatie kopieën voor bijschriften van toepassingen convolutional Neural-netwerken om objecten in een installatie kopie te identificeren en vervolgens een recurrent Neural-netwerk te gebruiken om de labels te omzetten in consistente zinnen.

### <a name="machine-translation"></a>Automatische vertaling

Met machine vertalingen worden woorden of zinnen uit de ene taal gebruikt en worden ze automatisch omgezet in een andere taal. De automatische vertaling van de machine is al lange tijd in de buurt, maar met een diep gaande leer proces worden indrukwekkende resultaten in twee specifieke gebieden gecentraliseerd: automatisch omzetten van tekst (en vertaling van spraak naar tekst) en automatische vertaling van afbeeldingen.

Met de juiste gegevens transformatie kan een Neural-netwerk tekst, audio en visuele signalen begrijpen. Machine vertalingen kunnen worden gebruikt om geluids fragmenten in grotere audio bestanden te identificeren en het gesp roken woord of de afbeelding als tekst te transcriberen.

### <a name="text-analytics"></a>Tekstanalyse

Voor tekst analyse op basis van diepe leer methoden moet u grote hoeveel heden tekst gegevens (bijvoorbeeld medische documenten of onkosten ontvangsten) analyseren, patronen herkennen en gestructureerde en beknopte informatie maken.

Bedrijven gebruiken dieper leren om tekst analyse uit te voeren om de handel en naleving van de overheid te detecteren. Een ander algemeen voor beeld is een verzekerings fraude: tekst analyse is vaak gebruikt voor het analyseren van grote hoeveel heden documenten om de kans op fraude van een verzekerings claim te herkennen. 

## <a name="artificial-neural-networks"></a>Kunst matige Neural netwerken

Kunst matige Neural netwerken worden gevormd door lagen van verbonden knoop punten. Uitgebreide leer modellen gebruiken Neural-netwerken met een groot aantal lagen. 

In de volgende secties worden de meest populaire kunst matige Neural-netwerk topologieën besproken.

### <a name="feedforward-neural-network"></a>Feedforward Neural-netwerk

Het Feedforward Neural-netwerk is het meest eenvoudige type kunst matig Neural netwerk. In een Feedforward-netwerk worden de gegevens in slechts één richting verplaatst van de invoer laag naar de uitvoer laag. Met Feedforward Neural-netwerken wordt een invoer getransformeerd door een reeks verborgen lagen te plaatsen. Elke laag bestaat uit een set neurons en elke laag is volledig verbonden met alle neurons in de laag. De laatste volledig verbonden laag (de uitvoer laag) vertegenwoordigt de gegenereerde voor spellingen.

### <a name="recurrent-neural-network"></a>Recurrent Neural-netwerk

Recurrente Neural Networks is een veelgebruikt Neural netwerk. In deze netwerken wordt de uitvoer van een laag opgeslagen en weer gegeven in de invoer laag om te helpen bij het voors pellen van het resultaat van de laag. Rehuidige Neural-netwerken hebben fantastische leer mogelijkheden. Ze worden veel gebruikt voor complexe taken, zoals het maken van een time series-prognose, het leren van hand schrift en het herkennen van taal.

### <a name="convolutional-neural-networks"></a>Convolutional Neural-netwerken

Een convolutional Neural-netwerk is een bijzonder effectief Neural netwerk en biedt een unieke architectuur. Lagen zijn ingedeeld in drie dimensies: breedte, hoogte en diepte. De neurons in de ene laag worden niet verbonden met alle neurons in de volgende laag, maar alleen voor een kleine regio van de neurons van de laag. De uiteindelijke uitvoer wordt gereduceerd tot één vector met waarschijnlijkheids scores, geordend op de diepte dimensie. 

Convolutional Neural Networks zijn gebruikt in gebieden zoals video herkenning, afbeeldings herkenning en aanbevolen systemen.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u de technologie voor diepe leren gebruikt in [Azure machine learning](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri):

- [Hand matige cijfers classificeren met behulp van een tensor flow-model](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Geclassificeerde handgeschreven cijfers door gebruik te maken van een tensor flow Estimator en Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Installatie kopieën classificeren met behulp van een Pytorch-model](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Hand matige cijfers classificeren met behulp van een ketting model](https://docs.microsoft.com/azure/machine-learning/how-to-train-ml-models)

U kunt ook het [Cheat-venster van machine learning Algorithm](algorithm-cheat-sheet.md) gebruiken om algoritmen voor uw model te kiezen.
