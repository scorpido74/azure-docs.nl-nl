---
title: Geautomatiseerde ML-classificatiemodellen maken
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen & classificatiemodellen implementeren met de geautomatiseerde ML-interface (Automated Machine Learning) van Azure Machine Learning.
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
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Zelfstudie: Een classificatiemodel maken met geautomatiseerde ML in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In deze zelfstudie leert u hoe u een basisclassificatiemodel maakt zonder één regel code te schrijven met de geautomatiseerde machine learning-interface van Azure Machine Learning. Dit classificatiemodel voorspelt of een klant zich zal abonneren op een storting met een bepaalde termijn bij een financiële instelling.

Met geautomatiseerde machine learning u tijdintensieve taken automatiseren. Geautomatiseerde machine learning snel iterates over vele combinaties van algoritmen en hyperparameters om u te helpen het beste model te vinden op basis van een succes metrische van uw keuze.

In deze zelfstudie leert u hoe u de volgende taken uitvoert:

> [!div class="checklist"]
> * Maak een Azure Machine Learning-werkruimte.
> * Een geautomatiseerd machine learning-experiment uitvoeren.
> * Bekijk de details van het experiment.
> * Het model implementeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree)aan.

* Download het [**bankmarketing_train.csv-gegevensbestand.**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) De **kolom y** geeft aan of een klant zich heeft geabonneerd op een storting met een vaste termijn, die later wordt geïdentificeerd als de doelkolom voor voorspellingen in deze zelfstudie. 

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisbron in de cloud die u gebruikt om machine learning-modellen te experimenteren, te trainen en te implementeren. Het koppelt uw Azure-abonnement en resourcegroep aan een gemakkelijk verbruikt object in de service. 

U maakt een werkruimte via de Azure-portal, een webconsole voor het beheren van uw Azure-bronnen.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Let op uw **werkruimte** en **abonnement.** U hebt deze nodig om ervoor te zorgen dat u uw experiment op de juiste plaats maakt. 

## <a name="create-and-run-the-experiment"></a>Het experiment maken en uitvoeren

U voltooit de volgende experimentset-up en https://ml.azure.comvoert stappen uit via Azure Machine learning op , een geconsolideerde webinterface met machine learning-tools om data science-scenario's uit te voeren voor gegevenswetenschapsbeoefenaars van alle vaardigheidsniveaus. Deze interface wordt niet ondersteund in internet explorer-browsers.

