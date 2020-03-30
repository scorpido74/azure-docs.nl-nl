---
title: Architectuur & sleutelbegrippen
titleSuffix: Azure Machine Learning
description: Meer informatie over de architectuur, termen, concepten en werkstromen die deel uitmaken van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/17/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f779781eee81bf85f6420e5bae6b0feb62680b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064172"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hoe Azure Machine Learning werkt: architectuur en concepten

Meer informatie over de architectuur, concepten en werkstroom voor Azure Machine Learning. De belangrijkste onderdelen van de service en de algemene werkstroom voor het gebruik van de service worden weergegeven in het volgende diagram:

![Azure Machine Learning-architectuur en -werkstroom](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Werkstroom

De machine learning-modelworkflow volgt over het algemeen deze reeks:

1. **Trein**
    + Ontwikkel machine learning trainingsscripts in **Python,** **R**of met de visual designer.
    + Een **rekendoel**maken en configureren .
    + **Verzend de scripts** naar een geconfigureerd compute-doel dat in die omgeving moet worden uitgevoerd. Tijdens de training kunnen de scripts lezen of schrijven naar **datastores.** De logboeken en uitvoer die tijdens de training worden geproduceerd, worden opgeslagen als **wordt opgeslagen** in de **werkruimte** en gegroepeerd onder **experimenten**.

1. **Pakket** - Nadat een bevredigende run is gevonden, registreert u het hardnekkige model in het **modelregister**.

1. **Query** - **het experiment** valideren voor geregistreerde statistieken uit de huidige en afgelopen uitvoeringen. Als de statistieken geen gewenst resultaat aangeven, loop je terug naar stap 1 en verbeeld je je scripts.

1. **Implementeren** - Een scorescript ontwikkelen dat het model gebruikt en **Het model implementeren** als **webservice** in Azure of naar een **IoT Edge-apparaat.**

1. **Monitor** - Monitor voor **gegevensdrift** tussen de trainingsgegevensset en gevolgtrekkingsgegevens van een geïmplementeerd model. Loop indien nodig terug naar stap 1 om het model om te scholen met nieuwe trainingsgegevens.

## <a name="tools-for-azure-machine-learning"></a>Hulpprogramma's voor Azure Machine Learning

Gebruik deze hulpprogramma's voor Azure Machine Learning:

+  Interactie met de service in elke Python-omgeving met de [Azure Machine Learning SDK voor Python.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Interactie met de service in elke R-omgeving met de [Azure Machine Learning SDK voor R.](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ Automatiseer uw machine learning-activiteiten met de [Azure Machine Learning CLI.](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)
+ Gebruik [Azure Machine Learning designer (preview)](concept-designer.md) om de werkstroomstappen uit te voeren zonder code te schrijven.


> [!NOTE]
> Hoewel in dit artikel termen en concepten worden gedefinieerd die worden gebruikt door Azure Machine Learning, worden geen termen en concepten voor het Azure-platform gedefinieerd. Zie de [woordenlijst van Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)voor meer informatie over de terminologie van azure-platforms.

## <a name="glossary"></a>Woordenlijst

* [Activiteit](#activities)
* [Werkruimte](#workspaces)
    * [Experimenten](#experiments)
        * [Uitvoeren](#runs) 
            * [Configuratie uitvoeren](#run-configurations)
            * [Momentopname](#snapshots)
            * [Git-tracking](#github-tracking-and-integration)
            * [Logboekregistratie](#logging)
    * [Machine learning-pijplijnen](#ml-pipelines)
    * [Modellen](#models)
        * [Omgevingen](#environments)
        * [Trainingsscript](#training-scripts)
        * [Schatters](#estimators)
    * [Eindpunten](#endpoints)
        * [Webservice](#web-service-endpoint)
        * [IoT-modules](#iot-module-endpoints)
    * [Gegevensset & datastores](#datasets-and-datastores)
    * [Rekendoelen](#compute-targets)

### <a name="activities"></a>Activiteiten

Een activiteit vertegenwoordigt een langlopende bewerking. De volgende bewerkingen zijn voorbeelden van activiteiten:

* Een rekendoel maken of verwijderen
* Een script uitvoeren op een compute-doel

Activiteiten kunnen meldingen bieden via de SDK of de webgebruikersinterface, zodat u de voortgang van deze bewerkingen eenvoudig volgen.

### <a name="workspaces"></a>Workspaces

[De werkruimte](concept-workspace.md) is de bron op het hoogste niveau voor Azure Machine Learning. Het biedt een centrale plek om te werken met alle artefacten die u maakt wanneer u Azure Machine Learning gebruikt. U een werkruimte delen met anderen. Zie [Wat is een Azure Machine Learning-werkruimte voor](concept-workspace.md)een gedetailleerde beschrijving van werkruimten?

### <a name="experiments"></a>Experimenten

Een experiment is een groepering van veel runs uit een bepaald script. Het behoort altijd tot een werkruimte. Wanneer u een run indient, geeft u een experimentnaam op. Informatie voor de run wordt opgeslagen in het kader van dat experiment. Als u een run verzendt en een experimentnaam opgeeft die niet bestaat, wordt er automatisch een nieuw experiment met die nieuw opgegeven naam gemaakt.

Zie [Zelfstudie: Uw eerste model trainen voor](tutorial-1st-experiment-sdk-train.md)een voorbeeld van het gebruik van een experiment.

### <a name="runs"></a>Wordt uitgevoerd

Een run is een enkele uitvoering van een trainingsscript. Een experiment bevat doorgaans meerdere uitvoeringen.

Azure Machine Learning registreert alle uitvoeringen en slaat de volgende gegevens op in het experiment:

* Metagegevens over de run (tijdstempel, duur, enzovoort)
* Statistieken die zijn vastgelegd door uw script
* Uitvoerbestanden die automatisch worden verzameld door het experiment of die door u expliciet zijn geüpload
* Een momentopname van de map die uw scripts bevat, voorafgaand aan de run

U produceert een run wanneer u een script indient om een model te trainen. Een run kan nul of meer onderliggende runs hebben. De run op het hoogste niveau kan bijvoorbeeld twee onderliggende uitvoeringen hebben, die elk een eigen onderliggende uitvoering kunnen hebben.

### <a name="run-configurations"></a>Configuraties uitvoeren

Een runconfiguratie is een set instructies die bepaalt hoe een script moet worden uitgevoerd in een opgegeven compute-doel. De configuratie bevat een brede set gedragsdefinities, zoals het al dan niet gebruiken van een bestaande Python-omgeving of het gebruik van een Conda-omgeving die is gebouwd op basis van een specificatie.

Een run-configuratie kan worden opgenomen in een bestand in de map dat uw trainingsscript bevat, of het kan worden geconstrueerd als een in-memory object en worden gebruikt om een run in te dienen.

Zie Bijvoorbeeld configuraties uitvoeren en [gebruik een rekendoel om uw model te trainen.](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Momentopnamen

Wanneer u een run verzendt, comprimeert Azure Machine Learning de map die het script als zip-bestand bevat en verzendt het naar het compute-doel. Het zip-bestand wordt vervolgens geëxtraheerd en het script wordt daar uitgevoerd. Azure Machine Learning slaat het zip-bestand ook op als momentopname als onderdeel van de run record. Iedereen met toegang tot de werkruimte kan door een run record bladeren en de momentopname downloaden.

> [!NOTE]
> Als u wilt voorkomen dat onnodige bestanden in de momentopname worden opgenomen, maakt u een negeren-bestand (.gitignore of .amlignore). Plaats dit bestand in de map Momentopname en voeg de bestandsnamen toe die u wilt negeren. Het bestand .amlignore gebruikt dezelfde [syntaxis en patronen als het .gitignore-bestand](https://git-scm.com/docs/gitignore). Als beide bestanden bestaan, heeft het bestand .amlignore voorrang.

### <a name="github-tracking-and-integration"></a>GitHub tracking en integratie

Wanneer u een trainingsrun start waarbij de bronmap een lokale Git-opslagplaats is, wordt informatie over de opslagplaats opgeslagen in de rungeschiedenis. Dit werkt met bewerkingen die zijn ingediend met behulp van een schatter, ML-pijplijn of scriptuitvoering. Het werkt ook voor runs ingediend van de SDK of Machine Learning CLI.

Zie [Git-integratie voor Azure Machine Learning voor](concept-train-model-git-integration.md)meer informatie.

### <a name="logging"></a>Logboekregistratie

Wanneer u uw oplossing ontwikkelt, gebruikt u de Azure Machine Learning Python SDK in uw Python-script om willekeurige statistieken te registreren. Na de run u de statistieken opvragen om te bepalen of de run het model heeft geproduceerd dat u wilt implementeren.

### <a name="ml-pipelines"></a>ML-pijpleidingen

U gebruikt machine learning-pijplijnen om workflows te maken en te beheren die machine learning-fasen aan elkaar verbinden. Een pijplijn kan bijvoorbeeld gegevensvoorbereiding, modeltraining, modelimplementatie en inference-/scoringsfasen bevatten. Elke fase kan meerdere stappen omvatten, die elk onbeheerd kunnen worden uitgevoerd in verschillende compute targets. 

Pijplijnstappen zijn herbruikbaar en kunnen worden uitgevoerd zonder de vorige stappen opnieuw uit te voeren als de uitvoer van deze stappen niet is gewijzigd. U bijvoorbeeld een model omscholen zonder dure stappen voor gegevensvoorbereiding opnieuw uit te voeren als de gegevens niet zijn gewijzigd. Met pijplijnen kunnen gegevenswetenschappers ook samenwerken terwijl ze werken aan afzonderlijke gebieden van een machine learning-workflow.

Zie [Pipelines en Azure Machine Learning](concept-ml-pipelines.md)voor meer informatie over machine learning-pijplijnen.

### <a name="models"></a>Modellen

Op zijn eenvoudigste, een model is een stukje code die een input neemt en produceert output. Het maken van een machine learning-model omvat het selecteren van een algoritme, het verstrekken van gegevens, en het afstemmen van hyperparameters. Training is een iteratief proces dat een getraind model produceert, dat inkapselt wat het model tijdens het trainingsproces heeft geleerd.

Een model wordt geproduceerd door een run in Azure Machine Learning. U ook een model gebruiken dat buiten Azure Machine Learning is getraind. U een model registreren in een Azure Machine Learning-werkruimte.

Azure Machine Learning is framework agnostisch. Wanneer u een model maakt, u elk populair machine learning-framework gebruiken, zoals Scikit-learn, XGBoost, PyTorch, TensorFlow en Chainer.

Zie [Zelfstudie: Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md)voor een voorbeeld van het trainen van een model met Scikit-learn en een schatter.

Het **modelregister** houdt alle modellen in uw Azure Machine Learning-werkruimte bij.

Modellen worden geïdentificeerd aan de basis en versie. Elke keer dat u een model registreert met dezelfde naam als een bestaand model, gaat het register ervan uit dat het een nieuwe versie is. De versie wordt verhoogd en het nieuwe model wordt onder dezelfde naam geregistreerd.

Wanneer u het model registreert, u extra metadatatags verstrekken en vervolgens de tags gebruiken wanneer u naar modellen zoekt.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die deel uitmaken van uw model. Als u bijvoorbeeld een model hebt dat in meerdere bestanden is opgeslagen, u deze als één model registreren in uw Azure Machine Learning-werkruimte. Na registratie u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

U een geregistreerd model dat wordt gebruikt door een actieve implementatie niet verwijderen.

Zie [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md)voor een voorbeeld van het registreren van een model.

### <a name="environments"></a>Omgevingen

Azure ML-omgevingen worden gebruikt om de configuratie (Docker / Python / Spark / enz.) te specificeren die wordt gebruikt om een reproduceerbare omgeving te creëren voor gegevensvoorbereiding, modeltraining en modelweergave. Ze worden beheerd en geversioned entiteiten binnen uw Azure Machine Learning-werkruimte die reproduceerbare, controleerbare en draagbare machine learning-workflows mogelijk maken voor verschillende rekendoelen.

U een omgevingsobject op uw lokale computer gebruiken om uw trainingsscript te ontwikkelen, dezelfde omgeving opnieuw te gebruiken op Azure Machine Learning Compute voor modeltraining op schaal en zelfs uw model met dezelfde omgeving te implementeren. 

Meer informatie over [het maken en beheren van een herbruikbare ML-omgeving](how-to-use-environments.md) voor training en gevolgtrekking.

### <a name="training-scripts"></a>Trainingsscripts

Als u een model wilt trainen, geeft u de map op die het trainingsscript en de bijbehorende bestanden bevat. U geeft ook een experimentnaam op, die wordt gebruikt om informatie op te slaan die tijdens de training wordt verzameld. Tijdens de training wordt de volledige map gekopieerd naar de trainingsomgeving (compute target) en wordt het script gestart dat is opgegeven door de runconfiguratie. Een momentopname van de map wordt ook opgeslagen onder het experiment in de werkruimte.

Zie [Zelfstudie: Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="estimators"></a>Schatters

Om modeltraining met populaire frameworks te vergemakkelijken, u met de schatterklasse eenvoudig hardloopconfiguraties bouwen. U een generieke [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainingsscripts in te dienen die gebruikmaken van elk leerkader dat u kiest (zoals scikit-learn).

Voor PyTorch-, TensorFlow- en Chainer-taken biedt Azure Machine Learning ook respectievelijk [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer-schatters](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) om het gebruik van deze frameworks te vereenvoudigen.

Raadpleeg voor meer informatie de volgende artikelen:

* [Train ML-modellen met schatters.](how-to-train-ml-models.md)
* [Train Pytorch deep learning-modellen op schaal met Azure Machine Learning.](how-to-train-pytorch.md)
* [Train en registreer TensorFlow-modellen op schaal met Azure Machine Learning](how-to-train-tensorflow.md).
* [Chainer-modellen op schaal trainen en registreren met Azure Machine Learning](how-to-train-chainer.md).

### <a name="endpoints"></a>Eindpunten

Een eindpunt is een instantiatie van uw model in een webservice die in de cloud kan worden gehost of een IoT-module voor geïntegreerde apparaatimplementaties.

#### <a name="web-service-endpoint"></a>Eindpunt van webservice

Bij het implementeren van een model als webservice kan het eindpunt worden geïmplementeerd op Azure Container Instances, Azure Kubernetes Service of FPGA's. U maakt de service op uit uw model, script en bijbehorende bestanden. Deze worden geplaatst in een basiscontainerafbeelding die de uitvoeringsomgeving voor het model bevat. De afbeelding heeft een HTTP-eindpunt met belastingsbalans dat scoringsaanvragen ontvangt die naar de webservice worden verzonden.

Azure helpt u uw webservice te controleren door telemetrie of modeltelemetrie van Application Insights te verzamelen, als u ervoor hebt gekozen deze functie in te schakelen. De telemetriegegevens zijn alleen voor u toegankelijk en worden opgeslagen in uw toepassingsinzichten en opslagaccountexemplaren.

Als u automatisch schalen hebt ingeschakeld, schaalt Azure uw implementatie automatisch.

Zie [Een afbeeldingsclassificatiemodel implementeren in Azure Container Instances](tutorial-deploy-models-with-aml.md)voor een voorbeeld van het implementeren van een model als webservice.

#### <a name="iot-module-endpoints"></a>Eindpunten van IoT-modules

Een geïmplementeerd IoT-moduleeindpunt is een Docker-container die uw model en bijbehorende script of toepassing en eventuele extra afhankelijkheden bevat. U implementeert deze modules met Azure IoT Edge op edge-apparaten.

Als u bewaking hebt ingeschakeld, verzamelt Azure telemetriegegevens van het model in de Azure IoT Edge-module. De telemetriegegevens zijn alleen voor u toegankelijk en worden opgeslagen in de instantie van uw opslagaccount.

Azure IoT Edge zorgt ervoor dat uw module wordt uitgevoerd en controleert het apparaat dat het host.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Rekeninstantie (voorbeeld)

Een **Azure Machine Learning-rekeninstantie** (voorheen Notebook VM) is een volledig beheerd cloudwerkstation dat meerdere hulpprogramma's en omgevingen bevat die zijn geïnstalleerd voor machine learning. Compute-exemplaren kunnen worden gebruikt als een rekendoel voor het trainen en inferencing taken. Voor grote taken zijn [Azure Machine Learning-computeclusters](how-to-set-up-training-targets.md#amlcompute) met multi-node scaling-mogelijkheden een betere keuze voor rekendoelen.

Meer informatie over [rekeninstanties](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Gegevenssets en datastores

**Azure Machine Learning Datasets** (preview) maken het eenvoudiger om toegang te krijgen tot en met uw gegevens te werken. Gegevenssets beheren gegevens in verschillende scenario's, zoals modeltraining en het maken van pijplijnen. Met de Azure Machine Learning SDK hebt u toegang tot onderliggende opslag, u gegevens verkennen en de levenscyclus van verschillende gegevenssetdefinities beheren.

Gegevenssets bieden methoden voor het werken met gegevens `from_delimited_files()` `to_pandas_dataframe()`in populaire indelingen, zoals het gebruik of .

Zie [Azure Machine Learning-gegevenssets maken en registreren](how-to-create-register-datasets.md)voor meer informatie.  Zie de [voorbeeldnotitieblokken voor](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets)meer voorbeelden met gegevenssets.

Een **datastore** is een opslagabstractie via een Azure-opslagaccount. De datastore kan een Azure blob-container of een Azure-bestandsshare gebruiken als back-endopslag. Elke werkruimte heeft een standaard gegevensarchief en u extra gegevensopslag registreren. Gebruik de Python SDK API of de Azure Machine Learning CLI om bestanden op te slaan en op te halen uit de datastore.

### <a name="compute-targets"></a>Rekendoelen

Met een [compute-doel](concept-compute-target.md) u de rekenbron opgeven waar u uw trainingsscript uitvoert of uw service-implementatie host. Deze locatie kan uw lokale machine zijn of een cloudgebaseerde compute resource.

Meer informatie over de [beschikbare rekendoelen voor training en implementatie](concept-compute-target.md).

### <a name="next-steps"></a>Volgende stappen

Zie het als nodig zijn om aan de slag te gaan met Azure Machine Learning:

* [Wat is Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)
* [Zelfstudie (deel 1): Een model trainen](tutorial-train-models-with-aml.md)
