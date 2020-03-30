---
title: 'Boosted Decision Tree Regression: Module Reference'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Boosted Decision Tree Regression in Azure Machine Learning om een ensemble van regressiebomen te maken met behulp van boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456723"
---
# <a name="boosted-decision-tree-regression-module"></a>De module Voor versterkte beslissingsstructuurregressie

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een ensemble van regressiebomen te maken met behulp van boosting. *Stimuleren* betekent dat elke boom afhankelijk is van eerdere bomen. Het algoritme leert door het aanbrengen van de restanten van de bomen die eraan vooraf gingen. Dus, het stimuleren van een beslissing boom ensemble heeft de neiging om de nauwkeurigheid te verbeteren met een klein risico van minder dekking.  
  
Deze regressiemethode is een begeleide leermethode en vereist daarom een *gelabelde gegevensset.* De labelkolom moet numerieke waarden bevatten.  

> [!NOTE]
> Gebruik deze module alleen met gegevenssets die numerieke variabelen gebruiken.  

Nadat u het model hebt gedefinieerd, traint u het met behulp van het [treinmodel.](./train-model.md)

  
## <a name="more-about-boosted-regression-trees"></a>Meer over versterkte regressiebomen  

Boosting is een van de vele klassieke methoden voor het maken van ensemble modellen, samen met zakken, willekeurige bossen, enzovoort.  In Azure Machine Learning gebruiken gepromote beslissingsbomen een efficiënte implementatie van het MART-verloopboosting-algoritme. Gradiëntstimuleren is een machine learning-techniek voor regressieproblemen. Het bouwt elke regressieboom op een stapsgewijze manier, met behulp van een vooraf gedefinieerde verliesfunctie om de fout in elke stap te meten en te corrigeren in de volgende. Aldus is het voorspellingsmodel eigenlijk een ensemble van zwakkere voorspellingsmodellen.  
  
In regressieproblemen bouwt het stimuleren van een reeks bomen op een stapsgewijze manier en selecteert vervolgens de optimale boom met behulp van een willekeurige differentieerbare verliesfunctie.  
  
Zie voor meer informatie de volgende artikelen:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Dit Wikipedia-artikel over gradiëntstimuleren biedt enige achtergrond informatie over gebooste bomen. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Van RankNet tot LambdaRank tot LambdaMART: Een overzicht. Door J.C. Burges.

De gradiëntboostmethode kan ook worden gebruikt voor classificatieproblemen door ze te reduceren tot regressie met een geschikte verliesfunctie. Zie [Two-Class Boosted Decision Tree](./two-class-boosted-decision-tree.md)voor meer informatie over de implementatie van gepromote bomen voor classificatietaken.  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Boosted Decision Tree Regression configureren

1.  Voeg de module **Boosted Decision Tree** toe aan uw pijplijn. U deze module vinden onder **Machine Learning**, **Initialiseren**, onder de categorie **Regressie.** 
  
2.  Geef op hoe u het model wilt trainen door de optie **Trainer-modus maken in** te stellen.  
  
    -   **Eén parameter:** selecteer deze optie als u weet hoe u het model wilt configureren en geef een specifieke set waarden op als argumenten. 
     
    -   **Parameterbereik:** Selecteer deze optie als u niet zeker bent van de beste parameters en een parametersweep wilt uitvoeren. Selecteer een reeks waarden om over te herhalen en de [Tune Model Hyperparameters](tune-model-hyperparameters.md) herhalen over alle mogelijke combinaties van de instellingen die u hebt opgegeven om de hyperparameters te bepalen die de optimale resultaten opleveren.    
   
  
3. **Maximum aantal bladeren per boom**: Geef het maximum aantal terminalknooppunten (bladeren) aan dat in elke boom kan worden gemaakt.  

    Door deze waarde te verhogen, u mogelijk de grootte van de boom vergroten en een betere precisie krijgen, met het risico van overfitting en langere trainingstijd.  

4. **Minimumaantal monsters per bladknooppunt**: Geef het minimumaantal gevallen aan dat nodig is om een eindknooppunt (blad) in een boom te maken.

    Door deze waarde te verhogen, verhoogt u de drempel voor het maken van nieuwe regels. Met de standaardwaarde van 1 kan bijvoorbeeld zelfs één aanvraag ertoe leiden dat een nieuwe regel wordt gemaakt. Als u de waarde verhoogt tot 5, moeten de trainingsgegevens ten minste 5 gevallen bevatten die aan dezelfde voorwaarden voldoen.

5. **Leersnelheid**: Typ een getal tussen 0 en 1 dat de stapgrootte definieert tijdens het leren. Het leerpercentage bepaalt hoe snel of langzaam de leerling convergeert op de optimale oplossing. Als de stapgrootte te groot is, u de optimale oplossing overshooten. Als de stapgrootte te klein is, duurt het langer om te convergeren naar de beste oplossing.

6. **Aantal bomen gebouwd**: Geef het totale aantal beslissingsbomen aan dat in het ensemble moet worden gemaakt. Door meer beslissingsbomen te maken, u mogelijk een betere dekking krijgen, maar de trainingstijd neemt toe.

    Deze waarde bepaalt ook het aantal bomen dat wordt weergegeven bij het visualiseren van het getrainde model. Als u één boom wilt zien of afdrukken, u de waarde instellen op 1; er wordt echter slechts één boom geproduceerd (de boom met de oorspronkelijke set parameters) en er worden geen verdere iteraties uitgevoerd.

7. **Willekeurig getalzaad**: Typ een optioneel niet-negatief geheel om te gebruiken als willekeurige zaadwaarde. Het opgeven van een zaad zorgt voor reproduceerbaarheid voor runs met dezelfde gegevens en parameters.

    Standaard wordt het willekeurige zaad ingesteld op 0, wat betekent dat de initiële zaadwaarde wordt verkregen uit de systeemklok.
  

9. Voeg een trainingsgegevensset en een van de trainingsmodules toe:

    - Als u de optie **De trainermodus maken** instelt op **Eén parameter,** gebruikt u de module [Treinmodel.](train-model.md)  
  
    

10. Verzend de pijplijn.  
  
## <a name="results"></a>Resultaten

Na de training is voltooid:

+ Als u het model wilt gebruiken om te scoren, verbindt u het met [scoremodel](./score-model.md)om waarden voor nieuwe invoervoorbeelden te voorspellen.

+ Als u een momentopname van het getrainde model wilt opslaan, selecteert u het tabblad **Uitvoer** in het rechterdeelvenster van **Het getrainde model** en klikt u op Pictogram **Gegevensset gegevensset registreren.** De kopie van het getrainde model wordt opgeslagen als een module in de modulestructuur en wordt niet bijgewerkt op opeenvolgende runs van de pijplijn.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
