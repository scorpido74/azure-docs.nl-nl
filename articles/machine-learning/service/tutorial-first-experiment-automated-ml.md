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
ms.date: 09/26/2019
ms.openlocfilehash: 3ddd228488d8ba4adc6780db1f65fdb634291d3b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350498"
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

U voltooit de volgende proef installatie en voert de stappen uit op de pagina voor het land van de werk ruimte, een geconsolideerde interface met machine learning-hulpprogram ma's voor het uitvoeren van data Science-scenario's voor data Science-artsen van alle vaardigheids niveaus. De landings pagina voor de werk ruimte wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij de pagina voor het land van de [werk ruimte](https://ml.azure.com/workspaceportal/).

1. Selecteer uw abonnement en de werk ruimte die u hebt gemaakt.

1. Selecteer **aan de slag**.

1. Selecteer in het linkerdeel venster **automatische ml** onder het gedeelte **Auteur** .

   Aangezien dit uw eerste geautomatiseerde ML-experiment is, ziet u het scherm aan de slag.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Selecteer **experiment maken**. 

1. Voer de naam van dit experiment in:`my-1st-automl-experiment`

1. Selecteer **een nieuwe berekening maken** en configureer uw reken doel. Een compute-doel is een lokale of cloud-gebaseerde resource omgeving die wordt gebruikt om uw trainings script uit te voeren of uw service-implementatie te hosten. Voor dit experiment gebruiken we een op de cloud gebaseerde compute. 

   Veld | Description | Waarde voor zelf studie
   ----|---|---
   Compute-naam |Een unieke naam die uw Compute-context identificeert.|automl-compute
   Grootte&nbsp;van&nbsp;virtuele machine| Selecteer de grootte van de virtuele machine voor de reken kracht.|Standard_DS12_V2
   Min-maximum aantal knoop punten (in geavanceerde instellingen)| Als u wilt profiel gegevens, moet u één of meer knoop punten opgeven.|Minimum aantal knoop punten: 1<br>Maximum aantal knoop punten: 6

   >[!NOTE]
   >Voor deze zelf studie gebruikt u het standaard opslag account en de container die is gemaakt met uw nieuwe reken proces. Deze worden automatisch ingevuld in het formulier.
    
1. Selecteer **maken** om het Compute-doel op te halen. 

   **Dit duurt enkele minuten.** 

1. Nadat u hebt gemaakt, selecteert u uw nieuwe reken doel in de vervolg keuzelijst en selecteert u **volgende**.

1. Selecteer **uploaden uit het lokale bestand** om een nieuwe gegevensset te maken. 

    1. Selecteer **Bladeren**.
    
    1. Kies het bestand **bankmarketing_train. CSV** op de lokale computer. Dit is het bestand dat u hebt gedownload als een [vereiste](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Geef uw gegevensset een unieke naam en geef een optionele beschrijving op. 

    1. Selecteer **volgende** linksonder om het te uploaden naar de standaard container die automatisch is ingesteld tijdens het maken van uw werk ruimte. Open bare preview ondersteunt alleen het uploaden van lokale bestanden. 
    
       Wanneer het uploaden is voltooid, worden de instellingen en het voorbeeld formulier vooraf ingevuld op basis van het bestands type. 
       
    1. Controleer of de **instellingen en het voorbeeld** formulier als volgt zijn ingevuld en selecteer **volgende**.
        
        Veld|Description| Waarde voor zelf studie
        ---|---|---
        Bestandsindeling|Hiermee definieert u de indeling en het type van de gegevens die zijn opgeslagen in een bestand.| Met scheidingstekens
        Scheidingsteken|Een of meer tekens voor het opgeven van de grens tussen @ no__t-0 afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevens stromen. |Komma
        Encoding|Hiermee wordt aangegeven welke bits-schema tabel moet worden gebruikt om de gegevensset te lezen.| UTF-8
        Kolomkoppen| Hiermee wordt aangegeven hoe de headers van de gegevensset, indien aanwezig, worden behandeld.| Alle bestanden hebben dezelfde kopteksten
        Rijen overs Laan | Hiermee wordt aangegeven hoeveel, indien van toepassing, rijen in de gegevensset worden overgeslagen.| Geen
    
        ![Configuratie van het tabblad voor beeld](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Selecteer **classificatie** als de Voorspellings taak.

1. Selecteer **y** als doel kolom, wat u wilt voors pellen. Deze kolom geeft aan of de client is geabonneerd op een term deposit of niet.

1. Vouw **Geavanceerde instellingen** uit en vul de velden als volgt in. Deze instellingen zijn een betere controle over de trainings taak. Anders worden de standaard waarden toegepast op basis van het experiment en de gegevens.

   >[!NOTE]
   > In deze zelf studie stelt u geen metrische Score of maximale kernen per herhalings drempel in. U kunt ook voor komen dat algoritmen worden getest.
   
   Geavanceerde&nbsp;instellingen|Description|Waarde&nbsp;voor&nbsp;zelf studie
   ------|---------|---
   Primaire metriek| Evaluatie-metrische gegevens waarop het algoritme van de machine learning wordt gemeten.|AUC_weighted
   Afsluit criteria| Als aan een criterium wordt voldaan, wordt de trainings taak gestopt. |Tijd&nbsp;trainings&nbsp;taak: 5 <br> <br> Maximum&nbsp;aantal#herhalingen&#58;10&nbsp;&nbsp;
   Verwerking| Hiermee wordt de voor verwerking van automatische machine learning mogelijk gemaakt. Dit omvat het automatisch opschonen, voorbereiden en transformeren van gegevens voor het genereren van synthetische functies.| Inschakelen
   Validatie type | Kies een type Kruis validatie.|Kruis validatie met K-vouwen
   Aantal validaties | Aantal testen. | 2 Kruis validaties 
   Gelijktijdigheid| Het maximum aantal gelijktijdige herhalingen.|5
   
1. Selecteer **starten** om het experiment uit te voeren. Er wordt een scherm weer gegeven met een status bericht wanneer de voor bereiding van het experiment begint.

>[!IMPORTANT]
> Voor bereiding duurt **10-15 minuten** om de uitvoering van het experiment voor te bereiden. Na de uitvoering duurt **2-3 minuten meer voor elke iteratie**.  
>
> In productie zou u waarschijnlijk een beetje weg lopen. Voor deze zelf studie wordt u echter aangeraden om de iteratie resultaten te verkennen wanneer deze zijn voltooid terwijl de andere nog actief zijn. 

##  <a name="explore-iteration-results"></a>Iteratie resultaten verkennen

Wanneer het experiment wordt uitgevoerd, werkt het scherm het **iteratie diagram** en de **iteratie lijst** bij met de verschillende iteraties (modellen) die zijn gemaakt als ze zijn voltooid. Standaard worden de herhalingen gesorteerd op metrische Score. Voor deze zelf studie is het model dat de hoogste score op basis van de gekozen **AUC_weighted** metriek boven aan de lijst weer gegeven.

Terwijl u wacht totdat alle experiment-iteraties zijn voltooid, selecteert u de **naam** van een voltooide iteratie om de prestatie details te verkennen. 

Hieronder ziet u de grafieken en voert u metrische gegevens uit die zijn gegenereerd voor elke herhaling, zoals een curve voor het intrekken van een precisie, Verwar ring matrix, gewogen nauw keurigheid, enzovoort. 

![Details van herhaling uitvoeren](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Het model implementeren

Automatische machine learning op de landings pagina voor de werk ruimte kunt u in een paar stappen het beste model als een webservice implementeren. Implementatie is de integratie van het model, zodat dit kan voors pellen op nieuwe gegevens en mogelijke verkoop kansen kan identificeren. Voor dit experiment betekent de implementatie van een webservice dat de financiële instelling nu een iteratieve en schaal bare weboplossing heeft voor het identificeren van potentiële klanten met vaste termijn storting. 

Zodra de uitvoering is voltooid, gaat u terug naar de detail pagina **iteratie diagram** en **iteraties lijst** . 

In dit experiment wordt **VotingEnsemble** beschouwd als het beste model, op basis van de **AUC_weighted** -metriek.  We implementeren dit model, maar u wordt aangeraden de implementatie ongeveer 20 minuten te volt ooien. Het implementatie proces omvat verschillende stappen, waaronder het registreren van het model, het genereren van resources en het configureren van deze voor de webservice.

1. Selecteer de knop **beste model implementeren** in de rechter bovenhoek.

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
+ Zie het artikel over [geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md#classification) voor meer informatie over classificatie gegevens en diagrammen.

>[!NOTE]
> Deze gegevensset voor Bank marketing wordt beschikbaar gesteld onder [de Creative Commons (CCO: Open bare domein)](https://creativecommons.org/publicdomain/zero/1.0/). Alle rechten in de afzonderlijke inhoud van de Data Base worden in licentie gegeven onder de licentie voor de [Data Base-inhoud](https://creativecommons.org/publicdomain/zero/1.0/) en beschikbaar op [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Deze gegevensset is oorspronkelijk beschikbaar in de [icb machine learning-data base](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez en P. Rita. Een gegevensgestuurde benadering voor het voors pellen van het slagen van Bank telemarketing. Decision Support Systems, Elsevier, 62:22-31, juni 2014.
