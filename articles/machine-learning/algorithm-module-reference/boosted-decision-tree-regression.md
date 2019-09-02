---
title: 'Regressie verbetering van de beslissings structuur: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de versterkde regressie-module voor de beslissings structuur in Azure Machine Learning service om een ensemble van regressie structuren te maken met behulp van Boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 5f26dfbdd8d3ef094ed380b7bd00ab0169152502
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208162"
---
# <a name="boosted-decision-tree-regression-module"></a>Regressie module voor versterkte beslissings structuur

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een ensemble van regressie structuren te maken met behulp van Boosting. *Versterking* houdt in dat elke boom structuur afhankelijk is van eerdere structuren. Het algoritme leert u door het aanpassen van de rest van de bomen die erop werden gevolgd. Zo kunt u in een beslissings structuur een ensemble verhogen om de nauw keurigheid van een klein risico van minder behoefte aan te nemen.  
  
Deze regressie methode is een gelabelde leer methode en vereist daarom een gegevensset met de *naam*. De label kolom moet numerieke waarden bevatten.  

> [!NOTE]
> Gebruik deze module alleen met gegevens sets die numerieke variabelen gebruiken.  

Nadat u het model hebt gedefinieerd, traint u het met het [Train model](./train-model.md).

> [!TIP]
> Wilt u meer weten over de structuur die is gemaakt? Nadat het model is getraind, klikt u met de rechter muisknop op de uitvoer van de module [Train model](./train-model.md) en selecteert u visualiseren om de boom structuur te zien die op elke iteratie is gemaakt. U kunt inzoomen op de splitsingen voor elke structuur en de regels voor elk knoop punt bekijken.  
  
## <a name="more-about-boosted-regression-trees"></a>Meer informatie over versterkte regressie structuren  

Versterking is een van de klassieke methoden voor het maken van ensemble-modellen, samen met behulp van de inbagging, wille keurige forests enzovoort.  In Azure Machine Learning gebruiken versterkte beslissings structuren een efficiënte implementatie van het Boost-algoritme voor de bloedkleur overgang. Verbetering van de kleur overgang is een machine learning techniek voor regressie problemen. Hiermee wordt elke regressie structuur op een stapsgewijse manier gebouwd, waarbij een vooraf gedefinieerde verlies functie wordt gebruikt om de fout in elke stap te meten en in de volgende weer te corrigeren. Daarom is het Voorspellings model eigenlijk een ensemble van zwakkere Voorspellings modellen.  
  
Bij regressie problemen bouwt het versterking van een reeks structuren op een stapsgewijse manier en selecteert de optimale boom structuur met een wille keurige differentiatie functie.  
  
Zie de volgende artikelen voor meer informatie:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    In dit Wikipedia-artikel over verloop versterking wordt een achtergrond op gestimuleerde structuren geboden. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Van RankNet tot LambdaRank naar LambdaMART: Een overzicht. Door J.C. Burges.

De methode voor het verhogen van de kleur overgang kan ook worden gebruikt voor classificatie problemen door deze te verminderen tot regressie met een geschikte verlies functie. Zie voor meer informatie over de implementatie van de versterkte bomen voor classificatie taken, [twee klasse boosted beslissings structuur](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Verbetering van de regressie beslissings structuur configureren

1.  Voeg de **Uitgeboostte beslissings structuur** module toe aan uw experiment. U kunt deze module vinden onder **machine learning**, **initialiseren**, onder de categorie **regressie** . 
  
2.  Geef op hoe u wilt dat het model wordt getraind door de optie **trainer modus maken** in te stellen.  
  
    -   **Eén para meter**: Selecteer deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden als argumenten op.  
   
  
3. **Maximum aantal Leaves per boom structuur**: Hiermee wordt het maximum aantal Terminal knooppunten (Leaves) aangegeven dat in een structuur kan worden gemaakt.  

    Door deze waarde te verg Roten, kunt u de grootte van de structuur verg Roten, waardoor het risico op overschrijding en een langere tijd beter is.  

4. **Minimum aantal steek proeven per blad knooppunt**: Geef het minimum aantal cases aan dat nodig is om een Terminal knooppunt (Leaf) in een structuur te maken.

    Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaard waarde 1 kan zelfs een enkele case ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde op 5 verhoogt, moeten de opleidings gegevens ten minste 5 gevallen bevatten die aan dezelfde voor waarden voldoen.

5. **Leer tempo**: Typ een getal tussen 0 en 1 dat de stap grootte tijdens het leren definieert. Het leer tempo bepaalt hoe snel of traag de kenniser convergeert met de optimale oplossing. Als de grootte van de stap te groot is, kunt u de optimale oplossing overschrijden. Als de grootte van de stap te klein is, neemt de training meer tijd in beslag op de beste oplossing.

6. **Aantal geconstrueerde structuren**: Geef het totaal aantal beslissings structuren op dat in de ensemble moet worden gemaakt. Door meer beslissings structuren te maken, kunt u een betere dekking krijgen, maar de trainings tijd wordt verhoogd.

    Deze waarde bepaalt ook het aantal structuren dat wordt weer gegeven wanneer het getrainde model wordt gevisualiseerd. Als u een Ingle-structuur wilt zien of afdrukken, kunt u de waarde instellen op 1. Er wordt echter slechts één structuur geproduceerd (de boom structuur met de initiële set para meters) en er worden geen verdere herhalingen uitgevoerd.

7. **Wille keurig aantal Seed**: Typ een optioneel niet-negatief geheel getal dat moet worden gebruikt als de wille keurige Seed-waarde. Het opgeven van een Seed zorgt voor een reproduceer baarheid van alle uitvoeringen die dezelfde gegevens en para meters hebben.

    Standaard is de wille keurige Seed ingesteld op 0, wat betekent dat de aanvankelijke Seed-waarde wordt opgehaald uit de systeem klok.
  
8. **Onbekende categorische-niveaus toestaan**: Selecteer deze optie als u een groep wilt maken voor onbekende waarden in de trainings-en validatie sets. Als u deze optie uitschakelt, kan het model alleen de waarden accepteren die deel uitmaken van de trainings gegevens. Het model is mogelijk minder nauw keurig voor bekende waarden, maar het kan betere voor spellingen bieden voor nieuwe (onbekende) waarden.

9. Voeg een trainings gegevensset en een van de trainings modules toe:

    - Als u de optie **trainers modus maken** instelt op **één para meter**, gebruikt u de module [Train model](train-model.md) .  
  
    

10. Voer het experiment uit.  
  
## <a name="results"></a>Resultaten

Nadat de training is voltooid:

+ Klik met de rechter muisknop op de uitvoer van de module [Train model](train-model.md) en selecteer visualiseren om de structuur weer te gevendie op elke iteratie is gemaakt.
  
     Klik op elke structuur om in te zoomen op de splitsingen en Bekijk de regels voor elk knoop punt.  

+ Als u het model voor scores wilt gebruiken, verbindt u het met het [score model](./score-model.md)om waarden te voors pellen voor nieuwe invoer voorbeelden.

+ Als u een moment opname van het getrainde model wilt opslaan, klikt u met de rechter muisknop op de getrainde **model** uitvoer van de trainings module en selecteert u **Opslaan als**. Het exemplaar van het getrainde model dat u opslaat, wordt niet bijgewerkt bij opeenvolgende uitvoeringen van het experiment.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 
