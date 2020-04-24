---
title: De vraag van het delen van fietsen met geautomatiseerd ML experiment
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en implementeren van een vraag prognose model met geautomatiseerde machine learning in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 01/27/2020
ms.openlocfilehash: 11e0a8a0076fb2e68c379b279f471ff74846df2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088240"
---
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Zelf studie: prognose voor delen van de vraag met automatische machine learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In deze zelf studie gebruikt u geautomatiseerde machine learning, of geautomatiseerd ML, in Azure Machine Learning Studio om een time series-prognose model te maken om de huur vraag voor een service voor het delen van fietsen te voors pellen.

In deze zelf studie leert u hoe u de volgende taken kunt uitvoeren:

> [!div class="checklist"]
> * Een gegevensset maken en laden.
> * Een automatische ML experiment configureren en uitvoeren.
> * Bekijk de resultaten van het experiment.
> * Implementeer het beste model.

## <a name="prerequisites"></a>Vereisten

* Een Enter prise Edition Azure Machine Learning-werk ruimte. Als u geen werk ruimte hebt, [maakt u een werk ruimte in de Enter prise-editie](how-to-manage-workspace.md). 
    * Automatische machine learning in Azure Machine Learning Studio is alleen beschikbaar voor werk ruimten in de Enter prise-editie. 
* Het gegevens bestand [Bike-No. CSV](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) downloaden

## <a name="get-started-in-azure-machine-learning-studio"></a>Aan de slag in Azure Machine Learning Studio

Voor deze zelf studie maakt u uw automatische ML-experiment in Azure Machine Learning Studio, een geconsolideerde interface met machine learning-hulpprogram ma's voor het uitvoeren van data Science-scenario's voor data Wetenschappen van alle vaardigheids niveaus. De Studio wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werk ruimte die u hebt gemaakt.

1. Selecteer **aan de slag**.

1. Selecteer in het linkerdeel venster **automatische ml** onder het gedeelte **Auteur** .

1. Selecteer **+ New Automated ml run**. 

## <a name="create-and-load-dataset"></a>Gegevensset maken en laden

Voordat u uw experiment gaat configureren, uploadt u uw gegevens bestand naar uw werk ruimte in de vorm van een Azure Machine Learning-gegevensset. Als u dit doet, kunt u ervoor zorgen dat uw gegevens op de juiste wijze zijn opgemaakt voor uw experiment.

1. Selecteer op het formulier **gegevensset selecteren** de optie **op basis van lokale bestanden** in de vervolg keuzelijst **gegevensset maken** . 

    1. Geef in het formulier **basis informatie** uw gegevensset een naam en geef een optionele beschrijving op. Het type gegevensset wordt standaard ingesteld op **tabellair**, omdat automatische milliliters in azure machine learning Studio momenteel alleen ondersteuning bieden voor gegevens sets in tabel vorm.
    
    1. Selecteer **volgende** linksonder

    1. Selecteer op het formulier **gegevens opslag en bestands selectie** de standaard gegevens opslag die automatisch is ingesteld tijdens het maken van de werk ruimte, **workspaceblobstore (Azure Blob Storage)**. Dit is de opslag locatie waar u uw gegevens bestand uploadt. 

    1. Selecteer **Bladeren**. 
    
    1. Kies het bestand **Bike-No. CSV** op de lokale computer. Dit is het bestand dat u hebt gedownload als een [vereiste](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv).

    1. Selecteer **volgende**

       Wanneer het uploaden is voltooid, worden de instellingen en het voorbeeld formulier vooraf ingevuld op basis van het bestands type. 
       
    1. Controleer of de **instellingen en het voorbeeld** formulier als volgt zijn ingevuld en selecteer **volgende**.
        
        Veld|Beschrijving| Waarde voor zelf studie
        ---|---|---
        Bestands indeling|Hiermee definieert u de indeling en het type van de gegevens die zijn opgeslagen in een bestand.| Gescheiden
        Scheidingsteken|Een of meer tekens voor het opgeven van de&nbsp; grens tussen afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevens stromen. |Komma
        Encoding|Hiermee wordt aangegeven welke bits-schema tabel moet worden gebruikt om de gegevensset te lezen.| UTF-8
        Kolomkoppen| Hiermee wordt aangegeven hoe de headers van de gegevensset, indien aanwezig, worden behandeld.| Kopteksten van het eerste bestand gebruiken
        Rijen overs Laan | Hiermee wordt aangegeven hoeveel, indien van toepassing, rijen in de gegevensset worden overgeslagen.| Geen

    1. Met het **schema** formulier kunt u uw gegevens voor dit experiment verder configureren. 
    
        1. Voor dit voor beeld moet u ervoor kiezen om de kolommen **informele** en **Gereg** te negeren. Deze kolommen vormen een uitsplitsing van de kolom **cnt** , dus we nemen deze niet op.

        1. Voor dit voor beeld moet u ook de standaard waarden voor de **Eigenschappen** en het **type opgeven**. 
        
        1. Selecteer **Next**.

    1. Controleer op het formulier **Details bevestigen** of de informatie overeenkomt met wat u eerder hebt ingevuld in de **basis informatie** en- **instellingen en de preview** -formulieren.

    1. Selecteer **maken** om het maken van de gegevensset te volt ooien.

    1. Selecteer uw gegevensset zodra deze in de lijst wordt weer gegeven.

    1. Selecteer **volgende**.

