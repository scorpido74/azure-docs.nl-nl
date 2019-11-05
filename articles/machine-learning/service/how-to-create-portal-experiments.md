---
title: De Automated ML-interface van Azure gebruiken voor het trainen & implementeren van modellen
titleSuffix: Azure Machine Learning
description: U kunt geautomatiseerde machine learning experimenten maken, beheren en implementeren in Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 05bae18d35eafc47eddc7c1450eb06af5bbe5562
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497194"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>Automatische machine learning experimenten maken, verkennen en implementeren met Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

 In dit artikel leert u hoe u geautomatiseerde machine learning experimenten kunt maken, verkennen en implementeren in Azure Machine Learning Studio zonder één regel code. Automatische machine learning automatiseert het proces van het selecteren van het beste algoritme dat voor uw specifieke gegevens moet worden gebruikt, zodat u snel een machine learning model kunt genereren. Meer [informatie over automatische machine learning](concept-automated-ml.md).

 Als u de voor keur geeft aan een meer op code gebaseerde ervaring, kunt u ook [uw geautomatiseerde machine learning experimenten in python configureren](how-to-configure-auto-train.md) met de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Een Azure Machine Learning-werk ruimte met een type **Enter prise-editie**. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md).  Als u een bestaande werk ruimte wilt bijwerken naar Enter prise Edition, raadpleegt [u upgrade to Enter prise Edition](how-to-manage-workspace.md#upgrade)(Engelstalig).

## <a name="get-started"></a>Aan de slag

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com). 

1. Selecteer uw abonnement en werk ruimte. 

1. Ga naar het linkerdeel venster. Selecteer **automatische ml** onder het gedeelte **Auteur** .

