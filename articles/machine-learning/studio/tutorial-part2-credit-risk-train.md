---
title: 'Zelf studie 2: Credit risico modellen trainen'
titleSuffix: ML Studio (classic) - Azure
description: Een gedetailleerde zelf studie waarin wordt getoond hoe u een predictive analytics oplossing kunt maken voor een beoordeling van een credit risico in Azure Machine Learning Studio (klassiek). Deze zelfstudie is deel twee van een driedelige reeks. Ze laat zien hoe u modellen moet trainen en evalueren.
keywords: kredietrisico, predictive analytics-oplossing, risico-evaluatie
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 8feca17f10bb891f0ca5577b2363f95901da4a46
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217875"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Zelf studie 2: Credit risico modellen trainen-Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

In deze zelfstudie wordt uitgebreid ingegaan op het ontwikkelingsproces van een predictive analytics-oplossing. U ontwikkelt een eenvoudig model in Machine Learning Studio (klassiek).  Vervolgens implementeert u het model als een Azure Machine Learning-webservice.  Dit geïmplementeerde model kan voorspellingen doen op basis van nieuwe gegevens. Deze zelfstudie is **deel twee van een driedelige reeks**.

Stel dat u iemands kredietrisico moet voorspellen op basis van de gegevens die deze persoon in een kredietaanvraag heeft ingevuld.  

Kredietrisicobeoordeling is een complex probleem, maar in deze zelfstudie wordt het enigszins vereenvoudigd. U gebruikt deze als voor beeld van hoe u een predictive analytics oplossing kunt maken met behulp van Microsoft Azure Machine Learning Studio (klassiek). U gebruikt Azure Machine Learning Studio (klassiek) en een Machine Learning-webservice voor deze oplossing.  

In deze driedelige zelfstudie begint u met openbaar beschikbare kredietrisicogegevens.  Vervolgens ontwikkelt en traint u een voorspellend model.  En ten slotte implementeert u het model als een webservice.

In [deel één van de zelf studie](tutorial-part1-credit-risk.md)hebt u een machine learning Studio (klassieke) werk ruimte gemaakt, gegevens geüpload en een experiment gemaakt.

In dit deel van de zelfstudie gaat u het volgende doen:
 
> [!div class="checklist"]
> * Meerdere modellen trainen
> * De modellen beoordelen en evalueren


In [deel drie van de zelfstudie](tutorial-part3-credit-risk-deploy.md), implementeert u het model als een webservice.

## <a name="prerequisites"></a>Vereisten

Voltooi [deel één van de zelfstudie](tutorial-part1-credit-risk.md).

## <a name="train"></a>Meerdere modellen trainen

Een van de voor delen van het gebruik van Azure Machine Learning Studio (klassiek) voor het maken van machine learning modellen is de mogelijkheid om meer dan één type model tegelijk te proberen in één experiment en de resultaten te vergelijken. Dit type experiment helpt u de beste oplossing voor uw probleem te vinden.

In het experiment dat we ontwikkelen in deze zelfstudie, maakt u twee verschillende soorten modellen en vergelijkt vervolgens hun scoreresultaten om te beslissen welk algoritme u wilt gebruiken in ons laatste experiment.  

Er zijn verschillende modellen waaruit u kunt kiezen. Als u de beschikbare modellen wilt zien, vouwt u het **Machine Learning**-knooppunt uit in het modulepalet en vervolgens **Initialize Model** en de knooppunten eronder. Voor de doel einden van dit experiment selecteert u de [Tweeklasse Support Vector machine][two-class-support-vector-machine] (SVM) en de [met twee klassen Geboostte beslissings structuur][two-class-boosted-decision-tree] modules.

> [!TIP]
> Raadpleeg [How to choose algorithms for Microsoft Azure machine learning Studio (klassiek)](algorithm-choice.md)als u wilt weten welke machine learning algoritme het beste past bij het probleem dat u wilt oplossen.
> 
> 

In dit experiment voegt u de [twee klasse-module voor de beslissings structuur][two-class-boosted-decision-tree] met twee klassen en de [ondersteunings vector machine module voor tweeklassen][two-class-support-vector-machine] toe.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree

