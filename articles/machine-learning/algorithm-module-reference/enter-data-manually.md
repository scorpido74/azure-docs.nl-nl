---
title: 'Gegevens hand matig invoeren: module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module gegevens hand matig invoeren in Azure Machine Learning service om een kleine gegevensset te maken door waarden te typen. De gegevensset kan meerdere kolommen bevatten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: bd5fb636dbf79534e84a21a461f8912ceb6d9633
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693165"
---
# <a name="enter-data-manually-module"></a>De module gegevens hand matig invoeren

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om een kleine gegevensset te maken door waarden te typen. De gegevensset kan meerdere kolommen bevatten.
  
Deze module kan handig zijn in scenario's zoals deze:  
  
- Een kleine set waarden voor testen genereren  
  
- Een korte lijst met labels maken
  
- Een lijst met kolom namen typen die in een gegevensset moeten worden ingevoegd

## <a name="enter-data-manually"></a>Gegevens handmatig invoeren 
  
1.  Voeg de module [gegevens hand matig invoeren](./enter-data-manually.md) toe aan de pijp lijn. U kunt deze module vinden in de categorie **gegevens invoer en uitvoer** in azure machine learning. 
  
2.  Selecteer een van de volgende opties voor **DataFormat**. Deze opties bepalen hoe de gegevens die u opgeeft, moeten worden geparseerd. De vereisten voor elke indeling verschillen aanzienlijk, dus zorg ervoor dat u de verwante onderwerpen leest.  
  
    -   **ARFF**. De kenmerk relatie bestands indeling, die wordt gebruikt door Fridge.   
  
    -   **CSV**. Indeling met door komma's gescheiden waarden. Zie [Convert to CSV](./convert-to-csv.md)(Engelstalig) voor meer informatie.  
  
    -   **SVMLight**. Een indeling die wordt gebruikt door Vowpal Wabbit en andere machine learning frameworks.  
  
    -   **TSV**. Indeling van door tabs gescheiden waarden.

     Als u een indeling kiest en geen gegevens levert die voldoen aan de indelings specificaties, treedt er een runtime fout op.
  
3.  Klik in het tekstvak **Data** om te beginnen met het invoeren van gegevens. Voor de volgende indelingen is speciale aandacht vereist:  
  
    - **CSV**: als u meerdere kolommen wilt maken, plakt u een door komma's gescheiden tekst of typt u meerdere kolommen met komma's tussen velden.
  
        Als u de optie **HasHeader** selecteert, kunt u de eerste rij met waarden gebruiken als de kolomkop.  
  
        Als u deze optie uitschakelt, worden de kolommen namen, Kol1, col2, enzovoort, gebruikt. U kunt later kolom namen toevoegen of wijzigen met behulp van [meta gegevens bewerken](./edit-metadata.md).  
  
    - **TSV**: als u meerdere kolommen wilt maken, plakt u een door tabs gescheiden tekst of typt u meerdere kolommen met tabs tussen velden.  
  
        Als u de optie **HasHeader** selecteert, kunt u de eerste rij met waarden gebruiken als de kolomkop.  
  
        Als u deze optie uitschakelt, worden de kolommen namen, Kol1, col2, enzovoort, gebruikt. U kunt later kolom namen toevoegen of wijzigen met behulp van [meta gegevens bewerken](./edit-metadata.md).  
  
    -   **ARFF**: plakken in een bestaand ARFF-indelings bestand. Als u waarden rechtstreeks typt, moet u de optionele header en de vereiste kenmerk velden toevoegen aan het begin van de gegevens. 
    
        De volgende koptekst-en kenmerk rijen kunnen bijvoorbeeld worden toegevoegd aan een eenvoudige lijst. De kolomkop zou worden `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Typ of plak waarden in de SVMLight-indeling.  
  
        Het volgende voor beeld vertegenwoordigt bijvoorbeeld de eerste paar regels van de gegevensset van de bloed donatie, in SVMight-indeling:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Wanneer u de module [gegevens hand matig invoeren](./enter-data-manually.md) uitvoert, worden deze regels als volgt geconverteerd naar een gegevensset van kolommen en index waarden:  
  
        |Kol1|Col2|Col3|Col4|Labels|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Druk na elke rij op ENTER om een nieuwe regel te starten.  
  
     **Druk op ENTER na de laatste rij.** 
     
     Als u meerdere keren op ENTER drukt om meerdere lege navolgende rijen toe te voegen, wordt de laatste lege rij verwijderd, maar andere lege rijen worden beschouwd als ontbrekende waarden.  
  
     Als u rijen met ontbrekende waarden maakt, kunt u deze altijd later filteren.  
  
5.  Klik met de rechter muisknop op de module en selecteer **uit te voeren selectie** voor het parseren van de gegevens en laad deze in uw werk ruimte als een gegevensset.  
  
     Als u de gegevensset wilt weer geven, klikt u op de uitvoer poort en selecteert u **visualiseren**.  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 