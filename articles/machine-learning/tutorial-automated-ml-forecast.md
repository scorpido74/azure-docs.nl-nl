---
title: Prognose fiets sharing vraag met geautomatiseerde ML experiment
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en implementeren van een prognosemodel voor vraag met geautomatiseerde machine learning in Azure Machine Learning-studio.
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
# <a name="tutorial-forecast-bike-sharing-demand-with-automated-machine-learning"></a>Zelfstudie: Prognose vraag naar het delen van fietsen met geautomatiseerde machine learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In deze zelfstudie gebruikt u geautomatiseerde machine learning of geautomatiseerde ML in de Azure Machine Learning-studio om een prognosemodel voor tijdreeksen te maken om de huurvraag naar een fietsdeelservice te voorspellen.

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Een gegevensset maken en laden.
> * Een geautomatiseerd ML-experiment configureren en uitvoeren.
> * Bekijk de resultaten van het experiment.
> * Implementeer het beste model.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte voor Enterprise-edities. Als u geen werkruimte hebt, [maakt u een werkruimte voor de Enterprise-editie](how-to-manage-workspace.md). 
    * Geautomatiseerde machine learning in de Azure Machine Learning-studio is alleen beschikbaar voor werkruimten voor Enterprise-edities. 
* Download het [bike-no.csv-gegevensbestand](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)

## <a name="get-started-in-azure-machine-learning-studio"></a>Aan de slag in Azure Machine Learning-studio

Voor deze zelfstudie maakt u uw geautomatiseerde ML-experiment dat wordt uitgevoerd in Azure Machine Learning-studio, een geconsolideerde interface met machine learning-hulpprogramma's om scenario's voor gegevenswetenschap uit te voeren voor beoefenaars van gegevenswetenschappen van alle vaardigheidsniveaus. De studio wordt niet ondersteund in internet explorer-browsers.

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werkruimte die u hebt gemaakt.

1. Selecteer **Aan de slag**.

1. Selecteer **Geautomatiseerde ML** in het linkerdeelvenster onder de sectie **Auteur.**

1. Selecteer **+Nieuwe geautomatiseerde ML-run**. 

## <a name="create-and-load-dataset"></a>Gegevensset maken en laden

Upload uw gegevensbestand naar uw werkruimte in de vorm van een Azure Machine Learning-gegevensset voordat u uw experiment configureert. Als u dit doet, u ervoor zorgen dat uw gegevens op de juiste manier zijn opgemaakt voor uw experiment.

