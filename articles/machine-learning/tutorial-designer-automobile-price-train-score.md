---
title: 'Zelf studie: prijs van auto Mobile voors pellen met de ontwerp functie'
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen, beoordelen en implementeren van een machine learning model met behulp van een slepen-en-neerzetten-interface. Deze zelf studie is deel één van een serie met twee delen voor het voors pellen van de prijzen van auto's met behulp van lineaire regressie.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 639a61cddde27b0d989e5a3dd4c599c353182a73
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720158"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Zelf studie: prijs van auto Mobile voors pellen met de ontwerp functie (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In deze tweedelige zelf studie leert u hoe u de Azure Machine Learning Designer kunt gebruiken om een predictive analytics oplossing te ontwikkelen en implementeren waarmee de prijs van elke auto wordt voor speld.

In deel één van de zelf studie leert u het volgende:

> [!div class="checklist"]
> * Maak een nieuwe pijp lijn.
> * Gegevens importeren.
> * Gegevens voorbereiden.
> * Train een machine learning model.
> * Een machine learning model evalueren.

In [deel twee](tutorial-designer-automobile-price-deploy.md) van de zelf studie implementeert u uw model als een real-time afnemend eind punt om de prijs van een auto te voors pellen op basis van technische specificaties die u verzendt. 

> [!NOTE]
>Een voltooide versie van deze zelf studie is beschikbaar als een voorbeeld pijplijn.
>
>Ga naar de ontwerp functie in uw werk ruimte om de app te vinden. Selecteer in de sectie **nieuwe pijp lijn** voor **Beeld 1 regressie: prijs voorspelling voor auto's (basis)** .

## <a name="create-a-new-pipeline"></a>Een nieuwe pijp lijn maken

Met Azure Machine Learning pijp lijnen worden meerdere machine learning-en gegevens verwerkings stappen in één resource ingedeeld. Met pijp lijnen kunt u complexe machine learning werk stromen in projecten en gebruikers ordenen, beheren en hergebruiken.

Als u een Azure Machine Learning pijp lijn wilt maken, hebt u een Azure Machine Learning-werk ruimte nodig. In deze sectie leert u hoe u deze resources kunt maken.

### <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