## <a name="configure-experiment-run"></a>Experiment-uitvoering configureren

Nadat u uw gegevens hebt geladen en geconfigureerd, stelt u uw externe Compute-doel in en selecteert u welke kolom in uw gegevens u wilt voors pellen.

1. Vul het formulier voor het uitvoeren van de **Configure** als volgt in:
    1. Voer een naam voor het experiment in:`automl-bikeshare`

    1. Selecteer **cnt** als doel kolom, wat u wilt voors pellen. In deze kolom wordt het totale aantal huren van de fiets delen aangegeven.

    1. Selecteer **een nieuwe berekening maken** en configureer uw reken doel. Automatische ML ondersteunt alleen Azure Machine Learning compute. 

        Veld | Beschrijving | Waarde voor zelf studie
        ----|---|---
        Compute name |Een unieke naam die uw Compute-context identificeert.|fiets-compute
        Grootte&nbsp;van&nbsp;virtuele machine| Selecteer de grootte van de virtuele machine voor de reken kracht.|Standard_DS12_V2
        Min-maximum aantal knoop punten (in geavanceerde instellingen)| Als u wilt profiel gegevens, moet u één of meer knoop punten opgeven.|Minimum aantal knoop punten: 1<br>Maximum aantal knoop punten: 6
  
        1. Selecteer **maken** om het Compute-doel op te halen. 

            **Dit duurt enkele minuten.** 

        1. Nadat u hebt gemaakt, selecteert u uw nieuwe reken doel in de vervolg keuzelijst.

    1. Selecteer **Next**.

## <a name="select-task-type-and-settings"></a>Taak type en instellingen selecteren

Voltooi de installatie voor uw automatische ML experiment door het machine learning taak type en configuratie-instellingen op te geven.

1. Selecteer op het **taak type en het instellingen** formulier **Time Series-prognose** als het taak type machine learning.

