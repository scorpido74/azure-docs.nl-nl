---
title: 'Tweeklassen versterkte beslissingsstructuur: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de tweeklassen-gebooste beslissingsstructuurmodule in Azure Machine Learning om een machine learning-model te maken dat is gebaseerd op het algoritme voor gepromote beslissingsbomen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920736"
---
# <a name="two-class-boosted-decision-tree-module"></a>Twee-klasse boosted Decision Tree module

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een machine learning-model te maken dat is gebaseerd op het algoritme voor gebooste beslissingsbomen. 

Een versterkte beslissingsboom is een ensemble leermethode waarbij de tweede boom corrigeert voor de fouten van de eerste boom, de derde boom corrigeert voor de fouten van de eerste en tweede bomen, enzovoort.  Voorspellingen zijn gebaseerd op het hele ensemble van bomen samen dat de voorspelling maakt.
  
Over het algemeen, wanneer goed geconfigureerd, verhoogde beslissingbomen zijn de gemakkelijkste methoden waarmee topprestaties te krijgen op een breed scala van machine learning taken. Echter, ze zijn ook een van de meer geheugen-intensieve leerlingen, en de huidige implementatie houdt alles in het geheugen. Daarom kan een gepromote beslissingsstructuurmodel mogelijk niet de grote gegevenssets verwerken die sommige lineaire leerlingen aankunnen.

## <a name="how-to-configure"></a>Configureren

Deze module maakt een ongetraind classificatiemodel. Omdat classificatie een begeleide leermethode is, hebt u een *gelabelde gegevensset* nodig die een labelkolom bevat met een waarde voor alle rijen.

U dit type model trainen met behulp van [Train Model](././train-model.md). 

1.  Voeg in Azure Machine Learning de module **Boosted Decision Tree** toe aan uw pijplijn.
  
2.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.
  
    + **Eén parameter:** als u weet hoe u het model wilt configureren, u een specifieke set waarden als argumenten opgeven.
  
    + **Parameterbereik:** Als u niet zeker bent van de beste parameters, u de optimale parameters vinden met behulp van de module Hyperparameters van [tunemodel.](tune-model-hyperparameters.md) U geeft een aantal waarden en de trainer wijzigt meerdere combinaties van de instellingen om de combinatie van waarden te bepalen die het beste resultaat oplevert.
  
3.  Voor **maximaal aantal bladeren per boom**geeft u het maximumaantal eindknooppunten (bladeren) aan dat in elke boom kan worden gemaakt.
  
     Door deze waarde te verhogen, u mogelijk de grootte van de boom vergroten en een betere precisie krijgen, met het risico van overfitting en langere trainingstijd.
  
4.  Voor **minimumaantal monsters per bladknooppunt**geeft u het aantal gevallen aan dat nodig is om een eindknooppunt (blad) in een boom te maken.  
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaardwaarde van 1 kan bijvoorbeeld zelfs één aanvraag ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde verhoogt tot 5, moeten de trainingsgegevens ten minste vijf gevallen bevatten die aan dezelfde voorwaarden voldoen.
  
5.  Typ **bij Leersnelheid**een getal tussen 0 en 1 dat de stapgrootte definieert tijdens het leren.  
  
     Het leerpercentage bepaalt hoe snel of langzaam de leerling convergeert op de optimale oplossing. Als de stapgrootte te groot is, u de optimale oplossing overshooten. Als de stapgrootte te klein is, duurt het langer om te convergeren naar de beste oplossing.
  
6.  Voor **Aantal bomen gebouwd,** geef het totale aantal beslissingbomen te maken in het ensemble. Door het creëren van meer beslissingbomen, u mogelijk een betere dekking te krijgen, maar de opleiding tijd zal toenemen.
  
     Deze waarde bepaalt ook het aantal bomen dat wordt weergegeven bij het visualiseren van het getrainde model. Als u één boom wilt zien of afdrukken, stelt u de waarde in op 1. Wanneer u dit echter doet, wordt er slechts één boom geproduceerd (de boom met de oorspronkelijke set parameters) en worden er geen verdere iteraties uitgevoerd.
  
7.  Typ **bij Willekeurig getalzaad**eventueel een niet-negatief geheel om te gebruiken als willekeurige zaadwaarde. Het opgeven van een zaad zorgt voor reproduceerbaarheid voor runs met dezelfde gegevens en parameters.  
  
     Het willekeurige zaad wordt standaard ingesteld op 0, wat betekent dat de oorspronkelijke zaadwaarde wordt verkregen uit de systeemklok.  Opeenvolgende runs met behulp van een willekeurig zaad kan verschillende resultaten hebben.
  

9. Train het model.
  
    + Als u **De trainermodus maken** instelt op Eén **parameter,** verbindt u een gelabelde gegevensset en de module [Treinmodel.](./train-model.md)  
   
## <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van de **module Treinmodel.** Selecteer het pictogram **Gegevensset registreren** om het model op te slaan als een herbruikbare module.

+ Als u het model wilt gebruiken om te scoren, voegt u de module **Scoremodel** toe aan een pijplijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 