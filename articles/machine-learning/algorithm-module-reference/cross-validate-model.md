---
title: 'Model voor kruis validatie: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de model module kruislings valideren in Azure Machine Learning service om de schatting van de para meters voor classificaties of regressie modellen te kruisen door de gegevens te partitioneren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512845"
---
# <a name="cross-validate-model"></a>Model voor kruis valideren

In dit artikel wordt beschreven hoe u de module voor **Kruis validatie** gebruikt in azure machine learning Designer (preview). *Kruis validatie* is een belang rijke techniek die vaak wordt gebruikt in machine learning om zowel de variabiliteit van een gegevensset als de betrouw baarheid te beoordelen van een model dat is getraind met behulp van die gegevens.  

De module voor **Kruis validatie** maakt als invoer een gegevensset met een label, samen met een niet-uitgetrainde classificatie of regressie model. De gegevensset wordt onderverdeeld in een aantal subsets (*vouwen*), bouwt een model op elke vouw en retourneert vervolgens een set nauw keurige statistieken voor elke vouw. Door de nauw keurige statistieken voor alle vouwen te vergelijken, kunt u de kwaliteit van de gegevensset interpreteren en begrijpen of het model gevoelig is voor variaties in de gegevens.  

Kruis validatie retourneert ook voorspelde resultaten en waarschijnlijkheid voor de gegevensset, zodat u de betrouw baarheid van de voor spellingen kunt beoordelen.  

### <a name="how-cross-validation-works"></a>Hoe Kruis validatie werkt

1. Door Kruis validatie worden de trainings gegevens wille keurig verdeeld over een aantal partities, ook wel *vouwen*genoemd. 

    + Het algoritme wordt standaard ingesteld op 10 vouwen als u de gegevensset niet eerder hebt gepartitioneerd. 
    + Als u de gegevensset wilt splitsen in een ander aantal vouwen, kunt u de module [Partition en sample](partition-and-sample.md) gebruiken en aangeven hoeveel vouwen u wilt gebruiken.  

2.  In de module worden de gegevens in vouw 1 gereserveerd om te worden gebruikt voor validatie (dit wordt ook wel de *evaluatie-vouw*genoemd) en worden de resterende vouwen gebruikt om een model te trainen. 

    Als u bijvoorbeeld vijf vouwen maakt, genereert de module vijf modellen tijdens de Kruis validatie, elk model dat is getraind met 4/5 van de gegevens en getest op de resterende 1/5.  

3.  Tijdens het testen van het model voor elke vouw, worden er meerdere nauwkeurigheids statistieken geëvalueerd. Welke statistieken worden gebruikt, is afhankelijk van het type model dat u evalueert. Verschillende statistieken worden gebruikt om classificatie modellen en regressie modellen te evalueren.  

4.  Wanneer het proces voor het maken en evalueren van alle vouwen is voltooid, genereert het **Kruis validatie model** een set prestatie gegevens en gescoorde resultaten voor alle gegevens. U moet deze metrische gegevens controleren om te zien of een enkele vouwen een zeer hoge of lage nauw keurigheid heeft 

### <a name="advantages-of-cross-validation"></a>Voor delen van kruis validatie

Een andere en algemene manier om een model te evalueren is door de gegevens te verdelen in een trainings-en testset met [gesplitste gegevens](split-data.md)en vervolgens het model op de trainings gegevens te valideren. Kruis validatie biedt echter een aantal voor delen:  

-   Kruis validatie maakt gebruik van meer test gegevens.

     Kruis validatie meet de prestaties van het model met de opgegeven para meters in een grotere gegevens ruimte. Dat wil zeggen dat kruis validatie gebruikmaakt van de volledige trainings gegevensset voor zowel training als evaluatie, in plaats van een deel van een gedeelte. Als u daarentegen een model valideert met behulp van gegevens die zijn gegenereerd op basis van een wille keurige splitsing, wordt het model doorgaans alleen op 30% of minder van de beschik bare gegevens geëvalueerd.  

     Omdat intervalidatie treinen echter meerdere keren in een grotere gegevensset worden gevalideerd, is het veel meer reken intensief en duurt het veel langer dan het valideren van een wille keurige splitsing.  

-   Bij Kruis validatie worden de gegevensset en het model geëvalueerd.

     Bij Kruis validatie wordt niet alleen de nauw keurigheid van een model gemeten, maar u kunt ook een idee krijgen van hoe representatief de gegevensset is en hoe gevoelig het model kan zijn voor variaties in de gegevens.  

## <a name="how-to-use-cross-validate-model"></a>Model voor meerdere validatie gebruiken

Kruis validatie kan veel tijd in beslag nemen als uw gegevensset groot is.  Daarom kunt u het **model met meerdere validaties** gebruiken in de eerste fase van het bouwen en testen van uw model, om de goed keuring van de model parameters te evalueren (ervan uitgaande dat de reken tijd Maxi maal is), en vervolgens uw model trainen en evalueren met behulp van de Er zijn para meters ingesteld met het [Train-model](train-model.md) en de [model modules evalueren](evaluate-model.md) .

In dit scenario moet u het model trainen en testen met behulp van een **model voor meerdere validaties**.

