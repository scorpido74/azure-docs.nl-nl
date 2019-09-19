---
title: Uw eerste geautomatiseerde machine learning-experiment maken
titleSuffix: Azure Machine Learning
description: Leer hoe u een classificatie model traint en implementeert met geautomatiseerde machine learning op de landings pagina van Azure Machine Learning werk ruimte (preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 2422a4525c94f3997dd0a9a0859135e9acf59ffa
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092010"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Zelfstudie: Uw eerste classificatie model maken met geautomatiseerde machine learning

In deze zelf studie leert u hoe u uw eerste geautomatiseerde machine learning-experiment kunt maken via de pagina voor het land van de werk ruimte (preview) zonder dat u maar één regel code hoeft te schrijven. In dit voor beeld wordt een classificatie model gemaakt om te voors pellen of een client zich abonneert op een vaste termijn storting met een financiële instelling.

Met geautomatiseerde machine learning kunt u tijdrovende taken automatiseren. Automatische machine learning snel door lopen met veel combi Naties van algoritmen en Hyper parameters om u te helpen het beste model te vinden op basis van een succes volle waarde van uw keuze.

In deze zelf studie leert u hoe u de volgende taken kunt uitvoeren:

> [!div class="checklist"]
> * Een Azure Machine Learning-werkruimte maken.
> * Voer een geautomatiseerd machine learning experiment uit.
> * Experiment details weer geven.
> * Het model implementeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree).

