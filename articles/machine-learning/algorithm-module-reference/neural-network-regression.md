---
title: 'Neuralnetworkregressie: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de neurale netwerkregressiemodule in Azure Machine Learning om een regressiemodel te maken met behulp van een aanpasbaar neuraal netwerkalgoritme..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456095"
---
# <a name="neural-network-regression-module"></a>Neuralnetworkregressiemodule

*Maakt een regressiemodel met behulp van een neurale netwerkalgoritme*  
  
 Categorie: Machine Learning / Initialize Model / Regressie
  
## <a name="module-overview"></a>Overzicht van de module  

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een regressiemodel te maken met behulp van een aanpasbaar neuraal netwerkalgoritme.
  
 Hoewel neurale netwerken zijn algemeen bekend voor gebruik in deep learning en modellering complexe problemen zoals beeldherkenning, ze zijn gemakkelijk aan te passen aan regressie problemen. Elke klasse van statistische modellen kan worden genoemd een neuraal netwerk als ze gebruik maken van adaptieve gewichten en kan benaderen niet-lineaire functies van hun ingangen. Zo is neurale netwerkregressie geschikt voor problemen waarbij een meer traditioneel regressiemodel niet in een oplossing past.
  
 Neurale netwerkregressie is een begeleide leermethode en vereist daarom een *gelabelde gegevensset*, die een labelkolom bevat. Omdat een regressiemodel een numerieke waarde voorspelt, moet de labelkolom een numeriek gegevenstype zijn.  
  
 U het model trainen door het model en de gelabelde gegevensset op te geven als input voor [Train Model.](./train-model.md) Het getrainde model kan vervolgens worden gebruikt om waarden voor de nieuwe invoervoorbeelden te voorspellen.  
  
## <a name="configure-neural-network-regression"></a>Neuralnetworkregressie configureren 

Neurale netwerken kunnen uitgebreid worden aangepast. In deze sectie wordt beschreven hoe u een model maakt met behulp van twee methoden:
  
+ [Een neuraal netwerkmodel maken met behulp van de standaardarchitectuur](#bkmk_DefaultArchitecture)  
  
    Als u de standaardneurale netwerkarchitectuur accepteert, gebruikt u het deelvenster **Eigenschappen** om parameters in te stellen die het gedrag van het neurale netwerk bepalen, zoals het aantal knooppunten in de verborgen laag, de leersnelheid en de normalisatie.

    Begin hier als je nieuw bent in neurale netwerken. De module ondersteunt veel aanpassingen, evenals modeltuning, zonder diepgaande kennis van neurale netwerken. 

+ Een aangepaste architectuur definiëren voor een neuraal netwerk 

    Gebruik deze optie als u extra verborgen lagen wilt toevoegen of de netwerkarchitectuur, de verbindingen en activeringsfuncties volledig wilt aanpassen.
    
    Deze optie is het beste als u al enigszins vertrouwd bent met neurale netwerken. U gebruikt de net#-taal om de netwerkarchitectuur te definiëren.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Een neuraal netwerkmodel maken met behulp van de standaardarchitectuur

1.  Voeg de **Neural Network Regression-module** toe aan uw pijplijn in de ontwerper. U deze module vinden onder **Machine Learning**, **Initialiseren**, in de categorie **Regressie.** 
  
2. Geef aan hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** kies deze optie als u al weet hoe u het model wilt configureren.

    -   **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.   

3.  Selecteer In **De specificatie van de verborgen laag**de optie Volledig verbonden **aanvraag**. Met deze optie maakt u een model met behulp van de standaard neurale netwerkarchitectuur, die voor een neurale netwerkregressiemodel de volgende kenmerken heeft:  
  
    + Het netwerk heeft precies één verborgen laag.
    + De uitvoerlaag is volledig verbonden met de verborgen laag en de verborgen laag is volledig verbonden met de invoerlaag.
    + Het aantal knooppunten in de verborgen laag kan door de gebruiker worden ingesteld (standaardwaarde is 100).  
  
    Omdat het aantal knooppunten in de invoerlaag wordt bepaald door het aantal functies in de trainingsgegevens, kan er in een regressiemodel slechts één knooppunt in de uitvoerlaag zijn.  
  
4. Typ **voor aantal verborgen knooppunten**het aantal verborgen knooppunten. De standaardinstelling is een verborgen laag met 100 knooppunten. (Deze optie is niet beschikbaar als u een aangepaste architectuur definieert met Net#.)
  
5.  Typ **bij Leersnelheid**een waarde die de stap definieert die bij elke iteratie is gezet, vóór correctie. Een grotere waarde voor leersnelheid kan ertoe leiden dat het model sneller convergeert, maar het kan lokale minima overlopen.

6.  Geef **bij Aantal leeriteraties**het maximum aantal keren dat het algoritme de trainingscases verwerkt.


8.  Typ **voor Het momentum**een waarde die moet worden toegepast tijdens het leren als een gewicht op knooppunten van eerdere iteraties.

10. Selecteer de optie **Shuffle-voorbeelden**om de volgorde van aanvragen tussen iteraties te wijzigen. Als u deze optie deselecteert, worden aanvragen telkens in exact dezelfde volgorde verwerkt wanneer u de pijplijn uitvoert.
  
11. Voor **willekeurig nummerzaad**u optioneel een waarde typen die u als zaad wilt gebruiken. Het opgeven van een seedwaarde is handig wanneer u wilt zorgen voor herhaalbaarheid in runs van dezelfde pijplijn.
  
13. Sluit een trainingsdataset en een van de [trainingsmodules](module-reference.md)aan: 
  
    -   Als u **De trainermodus maken** instelt op Eén **parameter,** gebruikt u [Treinmodel](./train-model.md).  
  
   
14. Verzend de pijplijn.  

## <a name="results"></a>Resultaten

Na de training is voltooid:

- Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 