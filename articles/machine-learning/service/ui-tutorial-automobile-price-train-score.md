---
title: 'Zelf studie: prijs van auto Mobile voors pellen met de visuele interface'
titleSuffix: Azure Machine Learning
description: Leer hoe u een machine learning model kunt trainen, beoordelen en implementeren met behulp van een visuele interface voor slepen en neerzetten. Deze zelf studie is deel één van een serie met twee delen voor het voors pellen van prijzen voor auto's met lineaire regressie.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: b0c9fd85171020c9b78dc166980f85bcd89d8d67
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692324"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Zelf studie: prijs van auto Mobile voors pellen met de visuele interface

In deze tweedelige zelf studie leert u hoe u de Azure Machine Learning Visual Interface kunt gebruiken voor het ontwikkelen en implementeren van een predictive analytics oplossing die de prijs van elke auto voorspelt. 

In deel één kunt u uw omgeving instellen, modules slepen en neerzetten op een interactief canvas en ze samen verbinden om een Azure Machine Learning pijp lijn te maken.

In deel één van de zelf studie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe pijp lijn maken
> * Gegevens importeren
> * Gegevens voorbereiden
> * Een machine learning model trainen
> * Een machine learning model evalueren

In [deel twee](ui-tutorial-automobile-price-deploy.md) van de zelf studie leert u hoe u uw voorspellende model kunt implementeren als een Azure-webservice om de prijs van een auto te voors pellen op basis van technische specificaties die u verzendt. 

> [!Note]
>Een voltooide versie van deze zelf studie is beschikbaar als een voorbeeld pijplijn.
>
>Ga naar de **Visual-Interface in uw werk ruimte**om de app te vinden. Selecteer in de sectie **nieuwe pijp lijn** voor **Beeld 1 regressie: prijs voorspelling voor auto's (basis)** .

## <a name="create-a-new-pipeline"></a>Een nieuwe pijp lijn maken

Met Azure Machine Learning pijp lijnen worden meerdere stappen voor het verwerken van afhankelijke gegevens in één resource ingedeeld. Met pijp lijnen kunt u complexe machine learning werk stromen in projecten en gebruikers organiseren, beheren en hergebruiken. Als u een Azure Machine Learning pijp lijn wilt maken, hebt u een Azure Machine Learning service-werk ruimte nodig. In deze sectie leert u hoe u deze resources kunt maken.

### <a name="create-a-new-workspace"></a>Een nieuwe werk ruimte maken

Als u een Azure Machine Learning-werk ruimte hebt, gaat u verder met de volgende sectie.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-a-pipeline"></a>Een pijplijn maken

