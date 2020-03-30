---
title: 'Model voor crossvalideren: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Model voor crossvalideren in Azure Machine Learning om parameterschattingen voor classificatie- of regressiemodellen te vergelijken door de gegevens te partitioneren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477643"
---
# <a name="cross-validate-model"></a>Kruisvalidatie van model valideren

In dit artikel wordt beschreven hoe u de module Model cross valideren gebruiken in Azure Machine Learning-ontwerper (voorbeeld). *Cross-validatie* is een techniek die vaak wordt gebruikt in machine learning om zowel de variabiliteit van een gegevensset als de betrouwbaarheid van elk model dat via die gegevens is getraind, te beoordelen.  

De module Cross Validate Model neemt als invoer een gelabelde gegevensset, samen met een ongetraind classificatie- of regressiemodel. Het verdeelt de gegevensset in een aantal subsets *(plooien),* bouwt een model op elke vouw, en vervolgens retourneert een set van nauwkeurigheid statistieken voor elke vouw. Door de nauwkeurigheidsstatistieken voor alle plooien te vergelijken, u de kwaliteit van de gegevensset interpreteren. U dan begrijpen of het model gevoelig is voor variaties in de gegevens.  

Cross Validate Model retourneert ook voorspelde resultaten en waarschijnlijkheden voor de gegevensset, zodat u de betrouwbaarheid van de voorspellingen beoordelen.  

### <a name="how-cross-validation-works"></a>Hoe cross-validatie werkt

1. Cross-validatie verdeelt trainingsgegevens willekeurig in plooien. 

   Het algoritme standaard op 10 vouwen als u de gegevensset nog niet eerder hebt verdeeld. Als u de gegevensset wilt verdelen in een ander aantal plooien, u de [partitie- en voorbeeldmodule](partition-and-sample.md) gebruiken en aangeven hoeveel vouwen u moet gebruiken.  

2.  De module zet de gegevens in vouw 1 opzij om te gebruiken voor validatie. (Dit wordt ook wel de *holdout fold*genoemd .) De module gebruikt de resterende plooien om een model te trainen. 

    Als u bijvoorbeeld vijf vouwen maakt, genereert de module vijf modellen tijdens cross-validatie. De module traint elk model met behulp van vier vijfde van de gegevens. Het test elk model op de resterende een vijfde.  

3.  Tijdens het testen van het model voor elke vouw evalueert de module meerdere nauwkeurigheidsstatistieken. Welke statistieken de module gebruikt, is afhankelijk van het type model dat u evalueert. Verschillende statistieken worden gebruikt om classificatiemodellen versus regressiemodellen te evalueren.  

4.  Wanneer het bouw- en evaluatieproces voor alle plooien is voltooid, genereert Cross Validate Model een reeks prestatiestatistieken en scoort het resultaten voor alle gegevens. Bekijk deze statistieken om te zien of een enkele vouw een hoge of lage nauwkeurigheid heeft. 

### <a name="advantages-of-cross-validation"></a>Voordelen van cross-validatie

Een andere en gangbare manier om een model te evalueren is om de gegevens te verdelen in een training en testset met behulp van [Split Data](split-data.md)en vervolgens het model op de trainingsgegevens te valideren. Maar cross-validatie biedt een aantal voordelen:  

-   Cross-validatie maakt gebruik van meer testgegevens.

    Cross-validatie meet de prestaties van het model met de opgegeven parameters in een grotere gegevensruimte. Dat wil zeggen, cross-validatie maakt gebruik van de volledige trainingsgegevensset voor zowel training als evaluatie, in plaats van een gedeelte. Als u een model daarentegen valideert met behulp van gegevens die zijn gegenereerd uit een willekeurige splitsing, evalueert u het model doorgaans op slechts 30 procent of minder van de beschikbare gegevens.  

    Omdat cross-validatie het model echter meerdere keren traint en valideert via een grotere gegevensset, is het veel rekenintensiever. Het duurt veel langer dan valideren op een willekeurige splitsing.  

-   Cross-validatie evalueert zowel de gegevensset als het model.

    Cross-validatie meet niet alleen de nauwkeurigheid van een model. Het geeft u ook een idee van hoe representatief de gegevensset is en hoe gevoelig het model kan zijn voor variaties in de gegevens.  

## <a name="how-to-use-cross-validate-model"></a>Cross Validate-model gebruiken

Cross-validatie kan lang duren als uw gegevensset groot is.  U dus Cross Validate Model gebruiken in de beginfase van het bouwen en testen van uw model. In die fase u de goedheid van de modelparameters evalueren (ervan uitgaande dat de berekeningstijd aanvaardbaar is). U vervolgens uw model trainen en evalueren met behulp van de vastgestelde parameters met de [modules Train Model](train-model.md) en Evaluate [Model.](evaluate-model.md)

In dit scenario traint en test u het model met behulp van Cross Validate Model.