1. Selecteer **datum** als uw **tijd kolom** en laat **groeperen op kolom (men)** leeg. 

    1. Selecteer **aanvullende configuratie-instellingen weer geven** en vul de velden in als volgt. Deze instellingen zijn een betere controle over de trainings taak. Anders worden de standaard waarden toegepast op basis van het experiment en de gegevens.

  
        Aanvullende&nbsp;configuraties|Beschrijving|Waarde&nbsp;voor&nbsp;zelf studie
        ------|---------|---
        Primaire metriek| Evaluatie-metrische gegevens waarop het algoritme van de machine learning wordt gemeten.|Genormaliseerde hoofd gemiddelde fout
        Automatische parametrisatie| Hiermee wordt voor verwerking ingeschakeld. Dit omvat het automatisch opschonen, voorbereiden en transformeren van gegevens voor het genereren van synthetische functies.| Inschakelen
        Aanbevolen model uitleggen (preview-versie)| Toont automatisch uitleg over het beste model dat is gemaakt met automatische MILLILITERs.| Inschakelen
        Geblokkeerde algoritmen | Algoritmen die u wilt uitsluiten van de trainings taak| Extreme, wille keurige structuren
        Aanvullende prognose-instellingen| Deze instellingen helpen de nauw keurigheid van het model te verbeteren <br><br> _**Prognose horizon**_: tijds duur in de toekomst die u wilt voors pellen <br> _**Prognose doel lags:**_ hoe ver terug u de lags van een doel variabele wilt maken <br> _**Doel venster**_: Hiermee geeft u de grootte van het doorlopende venster op waarover de functies, zoals de *Max, min* en *som*, worden gegenereerd. |Prognose horizon: 14 <br> Prognose&nbsp;doel&nbsp;lags: geen <br> Grootte&nbsp;van&nbsp;doel&nbsp;venster: geen
        Criterium afsluiten| Als aan een criterium wordt voldaan, wordt de trainings taak gestopt. |Tijd&nbsp;trainings&nbsp;taak (uren): 3 <br> Drempel&nbsp;waarde&nbsp;voor metrische Score: geen
        Validatie | Kies een type Kruis validatie en aantal testen.|Validatie type:<br>&nbsp;Kruis validatie met&nbsp;k-vouwen <br> <br> Aantal validaties: 5
        Gelijktijdigheid| Het maximum aantal parallelle iteraties dat per iteratie wordt uitgevoerd| Maximum&nbsp;aantal&nbsp;gelijktijdige herhalingen: 6
        
        Selecteer **Opslaan**.

## <a name="run-experiment"></a>Experiment uitvoeren

Selecteer **volt ooien**om uw experiment uit te voeren. Het scherm **Details uitvoeren** wordt geopend met de **uitvoerings status** boven naast het uitvoerings nummer. Deze status wordt bijgewerkt wanneer het experiment wordt uitgevoerd.

>[!IMPORTANT]
> Voor bereiding duurt **10-15 minuten** om de uitvoering van het experiment voor te bereiden.
> Na de uitvoering duurt **2-3 minuten meer voor elke iteratie**.  <br> <br>
> In productie zou u waarschijnlijk een beetje weglopen omdat dit proces tijd in beslag neemt. Terwijl u wacht, wordt u aangeraden om de geteste algoritmen te verkennen op het tabblad **modellen** , wanneer deze zijn voltooid. 

##  <a name="explore-models"></a>Modellen verkennen

Ga naar het tabblad **modellen** om de algoritmen (modellen) te zien die zijn getest. Standaard worden de modellen gesorteerd op basis van de metrische Score wanneer ze zijn voltooid. Voor deze zelf studie wordt het model met het hoogste score op basis van het gekozen **genormaliseerde hoofd gemiddelde fout** metriek boven aan de lijst weer gegeven.

Terwijl u wacht tot alle experimentele modellen zijn voltooid, selecteert u de naam van het **algoritme** van een voltooid model om de prestatie details te verkennen. 

In het volgende voor beeld wordt genavigeerd door de **model Details** en de **Visualisaties** tabbladen om de eigenschappen, metrische gegevens en prestatie grafieken van het geselecteerde model weer te geven. 