* Down load het gegevens bestand [**bankmarketing_train. CSV**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . De **y** -kolom geeft aan of een klant zich heeft geabonneerd op een vaste termijn storting, die later wordt aangeduid als de doel kolom voor voor spellingen in deze zelf studie. 

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werk ruimte is een Foundation-resource in de cloud die u gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren. Uw Azure-abonnement en resource groep worden gebonden aan een eenvoudig verbruikte object in de service. 

U maakt een werk ruimte via de Azure Portal, een webconsole voor het beheren van uw Azure-resources. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Noteer uw **werk ruimte** en **abonnement**. U hebt deze nodig om ervoor te zorgen dat u op de juiste plaats uw experiment kunt maken. 

## <a name="create-and-run-the-experiment"></a>Het experiment maken en uitvoeren

U voltooit de volgende proef installatie en voert de stappen uit op de pagina voor het land van de werk ruimte, een geconsolideerde interface met machine learning-hulpprogram ma's voor het uitvoeren van data Science-scenario's voor data Science-artsen van alle vaardigheids niveaus.

1. Meld u aan bij de pagina voor het land van de [werk ruimte](https://ml.azure.com/workspaceportal/).

1. Selecteer uw abonnement en de werk ruimte die u hebt gemaakt.

1. Selecteer **aan de slag**.

1.  Selecteer **automatische ml** onder het gedeelte **ontwerpen** in het deel venster aan de linkerkant.
Het scherm **aan** de slag wordt weer gegeven, omdat dit uw eerste experiment met automatische machine learning is.

    ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selecteer **experiment maken**. 

1. Voer **My-1ste-automl-experimenten** in als experiment naam.

1. Selecteer **een nieuwe Compute maken**. Een compute is een lokale of Cloud resource omgeving die wordt gebruikt om uw trainings script uit te voeren of uw service-implementatie te hosten. Voor dit experiment gebruiken we een op de cloud gebaseerde compute. 

    1. Configureer uw berekenings context voor dit experiment.
        
        Veld | Value
        ----|---
        Compute-naam |  Voer een unieke naam in die uw berekenings context identificeert. Voor dit voor beeld gebruikt u **automl-Compute**.
        Grootte van de virtuele machine| Selecteer de grootte van de virtuele machine voor de reken kracht. Gebruik de standaard **Standard_DS12_V2**.
        Aanvullende instellingen| *Min. knoop punt*: 1. Als u gegevens profilering wilt inschakelen, moet u een of meer knoop punten hebben. <br> *Maximum knoop punt*: 6.
 
    1. Selecteer **maken**om uw nieuwe Compute te maken. Dit duurt enkele minuten. 

    1. Wanneer het maken is voltooid, selecteert u de nieuwe reken kracht in de vervolg keuzelijst en selecteert u vervolgens **volgende**.

    >[!NOTE]
    >Voor deze zelf studie gebruikt u het standaard opslag account en de container die is gemaakt met uw nieuwe reken proces. Deze worden automatisch ingevuld in het formulier.

1. Selecteer **uploaden uit het lokale bestand**. Vanuit deze zelf studie maakt u een nieuwe gegevensset met het **bankmarketing_train. CSV** -bestand dat u eerder hebt gedownload. 

    1. Selecteer **Bladeren** en selecteer vervolgens het bestand **bankmarketing_train. CSV** op de lokale computer. 

    1. Geef uw gegevensset een unieke naam en geef een optionele beschrijving op. 

    1. Selecteer **volgende** om het te uploaden naar de standaard container die automatisch is ingesteld tijdens het maken van de werk ruimte. Open bare preview ondersteunt alleen het uploaden van lokale bestanden. 

    1. Wanneer het uploaden is voltooid, worden de **instellingen en het voorbeeld** formulier op intelligente wijze ingevuld op basis van het bestands type. Zorg ervoor dat het formulier is ingevuld als volgt.
        
        Veld|Value
        ---|---
        Bestands indeling| Gescheiden
        Scheidingsteken| Komma
        Encoding| UTF-8
        Kolomkoppen| Alle bestanden hebben dezelfde headers
        Rijen overs Laan | Geen

        >[!NOTE]
        > Als een van de instellingen in dit formulier is bijgewerkt, wordt de preview-versie dienovereenkomstig bijgewerkt.

        Selecteer **Volgende**.
    

    1. Met het **schema** formulier kunt u uw gegevens voor dit experiment verder configureren. Voor dit voor beeld selecteert u de wissel knop voor de functie **day_of_week** , zodat u deze niet voor dit experiment kunt gebruiken. Selecteer **gereed**om het uploaden en het maken van de gegevensset voor uw experiment te volt ooien.

        ![Configuratie van het tabblad voor beeld](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Selecteer **classificatie** als de Voorspellings taak.

1. Selecteer **y** als doel kolom, wat u wilt voors pellen. Deze kolom geeft aan of de client is geabonneerd op een term deposit of niet.

1. Vouw **Geavanceerde instellingen** uit en vul de velden als volgt in.

    Geavanceerde instellingen|Value
    ------|------
    Primaire metriek| AUC_weighted 
    Afsluit criteria| Wanneer aan een van deze criteria wordt voldaan, wordt de trainings taak beëindigd voordat deze volledig is voltooid: <br> *Tijd trainings taak (minuten)* : 5  <br> *Maximum aantal herhalingen*: 10 
    Verwerking| Hiermee wordt de voor verwerking van automatische machine learning mogelijk gemaakt. Dit omvat het automatisch opschonen, voorbereiden en transformeren van gegevens voor het genereren van synthetische functies.
    Validatie| Selecteer Kruis validatie met K-vouwen en **2** voor het aantal kruisingen. 
    Gelijktijdigheid| Selecteer **5** voor het maximum aantal gelijktijdige herhalingen.

   >[!NOTE]
   > Voor dit experiment stelt u de drempel waarde voor metrische gegevens of maximum aantal kernen per iteraties niet in. Het is ook niet mogelijk om te voor komen dat algoritmen worden getest.

1. Selecteer **starten** om het experiment uit te voeren.

   Wanneer het experiment wordt gestart, ziet u een leeg scherm met een status bericht aan de bovenkant.

Het experiment voorbereidende proces duurt enkele minuten. Wanneer dit proces is voltooid, wordt het status bericht gewijzigd in **uitvoeren**.

##  <a name="view-experiment-details"></a>Details van experiment weer geven

Wanneer het experiment wordt uitgevoerd, werkt het scherm het **iteratie diagram** en de **iteratie lijst** bij met de verschillende iteraties (modellen) die worden uitgevoerd. De lijst met herhalingen wordt gesorteerd op metrische Score. Standaard beoordeelt het model dat het hoogste verlaagt op basis van onze **AUC_weighted** -metriek, boven aan de lijst.

>[!WARNING]
> Het duurt enkele minuten voordat de trainings taken zijn voltooid.

[![Dash board Details uitvoeren](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Het model implementeren

Met behulp van automatische machine learning op de landings pagina voor de werk ruimte, kunt u in een paar stappen het beste model als een webservice implementeren. Implementatie is de integratie van het model, zodat dit kan voors pellen op nieuwe gegevens en mogelijke verkoop kansen kan identificeren. Voor dit experiment betekent de implementatie van een webservice dat de financiële instelling nu een iteratieve en schaal bare weboplossing heeft voor het identificeren van potentiële klanten met vaste termijn storting. 

In dit experiment wordt **VotingEnsemble** beschouwd als het beste model, op basis van de **AUC_weighted** -metriek.  We implementeren dit model, maar u wordt aangeraden de implementatie ongeveer 20 minuten te volt ooien. Het implementatie proces omvat verschillende stappen, waaronder het registreren van het model, het genereren van resources en het configureren van deze voor de webservice.

1. Selecteer op de pagina **detail gegevens uitvoeren** de knop **beste model implementeren** in de rechter bovenhoek.

1. Vul het deel venster **beste model implementeren** als volgt in:

    Veld| Value
    ----|----
    Implementatienaam| mijn-automl-implementeren
    Beschrijving van implementatie| Mijn eerste geautomatiseerde machine learning-experiment implementatie
    Score script| Automatisch genereren
    Omgevings script| Automatisch genereren
    
1. Selecteer **Implementeren**.  

    Het bericht implementatie voltooid wordt weer gegeven wanneer de implementatie is voltooid.
    
U hebt nu een Operational web service voor het genereren van voor spellingen.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatie bestanden zijn groter dan gegevens en experimenteer bestanden, zodat ze meer kosten in beslaan. Verwijder alleen de implementatie bestanden om de kosten voor uw account te minimaliseren, of als u uw werk ruimte en de bestanden wilt laten experimenteren. U kunt ook de hele resource groep verwijderen als u niet van plan bent om een van de bestanden te gebruiken.  

### <a name="delete-the-deployment-instance"></a>Het implementatie-exemplaar verwijderen

Verwijder alleen het implementatie-exemplaar van de Azure Portal als u de resource groep en-werk ruimte wilt blijven gebruiken voor andere zelf studies en verkennen. 

1. Ga naar de [Azure Portal](https://portal.azure.com//). Navigeer naar uw werk ruimte en selecteer aan de linkerkant onder het deel venster **assets** de optie **implementaties**. 

1. Selecteer de implementatie die u wilt verwijderen en selecteer **verwijderen**. 

1. Selecteer **door gaan**.

### <a name="delete-the-resource-group"></a>De resource groep verwijderen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie voor automatisch machine learning hebt u de landings pagina voor de werk ruimte gebruikt voor het maken en implementeren van een classificatie model. Zie deze artikelen voor meer informatie en volgende stappen:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](how-to-consume-web-service.md)

+ Meer informatie over voor [verwerking](how-to-create-portal-experiments.md#preprocess).
+ Meer informatie over [gegevens profilering](how-to-create-portal-experiments.md#profile).
+ Meer informatie over [automatische machine learning](concept-automated-ml.md).

>[!NOTE]
> Deze gegevensset voor Bank marketing wordt beschikbaar gesteld onder [de Creative Commons (CCO: Open bare domein)](https://creativecommons.org/publicdomain/zero/1.0/). Alle rechten in de afzonderlijke inhoud van de Data Base worden in licentie gegeven onder de licentie voor de [Data Base-inhoud](https://creativecommons.org/publicdomain/zero/1.0/) en beschikbaar op [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Deze gegevensset is oorspronkelijk beschikbaar in de [icb machine learning-data base](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez en P. Rita. Een gegevensgestuurde benadering voor het voors pellen van het slagen van Bank telemarketing. Decision Support Systems, Elsevier, 62:22-31, juni 2014.
