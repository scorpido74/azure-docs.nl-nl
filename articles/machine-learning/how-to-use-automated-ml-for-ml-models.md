---
title: AutoML gebruiken om modellen te maken & implementeren
titleSuffix: Azure Machine Learning
description: Automatische machine learning modellen maken, controleren en implementeren met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: aniththa
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/20/2020
ms.openlocfilehash: 44da1d67155efe3feb41e3648c820a8ada0b8fc3
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85119338"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Automatische machine learning modellen maken, controleren en implementeren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In dit artikel leert u hoe u geautomatiseerde machine learning modellen kunt maken, verkennen en implementeren zonder één regel code in de Studio-interface van Azure Machine Learning. Automatische machine learning is een proces waarbij het beste machine learning algoritme voor uw specifieke gegevens wordt geselecteerd. Met dit proces kunt u snel machine learning modellen genereren. Meer [informatie over automatische machine learning](concept-automated-ml.md).
 
Voor een end-to-end-voor beeld probeert [u de zelf studie voor het maken van een classificatie model met de automatische ml-interface van Azure machine learning](tutorial-first-experiment-automated-ml.md). 

[Configureer uw geautomatiseerde machine learning experimenten](how-to-configure-auto-train.md) met de Azure machine learning SDK voor een op een python-code gebaseerde ervaring.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een Azure Machine Learning-werk ruimte met een type **Enter prise-editie**. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).  Als u een bestaande werk ruimte wilt bijwerken naar Enter prise Edition, raadpleegt [u upgrade to Enter prise Edition](how-to-manage-workspace.md#upgrade)(Engelstalig).

## <a name="get-started"></a>Aan de slag

1. Meld u aan bij Azure Machine Learning op https://ml.azure.com . 

1. Selecteer uw abonnement en werk ruimte. 

1. Ga naar het linkerdeel venster. Selecteer **automatische ml** onder het gedeelte **Auteur** .

[![Navigatievenster van Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Als dit de eerste keer is dat er experimenten worden uitgevoerd, ziet u een lege lijst en koppelingen naar documentatie. 

Als dat niet het geval is, ziet u een lijst met uw recente geautomatiseerde machine learning experimenten, met inbegrip van die zijn gemaakt met de SDK. 

## <a name="create-and-run-experiment"></a>Experiment maken en uitvoeren

1. Selecteer **+ nieuwe automatische ml run** en vul het formulier in.

1. Selecteer een gegevensset uit uw opslag container of maak een nieuwe gegevensset. Gegevens sets kunnen worden gemaakt op basis van lokale bestanden, Web-url's, gegevens opslag of Azure open gegevens sets. 

    >[!Important]
    > Vereisten voor trainings gegevens:
    >* Gegevens moeten in tabel vorm zijn.
    >* De waarde die u wilt voors pellen (doel kolom), moet aanwezig zijn in de gegevens.

    1. Als u een nieuwe gegevensset wilt maken op basis van een bestand op uw lokale computer, selecteert u **Bladeren** en selecteert u vervolgens het bestand. 

    1. Geef uw gegevensset een naam en een optionele beschrijving. 

    1. Selecteer **volgende** om het **formulier gegevens opslag en bestand selecteren**te openen. Op dit formulier selecteert u waar u uw gegevensset wilt uploaden. de standaard opslag container die automatisch wordt gemaakt met uw werk ruimte, of kies een opslag container die u wilt gebruiken voor het experiment. 

    1. Controleer de **instellingen en het voorbeeld** formulier op nauw keurigheid. Het formulier wordt op intelligente wijze ingevuld op basis van het bestands type. 

        Veld| Beschrijving
        ----|----
        Bestandsindeling| Definieert de indeling en het type gegevens dat is opgeslagen in een bestand.
        Scheidingsteken| Een of meer tekens die de grens aangeven tussen  afzonderlijke, onafhankelijke regio's in tekst zonder opmaak of andere gegevensstromen.
        Encoding| Identificeert welke bit-naar-tekenschematabel er moet gebruikt worden om uw gegevensset te lezen.
        Kolomkoppen| Geeft aan hoe koppen van de gegevensset eventueel worden behandeld.
        Rijen overslaan | Geeft aan hoeveel rijen er eventueel worden overgeslagen in de gegevensset.
    
        Selecteer **Next**.

    1. Het **schema** formulier wordt op de slimme wijze ingevuld op basis van de selecties in het formulier **instellingen en preview** . Hier configureert u het gegevens type voor elke kolom, bekijkt u de kolom namen en selecteert u welke kolommen niet voor uw experiment moeten worden **toegevoegd** . 
            
        Selecteer **Volgende.**

    1. Het formulier **Details bevestigen** is een samen vatting van de gegevens die eerder zijn ingevuld in de **basis gegevens** en- **instellingen en preview** -formulieren. U kunt ook een gegevens profiel maken voor uw gegevensset met behulp van een profilerings functie ingeschakeld. Meer informatie over [gegevensprofilering](#profile).

        Selecteer **Next**.
1. Selecteer de zojuist gemaakte gegevensset zodra deze wordt weer gegeven. U kunt ook een preview van de gegevensset en voorbeeld statistieken bekijken. 

1. Voer een unieke naam voor het experiment in op het formulier voor het configureren van een **uitvoering** .

1. Selecteer een doel kolom. Dit is de kolom waarop u de voor spellingen wilt uitvoeren.

1. Selecteer een compute voor de taak voor gegevens profilering en training. In de vervolg keuzelijst vindt u een lijst met uw bestaande berekeningen. Volg de instructies in stap 7 om een nieuwe Compute te maken.

1. Selecteer **een nieuwe Compute maken** om uw berekenings context voor dit experiment te configureren.

    Veld|Beschrijving
    ---|---
    Naam berekening| Voer een unieke naam in die uw berekenings context identificeert.
    Grootte van de virtuele machine| Selecteer de grootte van de virtuele machine voor uw berekening.
    Min. / max. knooppunten (in Geavanceerde instellingen)| U moet u één of meer knooppunten opgeven om gegevens te profileren. Voer het maximum aantal knoop punten in voor de reken kracht. De standaard waarde is 6 knoop punten voor een AML-berekening.
    
    Selecteer **Maken**. Het maken van een nieuwe berekening kan enkele minuten duren.

    >[!NOTE]
    > De naam van de berekening geeft aan of de compute die u selecteert/maakt, *profile ring is ingeschakeld*. (Zie de sectie [gegevens profilering](#profile) voor meer informatie).

    Selecteer **Next**.

1. Selecteer op het **taak type en het instellingen** formulier het taak type: classificatie, regressie of prognose. Zie [taak typen definiëren](how-to-define-task-type.md) voor meer informatie.

    1. Voor classificatie kunt u ook diep gaande informatie inschakelen die wordt gebruikt voor tekst featurizations.

    1. Voor prognoses:
        1. Selecteer een tijd kolom: deze kolom bevat de tijd gegevens die moeten worden gebruikt.

        1. Prognose horizon selecteren: Geef aan hoeveel tijds eenheden (minuten/uren/dagen/weken/maanden/jaar) het model op de toekomst kan voors pellen. Verder is het model vereist om in de toekomst te voors pellen, hoe minder nauw keurig wordt. Meer [informatie over prognoses en prognoses horizon](how-to-auto-train-forecast.md).

1. Beschrijving Aanvullende configuratie-instellingen weer geven: extra instellingen die u kunt gebruiken om de trainings taak beter te beheren. Anders worden de standaardinstellingen toegepast op basis van de selectie en gegevens van het experiment. 

    Aanvullende configuraties|Beschrijving
    ------|------
    Primaire metrische gegevens| De belangrijkste waarde die wordt gebruikt voor het scoren van uw model. Meer [informatie over de metrische gegevens van modellen](how-to-configure-auto-train.md#explore-model-metrics).
    Automatische featurization| Selecteer deze optie om de parametrisatie in of uit te scha kelen die door automatische machine learning worden uitgevoerd. Automatische parametrisatie omvat het automatisch opschonen van gegevens, voorbereiden en transformeren voor het genereren van synthetische functies. Niet ondersteund voor het taak type time series-prognose. Meer [informatie over parametrisatie](how-to-configure-auto-features.md#featurization). 
    Aanbevolen model uitleggen | Schakel deze optie in of uit om de uitleg mogelijkheid van het aanbevolen model weer te geven
    Geblokkeerd algoritme| Selecteer de algoritmen die u wilt uitsluiten van de trainings taak.
    Criterium voor afsluiten| Wanneer aan een van deze criteria wordt voldaan, wordt de trainings taak gestopt. <br> *Tijd van trainings taak (uren)*: hoe lang het mogelijk is om de trainings taak uit te voeren. <br> *Drempel waarde voor metrische Score*: minimale metrische score voor alle pijp lijnen. Dit zorgt ervoor dat als u een gedefinieerde doel metriek hebt die u wilt bereiken, u niet meer tijd op de trainings taak brengt dan nodig is.
    Validatie| Selecteer een van de opties voor kruis validatie die u wilt gebruiken in de trainings taak. Meer [informatie over Kruis validatie](how-to-configure-cross-validation-data-splits.md#prerequisites).
    Gelijktijdigheid| Maximum aantal *gelijktijdige herhalingen*: Maxi maal toegestane pijp lijnen (iteraties) om in de trainings taak te testen. De taak wordt niet meer uitgevoerd dan het opgegeven aantal iteraties.

1. Beschrijving Parametrisatie-instellingen weer geven: als u ervoor kiest **automatische parametrisatie** in te scha kelen in het formulier **aanvullende configuratie-instellingen** , worden de standaard parametrisatie-technieken toegepast. In de **instellingen van de weer gave-parametrisatie** kunt u deze standaard waarden wijzigen en dienovereenkomstig aanpassen. Meer informatie over het [aanpassen van featurizations](#customize-featurization). 

    ![Formulier Azure Machine Learning Studio-taak type](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

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
Count| Totaal aantal ontbrekende en niet-ontbrekende vermeldingen in de kolom.
Niet-ontbrekend aantal| Het aantal vermeldingen in de kolom die niet ontbreken. Lege teken reeksen en fouten worden beschouwd als waarden, zodat ze niet bijdragen aan het aantal niet-ontbrekende items.
Quantiles| Geschatte waarden bij elke quantile om een idee te geven van de distributie van de gegevens.
Gemiddelde| Reken kundige gemiddelde of gemiddelde van de kolom.
Standaardafwijking| Meting van de hoeveelheid sprei ding of variatie van de gegevens van deze kolom.
Variantie| De mate waarin de gegevens van deze kolom worden verdeeld uit de gemiddelde waarde. 
Asymmetrie| Meting van de manier waarop de gegevens van de andere kolom van een normale distributie zijn.
Kurtosis| Meting van hoe sterk de gegevens van deze kolom worden vergeleken met een normale distributie.

## <a name="customize-featurization"></a>Parametrisatie aanpassen

In het formulier **parametrisatie** kunt u automatische parametrisatie in-en uitschakelen en de automatische parametrisatie-instellingen voor uw experiment aanpassen. Als u dit formulier wilt openen, raadpleegt u stap 10 in het gedeelte [experiment maken en uitvoeren](#create-and-run-experiment) . 

De volgende tabel bevat een overzicht van de aanpassingen die momenteel beschikbaar zijn via Studio. 

Kolom| Aanpassing
---|---
Inbegrepen | Hiermee geeft u op welke kolommen moeten worden toegevoegd voor de training.
Onderdeel type| Wijzig het waardetype voor de geselecteerde kolom.
Toegerekend met| Selecteer welke waarde er ontbrekende waarden moeten worden toegerekend met in uw gegevens.

![Formulier Azure Machine Learning Studio-taak type](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Experiment uitvoeren en resultaten weer geven

Selecteer **volt ooien** om uw experiment uit te voeren. Het voorbereiden van het experiment kan tot 10 minuten duren. Trainingstaken kunnen nog 2-3 minuten meer kosten voordat het uitvoeren van elke pijplijn is voltooid.

### <a name="view-experiment-details"></a>Details van experiment weer geven

Het scherm **detail uitvoeren** wordt geopend op het tabblad **Details** . In dit scherm ziet u een overzicht van de uitvoering van het experiment, met inbegrip van een status balk bovenaan naast het uitvoerings nummer. 

Het tabblad **Modellen** bevat een lijst met de gemaakte modellen, op volgorde van de metrische score. Standaardstaat het model dat het hoogst scoort op basis van het gekozen metrische gegeven bovenaan de lijst. Terwijl de trainingstaak meer modellen uitprobeert, worden deze toegevoegd aan de lijst. Gebruik dit om een snelle vergelijking te krijgen van de metrische gegevens voor de tot dusver geproduceerde modellen.

[![Dash board Details uitvoeren](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Details van de cursus uitvoering weer geven

Inzoomen op een van de voltooide modellen om details van de trainings uitvoering te bekijken, zoals metrische gegevens uitvoeren op het tabblad **model Details** of prestatie grafieken op het tabblad **Visualisaties** . meer [informatie over grafieken](how-to-understand-automated-ml.md).

[![Details van herhaling](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Uw model implementeren

Zodra u het beste model bij de hand hebt, is het tijd om het te implementeren als een webservice om nieuwe gegevens te voors pellen.

Automatische ML helpt u bij het implementeren van het model zonder code te schrijven:

1. U hebt een aantal opties voor implementatie. 

    + Optie 1: als u het beste model wilt implementeren (volgens de criteria die u hebt gedefinieerd), selecteert u de knop **beste model implementeren** op het tabblad **Details** .

    + Optie 2: als u een specifieke model herhaling van dit experiment wilt implementeren, zoomt u in op het model om het tabblad **model Details** te openen en selecteert u **model implementeren**.

1. Vul het deel venster **model implementeren** in.

    Veld| Waarde
    ----|----
    Naam| Voer een unieke naam in voor uw implementatie.
    Beschrijving| Voer een beschrijving in om beter te kunnen identificeren waarvoor deze implementatie is.
    Rekentype| Selecteer het type eind punt dat u wilt implementeren: *Azure Kubernetes service (AKS)* of *Azure container instance (ACI)*.
    Naam berekening| *Is alleen van toepassing op AKS:* Selecteer de naam van het AKS-cluster waarnaar u wilt implementeren.
    Verificatie inschakelen | Selecteer deze optie om verificatie op basis van tokens of sleutel toe te staan.
    Aangepaste implementatie-assets gebruiken| Schakel deze functie in als u uw eigen score script en omgevings bestand wilt uploaden. Meer [informatie over Score scripts](how-to-deploy-and-where.md#script).

    >[!Important]
    > Bestands namen moeten minder dan 32 tekens lang zijn en moeten beginnen en eindigen met een alfanumerieke teken reeks. Kan streepjes, onderstrepings tekens, punten en alfanumerieken bevatten. Spaties zijn niet toegestaan.

    Het menu *Geavanceerd* biedt standaard implementatie functies, zoals het [verzamelen van gegevens](how-to-enable-app-insights.md) en het gebruik van bron instellingen. Als u deze standaard waarden wilt overschrijven, doet u dit in dit menu.

1. Selecteer **Implementeren**. Het volt ooien van de implementatie kan ongeveer 20 minuten duren.
    Zodra de implementatie is gestart, wordt het tabblad **model Details** weer gegeven. Zie de voortgang van de implementatie in het gedeelte **Implementatie status** van het deel venster **Eigenschappen** . 

U hebt nu een Operational web service voor het genereren van voor spellingen. U kunt de voor spellingen testen door de service te doorzoeken van de [ingebouwde Azure machine learning ondersteuning van Power bi](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van een webservice](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* Krijg [inzicht in geautomatiseerde machine learning resultaten](how-to-understand-automated-ml.md).
* Meer [informatie over automatische machine learning](concept-automated-ml.md) en Azure machine learning.
