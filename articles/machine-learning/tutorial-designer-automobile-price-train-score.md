---
title: 'Tutorial: Voorspel autoprijs met de ontwerper'
titleSuffix: Azure Machine Learning
description: Leer hoe u een machine learning-model traint, scoreert en implementeert met behulp van een drag-and-drop-interface. Deze tutorial is deel een van een tweedelige serie over het voorspellen van autoprijzen met behulp van lineaire regressie.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 0488002352d222abb0166737f9a042060b1a1bb1
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389423"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>Tutorial: Voorspel autoprijs met de ontwerper (preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In deze tweedelige zelfstudie leert u hoe u de Azure Machine Learning-ontwerper gebruiken om een machine learning-model te trainen en te implementeren dat de prijs van elke auto voorspelt. De ontwerper is een drag-and-drop tool waarmee u machine learning-modellen maken zonder één regel code.

In deel één van de tutorial leer je hoe je:

> [!div class="checklist"]
> * Maak een nieuwe pijplijn.
> * Gegevens importeren.
> * Gegevens voorbereiden.
> * Train een machine learning model.
> * Evalueer een machine learning-model.

In [deel twee](tutorial-designer-automobile-price-deploy.md) van de zelfstudie implementeert u uw model als een realtime eindpunt om de prijs van elke auto te voorspellen op basis van technische specificaties die u verzendt. 

> [!NOTE]
>Een voltooide versie van deze zelfstudie is beschikbaar als voorbeeldpijplijn.
>
>Om het te vinden, ga je naar de ontwerper in je werkruimte. Selecteer in de sectie **Nieuwe pijplijn** de optie Voorbeeld 1 **- Regressie: Prijsvoorspelling auto's(Basic).**

## <a name="create-a-new-pipeline"></a>Een nieuwe pijplijn maken

Azure Machine Learning-pijplijnen organiseren meerdere stappen voor machine learning en gegevensverwerking in één bron. Met pijplijnen u complexe machine learning-workflows voor projecten en gebruikers organiseren, beheren en hergebruiken.

Als u een Azure Machine Learning-pijplijn wilt maken, hebt u een Azure Machine Learning-werkruimte nodig. In deze sectie leert u hoe u beide bronnen maakt.

### <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

Om de ontwerper te kunnen gebruiken, hebt u eerst een Azure Machine Learning-werkruimte nodig. De werkruimte is de bron op het hoogste niveau voor Azure Machine Learning, het biedt een centrale plek om te werken met alle artefacten die u maakt in Azure Machine Learning.

