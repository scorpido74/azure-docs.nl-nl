---
title: 'Versterkte beslissings structuur met twee klassen: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de geboostte beslissings structuur module met twee klassen in Azure Machine Learning service om een machine learning model te maken dat is gebaseerd op het gestimuleerde beslissings structuren algoritme.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 5795dc994872ac5da37a1226524dddd35aa35126
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692637"
---
# <a name="two-class-boosted-decision-tree-module"></a>Uitmuntende beslissings structuur module met twee klassen

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een machine learning model te maken dat is gebaseerd op het boosted Decision Trees-algoritme. 

Een versterkte beslissings structuur is een ensemble leer methode waarbij de tweede structuur de fouten van de eerste structuur corrigeert, de derde structuur corrigeert voor de fouten van de eerste en tweede bomen, enzovoort.  Voor spellingen zijn gebaseerd op de hele ensemble van structuren, samen met de voor spelling.
  
Over het algemeen is het zo dat gestimuleerde beslissings structuren optimaal worden geconfigureerd, waarmee u de beste prestaties kunt verkrijgen voor een groot aantal machine learning taken. Ze zijn echter ook een van de meer geheugenintensieve leerers, en de huidige implementatie houdt alles in het geheugen. Daarom kan een verbeterd beslissings structuur model mogelijk niet de grote gegevens sets verwerken die sommige lineaire leerers kunnen verwerken.

## <a name="how-to-configure"></a>Configureren

Deze module maakt een niet-getraind classificatie model. Omdat classificatie een leer methode met toezicht is, hebt u een *gecodeerde gegevensset* nodig die een kolom Label bevat met een waarde voor alle rijen.

U kunt dit type model trainen met [Train model](././train-model.md). 

1.  Voeg in Azure Machine Learning de **Uitgeboostte beslissings structuur** module toe aan uw pijp lijn.
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.
  
    + **Eén para meter**: als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.
  
  
3.  Voor het **maximum aantal Leaves per structuur**geeft u het maximum aantal Terminal knooppunten (bladeren) op dat in een wille keurige structuur kan worden gemaakt.
  
     Door deze waarde te verg Roten, kunt u de grootte van de structuur verg Roten, waardoor het risico op overschrijding en een langere tijd beter is.
  
4.  Geef voor het **minimum aantal steek proeven per blad knooppunt**het aantal cases op dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.  
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de trainings gegevens ten minste vijf gevallen bevatten die voldoen aan dezelfde voor waarden.
  
5.  Voor het **leer tempo**typt u een getal tussen 0 en 1 dat de stap grootte definieert tijdens het leren.  
  
     Het leer tempo bepaalt hoe snel of traag de kenniser convergeert met de optimale oplossing. Als de grootte van de stap te groot is, is het mogelijk dat u de optimale oplossing kunt overschrijden. Als de grootte van de stap te klein is, neemt de training meer tijd in beslag op de beste oplossing.
  
6.  Geef voor **aantal geconstrueerde structuren**het totale aantal beslissings structuren op dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar wordt de trainings tijd verhoogd.
  
     Deze waarde bepaalt ook het aantal structuren dat wordt weer gegeven wanneer het getrainde model wordt gevisualiseerd. Als u één structuur wilt zien of afdrukken, stelt u de waarde in op 1. Wanneer u dit echter doet, wordt er slechts één boom structuur geproduceerd (de structuur met de initiële set para meters) en worden er geen verdere herhalingen uitgevoerd.
  
7.  Voor **wille keurig getal zaad**typt u optioneel een niet-negatief geheel getal dat moet worden gebruikt als de wille keurige Seed-waarde. Het opgeven van een Seed zorgt voor een reproduceer baarheid van alle uitvoeringen die dezelfde gegevens en para meters hebben.  
  
     De wille keurige Seed wordt standaard ingesteld op 0, wat betekent dat de aanvankelijke Seed-waarde wordt opgehaald uit de systeem klok.  Opeenvolgende uitvoeringen met een wille keurige Seed kan verschillende resultaten hebben.
  

9. Train het model.
  
    + Als u de **modus trainer maken** instelt op **één para meter**, verbindt u een gecodeerde gegevensset en de module [Train model](./train-model.md) .  
  
   
## <a name="results"></a>Resultaten

Nadat de model training is voltooid, klikt u met de rechter muisknop op de uitvoer van [Train model](./train-model.md) om de resultaten weer te geven:

+ Selecteer **visualiseren**om de structuur te zien die op elke iteratie is gemaakt. 
+ Als u wilt inzoomen op de splitsingen en de regels voor elk knoop punt wilt weer geven, klikt u op elke boom structuur.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 