1. Meld u aan bij [Azure Machine Learning](https://ml.azure.com).

1. Selecteer uw abonnement en de werkruimte die u hebt gemaakt.

1. Selecteer **Aan de slag**.

1. Selecteer **Geautomatiseerde ML** in het linkerdeelvenster onder de sectie **Auteur.**

   Aangezien dit uw eerste geautomatiseerde ML-experiment is, ziet u een lege lijst en koppelingen naar documentatie.

   ![Pagina Aan de slag](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Selecteer **Nieuwe geautomatiseerde ML-run**. 

1. Maak een nieuwe gegevensset door **Uit lokale bestanden te** selecteren in de vervolgkeuzelijst **+Gegevensset maken.** 

    1. Geef in het **formulier Basisinformatie** een naam en geef een optionele beschrijving op. De geautomatiseerde ML-interface ondersteunt momenteel alleen TabularDatasets, dus het gegevenssettype moet standaard worden ingesteld op *Tabelvormig*.

    1. Selecteer **Volgende** linksonder

    1. Selecteer in het **gegevensarchief en het formulier voor bestandsselectie** het standaardgegevensarchief dat automatisch is ingesteld tijdens het maken van uw werkruimte, **workspaceblobstore (Azure Blob Storage).** Hier uploadt u uw gegevensbestand om het beschikbaar te maken voor uw werkruimte.

    1. Selecteer **Bladeren**.
    
    1. Kies het **bestand bankmarketing_train.csv** op uw lokale computer. Dit is het bestand dat u als [voorwaarde hebt](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)gedownload.

    1. Geef uw gegevensset een unieke naam en geef een optionele beschrijving. 

    1. Selecteer **Volgende** linksonder om deze te uploaden naar de standaardcontainer die automatisch is ingesteld tijdens het maken van uw werkruimte.  
    
       Wanneer de upload is voltooid, worden de instellingen en het voorbeeldformulier vooraf ingevuld op basis van het bestandstype. 
       
    1. Controleer of het **formulier Instellingen en voorbeeld** als volgt wordt ingevuld en selecteer **Volgende**.
        
        Veld|Beschrijving| Waarde voor zelfstudie
        ---|---|---
        Bestandsindeling|Hiermee definieert u de indeling en het type gegevens dat in een bestand is opgeslagen.| Gescheiden
        Scheidingsteken|Een of meer tekens voor&nbsp; het opgeven van de grens tussen afzonderlijke, onafhankelijke regio's in platte tekst of andere gegevensstromen. |Komma
        Encoding|Hiermee wordt aangegeven welke schematabel moet worden gebruikt om uw gegevensset te lezen.| UTF-8
        Kolomkoppen| Geeft aan hoe de eventuele kopteksten van de gegevensset worden behandeld.| Alle bestanden hebben dezelfde kopteksten
        Rijen overslaan | Geeft aan hoeveel, indien van toepassing, rijen worden overgeslagen in de gegevensset.| Geen

    1. Het **schemaformulier** maakt verdere configuratie van uw gegevens voor dit experiment mogelijk. Selecteer in dit voorbeeld de schakelschakelaar voor de **day_of_week-functie,** zodat deze niet voor dit experiment wordt opgenomen. Selecteer **Volgende**.

        ![Voorbeeld van tabconfiguratie](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. Controleer op het formulier **Details bevestigen** of de informatie overeenkomt met wat eerder is ingevuld op **basisgegevens** en **-instellingen en voorbeeldformulieren.**
    1. Selecteer **Maken** om de creatie van uw gegevensset te voltooien.
    1. Selecteer uw gegevensset zodra deze in de lijst wordt weergegeven.
    1. Bekijk het **voorbeeld gegevens** om te controleren of u **day_of_week** vervolgens niet hebt opgenomen, selecteert **U OK**.

    1. Selecteer **Volgende**.

1. Vul het formulier **Run configureren** als volgt in:
    1. Voer de naam van het experiment in:`my-1st-automl-experiment`

    1. Selecteer **y** als de doelkolom, wat u wilt voorspellen. In deze kolom wordt aangegeven of de klant een termijnstorting heeft geabonneerd of niet.
    1. Selecteer **Een nieuwe rekenkracht maken** en configureer uw rekendoel. Een compute target is een lokale of cloudgebaseerde resourceomgeving die wordt gebruikt om uw trainingsscript uit te voeren of uw service-implementatie te hosten. Voor dit experiment gebruiken we een cloudgebaseerde compute. 

        Veld | Beschrijving | Waarde voor zelfstudie
        ----|---|---
        Rekennaam |Een unieke naam die uw rekencontext identificeert.|automl-compute
        Virtuele&nbsp;&nbsp;machinegrootte| Selecteer de grootte van de virtuele machine voor uw computerberekening.|Standard_DS12_V2
        Min / Max-knooppunten (in geavanceerde instellingen)| Als u gegevens wilt profileren, moet u 1 of meer knooppunten opgeven.|Min-knooppunten: 1<br>Max.
  
        1. Selecteer **Maken** om het rekendoel op te halen. 

            **Dit duurt een paar minuten om te voltooien.** 

        1. Selecteer na het maken uw nieuwe rekendoel in de vervolgkeuzelijst.

    1. Selecteer **Volgende**.

1. Selecteer **Classificatie** als taaktype voor machine learning in het **taaktype Taaktypen en -instellingen.**

    1. Selecteer **Extra configuratie-instellingen weergeven** en vul de velden als volgt in. Deze instellingen zijn om de training beter te controleren. Anders worden standaardinstellingen toegepast op basis van experimentselectie en gegevens.

        >[!NOTE]
        > In deze zelfstudie stelt u geen metrische score of maximale cores per iteratiedrempel in. Ook zult u niet blokkeren algoritmen worden getest.
   
        Aanvullende&nbsp;configuraties|Beschrijving|Waarde&nbsp;&nbsp;voor zelfstudie
        ------|---------|---
        Primaire statistiek| Evaluatie statistiek dat de machine learning algoritme zal worden gemeten door.|AUC_weighted
        Automatische featurisatie| Maakt voorbewerking mogelijk. Dit omvat automatische gegevensreiniging, voorbereiding en transformatie om synthetische functies te genereren.| Inschakelen
        Geblokkeerde algoritmen | Algoritmen die u wilt uitsluiten van de trainingstaak| Geen
        Exitcriterium| Als aan een criterium wordt voldaan, wordt de opleidingstaak gestopt. |&nbsp;Opleidingstijd&nbsp;(uren): 1 <br> Metrische&nbsp;&nbsp;scoredrempel: Geen
        Validatie | Kies een type cross-validatie en het aantal tests.|Validatietype:<br>&nbsp;k-fold&nbsp;cross-validatie <br> <br> Aantal validaties: 2
        Gelijktijdigheid| Het maximum aantal parallelle iteraties per iteratie| &nbsp;Maximale&nbsp;gelijktijdige iteraties: 5
        
        Selecteer **Opslaan**.

1. Selecteer **Voltooien** om het experiment uit te voeren. Het scherm **Detail uitvoeren** wordt geopend met de **status Uitvoeren** bovenaan terwijl de voorbereiding van het experiment begint.

>[!IMPORTANT]
> Voorbereiding duurt **10-15 minuten** om het experiment voor te bereiden.
> Eenmaal uitgevoerd, duurt het **2-3 minuten meer voor elke iteratie.**  
> Selecteer **Regelmatig vernieuwen** om de status van de run te zien naarmate het experiment vordert.
>
> In de productie, zou je waarschijnlijk weglopen voor een beetje. Maar voor deze tutorial raden we je aan om de geteste algoritmen op het tabblad **Modellen** te verkennen terwijl de anderen nog actief zijn. 

##  <a name="explore-models"></a>Modellen verkennen

Navigeer naar het tabblad **Modellen** om de geteste algoritmen (modellen) te bekijken. Standaard worden de modellen geordend op metrische score als ze zijn voltooid. Voor deze zelfstudie staat het model dat het hoogst scoort op basis van de gekozen **AUC_weighted** statistiek bovenaan de lijst.

Terwijl u wacht tot alle experimentmodellen zijn voltooid, selecteert u de **naam Algoritme** van een voltooid model om de prestatiedetails te verkennen. 

Het volgende navigeert door de **modeldetails** en de tabbladen **Visualisaties** om de eigenschappen, statistieken en prestatiegrafieken van het geselecteerde model weer te geven. 

![Iteratiedetail uitvoeren](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Het beste model implementeren

Met de geautomatiseerde machine learning-interface u het beste model in een paar stappen als webservice implementeren. Implementatie is de integratie van het model, zodat het kan voorspellen op nieuwe gegevens en potentiële gebieden van kansen te identificeren. 

Voor dit experiment betekent implementatie naar een webservice dat de financiële instelling nu een iteratieve en schaalbare weboplossing heeft voor het identificeren van potentiële klanten met een vaste termijn. 

Zodra de run is voltooid, navigeert u terug naar de pagina **Detail uitvoeren** en selecteert u het tabblad **Modellen.**

In deze experimentcontext wordt **VotingEnsemble** beschouwd als het beste model, gebaseerd op de **AUC_weighted** statistiek.  We implementeren dit model, maar wees gewaarschuwd, implementatie duurt ongeveer 20 minuten om te voltooien. Het implementatieproces omvat verschillende stappen, waaronder het registreren van het model, het genereren van resources en het configureren ervan voor de webservice.

1. Selecteer de **knop Beste model implementeren** in de linkerbenedenhoek.

1. Vul het deelvenster Een model als volgt **implementeren:**

    Veld| Waarde
    ----|----
    Implementatienaam| my-automl-deploy
    Implementatiebeschrijving| Mijn eerste geautomatiseerde machine learning-experimentimplementatie
    Rekentype | Azure Compute Instance (ACI) selecteren
    Verificatie inschakelen| Uitschakelen 
    Aangepaste implementaties gebruiken| Uitschakelen Hiermee u het standaardstuurprogrammabestand (scorescript) en het omgevingsbestand automatisch genereren. 
    
    Voor dit voorbeeld gebruiken we de standaardinstellingen in het menu *Geavanceerd.* 

1. Selecteer **Implementeren**.  

    Boven aan het scherm **Uitvoeren** wordt een groen succesbericht weergegeven en in het **deelvenster Aanbevolen model** wordt een statusbericht weergegeven onder De status **Implementeren**. Selecteer Periodiek **vernieuwen** om de implementatiestatus te controleren.
    
Nu hebt u een operationele webservice om voorspellingen te genereren. 

Ga naar de [**volgende stappen**](#next-steps) voor meer informatie over het gebruik van uw nieuwe webservice en test uw voorspellingen met de ingebouwde Azure Machine Learning-ondersteuning van Power BI.

## <a name="clean-up-resources"></a>Resources opschonen

Implementatiebestanden zijn groter dan gegevens- en experimentbestanden, dus ze kosten meer om op te slaan. Verwijder alleen de implementatiebestanden om de kosten voor uw account te minimaliseren of als u uw werkruimte- en experimentbestanden wilt behouden. Verwijder anders de hele brongroep als u niet van plan bent een van de bestanden te gebruiken.  

### <a name="delete-the-deployment-instance"></a>De implementatie-instantie verwijderen

Verwijder alleen de implementatieinstantie uit Azure\/Machine Learning op https: /ml.azure.com/, als u de brongroep en werkruimte wilt behouden voor andere zelfstudies en verkenningen. 

1. Ga naar [Azure Machine Learning](https://ml.azure.com/). Navigeer naar uw werkruimte en selecteer links onder het deelvenster **Elementen** de optie **Eindpunten**. 

1. Selecteer de implementatie die u wilt verwijderen en selecteer **Verwijderen**. 

1. Selecteer **Doorgaan**.

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze geautomatiseerde machine learning-zelfstudie hebt u de geautomatiseerde ML-interface van Azure Machine Learning gebruikt om een classificatiemodel te maken en te implementeren. Zie deze artikelen voor meer informatie en de volgende stappen:

> [!div class="nextstepaction"]
> [Een webservice gebruiken](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Meer informatie over [geautomatiseerde machine learning](concept-automated-ml.md).
+ Zie het artikel [Automatisme machine learning-resultaten begrijpen](how-to-understand-automated-ml.md#classification) voor meer informatie over classificatiestatistieken en grafieken.+ Meer informatie over [featurisatie](how-to-use-automated-ml-for-ml-models.md#featurization).
+ Meer informatie over [gegevensprofilering](how-to-use-automated-ml-for-ml-models.md#profile).


>[!NOTE]
> Deze bankmarketinggegevensset wordt beschikbaar gesteld onder de [Creative Commons -licentie (CCO: Public Domain).](https://creativecommons.org/publicdomain/zero/1.0/) Alle rechten in de afzonderlijke inhoud van de database zijn gelicentieerd onder de [database-inhoudslicentie](https://creativecommons.org/publicdomain/zero/1.0/) en beschikbaar op [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Deze dataset was oorspronkelijk beschikbaar in de [UCI Machine Learning Database.](https://archive.ics.uci.edu/ml/datasets/bank+marketing)<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez en P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31 juni 2014.