Als u een Azure Machine Learning-werkruimte hebt met een Enterprise-editie, [gaat u naar de volgende sectie](#create-the-pipeline).

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>Maak de pijplijn

1. Meld u aan bij <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>en selecteer de werkruimte waarmee u wilt werken.

1. Selecteer **Ontwerper**.

    ![Schermafbeelding van de visuele werkruimte die laat zien hoe u toegang krijgt tot de ontwerper](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecteer **gebruiksvriendelijke voorgebouwde modules.**

1. Selecteer boven aan het canvas de standaardpijplijnnaam **Pipeline-Created-on**. Hernoem het naar *Automobile prijsvoorspelling*. De naam hoeft niet uniek te zijn.

## <a name="set-the-default-compute-target"></a>Het standaardgegevensdoel instellen

Een pijplijn wordt uitgevoerd op een compute target, dat is een compute resource die is gekoppeld aan uw werkruimte. Nadat u een rekendoel hebt gemaakt, u deze opnieuw gebruiken voor toekomstige uitvoeringen.

U een **standaardzoekdoel** instellen voor de hele pijplijn, waarmee elke module standaard hetzelfde rekendoel moet gebruiken. U echter rekendoelen per module opgeven.

1. Selecteer naast de naam van de pijplijn het](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) **tandwielpictogram** ![Screenshot van het tandwielpictogram boven aan het canvas om het deelvenster **Instellingen** te openen.

1. Selecteer in het deelvenster **Instellingen** rechts van het canvas de optie **Rekendoel selecteren**.

    Als u al een beschikbaar rekendoel hebt, u deze selecteren om deze pijplijn uit te voeren.

    > [!NOTE]
    > De ontwerper kan alleen experimenten uitvoeren op Azure Machine Learning Compute-doelen. Andere rekendoelen worden niet weergegeven.

1. Voer een naam in voor de compute resource.

1. Selecteer **Opslaan**.

    > [!NOTE]
    > Het duurt ongeveer vijf minuten om een compute resource te maken. Nadat de resource is gemaakt, u deze opnieuw gebruiken en deze wachttijd voor toekomstige uitvoeringen overslaan.
    >
    > De compute resource schaalt automatisch naar nul knooppunten wanneer deze niet actief is om kosten te besparen. Wanneer u het na een vertraging opnieuw gebruikt, u ongeveer vijf minuten wachttijd ervaren terwijl het een back-up opschaalt.

## <a name="import-data"></a>Gegevens importeren

Er zijn verschillende voorbeeldgegevenssets opgenomen in de ontwerper waarmee u experimenteren. Voor deze zelfstudie gebruikt u **auto's prijsgegevens (Raw).** 

1. Aan de linkerkant van het pijplijncanvas bevindt zich een palet van gegevenssets en modules. Selecteer **Gegevenssets**en bekijk de sectie **Voorbeelden** om de beschikbare voorbeeldgegevenssets weer te geven.

1. Selecteer de **gegevensset Autobevindt prijsgegevens (Raw)** en sleep deze naar het canvas.

   ![Gegevens naar canvas slepen](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>De gegevens visualiseren

U de gegevens visualiseren om inzicht te krijgen in de gegevensset die u zult gebruiken.

1. Selecteer de module **Auto's prijsgegevens (Raw).**

1. Selecteer **Uitvoer + logboek**in het deelvenster moduledetails rechts van het canvas .

1. Selecteer het grafiekpictogram om de gegevens te visualiseren.

    ![De gegevens visualiseren](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. Selecteer de verschillende kolommen in het gegevensvenster om informatie over elke kolom weer te geven.

    Elke rij vertegenwoordigt een auto en de variabelen die aan elke auto zijn gekoppeld, worden weergegeven als kolommen. Er zijn 205 rijen en 26 kolommen in deze gegevensset.

## <a name="prepare-data"></a>Gegevens voorbereiden

Gegevenssets vereisen doorgaans enige voorbewerking voordat ze worden geanalyseerd. Je hebt misschien een aantal ontbrekende waarden opgemerkt toen je de gegevensset inspecteerde. Deze ontbrekende waarden moeten worden gereinigd, zodat het model de gegevens correct kan analyseren.

### <a name="remove-a-column"></a>Een kolom verwijderen

Als je een model traint, moet je iets doen aan de gegevens die ontbreken. In deze gegevensset mist de kolom **genormaliseerde verliezen** veel waarden, dus u sluit die kolom helemaal uit van het model.

1. Vouw in het modulepalet links van het canvas de sectie **Gegevenstransformatie** uit en zoek de module **Kolommen selecteren in gegevensset.**

1. Sleep de module **Kolommen selecteren in gegevensset** naar het canvas. Zet de module onder de datasetmodule.

1. Verbind de **gegevensset 'Ruwe' voor auto's** met de module **Kolommen selecteren in gegevensset.** Sleep van de uitvoerpoort van de gegevensset, de kleine cirkel onder aan de gegevensset op het canvas, naar de invoerpoort van **Kolommen selecteren in gegevensset**, de kleine cirkel boven aan de module.

    > [!TIP]
    > U maakt een stroom van gegevens door uw pijplijn wanneer u de uitvoerpoort van de ene module aansluit op een invoerpoort van een andere.
    >

    ![Modules verbinden](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecteer de module **Kolommen selecteren in gegevensset.**

1. Selecteer **kolom bewerken**in het deelvenster moduledetails rechts van het canvas .

1. Vouw de vervolgkeuzelijst **Kolomnamen** uit naast **Include**en selecteer **Alle kolommen**.

1. Selecteer **+** de optie om een nieuwe regel toe te voegen.

1. Selecteer In de vervolgkeuzemenu's de optie **Namen uitsluiten** en Kolom **selecteren**.
    
1. Voer *genormaliseerde verliezen* in het tekstvak in.

1. Selecteer rechtsonder **Opslaan** om de kolomkiezer te sluiten.

    ![Een kolom uitsluiten](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecteer de module **Kolommen selecteren in gegevensset.** 

1. Selecteer in het deelvenster moduledetails rechts van het canvas het tekstvak **Opmerking** en voer *genormaliseerde verliezen uitsluiten*in.

    Er worden opmerkingen weergegeven in de grafiek om u te helpen uw pijplijn te organiseren.

### <a name="clean-missing-data"></a>Ontbrekende gegevens opschonen

Uw gegevensset heeft nog steeds ontbrekende waarden nadat u de kolom **genormaliseerde verliezen hebt** verwijderd. U de resterende ontbrekende gegevens verwijderen met behulp van de module **Ontbrekende gegevens opschonen.**

> [!TIP]
> Het reinigen van de ontbrekende waarden uit invoergegevens is een voorwaarde voor het gebruik van de meeste modules in de ontwerper.

1. Vouw in het modulepalet links van het canvas de sectie **Gegevenstransformatie**uit en zoek de module **Ontbrekende gegevens opschonen.**

1. Sleep de module **Ontbrekende gegevens opschonen** naar het pijplijncanvas. Sluit deze verbinding met de module **Kolommen selecteren in gegevensset.** 

1. Selecteer de module **Ontbrekende gegevens opschonen.**

1. Selecteer **Kolom bewerken**in het deelvenster moduledetails rechts van het canvas .

1. Vouw in het **venster Kolommen dat moet worden schoongemaakt** en wordt weergegeven het vervolgkeuzemenu naast Include **uit.** Alle **kolommen** selecteren, alle kolommen

1. Selecteer **Opslaan**

1. Selecteer in het deelvenster moduledetails rechts van het canvas de optie **Hele rij verwijderen** onder **reinigingsmodus**.

1. Selecteer in het deelvenster Moduledetails rechts van het canvas het vak **Opmerking** en voer *rijen voor ontbrekende waarde verwijderen*in. 

    Uw pijplijn moet er nu ongeveer zo uitzien:
    
    ![Kolom selecteren](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>Train een machine learning model

Nu u de modules hebt om de gegevens te verwerken, u de trainingsmodules instellen.

Omdat u de prijs wilt voorspellen, wat een getal is, u een regressiealgoritme gebruiken. Voor dit voorbeeld gebruikt u een lineair regressiemodel.

### <a name="split-the-data"></a>De gegevens splitsen

Het splitsen van gegevens is een veelvoorkomende taak in machine learning. U splitst uw gegevens op in twee afzonderlijke gegevenssets. De ene dataset traint het model en de andere test hoe goed het model heeft gepresteerd.

1. Vouw in het modulepalet de sectie **Gegevenstransformatie** uit en zoek de module **Gesplitste gegevens.**

1. Sleep de module **Gesplitste gegevens** naar het pijplijncanvas.

1. Sluit de linkerpoort van de module **Ontbrekende gegevens op** met de module **Gesplitste gegevens.**

    > [!IMPORTANT]
    > Zorg ervoor dat de linkeruitvoerpoorten van **Clean Missing Data** verbinding maken met **Gesplitste gegevens.** De linkerpoort bevat de opgeschoonde gegevens. De juiste poort bevat de gediscartte gegevens.

1. Selecteer de module **Gesplitste gegevens.**

1. Stel in het deelvenster moduledetails rechts van het canvas de **fractie van rijen in de eerste uitvoerset in** op 0,7.

    Deze optie splitst 70 procent van de gegevens om het model te trainen en 30 procent voor het testen ervan. De 70 procent dataset zal toegankelijk zijn via de linker uitvoerpoort. De overige gegevens zijn beschikbaar via de juiste uitvoerpoort.

1. Selecteer in het deelvenster moduledetails rechts van het canvas het vak **Opmerking** en voer *De gegevensset splitsen in trainingsset (0.7) en testset (0.3) in.*

### <a name="train-the-model"></a>Het model trainen

Train het model door het een dataset te geven die de prijs omvat. Het algoritme construeert een model dat de relatie tussen de functies en de prijs zoals gepresenteerd door de trainingsgegevens verklaart.

1. Vouw **machine learning-algoritmen**uit in het modulepalet.
    
    Met deze optie worden verschillende categorieën modules weergegeven die u gebruiken om leeralgoritmen te initialiseren.

1. Selecteer **Regressielineaire** > **regressie**en sleep deze naar het pijplijncanvas.

1. Sluit de uitvoer van de **module Lineaire regressie** aan op de linkerinvoer van de module **Treinmodel.**

1. Vouw in het modulepalet de **sectiemoduletraining**uit en sleep de module **Treinmodel** naar het canvas.

1. Selecteer de module **Treinmodel** en sleep deze naar het pijplijncanvas.

1. Sluit de trainingsgegevensuitvoer (linkerpoort) van de module **Gesplitste gegevens** aan op de juiste invoer van de **module Treinmodel.**
    
    > [!IMPORTANT]
    > Zorg ervoor dat de linkeruitvoerpoorten van **Split Data** verbinding maken met **Train Model.** De linkerpoort bevat de trainingsset. De juiste poort bevat de testset.

    ![Schermafbeelding van de juiste configuratie van de module Treinmodel. De module Lineaire Regressie maakt verbinding met de linkerpoort van de module Treinmodel en de module Split Data maakt verbinding met de rechterhaven van Train Model](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. Selecteer de module **Train Model**.

1. Selecteer **kolomkiezer** bewerken in het deelvenster moduledetails rechts van het canvas.

1. Vouw in het dialoogvenster **Kolom label** het vervolgkeuzemenu uit en selecteer **Kolomnamen**. 

1. Voer in het tekstvak de *prijs* in om de waarde op te geven die uw model gaat voorspellen.

    >[!IMPORTANT]
    > Zorg ervoor dat u de kolomnaam precies invoert. Niet profiteren van **de prijs**. 

    Uw pijplijn moet er als volgt uitzien:

    ![Schermafbeelding van de juiste configuratie van de pijplijn na het toevoegen van de module Treinmodel.](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

### <a name="add-the-score-model-module"></a>De module Scoremodel toevoegen

Nadat u uw model hebt getraind met behulp van 70 procent van de gegevens, u deze gebruiken om de andere 30 procent te scoren om te zien hoe goed uw model functioneert.

1. Voer *het scoremodel* in het zoekvak in om de module **Scoremodel** te vinden. Sleep de module naar het pijplijncanvas. 

1. Koppel de uitvoer van de module **Train Model** aan de linkerinvoerpoort van de module **Score Model**. Koppel de testgegevensuitvoer (rechterpoort) van de module **Split Data** aan de rechterinvoerpoort van de module **Score Model**.

### <a name="add-the-evaluate-model-module"></a>De module Evaluate Model toevoegen

Gebruik de module **Model evalueren** om te evalueren hoe goed uw model de testgegevensset heeft gescoord.

1. Voer *evalueren* in het zoekvak in om de module **Model evalueren** te zoeken. Sleep de module naar het pijplijncanvas. 

1. Sluit de uitvoer van de module **Scoremodel** aan op de linkerinvoer van **Het model evalueren**. 

    De uiteindelijke pijplijn moet er ongeveer zo uitzien:

    ![Schermafbeelding van de juiste configuratie van de pijplijn.](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="submit-the-pipeline"></a>De pijplijn verzenden

Nu uw pijplijn helemaal is ingesteld, u een pijplijnrun indienen om uw machine learning-model te trainen. U op elk gewenst moment een geldige pijplijnuitvoeren indienen, die kan worden gebruikt om wijzigingen in uw pijplijn tijdens de ontwikkeling te bekijken.

1. Selecteer Boven aan het canvas **Verzenden**.

1. Selecteer in het dialoogvenster **Pijplijnrun instellen** de optie **Nieuw maken**.

    > [!NOTE]
    > Experimenten groep soortgelijke pijplijn loopt samen. Als u een pijplijn meerdere keren uitvoert, u hetzelfde experiment voor opeenvolgende uitvoeringen selecteren.

    1. Voer een beschrijvende naam in voor **Nieuwe experimentnaam**.

    1. Selecteer **Indienen**.
    
    U de status en details van de run rechtsboven in het canvas bekijken.
    
    Als dit de eerste run is, kan het tot 20 minuten duren voordat de pijplijn is voltooid. De standaardcompute-instellingen hebben een minimale nodegrootte van 0, wat betekent dat de ontwerper resources moet toewijzen nadat deze niet is inactief. Herhaalde pijplijnuitvoeringen nemen minder tijd in beslag omdat de compute resources al zijn toegewezen. Daarnaast gebruikt de ontwerper in de cache resultaten voor elke module om de efficiëntie verder te verbeteren.

### <a name="view-scored-labels"></a>Gescoorde labels weergeven

Nadat de run is voltooid, u de resultaten van de pijplijnrun bekijken. Kijk eerst naar de voorspellingen die door het regressiemodel worden gegenereerd.

1. Selecteer de module **Model score** om de uitvoer weer te geven.

1. Selecteer in het deelvenster moduledetails rechts van het canvas de ![optie](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) Uitvoer **+ logboeken** > pictogram afbeelding van het grafiekpictogram om resultaten weer te geven.

    Hier ziet u de voorspelde prijzen en de werkelijke prijzen van de testgegevens.

    ![Schermafbeelding van de uitvoervisualisatie waarin de kolom Gescoord label wordt gemarkeerd](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>Modellen evalueren

Gebruik het **evaluatiemodel** om te zien hoe goed het getrainde model op de testgegevensset heeft gepresteerd.

1. Selecteer de module **Model evalueren** om de uitvoer weer te geven.

1. Selecteer in het deelvenster moduledetails rechts van het canvas de ![optie](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) Uitvoer **+ logboeken** > pictogram afbeelding van het grafiekpictogram om resultaten weer te geven.

De volgende statistieken worden weergegeven voor uw model:

* **Gemiddelde Absolute Fout (MAE)**: Het gemiddelde van absolute fouten. Een fout is het verschil tussen de voorspelde waarde en de werkelijke waarde.
* **Root Mean Squared Error (RMSE)**: De vierkantswortel van het gemiddelde van kwadraatfouten van voorspellingen die op de testgegevensset worden gemaakt.
* **Relative Absolute Error**: het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Relative Squared Error**: het gemiddelde aan gekwadrateerde fouten ten opzichte van het gekwadrateerde verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Bepalingscoëfficiënt**: Ook wel bekend als de R kwadraatwaarde, geeft deze statistische statistiek aan hoe goed een model bij de gegevens past.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een kleinere waarde geeft aan dat de voorspellingen dichter bij de werkelijke waarden staan. Voor de bepalingscoëfficiënt geldt: hoe dichter de waarde ervan bij één (1,0), hoe beter de voorspellingen.

## <a name="clean-up-resources"></a>Resources opschonen

Sla deze sectie over als u verder wilt gaan met deel 2 van de zelfstudie, [modellen implementeren](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deel twee leert u hoe u uw model implementeren als een realtime eindpunt.

> [!div class="nextstepaction"]
> [Doorgaan met het implementeren van modellen](tutorial-designer-automobile-price-deploy.md)
