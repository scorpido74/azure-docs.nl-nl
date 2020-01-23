---
title: 'Regressie van de beslissings structuur verbeterd: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u met behulp van de versterkte regressie module voor de beslissings structuur in Azure Machine Learning een ensemble van regressie structuren kunt maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: fb3ff990dcc207fd6dff6073c7464b6e410431af
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546874"
---
# <a name="boosted-decision-tree-regression-module"></a>Regressie module voor versterkte beslissings structuur

In dit artikel wordt een module in Azure Machine Learning Designer (preview) beschreven.

Gebruik deze module om een ensemble van regressie structuren te maken met behulp van Boosting. *Versterking* houdt in dat elke boom structuur afhankelijk is van eerdere structuren. Het algoritme leert u door het aanpassen van de rest van de bomen die erop werden gevolgd. Zo kunt u in een beslissings structuur een ensemble verhogen om de nauw keurigheid van een klein risico van minder behoefte aan te nemen.  
  
Deze regressie methode is een *gelabelde*leer methode en vereist daarom een gegevensset met de naam. De label kolom moet numerieke waarden bevatten.  

> [!NOTE]
> Gebruik deze module alleen met gegevens sets die numerieke variabelen gebruiken.  

Nadat u het model hebt gedefinieerd, traint u het met het [Train model](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Meer informatie over versterkte regressie structuren  

Versterking is een van de klassieke methoden voor het maken van ensemble-modellen, samen met behulp van de inbagging, wille keurige forests enzovoort.  In Azure Machine Learning gebruiken versterkte beslissings structuren een efficiënte implementatie van het Boost-algoritme voor de bloedkleur overgang. Verbetering van de kleur overgang is een machine learning techniek voor regressie problemen. Hiermee wordt elke regressie structuur op een stapsgewijse manier gebouwd, waarbij een vooraf gedefinieerde verlies functie wordt gebruikt om de fout in elke stap te meten en in de volgende weer te corrigeren. Daarom is het Voorspellings model eigenlijk een ensemble van zwakkere Voorspellings modellen.  
  
Bij regressie problemen bouwt het versterking van een reeks structuren op een stapsgewijse manier en selecteert de optimale boom structuur met een wille keurige differentiatie functie.  
  
Zie de volgende artikelen voor meer informatie:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    In dit Wikipedia-artikel over verloop versterking wordt een achtergrond op gestimuleerde structuren geboden. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Micro soft Research: van RankNet tot LambdaRank naar LambdaMART: een overzicht. Door J.C. Burges.

De methode voor het verhogen van de kleur overgang kan ook worden gebruikt voor classificatie problemen door deze te verminderen tot regressie met een geschikte verlies functie. Zie voor meer informatie over de implementatie van de versterkte bomen voor classificatie taken, [twee klasse boosted beslissings structuur](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Verbetering van de regressie beslissings structuur configureren

1.  Voeg de **Uitgeboostte beslissings structuur** module toe aan uw pijp lijn. U kunt deze module vinden onder **machine learning**, **initialiseren**, onder de categorie **regressie** . 
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: Selecteer deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden als argumenten op.  
   
  
3. **Maximum aantal Leaves per boom structuur**: Hiermee geeft u het maximum aantal Terminal knooppunten (bladeren) op dat in een wille keurige structuur kan worden gemaakt.  

    Door deze waarde te verg Roten, kunt u de grootte van de structuur verg Roten, waardoor het risico op overschrijding en een langere tijd beter is.  

4. **Minimum aantal steek proeven per blad knooppunt**: geeft het minimum aantal cases aan dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.

    Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de opleidings gegevens ten minste 5 gevallen bevatten die aan dezelfde voor waarden voldoen.

5. **Leer tempo**: Typ een getal tussen 0 en 1 dat de stap grootte tijdens het leren definieert. Het leer tempo bepaalt hoe snel of traag de kenniser convergeert met de optimale oplossing. Als de grootte van de stap te groot is, kunt u de optimale oplossing overschrijden. Als de grootte van de stap te klein is, neemt de training meer tijd in beslag op de beste oplossing.

6. **Aantal geconstrueerde structuren**: hier wordt het totale aantal beslissings structuren aangegeven dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar de trainings tijd wordt verhoogd.

    Deze waarde bepaalt ook het aantal structuren dat wordt weer gegeven wanneer het getrainde model wordt gevisualiseerd. Als u één boom structuur wilt zien of afdrukken, kunt u de waarde instellen op 1. Er wordt echter slechts één structuur geproduceerd (de boom structuur met de initiële set para meters) en er worden geen verdere herhalingen uitgevoerd.

7. **Wille keurig getal Seed**: Typ een optioneel niet-negatief geheel getal dat moet worden gebruikt als de wille keurige Seed-waarde. Het opgeven van een Seed zorgt voor een reproduceer baarheid van alle uitvoeringen die dezelfde gegevens en para meters hebben.

    Standaard is de wille keurige Seed ingesteld op 0, wat betekent dat de aanvankelijke Seed-waarde wordt opgehaald uit de systeem klok.
  
8. **Onbekende categorische-niveaus toestaan**: Selecteer deze optie als u een groep wilt maken voor onbekende waarden in de trainings-en validatie sets. Als u deze optie uitschakelt, kan het model alleen de waarden accepteren die deel uitmaken van de trainings gegevens. Het model is mogelijk minder nauw keurig voor bekende waarden, maar het kan betere voor spellingen bieden voor nieuwe (onbekende) waarden.

9. Voeg een trainings gegevensset en een van de trainings modules toe:

    - Als u de optie **trainers modus maken** instelt op **één para meter**, gebruikt u de module [Train model](train-model.md) .  
  
    

10. Voer de pijplijn uit.  
  
## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Als u het model voor scores wilt gebruiken, verbindt u het met het [score model](./score-model.md)om waarden te voors pellen voor nieuwe invoer voorbeelden.

+ Als u een moment opname van het getrainde model wilt opslaan, selecteert u het tabblad **uitvoer** in het rechterdeel venster van het **getrainde model** en klikt u op pictogram **gegevensset registreren** . De kopie van het getrainde model wordt als een module in de module structuur opgeslagen en wordt niet bijgewerkt op opeenvolgende uitvoeringen van de pijp lijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 
