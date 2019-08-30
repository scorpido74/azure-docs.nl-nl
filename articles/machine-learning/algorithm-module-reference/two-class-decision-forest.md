---
title: 'Besluitvormings forest met twee klassen: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module beslissings forest met twee klassen in Azure Machine Learning service om een machine learning model te maken op basis van het algoritme voor de beslissings bossen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37a2ce77e438145219df9cb553d1881626e8a2c6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128397"
---
# <a name="two-class-decision-forest-module"></a>Module voor beslissings forest met twee klassen

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een machine learning model te maken op basis van het algoritme voor de beslissings bossen.  

Beslissings bossen zijn snelle, onder Super visie gemodelleerde ensemble-modellen. Deze module is een goede keuze als u een doel wilt voors pellen met Maxi maal twee resultaten. 

## <a name="understanding-decision-forests"></a>Informatie over beslissings bossen

Deze beslissings forest-algoritme is een ensemble-leer methode die is bedoeld voor classificatie taken. Ensemble-methoden zijn gebaseerd op het algemene principe dat in plaats van op één model moet worden gebruikgemaakt, u krijgt betere resultaten en een meer gegeneraliseerde model door meerdere gerelateerde modellen te maken en deze op een of andere manier te combi neren. In het algemeen bieden ensemble-modellen betere dekking en nauw keurigheid dan enkelvoudige beslissings structuren. 

Er zijn veel manieren om afzonderlijke modellen te maken en deze te combi neren in een ensemble. Deze specifieke implementatie van een besluit forest werkt door meerdere beslissings structuren te bouwen en vervolgens te **stemmen** op de meest populaire uitvoer klasse. Stemmen is een van de betere bekende methoden voor het genereren van resultaten in een ensembles model. 

+ Er worden veel afzonderlijke classificatie structuren gemaakt met behulp van de volledige gegevensset, maar verschillende (meestal wille keurig) begin punten. Dit wijkt af van de benadering van een wille keurige forest, waarbij de afzonderlijke beslissings structuren mogelijk alleen een wille keurig deel van de gegevens of functies gebruiken.
+ Elke structuur in de structuur van het beslissings forest voert een niet-genormaliseerd frequentie histogram van labels uit. 
+ Het aggregatie proces berekent deze histogrammen en normaliseert het resultaat om de ' kansen ' voor elk label op te halen. 
+ De structuren die een hoog Voorspellings betrouwbaarheids niveau hebben, hebben een groter gewicht in de definitieve beslissing van de ensemble.

Beslissings structuren in het algemeen hebben veel voor delen voor classificatie taken:
  
- Ze kunnen niet-lineaire beslissings grenzen vastleggen.
- U kunt veel gegevens trainen en voors pellen, omdat ze efficiënt zijn in berekeningen en geheugen gebruik.
- De functie selectie is geïntegreerd in de trainings-en classificatie processen.  
- Structuren kunnen ruis gegevens en veel functies bevatten.  
- Dit zijn niet-parametrische modellen, wat betekent dat ze gegevens kunnen verwerken met verschillende distributies. 

Eenvoudige beslissings structuren kunnen echter overfit aan gegevens en zijn minder generaliseer dan een boom ensemble.

