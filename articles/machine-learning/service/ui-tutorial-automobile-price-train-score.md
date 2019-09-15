---
title: 'Zelfstudie: Prijs voor auto Mobile voors pellen met de visuele interface'
titleSuffix: Azure Machine Learning
description: Leer hoe u een machine learning model kunt trainen, beoordelen en implementeren met behulp van een visuele interface voor slepen en neerzetten. Deze zelf studie is deel één van een serie met twee delen voor het voors pellen van prijzen voor auto's met lineaire regressie.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: 11c65c217ef6c150c47f387f7f80070488a8df89
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996784"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Zelfstudie: Prijs voor auto Mobile voors pellen met de visuele interface

In deze tweedelige zelf studie leert u hoe u de visuele interface van Azure Machine Learning kunt gebruiken voor het ontwikkelen en implementeren van een voorspellende analytische oplossing die de prijs van elke auto voorspelt. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

In deel één gaat u uw omgeving instellen, gegevens sets en analyse modules naar een interactief canvas slepen en er verbinding mee maken om een experiment te maken. 

In deel één van de zelf studie leert u het volgende:

> [!div class="checklist"]
> * Een nieuw experiment maken
> * Gegevens importeren
> * Gegevens voorbereiden
> * Een machine learning model trainen
> * Een machine learning model evalueren

In [deel twee](ui-tutorial-automobile-price-deploy.md) van de zelf studie leert u hoe u uw voorspellende model als een Azure-webservice implementeert, zodat u deze kunt gebruiken om de prijs van een auto te voors pellen op basis van technische specificaties die u verzendt. 

Een voltooide versie van deze zelf studie is beschikbaar als een voor beeld van een experiment.

Als u dit wilt vinden, selecteert u op de **pagina experimenten**de optie **Nieuw toevoegen**en selecteert u vervolgens het **voor beeld 1 regressie: Experiment voor auto Mobile-prijs voorspelling (basis** ).

## <a name="create-a-new-experiment"></a>Een nieuw experiment maken

Voor het maken van een experiment voor een Visual-Interface hebt u eerst een Azure Machine Learning service-werk ruimte nodig. In deze sectie leert u hoe u deze resources kunt maken.

### <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

Als u een Azure Machine Learning-werk ruimte hebt, gaat u verder met de volgende sectie.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>Een experiment maken

