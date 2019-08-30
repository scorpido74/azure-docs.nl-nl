---
title: 'Gegevens samen voegen: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module koppeling samen voegen in Azure Machine Learning-service voor het samen voegen van gegevens sets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: 5fdb822a445e8a62cd36e6dd05aa218b9b41ce9e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128743"
---
# <a name="join-data"></a>Gegevens samenvoegen

In dit artikel wordt beschreven hoe u de module invoeg **gegevens** gebruikt in de Visual Interface van de Azure machine learning-service om twee gegevens sets samen te voegen met behulp van een koppelings bewerking in de database stijl.  

## <a name="how-to-configure-join-data"></a>Koppelings gegevens configureren

Als u een koppeling wilt uitvoeren op twee gegevens sets, moeten deze worden gerelateerd aan een sleutel kolom. Samengestelde sleutels met behulp van meerdere kolommen worden ook ondersteund. 

1. Voeg de gegevens sets toe die u wilt combi neren en sleep de module **samen voegen met gegevens** naar uw experiment. 

    U kunt de module in de categorie **gegevens transformatie** vinden onder **bewerken**.

1. Verbind de gegevens sets met de module voor **samen voegen** . 
 
1. Selecteer **starten kolom selecteren** om de sleutel kolom (men) te kiezen. Denk eraan dat u kolommen kiest voor de linker-en rechter invoer.

    Voor één sleutel:

    Selecteer één sleutel kolom voor beide invoer.
    
    Voor een samengestelde sleutel:

    Selecteer alle sleutel kolommen van links invoer en rechts invoer in dezelfde volg orde. De module **gegevens samen voegen** voegt de tabellen samen wanneer alle sleutel kolommen overeenkomen. Schakel de optie **dubbele waarden toestaan en kolom volgorde in selectie behouden in** als de volg orde van de kolom niet gelijk is aan de oorspronkelijke tabel. 

    ![kolom-selector](media/module/join-data-column-selector.png)


1. Selecteer de optie **hoofdletter gebruik** als u de hoofdletter gevoeligheid voor een tekst kolom koppeling wilt behouden. 
   
1. Gebruik de vervolg keuzelijst **type samen voegen** om op te geven hoe de gegevens sets moeten worden gecombineerd.  
  
    * **Inner join**: Een *inner join* is de meest voorkomende bewerking voor samen voegen. Het retourneert de gecombineerde rijen alleen wanneer de waarden van de sleutel kolommen overeenkomen.  
  
    * **Left outer join**: Een *left outer join* retourneert samengevoegde rijen voor alle rijen uit de linkertabel. Wanneer een rij in de linkertabel geen overeenkomende rijen in de rechter tabel heeft, bevat de geretourneerde rij ontbrekende waarden voor alle kolommen uit de rechter tabel. U kunt ook een vervangings waarde voor ontbrekende waarden opgeven.  
  
    * **Volledige outer join**: Een *full outer samen voegen* retourneert alle rijen uit de linkertabel (**Tabel1**) en uit de rechter tabel (**tabel2**).  
  
         Voor elk van de rijen in een tabel die geen overeenkomende rijen heeft, bevat het resultaat een rij met ontbrekende waarden.  
  
    * **Linker semi-koppeling**: Een *Left-koppeling* retourneert alleen de waarden uit de linkertabel wanneer de waarden van de sleutel kolommen overeenkomen.  

1. Voor de optie **behoud de juiste sleutel kolommen in een gekoppelde tabel**:

    * Selecteer deze optie om de sleutels uit beide invoer tabellen weer te geven.
    * Schakel deze optie uit als u de sleutel kolommen alleen wilt retour neren van de invoer links.

1. Voer het experiment uit of selecteer de module samenvoegings gegevens en de geselecteerde **uitvoering** om de koppeling uit te voeren.

1. Als u de resultaten wilt weer geven, klikt u met de rechter muisknop op de**resultaten gegevensset** > voor gegevens > samenvoeging.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 