Stel eerst het Boosted Decision Tree-model in.

1. Zoek de module met [twee klassen versterkte beslissings structuur][two-class-boosted-decision-tree] in het module palet en sleep deze naar het canvas.

1. Zoek de module [Train model][train-model] , sleep deze naar het canvas en verbind de uitvoer van de module met de [Geboostte beslissings structuur met twee klassen][two-class-boosted-decision-tree] naar de linker invoer poort van de module [Train model][train-model] .
   
   De uitmuntende [beslissings structuur van twee klassen][two-class-boosted-decision-tree] initialiseert het algemene model en [Train model][train-model] gebruikt trainings gegevens om het model te trainen. 

1. Verbind de linkse uitvoer van de [R-script][execute-r-script] module links naar de juiste invoer poort van de module [Train model][train-model] (in deze zelf studie hebt u [de gegevens uit de linkerkant](#train) van de module voor het splitsen van gegevens voor training gebruikt).
   
   > [!TIP]
   > u hebt niet twee van de invoer en een van de uitvoer van de [script module Execute R][execute-r-script] nodig voor dit experiment, zodat u ze ongekoppeld kunt houden. 
   > 
   > 

Dit gedeelte van ons experiment ziet er nu ongeveer als volgt uit:  

![Een model trainen](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

U moet nu de [Train model][train-model] -module vertellen dat u wilt dat het model de waarde van het credit risico kan voors pellen.

1. Selecteer de module [Train model][train-model] . Klik in het deelvenster **Properties** op **Launch column selector**.

1. In het dialoogvenster **Select a single column** typt u "Kredietrisico" in het zoekveld onder **Available Columns**, selecteert u "Kredietrisico" hieronder en klikt u op de rechter pijlknop ( **>** ) om "Kredietrisico’s" naar **Selected Columns** te verplaatsen. 

    ![Selecteer de kolom Kredietrisico voor de module Train Model](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klik op het **OK**-selectievakje.

### <a name="two-class-support-vector-machine"></a>Two-Class Support Vector Machine

Vervolgens stelt u het SVM-model in.  

Eerst een beetje uitleg over SVM. Boosted Decision Trees werken goed met functies van elk type. Omdat de SVM-module een lineaire classificatie genereert, heeft het model dat wordt gegenereerd echter de beste testfout wanneer alle numerieke functies dezelfde schaal hebben. Als u alle numerieke functies wilt omzetten in dezelfde schaal, gebruikt u een "TANH"-trans formatie (met de module [normalisatie gegevens][normalize-data] ). Dit transformeert onze cijfers naar het bereik [0,1]. De SVM-module converteert tekenreeksfuncties naar categorische functies en vervolgens naar binaire 0/1-functies, zodat u niet handmatig tekenreeksfuncties hoeft te transformeren. Ook moet u de kolom Kredietrisico (kolom 21) niet transformeren - deze is numeriek, maar het is de waarde die we het model aanleren om te voorspellen, daarom moet u deze ongewijzigd laten.  

Als u het SVM-model instelt, doe dan het volgende:

1. Zoek de [-machine module met twee klasse-ondersteunings vectoren][two-class-support-vector-machine] in het module palet en sleep deze naar het canvas.

1. Klik met de rechter muisknop op de module [Train model][train-model] , selecteer **kopiëren**, klik met de rechter muisknop op het canvas en selecteer **Plakken**. Het exemplaar van de [Train model][train-model] -module heeft dezelfde kolom selectie als het origineel.

1. Verbind de uitvoer van de machine module met [ondersteuning voor twee klassen][two-class-support-vector-machine] met de linker-invoer poort van de tweede [Train model][train-model] -module.

1. Zoek de module [Normal data-gegevens][normalize-data] en sleep deze naar het canvas.

1. Verbind de linkse uitvoer van de [R-script][execute-r-script] module links naar de invoer van deze module (merk op dat de uitvoer poort van een module mogelijk is verbonden met meer dan één andere module).

1. Verbind de linkse uitvoer poort van de module [normaliseren data][normalize-data] naar de juiste invoer poort van de tweede [Train model][train-model] -module.

Zo ziet dit deel van het experiment er ongeveer uit nadat het is uitgevoerd:  

![Het tweede model trainen](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Configureer nu de module [Normal data-gegevens][normalize-data] :

1. Klik om de module [Normal data][normalize-data] te selecteren. Selecteer in het **Properties**-venster **Tanh** als parameter voor **Transformation method**.

1. Klik op **Launch column selector**, selecteer "No columns" voor **Begin With**, selecteer **Include** in de eerste vervolgkeuzelijst, selecteer **column type** in de tweede vervolgkeuzelijst en selecteer **Numeric** in de derde vervolgkeuzelijst. Hiermee wordt aangegeven dat alle numerieke kolommen (en alleen numerieke) worden getransformeerd.

1. Klik op het plusteken (+) aan de rechterkant van deze rij. Hiermee maakt u een rij van de vervolgkeuzelijsten. Selecteer **Exclude** in de eerste vervolgkeuzelijst, selecteer **column names** in de tweede vervolgkeuzelijst en voer "Kredietrisico" in het tekstveld in. Hiermee wordt aangegeven dat de kolom Kredietrisico's moet worden genegeerd (u moet dit doen omdat deze kolom numeriek is en zou worden omgezet als u deze niet uitsluit).

1. Klik op het **OK**-selectievakje.  

    ![Selecteer de module Normalize Data](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


De module [normalisatie gegevens][normalize-data] is nu zo ingesteld dat er een TANH-trans formatie op alle numerieke kolommen wordt uitgevoerd, met uitzonde ring van de kolom credit risico.  

## <a name="score-and-evaluate-the-models"></a>De modellen beoordelen en evalueren

u gebruikt de gegevens die zijn gescheiden door de module [Splits data][split] om onze getrainde modellen te scoren. U kunt vervolgens de resultaten van de twee modellen vergelijken om te zien welke betere resultaten heeft gegenereerd.  

### <a name="add-the-score-model-modules"></a>De modules Score Model toevoegen

1. Zoek de module [score model][score-model] en sleep deze naar het canvas.

1. Verbind de [trein model][train-model] module die is verbonden met de uitmuntende [beslissings structuur module met twee klassen][two-class-boosted-decision-tree] naar de linker invoer poort van de module [score model][score-model] .

1. Verbind de juiste [Execute R-script][execute-r-script] module (onze test gegevens) naar de juiste invoer poort van de module [score model][score-model] .

    ![Module Score Model verbonden](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   De [score model][score-model] module kan nu de tegoed gegevens van de test gegevens nemen, uitvoeren via het model en de voor spellingen van het model vergelijken met de kolom met het werkelijke krediet risico in de test gegevens.

1. Kopieer en plak de module [score model][score-model] om een tweede kopie te maken.

1. Verbind de uitvoer van het SVM-model (dat wil zeggen, de uitvoer poort van de [trein model][train-model] module die is verbonden met de [2-klasse support vector machine][two-class-support-vector-machine] module) tot de invoer poort van de tweede [score model][score-model] module.

1. Voor het SVM-model moet u de dezelfde transformatie uitvoeren op de testgegevens als u met de trainingsgegevens heeft gedaan. Kopieer en plak de module [normaliseren data][normalize-data] om een tweede kopie te maken en deze te verbinden met de juiste [Execute R-script][execute-r-script] module.

1. Verbind de linkse uitvoer van de tweede module [normaliseren data][normalize-data] naar de juiste invoer poort van het tweede [score model][score-model] .

    ![Beide Score Model-modules verbonden](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>De module Evaluate Model toevoegen

Als u de twee Score resultaten wilt evalueren en deze wilt vergelijken, gebruikt u een module voor het evalueren van een [model][evaluate-model] .  

1. Zoek de module [Evaluate model][evaluate-model] en sleep deze naar het canvas.

1. Koppel de uitvoer poort van de module [score model][score-model] die is gekoppeld aan het versterkte model van de beslissings structuur aan de linkerkant invoer poort van de module [Evaluate model][evaluate-model] .

1. Verbind de module voor het andere [score model][score-model] met de juiste invoer poort.  

    ![Module Evaluate Model verbonden](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Voer het experiment uit en controleer de resultaten

Als u wilt het experiment uitvoeren, klikt u op de knop **RUN** onder het canvas. Dit kan enkele minuten duren. Een draaiende indicator op elke module laat zien dat deze wordt uitgevoerd en er wordt een groen vinkje weergegeven wanneer de module is voltooid. Wanneer alle modules een groen vinkje hebben, is het experiment voltooid.

Het experiment zou er nu ongeveer zo uit moeten zien:  

![Beide modellen evalueren](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Als u de resultaten wilt controleren, klikt u op de uitvoer poort van de module [Evaluate model][evaluate-model] en selecteert u **visualiseren**.  

De module [Evaluate model][evaluate-model] produceert een paar curven en metrische gegevens die u in staat stellen om de resultaten van de twee gescoorde modellen te vergelijken. U kunt de resultaten als Receiver Operator Characteristic (ROC)-curven, Precision/Recall-curven of Lift-curven weergeven. Aanvullende gegevens die worden weergegeven, bevatten een verwarringsmatrix, cumulatieve waarden voor het gebied onder de curve (AUC) en andere metrische gegevens. U kunt de drempelwaarde wijzigen door de schuifregelaar naar links of rechts te bewegen en te zien hoe dit van invloed is op de set met metrische gegevens.  

Aan de rechterkant van de grafiek, klikt u op **Scored dataset** of **Scored dataset to compare** om de bijbehorende curve te markeren en de bijbehorende metrische gegevens te tonen die hieronder worden weergegeven. In de legenda voor de curven komt de ' gescoorde gegevensset ' overeen met de linker invoer poort van de module [Evaluate model][evaluate-model] . in ons geval is dit het versterkte model voor beslissings structuur. "Scored dataset to compare" komt overeen met de juiste invoerpoort - het SVM-model in ons geval. Wanneer u op een van deze labels klikt, wordt de curve voor dit model gemarkeerd en de worden bijbehorende metrische gegevens weergegeven, zoals u ziet in de volgende afbeelding.  

![ROC-curve voor modellen](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Door deze waarden te onderzoeken, kunt u bepalen welk model de resultaten die u zoekt het dichtst benadert. U kunt teruggaan en uw experiment herhalen door parameterwaarden in de verschillende modellen te wijzigen. 

De wetenschap en kunst van het interpreteren van deze resultaten en het afstemmen van de modelprestaties vallen buiten deze zelfstudie. U kunt de volgende artikelen lezen voor meer informatie:
- [Model prestaties in Azure Machine Learning Studio evalueren (klassiek)](evaluate-model-performance.md)
- [Para meters kiezen voor het optimaliseren van uw algoritmen in Azure Machine Learning Studio (klassiek)](algorithm-parameters-optimize.md)
- [Model resultaten interpreteren in Azure Machine Learning Studio (klassiek)](interpret-model-results.md)

> [!TIP]
> Telkens wanneer u het experiment uitvoert, wordt een record van deze iteratie opgeslagen in de uitvoeringsgeschiedenis. U kunt deze iteraties bekijken en ze opnieuw bekijken door te klikken op **VIEW RUN HISTORY** onder het canvas. U kunt ook klikken op **Prior Ru** in het **Properties**-venster om terug te keren naar de iteratie direct vóór de versie die u hebt geopend.
> 
> U kunt een kopie van de iteraties van uw experiment maken door te klikken op **SAVE AS** onder het canvas. 
> Gebruik de eigenschappen **Summary** en **Description** van het experiment om bij te houden wat u hebt geprobeerd in uw iteraties.
> 
> Zie [experimenten-iteraties beheren in azure machine learning Studio (klassiek)](manage-experiment-iterations.md)voor meer informatie.  
> 
> 

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen voltooid: 
 
> [!div class="checklist"]
> * Een experiment maken
> * Meerdere modellen trainen
> * De modellen beoordelen en evalueren

U kunt nu de modellen voor deze gegevens implementeren.

> [!div class="nextstepaction"]
> [Zelfstudie 3 - modellen implementeren](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/