![Details uitvoeren](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Het model implementeren

Met geautomatiseerde machine learning in Azure Machine Learning Studio kunt u in een paar stappen het beste model als een webservice implementeren. Implementatie is de integratie van het model, zodat dit kan voors pellen op nieuwe gegevens en mogelijke verkoop kansen kan identificeren. 

Voor dit experiment houdt het implementeren naar een webservice voor dat het aandeel van de fiets share nu een iteratieve en schaal bare weboplossing heeft voor het voors pellen van het huren van fietsen. 

Zodra de uitvoering is voltooid, gaat u terug naar de **detail pagina uitvoeren** en selecteert u het tabblad **modellen** .

In deze experimentele context wordt **StackEnsemble** beschouwd als het beste model, op basis van het **genormaliseerde hoofd gemiddelde** van de fout metriek.  We implementeren dit model, maar u wordt aangeraden de implementatie ongeveer 20 minuten te volt ooien. Het implementatie proces omvat verschillende stappen, waaronder het registreren van het model, het genereren van resources en het configureren van deze voor de webservice.

1. Selecteer de knop **beste model implementeren** in de linkerbenedenhoek.

1. Vul het deel venster **een model implementeren** als volgt in:

    Veld| Waarde
    ----|----
    Implementatie naam| Bike share-implementeren
    Beschrijving van implementatie| implementatie van fiets share-vraag
    Reken type | Azure Compute-instantie (ACI) selecteren
    Verificatie inschakelen| Uitschakelen 
    Aangepaste implementatie-assets gebruiken| Uitschakelen Als u dit uitschakelt, kan het standaard stuurprogrammabestand (Score script) en het omgevings bestand automatisch worden gegenereerd. 
    
    In dit voor beeld gebruiken we de standaard instellingen in het menu *Geavanceerd* . 

1. Selecteer **Implementeren**.  

    Boven aan het scherm **uitvoeren** wordt een groen bericht weer gegeven dat de implementatie is gestart. De voortgang van de implementatie kan worden gevonden  
    in het deel venster **Aanbevolen model** onder **Implementatie status**.
    
Zodra de implementatie is voltooid, hebt u een Operational web service voor het genereren van voor spellingen. 

Ga verder met de [**volgende stappen**](#next-steps) om meer te weten te komen over het gebruik van uw nieuwe webservice en test uw voor spellingen met behulp van de ingebouwde Azure machine learning ondersteuning van Power bi.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatie bestanden zijn groter dan gegevens en experimenteer bestanden, zodat ze meer kosten in beslaan. Verwijder alleen de implementatie bestanden om de kosten voor uw account te minimaliseren, of als u uw werk ruimte en de bestanden wilt laten experimenteren. U kunt ook de hele resource groep verwijderen als u niet van plan bent om een van de bestanden te gebruiken.  

### <a name="delete-the-deployment-instance"></a>Het implementatie-exemplaar verwijderen

Verwijder alleen het implementatie-exemplaar uit de Azure Machine Learning Studio als u de resource groep en-werk ruimte wilt blijven gebruiken voor andere zelf studies en verkennen. 

1. Ga naar de [Azure machine learning Studio](https://ml.azure.com/). Navigeer naar uw werk ruimte en selecteer aan de linkerkant onder het deel venster **assets** de optie **eind punten**. 

1. Selecteer de implementatie die u wilt verwijderen en selecteer **verwijderen**. 

1. Selecteer **door gaan**.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u gebruikgemaakt van automatische MILLILITERs in de Azure Machine Learning Studio voor het maken en implementeren van een time series-prognose model waarmee de vraag naar aandelen huren wordt voor speld. 

Raadpleeg dit artikel voor stappen voor het maken van een Power BI ondersteund schema om het gebruik van uw pas geïmplementeerde webservice te vergemakkelijken:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Deze fiets share gegevensset is gewijzigd voor deze zelf studie. Deze gegevensset is beschikbaar gemaakt als onderdeel van een [Kaggle-competitie](https://www.kaggle.com/c/bike-sharing-demand/data) en was oorspronkelijk verkrijgbaar via het [kapitaal Bike share](https://www.capitalbikeshare.com/system-data). Dit kan ook worden gevonden in de [icb machine learning-data base](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset).<br><br>
> Bron: Fanaee-T, Hadi en Gama, Joao, Event labeling combineert ensembles detectoren en achtergrond kennis, voortgang in kunst matige intelligentie (2013): pp. 1-15, Springer Berlijn Heidelberg.