[navigatie deel venster van ![Azure Machine Learning Studio](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 Als dit de eerste keer is dat er experimenten worden uitgevoerd, ziet u een lege lijst en koppelingen naar documentatie. 

Als dat niet het geval is, ziet u een lijst met uw recente geautomatiseerde machine learning experimenten, met inbegrip van die zijn gemaakt met de SDK. 

## <a name="create-and-run-experiment"></a>Experiment maken en uitvoeren

1. Selecteer **+ experiment maken** en vul het formulier in.

1. Selecteer een gegevensset uit uw opslag container of maak een nieuwe gegevensset. Gegevens sets kunnen worden gemaakt op basis van lokale bestanden, Web-url's, gegevens opslag of Azure open gegevens sets. 

    >[!Important]
    > Vereisten voor trainings gegevens:
    >* Gegevens moeten in tabel vorm zijn.
    >* De waarde die u wilt voors pellen (doel kolom), moet aanwezig zijn in de gegevens.

    1. Als u een nieuwe gegevensset wilt maken op basis van een bestand op uw lokale computer, selecteert u **Bladeren** en selecteert u vervolgens het bestand. 

    1. Geef uw gegevensset een unieke naam en geef een optionele beschrijving op. 

    1. Selecteer **volgende**om het te uploaden naar de standaard opslag container die automatisch wordt gemaakt met uw werk ruimte, of kies een opslag container die u wilt gebruiken voor het experiment. 

    1. Controleer de **instellingen en het voorbeeld** formulier op nauw keurigheid. Het formulier wordt op intelligente wijze ingevuld op basis van het bestands type. 

        Veld| Beschrijving
        ----|----
        Bestands indeling| Hiermee definieert u de indeling en het type van de gegevens die zijn opgeslagen in een bestand.
        Vorm| Een of meer tekens voor het opgeven van de grens tussen afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevens stromen.
        Encoding| Hiermee wordt aangegeven welke bits-schema tabel moet worden gebruikt om de gegevensset te lezen.
        Kolom koppen| Hiermee wordt aangegeven hoe de headers van de gegevensset, indien aanwezig, worden behandeld.
        Rijen overs Laan | Hiermee wordt aangegeven hoeveel, indien van toepassing, rijen in de gegevensset worden overgeslagen.
    
        Selecteer **Volgende**.

    1. Het **schema** formulier wordt op de slimme wijze ingevuld op basis van de selecties in het formulier **instellingen en preview** . Hier configureert u het gegevens type voor elke kolom, bekijkt u de kolom namen en selecteert u welke kolommen niet voor uw experiment moeten worden **toegevoegd** . 
            
        Selecteer **Volgende.**

    1. Het formulier **Details bevestigen** is een samen vatting van de gegevens die eerder zijn ingevuld in de **basis gegevens** en- **instellingen en preview** -formulieren. U hebt ook de optie om uw gegevensset te profileren met behulp van een profilerings functie ingeschakeld. Meer informatie over [gegevens profilering](#profile).

        Selecteer **Volgende**.
1. Selecteer de zojuist gemaakte gegevensset zodra deze wordt weer gegeven. U kunt ook een preview van de gegevensset en voorbeeld statistieken bekijken. 

1. Voer een unieke naam voor het experiment in op het formulier voor het configureren van een **uitvoering** .

1. Selecteer een doel kolom. Dit is de kolom waarop u de voor spellingen wilt uitvoeren.

1. Selecteer een compute voor de taak voor gegevens profilering en training. In de vervolg keuzelijst vindt u een lijst met uw bestaande berekeningen. Volg de instructies in stap 7 om een nieuwe Compute te maken.

1. Selecteer **een nieuwe Compute maken** om uw berekenings context voor dit experiment te configureren.

    Veld|Beschrijving
    ---|---
    Compute name| Voer een unieke naam in die uw berekenings context identificeert.
    Grootte van virtuele machine| Selecteer de grootte van de virtuele machine voor de reken kracht.
    Min-maximum aantal knoop punten (in geavanceerde instellingen)| Als u wilt profiel gegevens, moet u één of meer knoop punten opgeven. Voer het maximum aantal knoop punten in voor de reken kracht. De standaard waarde is 6 knoop punten voor een AML-berekening.
    
    Selecteer **Maken**. Het maken van een nieuwe berekening kan enkele minuten duren.

    >[!NOTE]
    > De naam van de berekening geeft aan of de compute die u selecteert/maakt, *profile ring is ingeschakeld*. (Zie de sectie [gegevens profilering](#profile) voor meer informatie).

    Selecteer **Volgende**.

1. Selecteer op het **taak type en het instellingen** formulier het taak type: classificatie, regressie of prognose. 

    1. Voor classificatie kunt u ook diep gaande informatie inschakelen die wordt gebruikt voor tekst featurizations.

    1. Voor prognoses:
        1. Selecteer een tijd kolom: deze kolom bevat de tijd gegevens die moeten worden gebruikt.

        1. Prognose horizon selecteren: Geef aan hoeveel tijds eenheden (minuten/uren/dagen/weken/maanden/jaar) het model op de toekomst kan voors pellen. Verder is het model vereist om in de toekomst te voors pellen, hoe minder nauw keurig wordt. Meer [informatie over prognoses en prognoses horizon](how-to-auto-train-forecast.md).

1. Beschrijving Aanvullende configuraties: extra instellingen die u kunt gebruiken om de trainings taak beter te beheren. Anders worden de standaard waarden toegepast op basis van het experiment en de gegevens. 

    Aanvullende configuraties|Beschrijving
    ------|------
    Primaire metriek| De belangrijkste waarde die wordt gebruikt voor het scoren van uw model. Meer [informatie over de metrische gegevens van modellen](how-to-configure-auto-train.md#explore-model-metrics).
    Automatische parametrisatie| Selecteer deze optie om de voor verwerking van automatische machine learning in of uit te scha kelen. Preverwerking omvat het automatisch opschonen van gegevens, voorbereiden en transformeren voor het genereren van synthetische functies. [Meer informatie over voor verwerking](#preprocess).
    Geblokkeerd algoritme| Selecteer de algoritmen die u wilt uitsluiten van de trainings taak.
    Criterium afsluiten| Wanneer aan een van deze criteria wordt voldaan, wordt de trainings taak gestopt. <br> *Tijd van trainings taak (uren)* : hoe lang het mogelijk is om de trainings taak uit te voeren. <br> *Drempel waarde voor metrische Score*: minimale metrische score voor alle pijp lijnen. Dit zorgt ervoor dat als u een gedefinieerde doel metriek hebt die u wilt bereiken, u niet meer tijd op de trainings taak brengt dan nodig is.
    Validatie| Selecteer een van de opties voor kruis validatie die u wilt gebruiken in de trainings taak. Meer [informatie over Kruis validatie](how-to-configure-auto-train.md).
    Gelijktijdigheid| Maximum aantal *gelijktijdige herhalingen*: Maxi maal toegestane pijp lijnen (iteraties) om in de trainings taak te testen. De taak wordt niet meer uitgevoerd dan het opgegeven aantal iteraties. <br> *Max. aantal kernen per iteratie*: Selecteer de multi-core-limieten die u wilt gebruiken bij het gebruik van multi-core compute.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Overzichts statistieken voor gegevens profilering &

U kunt een groot aantal samenvattings statistieken in uw gegevensset ontvangen om te controleren of uw gegevensset van ML is. Voor niet-numerieke kolommen bevatten ze alleen basis statistieken zoals min, Max en aantal fouten. Voor numerieke kolommen kunt u ook hun statistische momenten en geschatte quantiles bekijken. Het gegevens profiel bevat met name:

>[!NOTE]
> Lege vermeldingen worden weer gegeven voor functies met irrelevante typen.

Statistiek|Beschrijving
------|------
Functie| De naam van de kolom die wordt samenvatten.
Profiel| In-line visualisatie op basis van het type dat is afgeleid. Teken reeksen, Booleaanse waarden en datums hebben bijvoorbeeld een aantal aantallen, terwijl decimalen (cijfers) geschatte histogrammen hebben. Zo kunt u een goed inzicht krijgen in de distributie van de gegevens.
Type distributie| In-line waarde van het aantal typen in een kolom. Nullen zijn hun eigen type, waardoor deze visualisatie nuttig is voor het detecteren van oneven of ontbrekende waarden.
Type|Het type van de kolom is afgeleid. Mogelijke waarden zijn: teken reeksen, booleans, datums en decimalen.
Min.| Minimum waarde van de kolom. Er worden geen lege items weer gegeven voor functies waarvan het type geen inherente ordening (bijvoorbeeld Boole-waarden) heeft.
Max.| De maximum waarde van de kolom. 
Aantal| Totaal aantal ontbrekende en niet-ontbrekende vermeldingen in de kolom.
Niet-ontbrekend aantal| Het aantal vermeldingen in de kolom die niet ontbreken. Lege teken reeksen en fouten worden beschouwd als waarden, zodat ze niet bijdragen aan het aantal niet-ontbrekende items.
Quantiles| Geschatte waarden bij elke quantile om een idee te geven van de distributie van de gegevens.
Gemiddelde| Reken kundige gemiddelde of gemiddelde van de kolom.
Standaardafwijking| Meting van de hoeveelheid sprei ding of variatie van de gegevens van deze kolom.
Variantie| De mate waarin de gegevens van deze kolom worden verdeeld uit de gemiddelde waarde. 
Asymmetrie| Meting van de manier waarop de gegevens van de andere kolom van een normale distributie zijn.
Kurtosis| Meting van hoe sterk de gegevens van deze kolom worden vergeleken met een normale distributie.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Geavanceerde voor verwerkings opties

Wanneer u uw experimenten configureert, kunt u de geavanceerde instelling `Preprocess`. Dit betekent dat de volgende stappen voor preprocesing en parametrisatie automatisch worden uitgevoerd.

|&nbsp;stappen voor voor verwerking| Beschrijving |
| ------------- | ------------- |
|Hoge kardinaliteit of geen variantie-functies verwijderen|Verwijder deze uit de trainings-en validatie sets, inclusief functies waarbij alle waarden ontbreken, dezelfde waarde over alle rijen of met extreem hoge kardinaliteit (bijvoorbeeld hashes, Id's of GUID'S).|
|Ontbrekende waarden toegerekend|Voor numerieke functies toegerekend met het gemiddelde van de waarden in de kolom.<br/><br/>Voor categorische-functies toegerekend met de meest frequente waarde.|
|Aanvullende functies genereren|Voor DateTime-functies: jaar, maand, dag, dag van de week, dag van jaar, kwar taal, week van het jaar, uur, minuut, seconde.<br/><br/>Voor tekst functies: term frequentie op basis van unigrams, bi-gram en Tri-Character-gram.|
|Transformeren en coderen |Numerieke functies met weinig unieke waarden worden omgezet in categorische-functies.<br/><br/>Er wordt één Hot encoding uitgevoerd voor lage kardinaliteit categorische; voor hoge kardinaliteit, een-hot-hash-code ring.|
|Woord insluitingen|Tekst featurizer waarmee vectoren van tekst tokens worden geconverteerd naar sentence vectoren met behulp van een vooraf getraind model. De insluitings vector van elk woord in een document wordt samen geaggregeerd om een document functie Vector te maken.|
|Doel codering|Voor categorische-functies wijst elke categorie toe met de gemiddelde doel waarde voor regressie problemen en op basis van de klasse-kans voor elke klasse voor classificatie problemen. De Kruis validatie op basis van frequentie en k-vouwen wordt toegepast om de toewijzing en het lawaai dat door sparse gegevens categorieën wordt veroorzaakt, te verminderen.|
|Coderen van tekst doel|Voor tekst invoer wordt een gestapeld lineair model met Bag-of-woorden gebruikt om de kans van elke klasse te genereren.|
|Gewicht van bewijs (WoE)|Hiermee wordt WoE berekend als een maat eenheid van de correlatie tussen categorische-kolommen en de doel kolom. Het wordt berekend als het logboek van de verhouding van de in-class versus van de cursus kansen. Met deze stap wordt één numerieke functie kolom per klasse uitgevoerd en wordt de nood zaak om ontbrekende waarden en uitschieter-behandeling expliciet te toegerekend.|
|Cluster afstand|Treinen a k: cluster model op alle numerieke kolommen.  Hiermee worden k nieuwe functies, een nieuwe numerieke functie per cluster, met de afstand van elk voor beeld naar de massa middelpunt van elk cluster uitgevoerd.|

## <a name="run-experiment-and-view-results"></a>Experiment uitvoeren en resultaten weer geven

Selecteer **Start** om uw experiment uit te voeren. Het voorbereiden van het experiment kan tot 10 minuten duren. Voor het uitvoeren van de uitvoering van trainings taken kan een extra 2-3 minuten voor elke pijp lijn langer duren.

### <a name="view-experiment-details"></a>Details van experiment weer geven

>[!NOTE]
> Selecteer regel matig **vernieuwen** om de status van de uitvoering weer te geven. 

Het scherm **detail uitvoeren** wordt geopend op het tabblad **Details** . In dit scherm ziet u een overzicht van de uitvoering van het experiment, met inbegrip van de **uitvoerings status**. 

Het tabblad **modellen** bevat een lijst met de modellen die zijn gemaakt op basis van de metrische Score. Standaard beoordeelt het model dat het hoogste verlaagt op basis van de gekozen metriek boven aan de lijst. Wanneer de trainings taak meer modellen probeert, worden deze toegevoegd aan de lijst. Gebruik deze om een snelle vergelijking te krijgen van de metrische gegevens voor de modellen die tot nu toe zijn geproduceerd.

[![dash board Details uitvoeren](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Details van de cursus uitvoering weer geven

Inzoomen op een van de voltooide modellen om details van de trainings uitvoering te bekijken, zoals metrische gegevens uitvoeren op het tabblad **model Details** of prestatie grafieken op het tabblad **Visualisaties** . meer [informatie over grafieken](how-to-understand-automated-ml.md).

[Details van ![herhaling](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Uw model implementeren

Zodra u het beste model bij de hand hebt, is het tijd om het te implementeren als een webservice om nieuwe gegevens te voors pellen.

Automatische ML helpt u bij het implementeren van het model zonder code te schrijven:

1. U hebt een aantal opties voor implementatie. 

    + Optie 1: als u het beste model wilt implementeren (volgens de criteria die u hebt gedefinieerd), selecteert u beste model implementeren op het tabblad Details.

    + Optie 2: als u een specifieke model herhaling van dit experiment wilt implementeren, zoomt u in op het model om het tabblad model details te openen en selecteert u model implementeren.

1. Vul het deel venster **model implementeren** in.

    Veld| Waarde
    ----|----
    Implementatie naam| Voer een unieke naam in voor uw implementatie.
    Beschrijving van implementatie| Voer een beschrijving in om beter te kunnen identificeren waarvoor deze implementatie is.
    Score script| Genereer of upload uw eigen score bestand automatisch. Meer [informatie over Score script](how-to-deploy-and-where.md#script).
    Omgevings script| Automatisch genereren of uploaden van uw eigen omgevings bestand.
    >[!Important]
    > Bestands namen moeten minder dan 32 tekens lang zijn en moeten beginnen en eindigen met een alfanumerieke teken reeks. Kan streepjes, onderstrepings tekens, punten en alfanumerieken bevatten. Spaties zijn niet toegestaan.

1. Selecteer **Implementeren**. Het volt ooien van de implementatie kan ongeveer 20 minuten duren.

U hebt nu een Operational web service voor het genereren van voor spellingen. U kunt de voor spellingen testen door de service te doorzoeken van de [ingebouwde Azure machine learning ondersteuning van Power bi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Volgende stappen

* Probeer de end-to-end- [zelf studie voor het maken van uw eerste geautomatiseerde ml experiment met Azure machine learning](tutorial-first-experiment-automated-ml.md). 
* Meer [informatie over automatische machine learning](concept-automated-ml.md) en Azure machine learning.
* Krijg [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).
* [Meer informatie over het gebruik van een webservice](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