1. Selecteer in het **formulier Gegevensset Selecteren** de optie Uit lokale **bestanden** in de vervolgkeuzelijst **'Gegevensset maken'.** 

    1. Geef in het **formulier Basisinformatie** een naam en geef een optionele beschrijving op. Het gegevenssettype moet standaard worden ingesteld **op Tabelvormig,** omdat geautomatiseerde ML in Azure Machine Learning-studio momenteel alleen tabelgegevenssets ondersteunt.
    
    1. Selecteer **Volgende** linksonder

    1. Selecteer in het **gegevensarchief en het formulier voor bestandsselectie** het standaardgegevensarchief dat automatisch is ingesteld tijdens het maken van uw werkruimte, **workspaceblobstore (Azure Blob Storage).** Dit is de opslaglocatie waar u uw gegevensbestand uploadt. 

    1. Selecteer **Bladeren**. 
    
    1. Kies het **bike-no.csv-bestand** op uw lokale computer. Dit is het bestand dat u als [voorwaarde hebt](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)gedownload.

    1. Selecteer **Volgende**

       Wanneer de upload is voltooid, worden de instellingen en het voorbeeldformulier vooraf ingevuld op basis van het bestandstype. 
       
    1. Controleer of het **formulier Instellingen en voorbeeld** als volgt wordt ingevuld en selecteer **Volgende**.
        
        Veld|Beschrijving| Waarde voor zelfstudie
        ---|---|---
        Bestandsindeling|Hiermee definieert u de indeling en het type gegevens dat in een bestand is opgeslagen.| Gescheiden
        Scheidingsteken|Een of meer tekens voor&nbsp; het opgeven van de grens tussen afzonderlijke, onafhankelijke regio's in platte tekst of andere gegevensstromen. |Komma
        Encoding|Hiermee wordt aangegeven welke schematabel moet worden gebruikt om uw gegevensset te lezen.| UTF-8
        Kolomkoppen| Geeft aan hoe de eventuele kopteksten van de gegevensset worden behandeld.| Kopteksten uit het eerste bestand gebruiken
        Rijen overslaan | Geeft aan hoeveel, indien van toepassing, rijen worden overgeslagen in de gegevensset.| Geen

    1. Het **schemaformulier** maakt verdere configuratie van uw gegevens voor dit experiment mogelijk. 
    
        1. Kies er in dit voorbeeld voor om de **informele** en **geregistreerde** kolommen te negeren. Deze kolommen zijn een uitsplitsing van de **cnt** kolom dus, daarom nemen we ze niet op.

        1. Laat ook in dit voorbeeld de standaardwaarden voor de **eigenschappen** en **het type achter.** 
        
        1. Selecteer **Volgende**.

    1. Controleer op het formulier **Details bevestigen** of de informatie overeenkomt met wat eerder is ingevuld op **basisgegevens** en **-instellingen en voorbeeldformulieren.**

    1. Selecteer **Maken** om de creatie van uw gegevensset te voltooien.

    1. Selecteer uw gegevensset zodra deze in de lijst wordt weergegeven.

    1. Selecteer **Volgende**.

## <a name="configure-experiment-run"></a>Experimentrun configureren

Nadat u uw gegevens hebt geladen en geconfigureerd, stelt u uw externe rekendoel in en selecteert u welke kolom in uw gegevens u wilt voorspellen.

1. Vul het **runformulier configureren** als volgt in:
    1. Voer een experimentnaam in:`automl-bikeshare`

    1. Selecteer **cnt** als de doelkolom, wat u wilt voorspellen. Deze kolom geeft het aantal totale fietsaandelen aan.

    1. Selecteer **Een nieuwe rekenkracht maken** en configureer uw rekendoel. Geautomatiseerde ML ondersteunt alleen Azure Machine Learning compute. 

        Veld | Beschrijving | Waarde voor zelfstudie
        ----|---|---
        Rekennaam |Een unieke naam die uw rekencontext identificeert.|bike-compute
        Virtuele&nbsp;&nbsp;machinegrootte| Selecteer de grootte van de virtuele machine voor uw computerberekening.|Standard_DS12_V2
        Min / Max-knooppunten (in geavanceerde instellingen)| Als u gegevens wilt profileren, moet u 1 of meer knooppunten opgeven.|Min-knooppunten: 1<br>Max.
  
        1. Selecteer **Maken** om het rekendoel op te halen. 

            **Dit duurt een paar minuten om te voltooien.** 

        1. Selecteer na het maken uw nieuwe rekendoel in de vervolgkeuzelijst.

    1. Selecteer **Volgende**.

## <a name="select-task-type-and-settings"></a>Taaktype en -instellingen selecteren

Voltooi de installatie voor uw geautomatiseerde ML-experiment door het type en de configuratie-instellingen voor machine learning op te geven.

1. Selecteer in het **formulier Taaktype en instellingen** De optie **Tijdreeksprognoses** als het taaktype machine learning.