1. Voeg de module Model cross validate toe aan uw pijplijn. U het vinden in Azure Machine Learning designer, in de categorie **Model scoring & Evaluatie.** 

2. Verbind de uitvoer van een classificatie- of regressiemodel. 

    Als u bijvoorbeeld **tweeklassen versterkte beslissingsstructuur** gebruikt voor classificatie, configureert u het model met de gewenste parameters. Sleep vervolgens een connector van de **niet-getrainde modelpoort** van de classificatie naar de overeenkomende poort van Cross Validate Model. 

    > [!TIP] 
    > U hoeft het model niet te trainen, omdat Cross-Validate Model het model automatisch traint als onderdeel van de evaluatie.  
3.  Verbind op de **gegevenssetpoort** van Cross Validate Model een gelabelde trainingsgegevensset.  

4.  Klik in het rechterdeelvenster van Model voor kruisvalidatie op **Kolom bewerken**. Selecteer de enkele kolom die het klassenlabel of de voorspelbare waarde bevat. 

5. Stel een waarde in voor de parameter **Random seed** als u de resultaten van cross-validatie wilt herhalen voor opeenvolgende uitvoeringen op dezelfde gegevens.  

6. Verzend de pijplijn.

7. Zie de sectie [Resultaten](#results) voor een beschrijving van de rapporten.

## <a name="results"></a>Resultaten

Nadat alle iteraties zijn voltooid, maakt Cross Validate Model scores voor de hele gegevensset. Het maakt ook prestatiestatistieken die u gebruiken om de kwaliteit van het model te beoordelen.

### <a name="scored-results"></a>Gescoorde resultaten

De eerste uitvoer van de module biedt de brongegevens voor elke rij, samen met enkele voorspelde waarden en gerelateerde waarschijnlijkheden. 

Als u de resultaten wilt weergeven, klikt u in de pijplijn met de rechtermuisknop op de module Model kruisvalideren. Selecteer **Resultaten visualiseren gescoord**.

| Nieuwe kolomnaam      | Beschrijving                              |
| -------------------- | ---------------------------------------- |
| Labels met een score        | Deze kolom wordt toegevoegd aan het einde van de gegevensset. Het bevat de voorspelde waarde voor elke rij. |
| Gescoorde waarschijnlijkheden | Deze kolom wordt toegevoegd aan het einde van de gegevensset. Het geeft de geschatte waarschijnlijkheid van de waarde in **Scored Labels**aan. |
| Vouwnummer          | Geeft de op nul gebaseerde index aan van de vouw waaraan elke rij gegevens is toegewezen tijdens crossvalidatie. |

 ### <a name="evaluation-results"></a>Evaluatieresultaten

Het tweede rapport is gegroepeerd op plooien. Vergeet niet dat Cross Validate Model tijdens de uitvoering de trainingsgegevens willekeurig opsplitst in *n-plooien* (standaard, 10). In elke iteratie over de gegevensset gebruikt Cross Validate Model één vouw als validatiegegevensset. Het maakt gebruik van de resterende *n-1* plooien om een model te trainen. Elk van de *n-modellen* wordt getest op basis van de gegevens in alle andere plooien.

In dit rapport worden de plooien weergegeven op indexwaarde, in oplopende volgorde.  Als u op een andere kolom wilt bestellen, u de resultaten opslaan als een gegevensset.

Als u de resultaten wilt weergeven, klikt u in de pijplijn met de rechtermuisknop op de module Model kruisvalideren. Selecteer **Evaluatieresultaten visualiseren op vouw**.


|Kolomnaam| Beschrijving|
|----|----|
|Vouwnummer| Een id voor elke vouw. Als u vijf plooien hebt gemaakt, zouden er vijf subsets van gegevens zijn, genummerd 0 tot 4.
|Aantal voorbeelden in gevouwen|Het aantal rijen dat aan elke vouw is toegewezen. Ze moeten ongeveer gelijk zijn. |


De module bevat ook de volgende statistieken voor elke vouw, afhankelijk van het type model dat u evalueert: 

+ **Classificatiemodellen**: Precisie, terugroepactie, F-score, AUC, nauwkeurigheid  

+ **Regressiemodellen**: Gemiddelde absolute fout, gemiddelde kwadraatfout, relatieve absolute fout, relatieve kwadraatfout en bepalingscoëfficiënt


## <a name="technical-notes"></a>Technische notities  

+ Het is een beste manier om gegevenssets te normaliseren voordat u ze gebruikt voor cross-validatie. 

+ Cross Validate Model is veel meer rekenintensief en duurt langer dan wanneer u het model hebt gevalideerd met behulp van een willekeurig verdeelde gegevensset. De reden hiervoor is dat Cross Validate Model het model meerdere keren traint en valideert.

+ Het is niet nodig om de gegevensset op te splitsen in trainings- en testsets wanneer u cross-validatie gebruikt om de nauwkeurigheid van het model te meten. 


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

