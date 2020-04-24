---
title: Automatische ML-classificatie modellen maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen van & implementatie van classificatie modellen met de geautomatiseerde machine learning-interface van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 03/04/2020
ms.openlocfilehash: b5a335a3f215ad5883b1b223245ca9d3f9967c3b
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366531"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Zelf studie: een classificatie model maken met automatische MILLILITERs in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In deze zelf studie leert u hoe u een basis classificatie model maakt zonder een enkele regel code te schrijven met behulp van de geautomatiseerde machine learning-interface van Azure Machine Learning. Dit classificatie model wordt voor speld als een client zich abonneert op een vaste termijn storting met een financiële instelling.

Met geautomatiseerde machine learning kunt u tijdrovende taken automatiseren. Automatische machine learning snel door lopen met veel combi Naties van algoritmen en Hyper parameters om u te helpen het beste model te vinden op basis van een succes volle waarde van uw keuze.

In deze zelf studie leert u hoe u de volgende taken kunt uitvoeren:

> [!div class="checklist"]
> * Maak een Azure Machine Learning-werk ruimte.
> * Een geautomatiseerd machine learning-experiment uitvoeren.
> * Experiment details weer geven.
> * Het model implementeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree).

* Down load het [**bankmarketing_train. CSV**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) -gegevens bestand. De **y** -kolom geeft aan of een klant zich heeft geabonneerd op een vaste termijn storting, die later wordt aangeduid als de doel kolom voor voor spellingen in deze zelf studie. 

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werk ruimte is een Foundation-resource in de cloud die u gebruikt om machine learning modellen te experimenteren, te trainen en te implementeren. Uw Azure-abonnement en resource groep worden gebonden aan een eenvoudig verbruikte object in de service. 

U maakt een werk ruimte via de Azure Portal, een webconsole voor het beheren van uw Azure-resources.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Noteer uw **werk ruimte** en **abonnement**. U hebt deze nodig om ervoor te zorgen dat u op de juiste plaats uw experiment kunt maken. 

## <a name="create-and-run-the-experiment"></a>Het experiment maken en uitvoeren

U voltooit de volgende proef versie en voert stappen uit via Azure machine learning op https://ml.azure.com, een geconsolideerde webinterface met machine learning-hulpprogram ma's voor het uitvoeren van data Science-scenario's voor data Wetenschappen van alle vaardigheids niveaus. Deze interface wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij [Azure machine learning](https://ml.azure.com).

1. Selecteer uw abonnement en de werk ruimte die u hebt gemaakt.

1. Selecteer **aan de slag**.