1. Selecteer **datum** als **kolom Tijd** en laat Groep op **kolom(en)** leeg. 

    1. Selecteer **Extra configuratie-instellingen weergeven** en vul de velden als volgt in. Deze instellingen zijn om de training beter te controleren. Anders worden standaardinstellingen toegepast op basis van experimentselectie en gegevens.

  
        Aanvullende&nbsp;configuraties|Beschrijving|Waarde&nbsp;&nbsp;voor zelfstudie
        ------|---------|---
        Primaire statistiek| Evaluatie statistiek dat de machine learning algoritme zal worden gemeten door.|Genormaliseerde root gemiddelde kwadraatfout
        Automatische featurisatie| Maakt voorbewerking mogelijk. Dit omvat automatische gegevensreiniging, voorbereiding en transformatie om synthetische functies te genereren.| Inschakelen
        Beste model uitleggen (voorbeeld)| Toont automatisch uitleg over het beste model dat door geautomatiseerde ML is gemaakt.| Inschakelen
        Geblokkeerde algoritmen | Algoritmen die u wilt uitsluiten van de trainingstaak| Extreme willekeurige bomen
        Aanvullende instellingen voor prognoses| Deze instellingen helpen de nauwkeurigheid van uw model te verbeteren <br><br> _**Prognosehorizon**_: tijdsduur in de toekomst die u wilt voorspellen <br> _**Prognose doel vertragingen:**_ hoe ver terug u wilt de vertragingen van een de doelvariabele te construeren <br> _**Doelrolvenster**_: geeft de grootte aan van het rollende venster waarover functies, zoals de *max, min* en *som,* worden gegenereerd. |Prognosehorizon: 14 <br> &nbsp;Prognosedoel&nbsp;blijft achter: Geen <br> Grootte&nbsp;&nbsp;van&nbsp;het rolvenster van doel: geen
        Exitcriterium| Als aan een criterium wordt voldaan, wordt de opleidingstaak gestopt. |&nbsp;Opleidingstijd&nbsp;(uren): 3 <br> Metrische&nbsp;&nbsp;scoredrempel: Geen
        Validatie | Kies een type cross-validatie en het aantal tests.|Validatietype:<br>&nbsp;k-fold&nbsp;cross-validatie <br> <br> Aantal validaties: 5
        Gelijktijdigheid| Het maximum aantal parallelle iteraties per iteratie| &nbsp;Maximale&nbsp;gelijktijdige iteraties: 6
        
        Selecteer **Opslaan**.

## <a name="run-experiment"></a>Experiment uitvoeren

Als u het experiment wilt uitvoeren, selecteert u **Voltooien**. Het scherm **Details uitvoeren** wordt geopend met de **status Uitvoeren** bovenaan naast het runnummer. Deze status wordt bijgewerkt naarmate het experiment vordert.

>[!IMPORTANT]
> Voorbereiding duurt **10-15 minuten** om het experiment voor te bereiden.
> Eenmaal uitgevoerd, duurt het **2-3 minuten meer voor elke iteratie.**  <br> <br>
> In de productie, zou je waarschijnlijk weglopen voor een beetje als dit proces kost tijd. Terwijl u wacht, raden we u aan de geteste algoritmen op het tabblad **Modellen** te verkennen terwijl deze zijn voltooid. 

##  <a name="explore-models"></a>Modellen verkennen

Navigeer naar het tabblad **Modellen** om de geteste algoritmen (modellen) te bekijken. Standaard worden de modellen geordend op metrische score als ze zijn voltooid. Voor deze zelfstudie staat het model dat het hoogste scoort op basis van de gekozen **genormaliseerde root-gemiddelde kwadraatfoutstatistiek** bovenaan de lijst.

Terwijl u wacht tot alle experimentmodellen zijn voltooid, selecteert u de **naam Algoritme** van een voltooid model om de prestatiedetails te verkennen. 

In het volgende voorbeeld navigeert u door de **tabbladen Modeldetails** en **Visualisaties** om de eigenschappen, statistieken en prestatiegrafieken van het geselecteerde model weer te geven. 

![Detail uitvoeren](./media/tutorial-automated-ml-forecast/explore-models-ui.gif)

## <a name="deploy-the-model"></a>Het model implementeren

Met geautomatiseerde machine learning in Azure Machine Learning-studio u het beste model als webservice in een paar stappen implementeren. Implementatie is de integratie van het model, zodat het kan voorspellen op nieuwe gegevens en potentiële gebieden van kansen te identificeren. 

Voor dit experiment betekent implementatie naar een webservice dat het fietssharebedrijf nu een iteratieve en schaalbare weboplossing heeft voor het voorspellen van de vraag naar fietsdelen. 