Als u een Azure Machine Learning-werk ruimte hebt met een Enter prise-editie, [gaat u naar de volgende sectie](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Maak de pijplijn

1. Meld u aan bij [ml.Azure.com](https://ml.azure.com)en selecteer de werk ruimte waarmee u wilt werken.

1. Selecteer **ontwerp functie**.

    ![Scherm afbeelding van de Visual-werk ruimte met weer gave van toegang tot de ontwerp functie](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecteer **eenvoudig te gebruiken vooraf gedefinieerde modules**.

1. Selecteer boven aan het canvas de standaard pijplijn naam **pijplijn die is gemaakt**. Wijzig de naam in de voor *Spelling van prijzen voor auto Mobile*. De naam hoeft niet uniek te zijn.

## <a name="import-data"></a>Gegevens importeren

Er zijn verschillende voorbeeld gegevens sets die zijn opgenomen in de ontwerp functie waarmee u kunt experimenteren. Gebruik voor deze zelf studie **Auto Mobile price data (RAW)** . 

1. Links van het pijplijn canvas is een palet met gegevens sets en modules. Selecteer **gegevens sets**en bekijk vervolgens de sectie voor **beelden** om de beschik bare voorbeeld gegevens sets weer te geven.

1. Selecteer de prijs gegevens van de gegevensset **Auto Mobile (onbewerkt)** en sleep deze naar het canvas.

   ![Gegevens naar canvas slepen](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>De gegevens visualiseren

U kunt de gegevens visualiseren om inzicht te krijgen in de gegevensset die u gaat gebruiken.

1. Selecteer de module **Auto Mobile price data (RAW)** .

1. Selecteer **uitvoer**in het deel venster Eigenschappen rechts van het canvas.

1. Selecteer het pictogram van de grafiek om de gegevens te visualiseren.

    ![De gegevens visualiseren](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Selecteer de verschillende kolommen in het venster gegevens om informatie over elke kolom weer te geven.

    Elke rij vertegenwoordigt een auto en de variabelen die aan elke auto zijn gekoppeld, worden weer gegeven als kolommen. Deze gegevensset bevat 205 rijen en 26 kolommen.

## <a name="prepare-data"></a>Gegevens voorbereiden

Gegevens sets vereisen doorgaans een voor verwerking vóór de analyse. Mogelijk hebt u enkele ontbrekende waarden gezien toen u de gegevensset inspecteert. Deze ontbrekende waarden moeten worden opgeschoond zodat het model de gegevens op de juiste wijze kan analyseren.

### <a name="remove-a-column"></a>Een kolom verwijderen

Wanneer u een model traint, moet u iets doen over de gegevens die ontbreken. In deze gegevensset ontbreken er veel waarden in de kolom **normaled-verliezen** , dus sluit u die kolom van het model niet samen.

1. Voer in het zoekvak boven aan het palet **selecteren** in om de module **select columns in dataset** te vinden.

1. Sleep de module **kolommen in gegevensset selecteren** naar het canvas. Verwijder de module onder de module DataSet.

1. Verbind de gegevensset **Auto Mobile price data (RAW)** met de module **select columns in dataset** . Sleep vanuit de uitvoer poort van de gegevensset, de kleine cirkel aan de onderkant van de gegevensset op het canvas, naar de invoer poort van **geselecteerde kolommen in gegevensset**, die de kleine cirkel boven aan de module vormt.

    > [!TIP]
    > U maakt een stroom met gegevens via uw pijp lijn wanneer u de uitvoer poort van een module verbindt met een andere invoer poort.
    >

    ![Modules verbinden](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecteer de module **select columns in dataset** .

1. Selecteer in het deel venster Eigenschappen rechts van het canvas de optie **alle kolommen**.

1. Selecteer de **+** om een nieuwe regel toe te voegen.

1. Selecteer in de vervolg keuzelijst de optie **uitsluiten** en **kolom namen**.
    
1. Voer in het tekstvak *genormaliseerde verliezen* in.

1. In de rechter benedenhoek selecteert u **Opslaan** om de kolom kiezer te sluiten.

    ![Een kolom uitsluiten](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecteer de module **select columns in dataset** . 

1. In het deel venster Eigenschappen selecteert u het tekstvak **Opmerking** en voert u *normaal verlies uitsluiten*in.

    Opmerkingen worden weer gegeven in de grafiek om uw pijp lijn te organiseren.

### <a name="clean-missing-data"></a>Schone, ontbrekende gegevens

Uw gegevensset heeft nog steeds ontbrekende waarden nadat u de kolom **normaled-verliezen** hebt verwijderd. U kunt de resterende ontbrekende gegevens verwijderen met behulp van de module **clean Missing Data** .

> [!TIP]
> Het schoonmaken van de ontbrekende waarden van invoer gegevens is een vereiste voor het gebruik van de meeste modules in de ontwerp functie.

1. Voer **schone** in het zoekvak in om de module **clean Missing Data** te vinden.

1. Sleep de module **clean Missing Data** naar het pijp lijn-canvas. Verbind deze met de module **select columns in dataset** . 

1. Selecteer in het deel venster Eigenschappen de optie **hele rij verwijderen** onder **reinigings modus**.

1. **In het vak deel** venster met eigenschappen geeft u de *rijen ontbrekende waarde verwijderen*op. 

    De pijp lijn moet er nu ongeveer als volgt uitzien:
    
    ![Select-kolom](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Een machine learning model trainen

Nu u beschikt over de modules voor het verwerken van de gegevens, kunt u de trainings modules instellen.

Omdat u de prijs wilt voors pellen, wat een getal is, kunt u een regressie algoritme gebruiken. Voor dit voor beeld gebruikt u een lineair regressie model.

### <a name="split-the-data"></a>De gegevens splitsen

Het splitsen van gegevens is een gemeen schappelijke taak in machine learning. U kunt uw gegevens in twee afzonderlijke gegevens sets splitsen. Met één gegevensset wordt het model getraind en de andere wordt getest hoe goed het model wordt uitgevoerd.

1. Voer **Split data** in het zoekvak in om de module **Split data** te vinden. Verbind de poort links van de module **clean Missing Data** in de module **Split data** .

    > [!IMPORTANT]
    > Zorg ervoor dat de linker uitvoer poorten van **schone ontbrekende gegevens** verbinding maken met **gesplitste gegevens**. De linker poort bevat de gereinigde gegevens. De juiste poort bevat de discarted-gegevens.

1. Selecteer de module **Split data** .

1. Stel in het deel venster Eigenschappen het **gedeelte van de rijen in de eerste uitvoer gegevensset** in op 0,7.

    Met deze optie splitst u 70 procent van de gegevens om het model te trainen en 30 procent voor het testen ervan. De gegevensset van 70 procent is toegankelijk via de linker uitvoer poort. De resterende gegevens zijn beschikbaar via de juiste uitvoer poort.

1. Voer in **het vak deel** venster met eigenschappen *de gegevensset in de trainingsset (0,7) en de testset (0,3)* .

### <a name="train-the-model"></a>Het model trainen

Train het model door het een gegevensset te geven die de prijs bevat. Het algoritme bouwt een model dat de relatie tussen de functies en de prijs aangeeft zoals gepresenteerd door de trainings gegevens.

1. Als u het leer algoritme wilt selecteren, wist u het zoekvak van het module palet.

1. Vouw **machine learning algoritmen**uit.
    
    Met deze optie worden verschillende categorieën modules weer gegeven die u kunt gebruiken voor het initialiseren van leer algoritmen.

1. Selecteer **regressie** > **lineaire regressie**en sleep deze naar het pijp lijn-canvas.

1. Zoek de module **Train model** en sleep deze naar het pijp lijn-canvas. 

1. Koppel de uitvoer van de module **Linear Regression** aan de linkerkant van de module **Train model** .

1. Verbind de resultaten van de trainings gegevens (links poort) van de module **gesplitste gegevens** met de juiste invoer van de module **Train model** .
    
    > [!IMPORTANT]
    > Zorg ervoor dat de linker uitvoer poorten van **gesplitste gegevens** verbinding maken met het **Train-model**. De linker poort bevat de set training. De juiste poort bevat de testset.

    ![Scherm opname van de juiste configuratie van de Train model-module. De lineaire regressie-module maakt verbinding met de linker poort van Train model module en de module voor gesplitste gegevens maakt verbinding met de juiste poort van Train model](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Selecteer de module **Train model** .

1. Selecteer in het deel venster Eigenschappen de optie **kolom kiezer bewerken** .

1. Vouw in het dialoog venster **Label kolom** de vervolg keuzelijst uit en selecteer **kolom namen**. 

1. Voer in het tekstvak de *prijs* in om de waarde op te geven die uw model gaat voors pellen.

    Uw pijp lijn moet er als volgt uitzien:

    ![Scherm opname van de juiste configuratie van de pijp lijn na het toevoegen van de module Train model.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="score-a-machine-learning-model"></a>Een machine learning model beoordelen

Nadat u uw model hebt getraind met 70 procent van de gegevens, kunt u het gebruiken om de andere 30 procent te scoren om te zien hoe goed uw model functioneert.

1. Voer het *score model* in het zoekvak in om de module **score model** te vinden. Sleep de module naar het pijp lijn-canvas. 

1. Koppel de uitvoer van de module **Train model** aan de linker invoer poort van het **score model**. Koppel de test gegevens uitvoer (rechter poort) van de module **Split data** aan de juiste invoer poort van het **score model**.

## <a name="evaluate-a-machine-learning-model"></a>Een machine learning model evalueren

Gebruik de module voor het **evalueren van modellen** om te evalueren hoe goed uw model de test-gegevensset heeft gescoord.

1. Geef *evalueren* op in het zoekvak om de module **Evaluate model** te vinden. Sleep de module naar het pijp lijn-canvas. 

1. Koppel de uitvoer van de module **score model** aan de linker invoer van het **Evaluate-model**. 

    De uiteindelijke pijp lijn moet er ongeveer als volgt uitzien:

    ![Scherm afbeelding met de juiste configuratie van de pijp lijn.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>De pijplijn uitvoeren

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-scored-labels"></a>Gescoorde labels weer geven

Nadat de uitvoering is voltooid, kunt u de resultaten van de pijplijn uitvoering bekijken. Bekijk eerst de voor spellingen die zijn gegenereerd door het regressie model.

1. Selecteer de module **score model** om de uitvoer ervan weer te geven.

1. Selecteer in het deel venster Eigenschappen de optie **uitvoer** > grafiek pictogram ![pictogram visualiseren](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) om de resultaten weer te geven.

    Hier vindt u de voorspelde prijzen en de werkelijke prijzen van de test gegevens.

    ![Scherm afbeelding van de uitvoer visualisatie die de kolom met de gescoorde label markeert](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Modellen evalueren

Gebruik het **evalueren model** om te zien hoe goed het getrainde model op de test-gegevensset is uitgevoerd.

1. Selecteer de module **Evaluate model** om de uitvoer ervan weer te geven.

1. Selecteer in het deel venster Eigenschappen de optie **uitvoer** > grafiek pictogram ![pictogram visualiseren](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) om de resultaten weer te geven.

De volgende statistieken worden weer gegeven voor uw model:

* **Gemiddelde absolute fout (Mae)** : het gemiddelde van absolute fouten. Een fout is het verschil tussen de voorspelde waarde en de werkelijke waarde.
* **Root mean error (RMSE)** : de vierkantswortel van het gemiddelde van de gekwadrateerde fouten van voor spellingen die zijn aangebracht in de test-gegevensset.
* **Relative Absolute Error**: het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Relative Squared Error**: het gemiddelde aan gekwadrateerde fouten ten opzichte van het gekwadrateerde verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Determinatie coëfficiënt**: ook wel bekend als R-kwadraat, geeft deze statistische waarde aan hoe goed een model past bij de gegevens.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een kleinere waarde geeft aan dat de voor spellingen dichter bij de werkelijke waarden liggen. Voor de coëfficiënt van de bepaling is de waarde een (1,0), hoe beter de voor spellingen.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deel één van deze zelf studie hebt u de volgende taken uitgevoerd:

* Een pijplijn maken
* De gegevens voorbereiden
* Het model trainen
* Het model beoordelen en evalueren

In deel twee leert u hoe u uw model implementeert als een real-time-eind punt.

> [!div class="nextstepaction"]
> [Door gaan met de implementatie van modellen](tutorial-designer-automobile-price-deploy.md)