Zie [Decision bossen](https://go.microsoft.com/fwlink/?LinkId=403677)voor meer informatie.  

## <a name="how-to-configure"></a>Configureren
  
1.  Voeg de module **beslissings forest met twee klassen** toe aan uw experiment in azure machine learning en open het deel venster **Eigenschappen** van de module. 

    U kunt de module vinden onder **machine learning**. Vouw **initialiseren**en vervolgens **classificatie**uit.  
  
2.  Kies voor **resampling methode**de methode die is gebruikt voor het maken van de afzonderlijke structuren.  U kunt kiezen uit een of meer van de **replicatie**.  
  
    -   Overbaggen: De functie voor het verzamelen van de functie wordt ook *Boots trap aggregatie*genoemd. In deze methode wordt elke structuur op een nieuw voor beeld gekweekt, gemaakt door de oorspronkelijke gegevensset wille keurig te bemonsteren, totdat u een gegevensset hebt die de oorspronkelijke grootte heeft.  
  
         De uitvoer van de modellen worden gecombineerd door middel van *stemmen*. Dit is een vorm van aggregatie. Elke structuur in een forest met classificatie beslissingen voert een niet-genormaliseerd frequentie histogram van labels uit. De aggregatie is het opsommen van deze histogrammen en normaliseren om de ' kansen ' voor elk label op te halen. Op deze manier hebben de structuren met een hoog Voorspellings betrouwbaarheids niveau een groter gewicht in de definitieve beslissing van de ensemble.  
  
         Zie de Wikipedia-vermelding voor Boots trap aggregatie voor meer informatie.  
  
    -   **Repliceren**: In replicatie wordt elke structuur getraind op exact dezelfde invoer gegevens. De bepaling waarvan het gesplitste predicaat voor elk structuur knooppunt wordt gebruikt, blijft wille keurig en de structuur is divers.   
  
3.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: Als u weet hoe u het model wilt configureren, kunt u een specifieke set waarden als argumenten opgeven.
  
4.  Voor het **aantal beslissings structuren**typt u het maximum aantal beslissings structuren dat kan worden gemaakt in de ensemble. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar de trainings tijd wordt verhoogd.  
  
    > [!NOTE]
    >  Deze waarde bepaalt ook het aantal structuren dat wordt weer gegeven wanneer het getrainde model wordt gevisualiseerd. Als u één boom structuur wilt zien of afdrukken, kunt u de waarde instellen op 1. Er kan echter maar één structuur worden geproduceerd (de boom structuur met de initiële set para meters) en er worden geen verdere herhalingen uitgevoerd.
  
5.  Voor **de maximale diepte van de beslissings structuren**typt u een getal om de maximale diepte van een beslissings structuur te beperken. Het verg Roten van de diepte van de structuur kan nauw keuriger zijn, met het risico van een aantal verdeelde en verhoogde opleidings tijd.
  
6.  Voor het **aantal wille keurige splitsingen per knoop punt**typt u het aantal splitsingen dat moet worden gebruikt bij het maken van elk knoop punt van de structuur. Een *splitsing* houdt in dat onderdelen in elk niveau van de structuur (knoop punt) wille keurig worden gedeeld.
  
7.  Geef voor het **minimum aantal steek proeven per blad knooppunt**het minimale aantal cases op dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.
  
     Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de trainings gegevens ten minste vijf gevallen bevatten die voldoen aan dezelfde voor waarden.  
  
8.  Selecteer de optie **onbekende waarden toestaan voor categorische functies** om een groep te maken voor onbekende waarden in de trainings-of validatie sets. Het model is mogelijk minder nauw keurig voor bekende waarden, maar het kan betere voor spellingen bieden voor nieuwe (onbekende) waarden. 

     Als u deze optie uitschakelt, kan het model alleen de waarden accepteren die deel uitmaken van de trainings gegevens.
  
9. Koppel een gegevensset met een label en een van de [trainings modules](module-reference.md):  
  
    -   Als u de **modus trainer maken** instelt op **één para meter**, gebruikt u de module [Train model](./train-model.md) .  
  
    
## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Klik met de rechter muisknop op de uitvoer van de module [Train model](./train-model.md) en selecteer visualiseren om de structuur weer te geven dieop elke iteratie is gemaakt.
  
    Klik op elke structuur om in te zoomen op de splitsingen en Bekijk de regels voor elk knoop punt.

+ Als u een moment opname van het model wilt opslaan, klikt u met de rechter muisknop op de getrainde **model** uitvoer en selecteert u **model opslaan**. Het opgeslagen model wordt niet bijgewerkt bij opeenvolgende uitvoeringen van het experiment.

+ Als u het model voor scores wilt gebruiken, voegt u de module **score model** toe aan een experiment.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 