1. Meld u aan bij [ml.Azure.com](https://ml.azure.com) en selecteer de werk ruimte waarmee u wilt werken.

1. Selecteer de **visuele interface**.

    ![Scherm afbeelding van de Visual-werk ruimte met weer gave van toegang tot de visuele interface](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. Selecteer **lege pijp lijn**.

1. Selecteer de standaard pijplijn naam **pijp lijn gemaakt-aan** aan de bovenkant van het canvas en wijzig deze in iets wat nuttig is. Bijvoorbeeld **' prijs voorspelling voor auto Mobile '** . De naam hoeft niet uniek te zijn.

## <a name="import-data"></a>Gegevens importeren

De visuele interface bevat verschillende voorbeeld gegevens sets waarmee u kunt experimenteren. Voor deze zelf studie gebruikt u de voor beeld-gegevensset **prijs gegevens (onbewerkt)** . 

1. Links van het pijplijn canvas is een palet met gegevens sets en modules. Selecteer **gegevens sets** en bekijk vervolgens de sectie voor **beelden** om de beschik bare voorbeeld gegevens sets weer te geven.

1. Selecteer de gegevensset, **prijs gegevens auto Mobile (onbewerkt)** en sleep deze naar het canvas.

   ![Gegevens naar canvas slepen](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Selecteer de kolommen met gegevens waarmee u wilt werken. Typ **in** het zoekvak boven aan het palet om de module **select columns in dataset** te vinden.

1. Klik en sleep de module **kolommen in gegevensset selecteren** op het canvas. Verwijder de module onder de module DataSet.

1. Verbind de gegevensset die u eerder hebt toegevoegd aan de module **select columns in dataset** door te klikken en slepen. Sleep vanuit de uitvoer poort van de gegevensset, de kleine cirkel aan de onderkant van de gegevensset op het canvas, naar de invoer poort van **geselecteerde kolommen in gegevensset**, die de kleine cirkel boven aan de module vormt.

    > [!TIP]
    > U maakt een stroom met gegevens via uw pijp lijn wanneer u de uitvoer poort van een module verbindt met een andere invoer poort.
    >

    ![Modules verbinden](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selecteer de module **select columns in dataset** .

1. Selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie **kolom bewerken**.

    Selecteer in het dialoog venster **kolommen selecteren** **alle kolommen** en voeg **alle onderdelen**toe.

1. Selecteer op de rechter benedenhoek de optie **Opslaan** om de kolom kiezer te sluiten.

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren

Klik op elk gewenst moment op de uitvoer poort van een gegevensset of module om te zien hoe de gegevens eruitzien op dat punt in de gegevens stroom. Als het tabblad **uitvoer** niet wordt weer gegeven, moet u eerst de pijp lijn uitvoeren.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="visualize-the-data"></a>De gegevens visualiseren

U kunt de gegevens visualiseren om inzicht te krijgen in de gegevensset die u gaat gebruiken.

1. Selecteer de module **Auto Mobile price data (RAW)** .

1. Selecteer **uitvoer**in het deel venster **Eigenschappen** rechts van het canvas.

1. Selecteer het pictogram van de grafiek om de gegevens te visualiseren.

    ![De gegevens visualiseren](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. Selecteer de verschillende kolommen in het venster gegevens om informatie over elke kolom weer te geven.

    Elke rij vertegenwoordigt een auto en de variabelen die aan elke auto zijn gekoppeld, worden weer gegeven als kolommen. Deze gegevensset bevat 205 rijen en 26 kolommen.

## <a name="prepare-data"></a>Gegevens voorbereiden

Gegevens sets vereisen doorgaans een voor verwerking vóór de analyse. Mogelijk hebt u enkele ontbrekende waarden gezien tijdens het visualiseren van de gegevensset. Deze ontbrekende waarden moeten worden opgeschoond, zodat de gegevens correct kunnen worden geanalyseerd. U verwijdert rijen met ontbrekende waarden.

1. Typ **in** het zoekvak boven aan het palet om de module **select columns in dataset** te vinden.

1. Klik en sleep de module **kolommen in gegevensset selecteren** op het canvas. Verwijder de module onder de module DataSet.

1. Verbind de gegevensset die u eerder hebt toegevoegd aan de module **select columns in dataset** door te klikken en slepen. Sleep vanuit de uitvoer poort van de gegevensset, de kleine cirkel aan de onderkant van de gegevensset op het canvas, naar de invoer poort van **geselecteerde kolommen in gegevensset**, die de kleine cirkel boven aan de module vormt.

    ![Modules verbinden](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Selecteer de module **select columns in dataset** .

1. Selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie **kolom bewerken**.

    Selecteer in het dialoog venster **kolommen selecteren** **alle kolommen** en voeg **alle onderdelen**toe.

1. Selecteer op de rechter benedenhoek de optie **Opslaan** om de kolom kiezer te sluiten.

> [!TIP]
> Het schoonmaken van de ontbrekende waarden van invoer gegevens is een vereiste voor het gebruik van de meeste modules in de visuele interface.

### <a name="remove-column"></a>Kolom verwijderen

Wanneer u een model traint, moet u iets doen over de ontbrekende gegevens. In deze gegevensset heeft de kolom **genormaliseerde-verliezen** een groot aantal ontbrekende waarden, dus sluit die kolom af van het model.

1. Selecteer de module **select columns in dataset** .

1. Selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie **para meters**  > **kolom bewerken**.

1. Selecteer de **+** om een nieuwe regel toe te voegen.

1. Selecteer in de vervolg keuzelijst de optie **uitsluiten** en **kolom namen**.
    
1. Voer **genormaliseerde verliezen** in het tekstvak in.

1. In de rechter benedenhoek selecteert u **Opslaan** om de kolom kiezer te sluiten.

    ![Een kolom uitsluiten](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    Het deel venster met eigenschappen geeft aan dat de kolom **genormaliseerde-verliezen** is uitgesloten.

1. Selecteer de module **select columns in dataset** . 

1. Selecteer in de **Eigenschappen** **para meters**  > **Opmerking** en voer ' normale verliezen uitsluiten ' in.

### <a name="clean-missing-data"></a>Ontbrekende gegevens opschonen

Uw gegevensset heeft nog steeds ontbrekende waarden na het verwijderen van de kolom **normald-verliezen** . U kunt de resterende ontbrekende gegevens verwijderen met de module **clean Missing Data** .

1. Typ **clean** in het zoekvak om de module **clean Missing Data** te vinden.

1. Sleep de module **clean Missing Data** naar het pijp lijn-canvas en verbind deze met de module **select columns in dataset** . 

1. Selecteer in het deel venster Eigenschappen de optie **hele rij verwijderen** onder **reinigings modus**.

1. In het deel venster Eigenschappen voert u ' rijen met ontbrekende waarden verwijderen ' in. in het vak **Opmerking** .  

    De pijp lijn moet er nu ongeveer als volgt uitzien:
    
    ![Select-kolom](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Een machine learning model trainen

Nu de gegevens zijn voor verwerking, kunt u een voorspellend model maken. U gebruikt uw gegevens om het model te trainen. Vervolgens test u het model om te zien hoe de prijzen kunnen worden voor speld.

### <a name="select-an-algorithm"></a>Een algoritme selecteren

**Classificatie** en **regressie** zijn twee soorten beheerde machine learning-algoritmen. Met **classificatie** wordt een antwoord voor speld op basis van een gedefinieerde set categorieën, zoals een kleur (rood, blauw of groen). **Regressie** wordt gebruikt om een getal te voors pellen.

Omdat u de prijs wilt voors pellen, wat een getal is, kunt u een regressie algoritme gebruiken. Voor dit voor beeld gebruikt u een lineair regressie model.

### <a name="split-the-data"></a>De gegevens splitsen

Gebruik uw gegevens voor de training van het model en het testen door de gegevens te splitsen in twee afzonderlijke gegevens sets.

1. Typ **Split data** in het zoekvak om de module **Split data** te vinden en deze te verbinden met de poort links van de module **clean Missing Data** .

1. Selecteer de module **Split data** .

1. Stel in het deel venster Eigenschappen het **gedeelte van de rijen in de eerste uitvoer gegevensset** in op 0,7.

    Hiermee worden 70 procent van de gegevens gesplitst om het model te trainen en wordt 30 procent voor het testen teruggestuurd.

1. In het deel venster Eigenschappen voert u ' Splits de gegevensset in de Trainingsset (0,7) en test set (0,3) ' in. in het vak **Opmerking** .

### <a name="train-the-model"></a>Het model trainen

Train het model door het een set gegevens te geven die de prijs bevat. Het model scant de gegevens en zoekt naar correlaties tussen de functies van een auto en de prijs.

1. Als u het leer algoritme wilt selecteren, wist u het zoekvak van het module palet.

1. Vouw **machine learning algoritmen**uit.
    
    Er worden verschillende categorieën weergegeven die kunnen worden gebruikt om de machine learning-algoritmen te initialiseren.

1. Selecteer voor deze pijp lijn **regressie**  > **lineaire regressie** en sleep deze naar het pijplijn doek.

1. Zoek de module **Train model** en sleep deze naar het pijp lijn-canvas. 

1. Koppel de uitvoer van de module Linear Regression aan de linkerkant van de module Train model.

1. Verbind de resultaten van de trainings gegevens (links poort) van de module **gesplitste gegevens** met de juiste invoer van de module **Train model** .

    ![Scherm opname van de juiste configuratie van de Train model-module. De lineaire regressie-module maakt verbinding met de linker poort van Train model module en de module voor gesplitste gegevens maakt verbinding met de juiste poort van Train model](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. Selecteer de module **Train model** .

1. Selecteer in het deel venster Eigenschappen de optie **kolom kiezer bewerken** .

1. Vouw in het dialoog venster **Label kolom** de vervolg keuzelijst uit en selecteer **kolom namen**. Voer in het tekstvak de **prijs**in. Prijs is de waarde die uw model gaat voors pellen.

    Uw pijp lijn moet er als volgt uitzien:

    ![Scherm opname van de juiste configuratie van de pijp lijn na het toevoegen van de module Train model.](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Een machine learning model evalueren

Nu u het model hebt getraind met 70 procent van uw gegevens, kunt u het gebruiken om de andere 30 procent van de gegevens te scoren om te zien hoe goed uw model functioneert.

1. Typ **score model** in het zoekvak om de module **score model** te vinden en sleep de module naar het pijp lijn papier. 

1. Koppel de uitvoer van de module **Train model** aan de linker invoer poort van het **score model**. Koppel de test gegevens uitvoer (rechter poort) van de module **Split data** aan de juiste invoer poort van het **score model**.

1. Typ **Evaluate** in het zoekvak om het **evaluatie model** te vinden en sleep de module naar het pijp lijn-canvas. 

1. Koppel de uitvoer van de module **score model** aan de linker invoer van het **Evaluate-model**. 

    De uiteindelijke pijp lijn moet er ongeveer als volgt uitzien:

    ![Scherm afbeelding met de juiste configuratie van de pijp lijn.](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>Resultaten weergeven

Nadat de uitvoering is voltooid, kunt u de resultaten van de pijplijn uitvoering bekijken. 

1. Bekijk de uitvoer van de module **score model** door de module **score model** te selecteren.

1. Selecteer in het deel venster **Eigenschappen** de optie **uitvoer**  > **visualiseren**. De uitvoer bevat de voorspelde waarden voor de prijs en de bekende waarden uit de testgegevens.

    ![Scherm afbeelding van de uitvoer visualisatie die de kolom ' gescoorde label ' markeert](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Bekijk de uitvoer van de module **Evaluate model** door de module **score model** te selecteren.

1. Selecteer in het deel venster **Eigenschappen** de optie **uitvoer**  > **visualiseren**en selecteer vervolgens **visualiseren**.

De volgende statistieken worden weer gegeven voor uw model:

* **Gemiddelde absolute fout (Mae)** : het gemiddelde van absolute fouten (een fout is het verschil tussen de voorspelde waarde en de werkelijke waarde).
* **Root mean error (RMSE)** : de vierkantswortel van het gemiddelde van de gekwadrateerde fouten van voor spellingen die zijn aangebracht in de test-gegevensset.
* **Relative Absolute Error**: het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Relative Squared Error**: het gemiddelde aan gekwadrateerde fouten ten opzichte van het gekwadrateerde verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Determinatie coëfficiënt**: ook wel bekend als R-kwadraat, is dit een statistische waarde die aangeeft hoe goed een model past bij de gegevens.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een kleine waarde geeft aan dat de voorspelling dichter bij de werkelijke waarde ligt. Voor een coëfficiënt van de bepaling is de waarde één (1,0), hoe beter de voor spellingen.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deel één van deze zelf studie hebt u de volgende stappen uitgevoerd:

* Een pijp lijn gemaakt
* De gegevens voorbereiden
* Het model is getraind
* Het model wordt gescoord en geëvalueerd

In deel twee leert u hoe u uw model implementeert als een pijplijn eindpunt.

> [!div class="nextstepaction"]
> [Door gaan met de implementatie van modellen](ui-tutorial-automobile-price-deploy.md)
