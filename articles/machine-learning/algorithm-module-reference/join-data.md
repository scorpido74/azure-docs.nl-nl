---
title: 'Gegevens deelnemen: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de join-gegevensmodule in Azure Machine Learning om gegevenssets samen te voegen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 38606f424e38fc68519181f485b5b698d0705d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477575"
---
# <a name="join-data"></a>Gegevens samenvoegen

In dit artikel wordt beschreven hoe u de module **Join Data** gebruiken in azure machine learning-ontwerper (voorbeeld) om twee gegevenssets samen te voegen met behulp van een joinbewerking in databasestijl.  

## <a name="how-to-configure-join-data"></a>Joingegevens configureren

Als u een join op twee gegevenssets wilt uitvoeren, moeten deze worden gerelateerd door een sleutelkolom. Samengestelde toetsen met meerdere kolommen worden ook ondersteund. 

1. Voeg de gegevenssets toe die u wilt combineren en sleep de module **Join-gegevens** in uw pijplijn. 

    U de module vinden in de categorie **Gegevenstransformatie** onder **Manipulatie**.

1. Verbind de gegevenssets met de module **Join Data.** 
 
1. Selecteer **Kolomkiezer starten** om toetskolom(en) te kiezen. Vergeet niet om kolommen te kiezen voor zowel de linker- als de rechterinvoer.

    Voor één sleutel:

    Selecteer één sleutelkolom voor beide ingangen.
    
    Voor een samengestelde sleutel:

    Selecteer alle belangrijke kolommen van links invoer en rechts invoer in dezelfde volgorde. De module **Join data** wordt aan de tabellen gekoppeld wanneer alle belangrijke kolommen overeenkomen. Schakel de optie **Duplicaten toestaan en de kolomvolgorde behouden in selectie** als de kolomvolgorde niet hetzelfde is als de oorspronkelijke tabel. 

    ![kolomkiezer](media/module/join-data-column-selector.png)


1. Selecteer de optie **Kwestie overeenkomen** als u de hoofdlettergevoeligheid wilt behouden voor een join van een tekstkolom. 
   
1. Gebruik de **vervolgkeuzelijst Join type** om op te geven hoe de gegevenssets moeten worden gecombineerd.  
  
    * **Inner Join**: Een *innerlijke join* is de meest voorkomende join operatie. De gecombineerde rijen worden alleen geretourneerd wanneer de waarden van de toetskolommen overeenkomen.  
  
    * **Links Buitenlid Join**: Een *linker buitenste join* retourneert samengevoegde rijen voor alle rijen van de linkertabel. Wanneer een rij in de linkertabel geen overeenkomende rijen in de rechtertabel heeft, bevat de geretourneerde rij ontbrekende waarden voor alle kolommen die uit de juiste tabel komen. U ook een vervangingswaarde opgeven voor ontbrekende waarden.  
  
    * **Volledige outer join**: Een *volledige buitenste join* retourneert alle rijen uit de linkertabel **(tabel1**) en van de rechtertabel **(tabel2**).  
  
         Voor elk van de rijen in een van de tabel die geen overeenkomende rijen in de andere hebben, bevat het resultaat een rij met ontbrekende waarden.  
  
    * **Links Semi-Join**: Een *linker semi-join* retourneert alleen de waarden uit de linkertabel wanneer de waarden van de belangrijkste kolommen overeenkomen.  

1. Ga voor de optie **Rechter sleutelkolommen in de samengevoegde tabel houden:**

    * Selecteer deze optie om de toetsen uit beide invoertabellen weer te geven.
    * Schakel de selectie uit om alleen de toetskolommen van de linkerinvoer terug te geven.

1. Verzend de pijplijn.

1. Als u de resultaten wilt weergeven, klikt u met de rechtermuisknop op **De samengevoegde gegevens** en selecteert u **Visualiseren**.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 