1. Selecteer in het linkerdeel venster **automatische ml** onder het gedeelte **Auteur** .

   Aangezien dit uw eerste geautomatiseerde ML-experiment is, ziet u een lege lijst en koppelingen naar documentatie.

   ![Pagina Aan de slag](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecteer **nieuwe automatische ml-uitvoering**. 

1. Maak een nieuwe gegevensset door te selecteren **uit lokale bestanden** in de vervolg keuzelijst **+ gegevensset maken** . 

    1. Geef in het formulier **basis informatie** uw gegevensset een naam en geef een optionele beschrijving op. De automatische ML-interface ondersteunt momenteel alleen TabularDatasets, zodat het type gegevensset standaard wordt ingesteld op *tabellair*.

    1. Selecteer **volgende** linksonder

    1. Selecteer op het formulier **gegevens opslag en bestands selectie** de standaard gegevens opslag die automatisch is ingesteld tijdens het maken van de werk ruimte, **workspaceblobstore (Azure Blob Storage)**. Hier uploadt u uw gegevens bestand om het beschikbaar te maken voor uw werk ruimte.

    1. Selecteer **Bladeren**.
    
    1. Kies het **bankmarketing_train. CSV** -bestand op uw lokale computer. Dit is het bestand dat u hebt gedownload als een [vereiste](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Geef uw gegevensset een unieke naam en geef een optionele beschrijving op. 

    1. Selecteer **volgende** linksonder om het te uploaden naar de standaard container die automatisch is ingesteld tijdens het maken van uw werk ruimte.  
    
       Wanneer het uploaden is voltooid, worden de instellingen en het voorbeeld formulier vooraf ingevuld op basis van het bestands type. 
       
    1. Controleer of de **instellingen en het voorbeeld** formulier als volgt zijn ingevuld en selecteer **volgende**.
        
        Veld|Beschrijving| Waarde voor zelf studie
        ---|---|---
        Bestands indeling|Hiermee definieert u de indeling en het type van de gegevens die zijn opgeslagen in een bestand.| Gescheiden
        Scheidingsteken|Een of meer tekens voor het opgeven van de&nbsp; grens tussen afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevens stromen. |Komma
        Encoding|Hiermee wordt aangegeven welke bits-schema tabel moet worden gebruikt om de gegevensset te lezen.| UTF-8
        Kolomkoppen| Hiermee wordt aangegeven hoe de headers van de gegevensset, indien aanwezig, worden behandeld.| Alle bestanden hebben dezelfde headers
        Rijen overs Laan | Hiermee wordt aangegeven hoeveel, indien van toepassing, rijen in de gegevensset worden overgeslagen.| Geen

    1. Met het **schema** formulier kunt u uw gegevens voor dit experiment verder configureren. Voor dit voor beeld selecteert u de wissel knop voor de functie **day_of_week** , zodat u deze niet voor dit experiment kunt gebruiken. Selecteer **Next**.

        ![Configuratie van het tabblad voor beeld](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. Controleer op het formulier **Details bevestigen** of de informatie overeenkomt met wat u eerder hebt ingevuld in de **basis informatie** en- **instellingen en de preview** -formulieren.
    1. Selecteer **maken** om het maken van de gegevensset te volt ooien.
    1. Selecteer uw gegevensset zodra deze in de lijst wordt weer gegeven.
    1. Bekijk het **voor beeld** van de gegevens om te controleren of **day_of_week** vervolgens niet is inge sloten. Selecteer **OK**.

    1. Selecteer **volgende**.

1. Vul het formulier voor het uitvoeren van de **Configure** als volgt in:
    1. Voer de naam van dit experiment in:`my-1st-automl-experiment`

    1. Selecteer **y** als doel kolom, wat u wilt voors pellen. Deze kolom geeft aan of de client is geabonneerd op een term deposit of niet.
    1. Selecteer **een nieuwe berekening maken** en configureer uw reken doel. Een compute-doel is een lokale of cloud-gebaseerde resource omgeving die wordt gebruikt om uw trainings script uit te voeren of uw service-implementatie te hosten. Voor dit experiment gebruiken we een op de cloud gebaseerde compute. 

        Veld | Beschrijving | Waarde voor zelf studie
        ----|---|---
        Compute name |Een unieke naam die uw Compute-context identificeert.|automl-compute
        Grootte&nbsp;van&nbsp;virtuele machine| Selecteer de grootte van de virtuele machine voor de reken kracht.|Standard_DS12_V2
        Min-maximum aantal knoop punten (in geavanceerde instellingen)| Als u wilt profiel gegevens, moet u één of meer knoop punten opgeven.|Minimum aantal knoop punten: 1<br>Maximum aantal knoop punten: 6
  
        1. Selecteer **maken** om het Compute-doel op te halen. 

            **Dit duurt enkele minuten.** 

        1. Nadat u hebt gemaakt, selecteert u uw nieuwe reken doel in de vervolg keuzelijst.

    1. Selecteer **Next**.

1. Selecteer op het **taak type en het instellingen** formulier **classificatie** als het taak type machine learning.

    1. Selecteer **aanvullende configuratie-instellingen weer geven** en vul de velden in als volgt. Deze instellingen zijn een betere controle over de trainings taak. Anders worden de standaard waarden toegepast op basis van het experiment en de gegevens.

        >[!NOTE]
        > In deze zelf studie stelt u geen metrische Score of maximale kernen per herhalings drempel in. U kunt ook voor komen dat algoritmen worden getest.
   
        Aanvullende&nbsp;configuraties|Beschrijving|Waarde&nbsp;voor&nbsp;zelf studie
        ------|---------|---
        Primaire metriek| Evaluatie-metrische gegevens waarop het algoritme van de machine learning wordt gemeten.|AUC_weighted
        Automatische parametrisatie| Hiermee wordt voor verwerking ingeschakeld. Dit omvat het automatisch opschonen, voorbereiden en transformeren van gegevens voor het genereren van synthetische functies.| Inschakelen
        Geblokkeerde algoritmen | Algoritmen die u wilt uitsluiten van de trainings taak| Geen
        Criterium afsluiten| Als aan een criterium wordt voldaan, wordt de trainings taak gestopt. |Tijd&nbsp;trainings&nbsp;taak (uren): 1 <br> Drempel&nbsp;waarde&nbsp;voor metrische Score: geen
        Validatie | Kies een type Kruis validatie en aantal testen.|Validatie type:<br>&nbsp;Kruis validatie met&nbsp;k-vouwen <br> <br> Aantal validaties: 2
        Gelijktijdigheid| Het maximum aantal parallelle iteraties dat per iteratie wordt uitgevoerd| Maximum&nbsp;aantal&nbsp;gelijktijdige herhalingen: 5
        
        Selecteer **Opslaan**.

1. Selecteer **volt ooien** om het experiment uit te voeren. Het scherm **Details uitvoeren** wordt geopend met de **uitvoerings status** aan de bovenkant wanneer de voor bereiding van het experiment begint.

>[!IMPORTANT]
> Voor bereiding duurt **10-15 minuten** om de uitvoering van het experiment voor te bereiden.
> Na de uitvoering duurt **2-3 minuten meer voor elke iteratie**.  
> Selecteer regel matig **vernieuwen** om de status van de uitvoering van het experiment te bekijken.
>
> In productie zou u waarschijnlijk een beetje weg lopen. Voor deze zelf studie wordt u echter aangeraden om de geteste algoritmen op het tabblad **modellen** te verkennen wanneer deze zijn voltooid terwijl de andere nog actief zijn. 

##  <a name="explore-models"></a>Modellen verkennen

Ga naar het tabblad **modellen** om de algoritmen (modellen) te zien die zijn getest. Standaard worden de modellen gesorteerd op basis van de metrische Score wanneer ze zijn voltooid. Voor deze zelf studie wordt het model dat de hoogste score op basis van de gekozen **AUC_weighted** metriek boven aan de lijst weer gegeven.

Terwijl u wacht tot alle experimentele modellen zijn voltooid, selecteert u de naam van het **algoritme** van een voltooid model om de prestatie details te verkennen. 

In het volgende voor beeld wordt genavigeerd door de **model Details** en de **Visualisaties** tabbladen om de eigenschappen, metrische gegevens en prestatie grafieken van het geselecteerde model weer te geven. 

![Details van herhaling uitvoeren](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Het beste model implementeren

Met de geautomatiseerde machine learning-interface kunt u in een paar stappen het beste model als een webservice implementeren. Implementatie is de integratie van het model, zodat dit kan voors pellen op nieuwe gegevens en mogelijke verkoop kansen kan identificeren. 

Voor dit experiment betekent de implementatie van een webservice dat de financiële instelling nu een iteratieve en schaal bare weboplossing heeft voor het identificeren van potentiële klanten met vaste termijn storting. 

Zodra de uitvoering is voltooid, gaat u terug naar de **detail pagina uitvoeren** en selecteert u het tabblad **modellen** .

In dit experiment wordt **VotingEnsemble** beschouwd als het beste model, op basis van de **AUC_weighted** metriek.  We implementeren dit model, maar u wordt aangeraden de implementatie ongeveer 20 minuten te volt ooien. Het implementatie proces omvat verschillende stappen, waaronder het registreren van het model, het genereren van resources en het configureren van deze voor de webservice.

1. Selecteer de knop **beste model implementeren** in de linkerbenedenhoek.

1. Vul het deel venster **een model implementeren** als volgt in:

    Veld| Waarde
    ----|----
    Implementatie naam| mijn-automl-implementeren
    Beschrijving van implementatie| Mijn eerste geautomatiseerde machine learning-experiment implementatie
    Reken type | Azure Compute-instantie (ACI) selecteren
    Verificatie inschakelen| Uitschakelen 
    Aangepaste implementaties gebruiken| Uitschakelen Hiermee staat u toe dat het standaard stuurprogrammabestand (Score script) en het omgevings bestand automatisch worden gegenereerd. 
    
    In dit voor beeld gebruiken we de standaard instellingen in het menu *Geavanceerd* . 

1. Selecteer **Implementeren**.  

    Boven aan het scherm **uitvoeren** wordt een groen bericht weer gegeven. in het deel venster **Aanbevolen model** verschijnt een status bericht onder status van **implementatie**. Selecteer periodiek **vernieuwen** om de implementatie status te controleren.
    
U hebt nu een Operational web service voor het genereren van voor spellingen. 

Ga verder met de [**volgende stappen**](#next-steps) om meer te weten te komen over het gebruik van uw nieuwe webservice en test uw voor spellingen met behulp van de ingebouwde Azure machine learning ondersteuning van Power bi.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatie bestanden zijn groter dan gegevens en experimenteer bestanden, zodat ze meer kosten in beslaan. Verwijder alleen de implementatie bestanden om de kosten voor uw account te minimaliseren, of als u uw werk ruimte en de bestanden wilt laten experimenteren. U kunt ook de hele resource groep verwijderen als u niet van plan bent om een van de bestanden te gebruiken.  

### <a name="delete-the-deployment-instance"></a>Het implementatie-exemplaar verwijderen

Verwijder alleen het implementatie-exemplaar van Azure Machine Learning op https\/:/ml.Azure.com/als u de resource groep en-werk ruimte wilt blijven gebruiken voor andere zelf studies en verkennen. 

1. Ga naar [Azure machine learning](https://ml.azure.com/). Navigeer naar uw werk ruimte en selecteer aan de linkerkant onder het deel venster **assets** de optie **eind punten**. 

1. Selecteer de implementatie die u wilt verwijderen en selecteer **verwijderen**. 

1. Selecteer **door gaan**.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze automatische machine learning zelf studie hebt u de interface van de geautomatiseerde (ML) Azure Machine Learning gebruikt voor het maken en implementeren van een classificatie model. Zie deze artikelen voor meer informatie en volgende stappen:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Meer informatie over [automatische machine learning](concept-automated-ml.md).
+ Zie het artikel over [geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md#classification) voor meer informatie over classificatie gegevens en diagrammen. + meer informatie over [parametrisatie](how-to-use-automated-ml-for-ml-models.md#featurization).
+ Meer informatie over [gegevens profilering](how-to-use-automated-ml-for-ml-models.md#profile).


>[!NOTE]
> Deze gegevensset voor Bank marketing wordt beschikbaar gesteld in het [Creative Commons-licentie (CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Alle rechten in de afzonderlijke inhoud van de Data Base worden in licentie gegeven onder de licentie voor de [Data Base-inhoud](https://creativecommons.org/publicdomain/zero/1.0/) en beschikbaar op [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Deze gegevensset is oorspronkelijk beschikbaar in de [icb machine learning-data base](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez en P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, juni 2014.
