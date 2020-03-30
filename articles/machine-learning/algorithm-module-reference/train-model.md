---
title: 'Treinmodel: modulereferentie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module **Treinmodel** in Azure Machine Learning om een classificatie- of regressiemodel te trainen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 0a9728e05aee27e74054a77e2c9be7dc08968207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455856"
---
# <a name="train-model-module"></a>Treinmodelmodule

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om een classificatie- of regressiemodel te trainen. Training vindt plaats nadat u een model hebt gedefinieerd en de parameters hebt ingesteld, en vereist gecodeerde gegevens. U **Train Model** ook gebruiken om een bestaand model om te scholen met nieuwe gegevens. 

## <a name="how-the-training-process-works"></a>Hoe het trainingsproces werkt

In Azure Machine Learning is het maken en gebruiken van een machine learning-model meestal een proces in drie stappen. 

1. U configureert een model door een bepaald type algoritme te kiezen en de parameters of hyperparameters te definiëren. Kies een van de volgende modeltypen: 

    + **Classificatiemodellen,** gebaseerd op neurale netwerken, beslissingsbomen en beslissingsbossen en andere algoritmen.
    + **Regressiemodellen,** die standaard lineaire regressie kunnen bevatten, of die andere algoritmen gebruiken, waaronder neurale netwerken en Bayesiaanse regressie.  

2. Geef een gegevensset op die is gelabeld en gegevens heeft die compatibel zijn met het algoritme. Sluit zowel de gegevens als het model aan **op Train Model**.

    Wat training oplevert is een specifiek binair formaat, de iLearner, dat de statistische patronen inkapselt die uit de gegevens zijn geleerd. U deze indeling niet rechtstreeks wijzigen of lezen; andere modules kunnen dit getrainde model echter gebruiken. 
    
    U ook de eigenschappen van het model weergeven. Zie de sectie Resultaten voor meer informatie.

3. Nadat de training is voltooid, gebruikt u het getrainde model met een van de [scoremodules](./score-model.md)om voorspellingen te doen over nieuwe gegevens.

## <a name="how-to-use-train-model"></a>Treinmodel gebruiken 
  
1.  Configureer in Azure Machine Learning een classificatiemodel of regressiemodel.
    
2. Voeg de module **Treinmodel** toe aan de pijplijn.  U vindt deze module onder de categorie **Machine Learning.** Vouw **Train**uit en sleep vervolgens de module **Treinmodel** in uw pijplijn.
  
3.  Bevestig aan de linkerkant de ongetrainde modus. Bevestig de trainingsgegevensset aan de rechterinvoer van **Train Model**.

    De trainingsgegevensset moet een labelkolom bevatten. Alle rijen zonder labels worden genegeerd.
  
4.  Klik bij **de kolom Label**op Kolom **bewerken** in het rechterdeelvenster van de module en kies één kolom met resultaten die het model kan gebruiken voor training.
  
    - Voor classificatieproblemen moet de labelkolom **categorische** waarden of **afzonderlijke** waarden bevatten. Enkele voorbeelden zijn een ja/nee-classificatie, een ziekteclassificatiecode of -naam of een inkomensgroep.  Als u een niet-categorische kolom kiest, geeft de module een fout tijdens de training.
  
    -   Voor regressieproblemen moet de labelkolom **numerieke** gegevens bevatten die de antwoordvariabele vertegenwoordigen. Idealiter vertegenwoordigen de numerieke gegevens een continue schaal. 
    
    Voorbeelden hiervan zijn een credit risk score, de verwachte tijd om te mislukken voor een harde schijf, of het voorspelde aantal oproepen naar een call center op een bepaalde dag of tijd.  Als u geen numerieke kolom kiest, krijgt u mogelijk een foutmelding.
  
    -   Als u niet opgeeft welke labelkolom u wilt gebruiken, probeert Azure Machine Learning met behulp van de metagegevens van de gegevensset af te leiden welke de juiste labelkolom is. Als de verkeerde kolom wordt geselecteerd, gebruikt u de kolomkiezer om deze te corrigeren.
  
    > [!TIP] 
    > Zie het artikel [Kolommen selecteren in gegevensset](./select-columns-in-dataset.md) voor tips als u problemen hebt met het gebruik van de kolomkiezer. Het beschrijft een aantal veelvoorkomende scenario's en tips voor het gebruik van de **opties MET REGELS** en **NAAM.**
  
5.  Verzend de pijplijn. Als u veel gegevens hebt, kan dit even duren.

## <a name="results"></a><a name="bkmk_results"></a>Resultaten

Nadat het model is opgeleid:


+ Als u het model in andere pijplijnen wilt gebruiken, selecteert u de module en selecteert u het pictogram **Gegevensset registreren** onder het tabblad **Uitvoer** in het rechterdeelvenster. U hebt toegang tot opgeslagen modellen in het modulepalet onder **Gegevenssets**.

+ Als u het model wilt gebruiken bij het voorspellen van nieuwe waarden, verbindt u het met de module [Scoremodel,](./score-model.md) samen met nieuwe invoergegevens.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 