Zodra de run is voltooid, navigeert u terug naar de pagina **Detail uitvoeren** en selecteert u het tabblad **Modellen.**

In deze experimentcontext wordt **StackEnsemble** beschouwd als het beste model, gebaseerd op de **genormaliseerde rootgemiddelde kwadraatfoutstatistiek.**  We implementeren dit model, maar wees gewaarschuwd, implementatie duurt ongeveer 20 minuten om te voltooien. Het implementatieproces omvat verschillende stappen, waaronder het registreren van het model, het genereren van resources en het configureren ervan voor de webservice.

1. Selecteer de **knop Beste model implementeren** in de linkerbenedenhoek.

1. Vul het deelvenster Een model als volgt **implementeren:**

    Veld| Waarde
    ----|----
    Implementatienaam| bikeshare-deploy
    Implementatiebeschrijving| bike share demand deployment bike share demand deployment bike share demand deployment bike share
    Rekentype | Azure Compute Instance (ACI) selecteren
    Verificatie inschakelen| Uitschakelen 
    Aangepaste implementatiemiddelen gebruiken| Uitschakelen Als u uitschakelt, kunnen het standaardstuurprogrammabestand (scorescript) en het omgevingsbestand automatisch worden gegenereerd. 
    
    Voor dit voorbeeld gebruiken we de standaardinstellingen in het menu *Geavanceerd.* 

1. Selecteer **Implementeren**.  

    Boven aan het scherm **Uitvoeren** wordt een groen succesbericht weergegeven waarin staat dat de implementatie is gestart. De voortgang van de implementatie is te vinden  
    in het **deelvenster Aanbevolen model** onder **Deploy-status**.
    
Zodra de implementatie is geslaagd, hebt u een operationele webservice om voorspellingen te genereren. 

Ga naar de [**volgende stappen**](#next-steps) voor meer informatie over het gebruik van uw nieuwe webservice en test uw voorspellingen met de ingebouwde Azure Machine Learning-ondersteuning van Power BI.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatiebestanden zijn groter dan gegevens- en experimentbestanden, dus ze kosten meer om op te slaan. Verwijder alleen de implementatiebestanden om de kosten voor uw account te minimaliseren of als u uw werkruimte- en experimentbestanden wilt behouden. Verwijder anders de hele brongroep als u niet van plan bent een van de bestanden te gebruiken.  

### <a name="delete-the-deployment-instance"></a>De implementatie-instantie verwijderen

Verwijder alleen de implementatieinstantie uit de Azure Machine Learning-studio als u de brongroep en werkruimte wilt behouden voor andere zelfstudies en verkenningen. 

1. Ga naar de [Azure Machine Learning-studio](https://ml.azure.com/). Navigeer naar uw werkruimte en selecteer links onder het deelvenster **Elementen** de optie **Eindpunten**. 

1. Selecteer de implementatie die u wilt verwijderen en selecteer **Verwijderen**. 

1. Selecteer **Doorgaan**.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geautomatiseerde ML in de Azure Machine Learning-studio gebruikt om een prognosemodel voor tijdreeksen te maken en te implementeren dat de huurvraag voor fietsdelen voorspelt. 

Zie dit artikel voor stappen over het maken van een door Power BI ondersteund schema om het verbruik van uw nieuw geïmplementeerde webservice te vergemakkelijken:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](how-to-consume-web-service.md#consume-the-service-from-power-bi)


>[!NOTE]
> Deze gegevensset voor het delen van fietsen is gewijzigd voor deze zelfstudie. Deze dataset werd beschikbaar gesteld als onderdeel van een [Kaggle-wedstrijd](https://www.kaggle.com/c/bike-sharing-demand/data) en was oorspronkelijk beschikbaar via [Capital Bikeshare](https://www.capitalbikeshare.com/system-data). Het is ook te vinden in de [UCI Machine Learning Database.](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)<br><br>
> Bron: Fanaee-T, Hadi, and Gama, Joao, Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg.