1. Open uw werk ruimte in de [Azure Portal](https://portal.azure.com/).

1. Selecteer in uw werk ruimte de optie **visuele interface**. Selecteer vervolgens **Visual Interface starten**. 

    ![Scherm afbeelding van de Azure Portal die laat zien hoe u toegang krijgt tot de visuele interface vanuit een Machine Learning service-werk ruimte](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. Maak een nieuw experiment door onder aan het venster van de Visual-Interface **+ Nieuw** te selecteren.

1. Selecteer **leeg experiment**.

1. Selecteer de naam van het standaard experiment **' experiment gemaakt op..** . ' boven aan het canvas en wijzig deze in een iets zinvolle. Bijvoorbeeld **' prijs voorspelling voor auto Mobile '** . De naam hoeft niet uniek te zijn.

## <a name="import-data"></a>Gegevens importeren

Machine learning is afhankelijk van gegevens. Gelukkig, er zijn verschillende voorbeeld gegevens sets opgenomen in deze interface waarmee u kunt experimenteren. Voor deze zelf studie gebruikt u de voor beeld-gegevensset **prijs gegevens (onbewerkt)** . 

1. Aan de linkerkant van het experimentcanvas bevindt zich een palet met gegevenssets en modules. Selecteer **opgeslagen gegevens sets** en selecteer voor **beelden** om de beschik bare voorbeeld gegevens sets weer te geven.

1. Selecteer de gegevensset, **prijs gegevens auto Mobile (onbewerkt)** en sleep deze naar het canvas.

   ![Gegevens naar canvas slepen](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. Selecteer de kolommen met gegevens waarmee u wilt werken. Typ **in** het zoekvak boven aan het palet om de module **select columns in dataset** te vinden.

1. Klik en sleep de module **kolommen in gegevensset selecteren** op het canvas. Verwijder de module onder de module DataSet.

1. Verbind de gegevensset die u eerder hebt toegevoegd aan de module **select columns in dataset** door te klikken en slepen. Sleep vanuit de uitvoer poort van de gegevensset, die de kleine cirkel aan de onderkant van de gegevensset op het canvas is, naar de invoer poort van **select columns in dataset**. Dit is de kleine cirkel aan de bovenkant van de module.

    > [!TIP]
    > U maakt een stroom met gegevens door uw experiment wanneer u de uitvoer poort van een module verbindt met een invoer poort van een andere.
    >

    ![Modules verbinden](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    De rode uitroep teken geeft aan dat u de eigenschappen voor de module nog niet hebt ingesteld.

1. Selecteer de module **select columns in dataset** .

1. Selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie **kolommen bewerken**.

    Selecteer in het dialoog venster **kolommen selecteren** **alle kolommen** en voeg **alle onderdelen**toe. Het dialoogvenster zou er zo uit moeten zien:

     ![kolom-selector](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. Selecteer op de rechter benedenhoek **OK** om de kolom kiezer te sluiten.

### <a name="run-the-experiment"></a>Het experiment uitvoeren

Klik op elk gewenst moment op de uitvoer poort van een gegevensset of module om te zien hoe de gegevens eruitzien op dat punt in de gegevens stroom. Als de optie visualiseren is uitgeschakeld, moet u eerst het experiment uitvoeren.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Nadat het Compute-doel beschikbaar is, wordt het experiment uitgevoerd. Wanneer de uitvoering is voltooid, verschijnt er een groen vinkje op elke module.


### <a name="visualize-the-data"></a>De gegevens visualiseren

Nu u het eerste experiment hebt uitgevoerd, kunt u de gegevens visualiseren om meer te weten te komen over de gegevensset die u hebt.

1. Selecteer de uitvoer poort aan de onderkant van de **kolommen selecteren in gegevensset** en selecteervervolgens visualiseren.

1. Klik op verschillende kolommen in het venster gegevens om informatie over die kolom weer te geven.

    In deze gegevensset wordt elke auto weergegeven als een rij. De variabelen die aan elke auto zijn gekoppeld, worden weergegeven als kolommen. Deze gegevensset bevat 205 rijen en 26 kolommen.

    Telkens wanneer u op een kolom met gegevens klikt, wordt de informatie over de **Statistieken** en de **visualisatie** afbeelding van die kolom links weer gegeven.

    [![Voor beeld van de gegevens](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. Klik op elke kolom om meer te weten te komen over uw gegevensset en denk na over de vraag of deze kolommen nuttig zijn om de prijs van een auto te voors pellen.

## <a name="prepare-data"></a>Gegevens voorbereiden

Een gegevensset vereist meestal een voor verwerking voordat deze kan worden geanalyseerd. Mogelijk hebt u enkele ontbrekende waarden gezien tijdens het visualiseren van de gegevensset. Deze ontbrekende waarden moeten worden opgeschoond, zodat de gegevens correct kunnen worden geanalyseerd. U verwijdert rijen met ontbrekende waarden. De genormaliseerde **-verliezen** kolom heeft ook een groot deel van ontbrekende waarden, dus sluit die kolom af van het model.

> [!TIP]
> Voor de meeste modules geldt dat voor het gebruik van de module de ontbrekende invoergegevens moeten worden opgeschoond.

### <a name="remove-column"></a>Kolom verwijderen

Verwijder eerst de kolom **normald-verliezen** volledig.

1. Selecteer de module **select columns in dataset** .

1. Selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie **kolommen bewerken**.

    * U hoeft niets te doen **met regels** en **alle geselecteerde kolommen** .

    * Selecteer in de vervolgkeuzelijsten **Exclude** en **column names** en klik in het tekstvak. Typ **genormaliseerd-verliezen**.

    * Selecteer op de rechter benedenhoek **OK** om de kolom kiezer te sluiten.

    ![Een kolom uitsluiten](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    Nu wordt in het deel venster Eigenschappen voor geselecteerde kolommen in gegevensset aangegeven dat alle kolommen uit de gegevensset worden door gegeven behalve genormaliseerde **verliezen**.
        
    Het deel venster met eigenschappen geeft aan dat de kolom genormaliseerde **-verliezen** is uitgesloten.

1. Dubbel klik op de module **select columns in dataset** en typ de opmerking ' normale verliezen uitsluiten '. 
    
    Nadat u de opmerking hebt getypt, klikt u buiten de module. Er wordt een pijl-omlaag weer gegeven om aan te geven dat de module een opmerking bevat.

1. Klik op de pijl-omlaag om de opmerking weer te geven.

    De module toont nu een pijl-omhoog om de opmerking te verbergen.
        
    ![Opmerkingen](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>Schone, ontbrekende gegevens

Wanneer u een model traint, moet u iets doen over de ontbrekende gegevens. In dit geval voegt u een module toe om alle resterende rijen met ontbrekende gegevens te verwijderen.

1. Typ **clean** in het zoekvak om de module **clean Missing Data** te vinden.

1. Sleep de module **clean Missing Data** naar het canvas op het experiment en verbind deze met de module **select columns in dataset** . 

1. Selecteer in het deel venster Eigenschappen de optie **hele rij verwijderen** onder **reinigings modus**.

1. Dubbelklik op de module en typ de opmerking 'Rijen met ontbrekende waarde verwijderen'.

    Het experiment moet er nu ongeveer als volgt uitzien:
    
    ![Select-kolom](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>Een machine learning model trainen

Nu de gegevens gereed zijn, kunt u een voorspellend model maken. U gebruikt uw gegevens om het model te trainen. Vervolgens test u het model om te zien hoe de prijzen kunnen worden voor speld.

### <a name="select-an-algorithm"></a>Een algoritme selecteren

**Classificatie** en **regressie** zijn twee soorten beheerde machine learning-algoritmen. Met **classificatie** wordt een antwoord voor speld op basis van een gedefinieerde set categorieën, zoals een kleur (rood, blauw of groen). **Regressie** wordt gebruikt om een getal te voors pellen.

Omdat u de prijs wilt voors pellen, wat een getal is, kunt u een regressie algoritme gebruiken. Voor dit voor beeld gebruikt u een lineair regressie model.

### <a name="split-the-data"></a>De gegevens splitsen

Gebruik uw gegevens voor de training van het model en het testen door de gegevens te splitsen in afzonderlijke trainingen en gegevens sets testen.

1. Typ **Split data** in het zoekvak om de module **Split data** te vinden en deze te verbinden met de poort links van de module **clean Missing Data** .

1. Selecteer de module **Split data** . Stel in het deel venster Eigenschappen het gedeelte van de rijen in de eerste uitvoer gegevensset in op 0,7. Op deze manier gebruiken we 70 procent van de gegevens om het model te trainen en 30 procent voor het testen terug te zetten.

1. Dubbel klik op de **gesplitste gegevens** en typ de opmerking ' Splits de gegevensset in de Trainingsset (0,7) en de test set (0,3) '.

### <a name="train-the-model"></a>Het model trainen

Train het model door het een set gegevens te geven die de prijs bevat. Het model scant de gegevens en zoekt naar correlaties tussen de functies van een auto en de prijs.

1. Als u het leer algoritme wilt selecteren, wist u het zoekvak van het module palet.

1. Vouw de **machine learning** uit en vouw vervolgens **Initialiseer model**uit. Er worden verschillende categorieën weergegeven die kunnen worden gebruikt om de machine learning-algoritmen te initialiseren.

1. Voor dit experiment selecteert u **regressie** > **lineaire regressie** en sleept u deze naar het canvas op het experiment.

1. Zoek de module **Train model** en sleep deze naar het canvas van het experiment. Koppel de uitvoer van de module Linear Regression aan de linkerkant van de module Train model en koppel de gegevens uitvoer (links poort) van de module gesplitste **gegevens** aan de rechter invoer van de module **Train model** .

    ![Scherm opname van de juiste configuratie van de Train model-module. De lineaire regressie-module maakt verbinding met de linker poort van Train model module en de module voor gesplitste gegevens maakt verbinding met de juiste poort van Train model](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Selecteer de module **Train model** . Selecteer in het deel venster Eigenschappen de optie kolom kiezer starten en typ vervolgens de **prijs** naast **kolom namen toevoegen**. Prijs is de waarde die uw model gaat voors pellen

    ![Scherm afbeelding met de juiste configuratie voor de module kolom kiezer. Met regels > kolom Namen > ' prijs ' bevatten](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Uw experiment moet er als volgt uitzien:

    ![Scherm afbeelding met de juiste configuratie van het experiment na het toevoegen van de module Train model.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>Een machine learning model evalueren

Nu u het model hebt getraind met 70 procent van uw gegevens, kunt u het gebruiken om de andere 30 procent van de gegevens te scoren om te zien hoe goed uw model functioneert.

1. Typ **score model** in het zoekvak om de module **score model** te vinden en sleep de module naar het canvas van het experiment. Koppel de uitvoer van de module **Train model** aan de linker invoer poort van het **score model**. Koppel de test gegevens uitvoer (rechter poort) van de module **Split data** aan de juiste invoer poort van het **score model**.

1. Typ **Evaluate** in het zoekvak om het **evaluatie model** te vinden en sleep de module naar het canvas van het experiment. Koppel de uitvoer van de module **score model** aan de linker invoer van het evaluate- **model**. Het laatste experiment ziet er ongeveer als volgt uit:

    ![Scherm afbeelding met de definitieve juiste configuratie van het experiment.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Voer het experiment uit met behulp van de reken resource die u eerder hebt gemaakt.

1. Bekijk de uitvoer van de module **score model** door de uitvoer poort van het **score model** te selecteren en **visualiseren**te selecteren. De uitvoer bevat de voorspelde waarden voor de prijs en de bekende waarden uit de testgegevens.

    ![Scherm afbeelding van de uitvoer visualisatie die de kolom ' gescoorde label ' markeert](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Als u de uitvoer van de module **Evaluate model** wilt weer geven, selecteert u de uitvoer poort en selecteert u vervolgens **visualiseren**.

    ![Scherm opname met de resultaten van de evaluatie voor het laatste experiment.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

De volgende statistieken worden weer gegeven voor uw model:

* **Gemiddelde absolute fout (Mae)** : Het gemiddelde van absolute fouten (een fout is het verschil tussen de voorspelde waarde en de werkelijke waarde).
* **Wortel gemiddelde fout (RMSE)** : De vierkantswortel uit het gemiddelde aan kwadratische fouten voor de voorspellingen op basis van de testgegevensset.
* **Relatieve absolute fout**: Het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Relatieve kwadratische fout**: Het gemiddelde aan kwadratische fouten ten opzichte van het kwadratische verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Determinatiecoëfficiënt**: Dit is ook een statistische waarde die aangeeft hoe goed een model past bij de gegevens.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een kleine waarde geeft aan dat de voorspelling dichter bij de werkelijke waarde ligt. Voor een coëfficiënt van de bepaling is de waarde één (1,0), hoe beter de voor spellingen.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deel één van deze zelf studie hebt u de volgende stappen uitgevoerd:

* Een experiment gemaakt
* De gegevens voorbereiden
* Het model trainen
* Het model beoordelen en evalueren

In deel twee leert u hoe u uw model als een Azure-webservice implementeert.

> [!div class="nextstepaction"]
> [Door gaan met de implementatie van modellen](ui-tutorial-automobile-price-deploy.md)