1. Voeg de module **model voor kruis validatie** toe aan uw pijp lijn. U kunt het vinden in Azure Machine Learning designer in de categorie **beoordeling & evaluatie** van het model. 

2. Verbind de uitvoer van een **classificatie** of **regressie** model. 

    Als u bijvoorbeeld een **twee klasse Bayes-punt machine** voor classificatie gebruikt, configureert u het model met de gewenste para meters en sleept u een connector van de niet- **getrainde model** poort van de classificatie naar de overeenkomende poort van **kruislings valideren Model**. 

    > [!TIP] 
    > Het model hoeft niet te worden getraind omdat **Kruis validatie** het model automatisch als onderdeel van de evaluatie ondertraint.  
3.  Verbind op de **gegevensset** -poort van **kruislings valideren model**een trainings gegevensset met een label.  

4.  Klik in het deel venster **Eigenschappen** van het **model Kruis valideren**op **kolom kiezer starten** en kies de afzonderlijke kolom die het klassen label of de voorspel bare waarde bevat. 

5. Stel een waarde in voor de **wille keurige Seed** -para meter als u de resultaten van kruis validatie wilt herhalen in opeenvolgende uitvoeringen van dezelfde gegevens.  

6.  Voer de pijplijn uit.

7. Zie de sectie met [resultaten](#results) voor een beschrijving van de rapporten.

    Als u een kopie van het model later opnieuw wilt gebruiken, klikt u met de rechter muisknop op de uitvoer van de module die het algoritme bevat (bijvoorbeeld de **twee klasse Bayes punt machine**) en klikt u op **Opslaan als getraind model**.

## <a name="results"></a>Resultaten

Nadat alle iteraties zijn voltooid, maakt het **Kruis validatie model** scores voor de hele gegevensset, evenals de metrische gegevens over prestaties die u kunt gebruiken om de kwaliteit van het model te beoordelen.

### <a name="scored-results"></a>Gescoorde resultaten

De eerste uitvoer van de module levert de bron gegevens voor elke rij, samen met enkele voorspelde waarden en gerelateerde kansen. 

Als u deze resultaten wilt weer geven, klikt u in de pijp lijn met de rechter muisknop op de **model module Kruis valideren** , selecteert u **resultaten scores**en klikt u op **visualiseren**.

| Nieuwe kolom naam      | Beschrijving                              |
| -------------------- | ---------------------------------------- |
| Gescoorde labels        | Deze kolom wordt toegevoegd aan het einde van de gegevensset en bevat de voorspelde waarde voor elke rij |
| Gescoorde kansen | Deze kolom wordt toegevoegd aan het einde van de gegevensset en geeft de geschatte waarschijnlijkheid van de waarde in de **gescoorde labels**aan. |
| Vouw nummer          | Hiermee wordt de op 0 gebaseerde index van de vouw aangegeven waarmee elke rij met gegevens tijdens Kruis validatie werd toegewezen. |

 ### <a name="evaluation-results"></a>Evaluatieresultaten

Het tweede rapport is gegroepeerd op vouwen. Houd er rekening mee dat tijdens de uitvoering door **Kruis valideren model** de opleidings gegevens wille keurig splitst in *n* vouwen (standaard 10). In elke iteratie over de gegevensset gebruikt het **Kruis validatie model** één vouw als een validatie gegevensset en worden de resterende *n-1* vouwen gebruikt om een model te trainen. Elk van de *n* -modellen wordt getest op basis van de gegevens in alle andere vouwen.

In dit rapport worden de vouwen weer gegeven op index waarde, in oplopende volg orde.  Als u wilt sorteren op een andere kolom, kunt u de resultaten opslaan als een gegevensset.

Als u deze resultaten wilt weer geven, klikt u in de pijp lijn met de rechter muisknop op de **model module Kruis valideren** , selecteert u **evaluatie resultaten per vouw**en klikt u op **visualiseren**.


|Kolom naam| Beschrijving|
|----|----|
|Vouw nummer| Een id voor elke vouw. Als u vijf vouwen hebt gemaakt, zijn er vijf subsets met gegevens, genummerd van 0 tot 4.
|Aantal voor beelden in vouwen|Het aantal rijen dat aan elke vouw is toegewezen. Ze moeten ongeveer gelijk zijn. |


Daarnaast zijn de volgende metrische gegevens opgenomen voor elke vouw, afhankelijk van het type model dat u evalueert. 

+ **Classificatie modellen**: precisie, intrekken, F-Score, AUC, nauw keurigheid  

+ **Regressie modellen**: absolute fout, wortel gemiddelde fout, relatieve absolute fout, relatieve kwadraat fout en coëfficiënt van bepaling


## <a name="technical-notes"></a>Technische opmerkingen  

+ Het is een best practice om gegevens sets te normaliseren voordat ze worden gebruikt voor kruis validatie. 

+ Omdat **Intervalidate model** treinen en het model meerdere keren valideert, is het veel meer reken intensief en duurt het langer dan als u het model hebt gevalideerd met een wille keurig verdeelde gegevensset. 

+ Het is niet nodig om de gegevensset te splitsen in trainings-en test sets wanneer u kruis validatie gebruikt om de nauw keurigheid van het model te meten. 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 

