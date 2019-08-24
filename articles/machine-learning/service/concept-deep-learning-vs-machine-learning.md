---
title: Diep gaande informatie versus machine learning
titleSuffix: Azure Machine Learning service
description: Meer informatie over de relatie tussen uitgebreide kennis versus machine learning en hoe ze in de categorie van kunst matige intelligentie passen. In dit artikel wordt beschreven hoe u met uitgebreid leren scenario's kunt oplossen, zoals fraude detectie, spraak-en gezichts herkenning, sentiment Analytics en time series-prognoses.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 5301be493ef22fc2d74cc337d88b04caf391fdaa
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982810"
---
# <a name="deep-learning-vs-machine-learning"></a>Diep gaande informatie versus machine learning

Dit artikel helpt u bij het begrijpen van de relatie tussen uitgebreide kennis en machine learning. U leert hoe de twee concepten worden vergeleken en hoe ze in de bredere categorie van kunst matige intelligentie passen. Ten slotte wordt in het artikel beschreven hoe diep leren kan worden toegepast op praktijk scenario's, zoals fraude detectie, spraak-en gezichts herkenning, sentiment Analytics en time series-prognoses.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Hoe hebben diep leren, machine learning en AI betrekking op elkaar?

![Relatie diagram: AI versus machine learning versus diep gaande lessen](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Om inzicht te krijgen in het verschil tussen diep gaande lessen versus machine learning versus AI, meer informatie over elk van deze studie velden:

- **Diep leren** is een subset van machine learning op basis van kunst matige Neural netwerken waarmee een machine zichzelf kan trainen. In dit geval wordt het _leer proces_ diepergenoemd, omdat de structuur van kunst matige Neural netwerken uit meerdere invoer-, uitvoer-en verborgen lagen bestaat. Elke laag bevat eenheden die de invoer gegevens transformeren naar informatie die de volgende laag kan gebruiken om een bepaalde voorspellende taak uit te voeren. Dankzij deze structuur kan een machine een eigen gegevens verwerking leren.

- **Machine learning** is een subset van kunst matige intelligentie die technieken bevat (zoals diepe learning) waarmee computers kunnen verbeteren bij taken met ervaring. In dit geval is het _leer proces_ gebaseerd op de volgende stappen:

    1. Voer een algoritme in met gegevens door er meer informatie over te geven (bijvoorbeeld door het extra heren van functies uit te voeren).
    2. Gebruik deze gegevens om een model te trainen.
    3. Het model testen en implementeren.
    4. Gebruik het geïmplementeerde model om een bepaalde automatische voorspellende taak uit te voeren.

- **Kunst matige intelligentie (AI)** is een techniek waarmee computers Human Intelligence kunnen nabootsen. Het bevat machine learning. Het is belang rijk om inzicht te krijgen in de relatie tussen _AI versus machine learning vergeleken met diep gaande lessen_: Machine learning is een manier om kunst matige intelligentie te bereiken. Dit houdt in het gebruik van machine learning en diepe Leer technieken om computer systemen en-toepassingen te bouwen die doorgaans taken kunnen uitvoeren die vaak zijn gekoppeld aan Human Intelligence, zoals visuele perceptie, spraak herkenning, besluit vorming en vertaling tussen talen.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Vergelijking van diepe kennis versus machine learning

Nu u een overzicht hebt van machine learning versus diep gaande kennis, kunt u de twee technieken vergelijken. In het geval van machine learning moet het algoritme worden aangegeven hoe u een nauw keurige voor spelling maakt door het te voorzien van meer informatie. in het geval van een diep gaande leer proces is het algoritme in staat om te leren dat door de eigen gegevens verwerking dankzij de kunst matige Neural-netwerk structuur.

De volgende tabel vergelijkt de twee technieken in meer detail:

| |Alle machine learning |Alleen diep gaande lessen|
|---|---|---|
|  **Aantal gegevens punten** | Kan kleine gegevens bedragen van gebruikers gebruiken | Vereist een grote hoeveelheid trainings gegevens om beknopte conclusies te maken |
|  **Hardware-afhankelijkheden** | Kan werken op computers met een laag eind punt. Er is geen grote hoeveelheid reken kracht nodig | Is afhankelijk van high-end-machines. Er wordt een groot aantal bewerkingen voor matrix vermenigvuldiging uitgevoerd. Deze bewerkingen kunnen efficiënt worden geoptimaliseerd met behulp van een GPU |
|  **Parametrisatie-proces** | Vereist dat functies nauw keurig worden geïdentificeerd en gemaakt door gebruikers | Het leert op hoog niveau functies van gegevens en maakt zelf nieuwe functies |
|  **Leer benadering** | Verdeelt taken in kleine delen en combineert de ontvangen resultaten in één conclusie | Hiermee lost u het probleem op een end-to-end-basis |
|  **Uitvoerings tijd** | Relatief veel tijd in beslag neemt, variërend van een paar seconden tot enkele uren | Neemt een ongebruikelijke lange tijd in beslag, omdat er veel para meters zijn in een diepe leer algoritme |
|  **Uitvoer** | De uitvoer is doorgaans een numerieke waarde, zoals een score of classificatie | De uitvoer kan iets zijn van een score, een tekst, een element of een geluid |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Uitgebreide leer cases: Welke problemen worden opgelost?

Vanwege de kunst matige Neural-netwerk structuur worden er diep gaande kennissen van Excel bij het identificeren van patronen in ongestructureerde gegevens, zoals afbeeldingen, geluid, video en tekst. Daarom is het snel mogelijk om veel branches te transformeren, met inbegrip van gezondheids zorg, energie, fintech, Trans Port en anderen, om traditionele bedrijfs processen te herzien. Enkele van de meest voorkomende toepassingen voor diep gaande lessen worden beschreven in de volgende alinea's.

### <a name="named-entity-recognition"></a>Herkenning benoemde entiteit

Een voor deel van het gebruik van diepe-Learning netwerken is het herkennen van een entiteit, die een manier is om te extra heren uit ongestructureerde, niet-gelabelde gegevens typen, zoals mensen, plaatsen, bedrijven of zaken. Deze informatie kan vervolgens worden opgeslagen in een Structured schema om een lijst met adressen te maken of om te fungeren als een bench Mark voor een identiteits validatie-engine.

### <a name="object-detection"></a>Objectdetectie

Grondige kennis is toegepast in veel object detectie-use cases. Object detectie is in feite een proces dat uit twee delen bestaat: afbeeldings classificatie en vervolgens afbeeldings lokalisatie. De classificatie van een afbeelding bepaalt wat de objecten in de afbeelding zijn, zoals een auto of persoon, terwijl de lokalisatie van de installatie kopie de specifieke locatie van deze objecten biedt. Object detectie wordt al gebruikt in de bedrijfs takken van games, detail handel, toer isme en zelf aangedreven auto's.

### <a name="image-caption-generation"></a>Bijschrift bij afbeelding genereren

Net als bij de afbeeldings herkennings taak is afbeeldings bijschriften de taak waarbij voor een bepaalde afbeelding een bijschrift moet worden gegenereerd waarin de inhoud van de installatie kopie wordt beschreven. Als u objecten op Foto's kunt detecteren en labels voor die objecten wilt genereren, kunt u zien dat de volgende stap het omzetten van deze labels in een samenhangende zin beschrijving is. Over het algemeen is het gebruik van zeer grote convolutional Neural-netwerken voor de object detectie in de Foto's en vervolgens een recurrent Neural-netwerk (RNN) om de labels in een samenhangende zin te zetten.

### <a name="machine-translation"></a>Machinevertaling

Bij automatische vertaling worden woorden, zinsdelen of zinnen uit de ene taal gebruikt en worden ze automatisch omgezet in een andere taal. Automatische vertaling van de machine is al lange tijd in de buurt, maar het is zeer belang rijke resultaten te bereiken in twee specifieke gebieden: automatische vertaling van tekst (en spraak naar tekst) en automatische vertaling van installatie kopieën. Met de juiste gegevens transformatie is een diep netwerk in staat om tekst, audio en visuele signalen te leren kennen. Machine vertalingen kunnen worden gebruikt om geluids fragmenten in grotere audio bestanden te identificeren en het gesp roken woord of de afbeelding als tekst te transcriberen.

### <a name="text-analytics"></a>Tekstanalyse

Een belang rijke taak die grondige kennis kan uitvoeren, is e-discovery. Bedrijven gebruiken uitgebreide analyse op basis van analyses voor de detectie van insider-handels voorwaarden en naleving van de overheid. Hedge funds gebruiken tekst analyse om in te zoomen op enorme document opslagplaatsen om inzicht te verkrijgen in toekomstige investerings prestaties en markt sentiment. Het gebruik van een grondige analyse op basis van analyses draait rond de mogelijkheid om enorme hoeveel heden tekst gegevens te parseren voor het uitvoeren van analyses of aggregaties van het rendement.

## <a name="what-are-artificial-neural-networks"></a>Wat zijn kunst matige Neural-netwerken?

Kunst matige Neural netwerken worden gevormd door lagen van verbonden knoop punten. Uitgebreide leer modellen gebruiken Neural-netwerken met een zeer groot aantal lagen. De meest populaire kunst matige Neural-netwerk-topologieën worden hieronder besproken.

### <a name="feedforward-neural-network"></a>Feedforward Neural-netwerk

Het Feedforward Neural-netwerk is het meest eenvoudige type kunst matig Neural netwerk, waarin de informatie in slechts één richting van de invoer laag naar de uitvoer laag wordt verplaatst. Met Feedforward Neural-netwerken wordt een invoer getransformeerd door een reeks verborgen lagen te plaatsen. Elke laag bestaat uit een set neurons, waarbij elke laag volledig is verbonden met alle neurons in de laag. Ten slotte is er een laatste volledig verbonden laag, de uitvoer laag, die de gegenereerde voor spellingen vertegenwoordigt.

### <a name="recurrent-neural-network"></a>Recurrent Neural-netwerk

Recurrente Neural Networks is een veel gebruikte soort kunst matig Neural netwerk. Dit is het principe van het opslaan van de uitvoer van een laag en het opnieuw bezorgen van het resultaat van de laag. Deze Neural-netwerken hebben meer leer vaardigheden en worden veel gebruikt voor complexere taken, zoals het leren van hand schrift of taal herkenning.

### <a name="convolutional-neural-networks"></a>Convolutional Neural-netwerken

Een convolutional Neural-netwerk is een bijzonder effectief type kunst matig Neural netwerk dat een unieke architectuur vormt. Eerst zijn de lagen ingedeeld in drie dimensies: breedte, hoogte en diepte. Verder wordt de neurons in één laag niet verbonden met alle neurons in de volgende laag, maar alleen naar een kleine regio. Ten slotte wordt de uiteindelijke uitvoer gereduceerd tot één vector van waarschijnlijkheids scores, geordend op de diepte dimensie. Deze Neural-netwerken zijn gebruikt in gebieden zoals het herkennen en classificeren van afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u de technologie voor diepe leren gebruikt in de [Azure machine learning-service](/azure/machine-learning/service/):

- [Classificatie van handgeschreven cijfers met een tensor flow-model](how-to-train-tensorflow.md)
- [Classificatie van handgeschreven cijfers met een tensor flow-Estimator en Keras](how-to-train-keras.md)
- [Afbeeldingen classificeren met een Pytorch-model](how-to-train-pytorch.md)
- [Classificatie van handgeschreven cijfers met een ketting model](how-to-train-pytorch.md)
