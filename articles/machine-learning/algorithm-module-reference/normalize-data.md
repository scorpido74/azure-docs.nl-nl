---
title: 'Gegevens normaliseren: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Gegevens normaliseren in Azure Machine Learning om een gegevensset te transformeren door middel *van normalisatie*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477524"
---
# <a name="normalize-data-module"></a>Gegevensmodule normaliseren

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een gegevensset te transformeren door middel *van normalisatie.*

Normalisatie is een techniek die vaak wordt toegepast als onderdeel van gegevensvoorbereiding voor machine learning. Het doel van normalisatie is om de waarden van numerieke kolommen in de gegevensset te wijzigen om een gemeenschappelijke schaal te gebruiken, zonder verschillen in waardenbereiken te verstoren of informatie te verliezen. Normalisatie is ook vereist voor sommige algoritmen om de gegevens correct te modelleren.

Stel dat uw invoergegevensset één kolom bevat met waarden variërend van 0 tot 1 en een andere kolom met waarden variërend van 10.000 tot 100.000. Het grote verschil in de *schaal* van de getallen kan problemen veroorzaken wanneer u probeert om de waarden te combineren als functies tijdens het modelleren.

*Normalisering* voorkomt deze problemen door nieuwe waarden te maken die de algemene verdeling en verhoudingen in de brongegevens behouden, terwijl waarden binnen een schaal blijven die wordt toegepast op alle numerieke kolommen die in het model worden gebruikt.

Deze module biedt verschillende opties voor het transformeren van numerieke gegevens:

- U alle waarden wijzigen in een schaal van 0-1 of de waarden transformeren door ze te vertegenwoordigen als percentielrangen in plaats van absolute waarden.
- U normalisatie toepassen op één kolom of op meerdere kolommen in dezelfde gegevensset.
- Als u de pijplijn moet herhalen of dezelfde normalisatiestappen op andere gegevens moet toepassen, u de stappen opslaan als een normalisatietransformatie en deze toepassen op andere gegevenssets met hetzelfde schema.

> [!WARNING]
> Sommige algoritmen vereisen dat gegevens worden genormaliseerd voordat ze een model trainen. Andere algoritmen voeren hun eigen gegevensschaling of normalisatie uit. Wanneer u daarom een machine learning-algoritme kiest om te gebruiken bij het bouwen van een voorspellend model, moet u de gegevensvereisten van het algoritme controleren voordat u normalisatie toepast op de trainingsgegevens.

##  <a name="configure-normalize-data"></a>Normalize-gegevens configureren

U met deze module slechts één normalisatiemethode tegelijk toepassen. Daarom wordt dezelfde normalisatiemethode toegepast op alle kolommen die u selecteert. Als u verschillende normalisatiemethoden wilt gebruiken, gebruikt u een tweede instantie **van Gegevens normaliseren**.

1. Voeg de module **Gegevens normaliseren** toe aan uw pijplijn. U vindt de module In Azure Machine Learning, onder **Gegevenstransformatie,** in de categorie **Schalen en Verkleinen.**

2. Sluit een gegevensset aan die ten minste één kolom van alle getallen bevat.

3. Gebruik de kolomkiezer om de numerieke kolommen te kiezen om te normaliseren. Als u geen afzonderlijke kolommen kiest, worden standaard **alle** numerieke typekolommen in de invoer opgenomen en wordt hetzelfde normalisatieproces toegepast op alle geselecteerde kolommen. 

    Dit kan leiden tot vreemde resultaten als u numerieke kolommen die niet moeten worden genormaliseerd! Controleer de kolommen altijd zorgvuldig.

    Als er geen numerieke kolommen worden gedetecteerd, controleert u de kolommetagegevens om te controleren of het gegevenstype van de kolom een ondersteund numeriek type is.

    > [!TIP]
    > Als u ervoor wilt zorgen dat kolommen van een bepaald type als invoer worden geleverd, probeert u de module [Kolommen selecteren in gegevensset](./select-columns-in-dataset.md) te gebruiken voordat **gegevens worden genormaliterd**.

4. **Gebruik 0 voor constante kolommen wanneer deze optie wordt ingeschakeld:** Selecteer deze optie wanneer een numerieke kolom één onveranderlijke waarde bevat. Dit zorgt ervoor dat dergelijke kolommen niet worden gebruikt bij normalisatiebewerkingen.

5. Kies in de vervolgkeuzelijst **Transformatiemethode** één wiskundige functie die u wilt toepassen op alle geselecteerde kolommen. 
  
    - **Zscore**: Converteert alle waarden naar een z-score.
    
      De waarden in de kolom worden getransformeerd met de volgende formule:  
  
      ![normalisatie met z&#45;scores](media/module/aml-normalization-z-score.png)
  
      Gemiddelde en standaarddeviatie worden voor elke kolom afzonderlijk berekend. De standaarddeviatie van de populatie wordt gebruikt.
  
    - **MinMax**: De min-max normalisator reschaalt elke functie lineair naar het interval [0,1].
    
      Opnieuw schalen naar het interval [0,1] wordt gedaan door de waarden van elke functie te verschuiven, zodat de minimale waarde 0 is en vervolgens te delen door de nieuwe maximale waarde (het verschil tussen de oorspronkelijke maximale en minimale waarden).
      
      De waarden in de kolom worden getransformeerd met de volgende formule:  
  
      ![normalisatie met behulp van de min&#45;max-functie](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistiek**: De waarden in de kolom worden getransformeerd met behulp van de volgende formule:

      ![formule voor normalisatie door logistieke functie](media/module/aml-normalization-logistic.png "AML_normalization-logistiek")  
  
    - **LogNormal:** Deze optie converteert alle waarden naar een lognormale schaal.
  
      De waarden in de kolom worden getransformeerd met de volgende formule:
  
      ![formulelogboek&#45;normale verdeling](media/module/aml-normalization-lognormal.png "AML_normalization-lognormaal")
    
      Hier μ en σ zijn de parameters van de distributie, die empirisch van de gegevens als maximumwaarschijnlijkheidsramingen, voor elke kolom afzonderlijk wordt gegevens berekend.  
  
    - **TanH**: Alle waarden worden omgezet in een hyperbolische raaklijn.
    
      De waarden in de kolom worden getransformeerd met de volgende formule:
    
      ![normalisatie met de tanh-functie](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Verzend de pijplijn of dubbelklik op de module **Gegevens normaliseren** en selecteer **Geselecteerd uitvoeren**. 

## <a name="results"></a>Resultaten

De module **Gegevens normaliseren** genereert twee uitgangen:

- Als u de getransformeerde waarden wilt weergeven, klikt u met de rechtermuisknop op de module en selecteert u **Visualiseren**.

    Waarden worden standaard op hun plaats getransformeerd. Als u de getransformeerde waarden wilt vergelijken met de oorspronkelijke waarden, gebruikt u de module [Kolommen toevoegen](./add-columns.md) om de gegevenssets opnieuw te combineren en de kolommen naast elkaar weer te geven.

- Als u de transformatie wilt opslaan zodat u dezelfde normalisatiemethode op een andere gegevensset toepassen, selecteert u de module en selecteert u **Gegevensset Registreren** onder het tabblad **Uitvoer** in het rechterdeelvenster.

    U vervolgens de opgeslagen transformaties laden vanuit de groep **Transformaties** van het linkernavigatiedeelvenster en deze toepassen op een gegevensset met hetzelfde schema met behulp [van Transformatie toepassen](apply-transformation.md).  


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 