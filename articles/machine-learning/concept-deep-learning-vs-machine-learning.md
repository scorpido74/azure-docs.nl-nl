---
title: Deep learning versus machine learning
titleSuffix: Azure
description: Ontdek hoe deep learning zich verhoudt tot machine learning en kunstmatige intelligentie. Deep learning wordt gebruikt in scenario's zoals fraudedetectie, spraak- & gezichtsherkenning, sentimentanalyses en tijdreeksvoorspellingen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: b024010583ba1c6e0ffdf663f7335011ce212bf1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414586"
---
# <a name="deep-learning-vs-machine-learning"></a>Deep learning versus machine learning

In dit artikel u deep learning versus machine learning vergelijken. Je leert hoe de twee concepten zich verhouden en hoe ze passen in de bredere categorie van kunstmatige intelligentie. Het artikel beschrijft ook hoe deep learning kan worden toegepast op reële scenario's zoals fraudedetectie, spraak- en gezichtsherkenning, sentimentanalyses en tijdreeksprognoses.

## <a name="deep-learning-machine-learning-and-ai"></a>Deep learning, machine learning en AI

![Relatiediagram: AI vs. machine learning vs. deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Overweeg de volgende definities om deep learning versus machine learning vs. AI te begrijpen:

- **Deep learning** is een subset van machine learning die gebaseerd is op kunstmatige neurale netwerken. Het _leerproces_ is _diep_ omdat de structuur van kunstmatige neurale netwerken bestaat uit meerdere input, output en verborgen lagen. Elke laag bevat eenheden die de invoergegevens omzetten in informatie die de volgende laag kan gebruiken voor een bepaalde voorspellende taak. Dankzij deze structuur kan een machine leren door middel van zijn eigen gegevensverwerking.

- **Machine learning** is een subset van kunstmatige intelligentie die gebruik maakt van technieken (zoals deep learning) waarmee machines ervaring kunnen gebruiken om taken te verbeteren. Het _leerproces_ is gebaseerd op de volgende stappen:

   1. Gegevens in een algoritme verwerken. (In deze stap u aanvullende informatie aan het model verstrekken, bijvoorbeeld door functieextractie uit te voeren.)
   1. Gebruik deze gegevens om een model te trainen.
   1. Test en implementeer het model.
   1. Gebruik het geïmplementeerde model om een geautomatiseerde voorspellende taak uit te voeren. (Met andere woorden, bel en gebruik het geïmplementeerde model om de voorspellingen te ontvangen die door het model worden geretourneerd.)

- **Kunstmatige intelligentie (AI)** is een techniek die computers in staat stelt om menselijke intelligentie na te bootsen. Het omvat machine learning. 
 
Het is belangrijk om de relatie tussen AI, machine learning en deep learning te begrijpen. Machine learning is een manier om kunstmatige intelligentie te bereiken. Door machine learning en deep learning-technieken te gebruiken, u computersystemen en toepassingen bouwen die taken uitvoeren die vaak worden geassocieerd met menselijke intelligentie. Deze taken omvatten beeldherkenning, spraakherkenning en taalvertaling.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Technieken van deep learning versus machine learning 

Nu je het overzicht hebt van machine learning versus deep learning, laten we de twee technieken vergelijken. Bij machine learning moet het algoritme worden verteld hoe het een nauwkeurige voorspelling kan doen door meer informatie te gebruiken (bijvoorbeeld door functieextractie uit te voeren). In deep learning kan het algoritme leren hoe het een nauwkeurige voorspelling kan doen door middel van zijn eigen gegevensverwerking, dankzij de kunstmatige neurale netwerkstructuur.

In de volgende tabel worden de twee technieken nader vergeleken:

| |Alle machine learning |Alleen deep learning|
|---|---|---|
|  **Aantal gegevenspunten** | Kan kleine hoeveelheden gegevens gebruiken om voorspellingen te doen. | Moet grote hoeveelheden trainingsgegevens gebruiken om voorspellingen te doen. |
|  **Hardwareafhankelijkheden** | Kan werken op low-end machines. Het heeft geen grote hoeveelheid rekenkracht nodig. | Hangt af van high-end machines. Het doet inherent een groot aantal matrix vermenigvuldiging operaties. Een GPU kan deze bewerkingen efficiënt optimaliseren. |
|  **Featurisatieproces** | Vereist dat functies nauwkeurig worden geïdentificeerd en gemaakt door gebruikers. | Leert functies op hoog niveau uit gegevens en creëert zelf nieuwe functies. |
|  **Leeraanpak** | Verdeelt het leerproces in kleinere stappen. Het combineert vervolgens de resultaten van elke stap in een output. | Beweegt door het leerproces door het oplossen van het probleem op een end-to-end basis. |
|  **Uitvoeringstijd** | Duurt relatief weinig tijd om te trainen, variërend van een paar seconden tot een paar uur. | Meestal duurt het lang om te trainen omdat een deep learning-algoritme veel lagen omvat. |
|  **Uitvoer** | De uitvoer is meestal een numerieke waarde, zoals een score of een classificatie. | De uitvoer kan meerdere indelingen hebben, zoals een tekst, een score of een geluid. |

## <a name="deep-learning-use-cases"></a>Gebruikscases voor deep learning

Door de kunstmatige neurale netwerkstructuur blinkt deep learning uit in het identificeren van patronen in ongestructureerde gegevens zoals afbeeldingen, geluid, video en tekst. Om deze reden transformeert deep learning snel vele industrieën, waaronder gezondheidszorg, energie, financiën en transport. Deze industrieën heroverwegen nu traditionele bedrijfsprocessen. 

Enkele van de meest voorkomende toepassingen voor deep learning worden beschreven in de volgende paragrafen.

### <a name="named-entity-recognition"></a>Herkenning van de benoemde entiteit

Named-entity recognition is een deep learning methode die een stuk tekst als invoer neemt en omzet in een vooraf gespecificeerde klasse. Deze nieuwe informatie kan een postcode, een datum, een product-ID zijn. De informatie kan vervolgens worden opgeslagen in een gestructureerd schema om een lijst met adressen op te stellen of als benchmark voor een identiteitsvalidatie-engine te dienen.

### <a name="object-detection"></a>Objectdetectie

Deep learning is toegepast in veel use cases voor objectdetectie. Objectdetectie bestaat uit twee delen: beeldclassificatie en vervolgens beeldlokalisatie. _Afbeeldingsclassificatie_ identificeert de objecten van de afbeelding, zoals auto's of personen. _Beeldlokalisatie_ geeft de specifieke locatie van deze objecten. 

Objectdetectie wordt al gebruikt in sectoren zoals gaming, retail, toerisme en zelfrijdende auto's.

### <a name="image-caption-generation"></a>Afbeeldingbijschriftgeneratie

Net als beeldherkenning, in beeldondertiteling, voor een bepaalde afbeelding, moet het systeem een bijschrift genereren dat de inhoud van de afbeelding beschrijft. Wanneer u objecten in foto's detecteren en labelen, is de volgende stap om deze labels om te zetten in beschrijvende zinnen. 

Meestal gebruiken toepassingen voor beeldondertiteling convolutional neurale netwerken om objecten in een afbeelding te identificeren en vervolgens een terugkerend neuraal netwerk te gebruiken om de labels om te zetten in consistente zinnen.

### <a name="machine-translation"></a>Machinevertaling

Machine translation neemt woorden of zinnen uit de ene taal en vertaalt ze automatisch naar een andere taal. Machine translation bestaat al een lange tijd, maar deep learning levert indrukwekkende resultaten op twee specifieke gebieden: automatische vertaling van tekst (en vertaling van spraak naar tekst) en automatische vertaling van afbeeldingen.

Met de juiste gegevenstransformatie kan een neuraal netwerk tekst-, audio- en visuele signalen begrijpen. Machinevertaling kan worden gebruikt om geluidsfragmenten in grotere audiobestanden te identificeren en het gesproken woord of beeld als tekst te transcriberen.

### <a name="text-analytics"></a>Tekstanalyse

Tekstanalyse op basis van deep learning-methoden omvat het analyseren van grote hoeveelheden tekstgegevens (bijvoorbeeld medische documenten of uitgavenontvangstbewijzen), het herkennen van patronen en het maken van georganiseerde en beknopte informatie.

Bedrijven gebruiken deep learning om tekstanalyse uit te voeren om handel met voorkennis en naleving van overheidsvoorschriften te detecteren. Een ander veelvoorkomend voorbeeld is verzekeringsfraude: tekstanalyse is vaak gebruikt om grote hoeveelheden documenten te analyseren om de kans te herkennen dat een verzekeringsclaim fraude is. 

## <a name="artificial-neural-networks"></a>Kunstmatige neurale netwerken

Kunstmatige neurale netwerken worden gevormd door lagen van verbonden knooppunten. Deep learning-modellen maken gebruik van neurale netwerken die een groot aantal lagen hebben. 

De volgende secties verkennen de meest populaire kunstmatige neurale netwerk typologieën.

### <a name="feedforward-neural-network"></a>Feedforward neurale netwerk

Het feedforward neurale netwerk is het meest elementaire type kunstmatig neuraal netwerk. In een feedforward-netwerk wordt informatie in slechts één richting van invoerlaag naar uitvoerlaag verplaatst. Feedforward neurale netwerken transformeren een ingang door het door een reeks verborgen lagen. Elke laag bestaat uit een set neuronen, en elke laag is volledig verbonden met alle neuronen in de laag voor. De laatste volledig verbonden laag (de uitvoerlaag) vertegenwoordigt de gegenereerde voorspellingen.

### <a name="recurrent-neural-network"></a>Terugkerend neuraal netwerk

Terugkerende neurale netwerken zijn een veel gebruikt kunstmatig neuraal netwerk. Deze netwerken slaan de uitvoer van een laag op en voeren deze terug naar de invoerlaag om de uitkomst van de laag te voorspellen. Terugkerende neurale netwerken hebben geweldige leervaardigheden. Ze worden veel gebruikt voor complexe taken zoals tijdreeksen voorspellen, handschrift leren en taal herkennen.

### <a name="convolutional-neural-networks"></a>Convolutional neurale netwerken

Een convolutional neuraal netwerk is een bijzonder effectief kunstmatig neuraal netwerk, en het presenteert een unieke architectuur. Lagen zijn ingedeeld in drie dimensies: breedte, hoogte en diepte. De neuronen in één laag verbinden niet met alle neuronen in de volgende laag, maar alleen met een klein gebied van de neuronen van de laag. De uiteindelijke uitvoer wordt gereduceerd tot één vector van waarschijnlijkheidsscores, georganiseerd langs de dieptedimensie. 

Convolutional neurale netwerken zijn gebruikt in gebieden zoals videoherkenning, beeldherkenning en recommender systemen.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u deep learning-technologie gebruiken in [Azure Machine Learning:](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri)

- [Handgeschreven cijfers classificeren met behulp van een TensorFlow-model](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Handgeschreven cijfers classificeren met behulp van een TensorFlow-schatter en Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Afbeeldingen classificeren met behulp van een Pytorch-model](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Handgeschreven cijfers classificeren met behulp van een Chainer-model](https://docs.microsoft.com/azure/machine-learning/how-to-train-ml-models)

Gebruik ook de [Machine Learning Algorithm Cheat Sheet](algorithm-cheat-sheet.md) om algoritmen voor uw model te kiezen.
