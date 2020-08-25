---
title: Belangrijkste concepten van architectuur &
titleSuffix: Azure Machine Learning
description: Meer informatie over de architectuur, voor waarden en concepten van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750849"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Hoe Azure Machine Learning werkt: architectuur en concepten

Meer informatie over de architectuur en concepten voor Azure Machine Learning.

> [!NOTE]
> Hoewel in dit artikel voor waarden en concepten worden gedefinieerd die worden gebruikt door Azure Machine Learning, worden er geen termen en concepten voor het Azure-platform gedefinieerd. Zie de [Microsoft Azure verklarende woorden lijst](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)voor meer informatie over de terminologie van het Azure-platform.

## <a name="workspace"></a><a name="workspace"></a> Werk ruimte

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Azure Machine Learning architectuur":::

Een [machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor Azure machine learning.  De werk ruimte is de centrale locatie waar u het volgende kunt doen:
* Resources beheren die u gebruikt voor de training en implementatie van modellen, zoals [berekeningen](#compute-instance)
* Store-assets die u maakt wanneer u Azure Machine Learning gebruikt, zoals:
  * [Omgevingen](#environments)
  * [Wordt uitgevoerd](#runs)
  * [Pijplijnen](#ml-pipelines)
  * [Gegevenssets](#datasets-and-datastores)
  * [Modellen](#models)
  * [Eind punten](#endpoints)

Een werk ruimte bevat andere Azure-resources die worden gebruikt door de werk ruimte:

+ [Azure container Registry](https://azure.microsoft.com/services/container-registry/): registreert docker-containers die u tijdens de training gebruikt en wanneer u een model implementeert. Om de kosten te minimaliseren, wordt ACR **Lazy geladen** totdat implementatie installatie kopieën zijn gemaakt.
+ [Azure Storage account](https://azure.microsoft.com/services/storage/): wordt gebruikt als de standaard gegevens opslag voor de werk ruimte.  Jupyter-notebooks die worden gebruikt met uw Azure Machine Learning Reken instanties worden hier ook opgeslagen.
+ [Azure-toepassing Insights](https://azure.microsoft.com/services/application-insights/): slaat bewakings informatie over uw modellen op.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): slaat geheimen op die worden gebruikt door Compute-doelen en andere gevoelige informatie die nodig is voor de werk ruimte.

U kunt een werk ruimte delen met anderen.

## <a name="studio"></a>Studio

[Azure machine learning Studio](https://ml.azure.com) biedt een webweergave van alle artefacten in uw werk ruimte.  In deze Portal hebt u ook toegang tot de interactieve hulpprogram ma's die deel uitmaken van Azure Machine Learning:

+ [Azure machine learning Designer (preview)](concept-designer.md) om werk stroom stappen uit te voeren zonder code te schrijven
+ Webervaring voor [automatische machine learning](concept-automated-ml.md)
+ [Gegevens label projecten](how-to-create-labeling-projects.md) voor het maken, beheren en bewaken van projecten naar een label van uw gegevens

##  <a name="computes"></a>Berekeningen

<a name="compute-targets"></a> Een [Compute-doel](concept-compute-target.md) is de computer of set machines waarop u uw trainings script uitvoert of uw service-implementatie host. Deze locatie kan uw lokale computer of een externe Compute-resource zijn.

Azure Machine Learning introduceert twee volledig beheerde reken bronnen op basis van de cloud die zijn geconfigureerd voor machine learning taken:

* <a name="compute-instance"></a>**Reken instantie** ([Computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)): een reken instantie is een virtuele machine (VM) met meerdere hulpprogram ma's en omgevingen die zijn geïnstalleerd voor machine learning. Gebruik een reken instantie als uw ontwikkel werkstation om te beginnen met het uitvoeren van voorbeeld notitieblokken zonder dat Setup is vereist. Kan ook worden gebruikt als een reken doel voor trainings-en afdruk taken.
* **Reken clusters** ([Amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)): cluster van virtuele machines met schaal mogelijkheden voor meerdere knoop punten. Wordt automatisch geschaald wanneer een taak wordt verzonden. Beter geschikt voor reken doelen voor grote taken en productie. Gebruik als een trainings berekenings doel of voor een dev/test-implementatie.

Zie [trainings Compute-doelen](concept-compute-target.md#train)voor meer informatie over de doelen van de trainings compute.  Zie [implementatie doelen](concept-compute-target.md#deploy)voor meer informatie over Compute-doelen voor de implementatie.

## <a name="datasets-and-datastores"></a>Gegevens sets en gegevens opslag

Met [**Azure machine learning gegevens sets**](concept-data.md#datasets) kunt u eenvoudig toegang krijgen tot uw gegevens en ermee werken. Gegevens sets worden in verschillende scenario's beheerd, zoals model training en het maken van pijp lijnen. Met de Azure Machine Learning SDK hebt u toegang tot de onderliggende opslag, gegevens te verkennen en de levens cyclus van verschillende gegevensset-definities te beheren.

Gegevens sets bieden methoden voor het werken met in populaire indelingen, zoals het gebruik van `from_delimited_files()` of `to_pandas_dataframe()` .

Zie [Azure machine learning gegevens sets maken en registreren](how-to-create-register-datasets.md)voor meer informatie.  Raadpleeg de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)voor meer voor beelden met behulp van gegevens sets.

Een [**gegevens archief**](concept-data.md#datastores) is een opslag abstractie van een Azure-opslag account. Elke werk ruimte heeft een standaard gegevens opslag en u kunt aanvullende gegevens opslag registreren. Gebruik de python SDK API of de Azure Machine Learning CLI om bestanden op te slaan en op te halen uit de gegevens opslag. 

## <a name="models"></a>Modellen

Een model is de eenvoudigste is een stukje code dat een invoer maakt en uitvoer produceert. Het maken van een machine learning model bestaat uit het selecteren van een algoritme, het leveren van gegevens en het [afstemmen van Hyper parameters](how-to-tune-hyperparameters.md). Training is een iteratief proces dat een getraind model produceert dat het model dat u tijdens het trainings proces hebt geleerd.

Een model wordt gemaakt door een [uitvoering](#runs) van een [experiment](#experiments) in azure machine learning. U kunt ook een model gebruiken dat is getraind buiten Azure Machine Learning. Vervolgens [registreert u het model](#register-model) in de werk ruimte.

Azure Machine Learning is Framework neutraal. Wanneer u een model maakt, kunt u een populair machine learning Framework gebruiken, zoals Scikit-learn, XGBoost, PyTorch, tensor flow en Chainer.

Voor een voor beeld van een model trainen met Scikit-learn, Zie [zelf studie: een classificatie model voor een installatie kopie trainen met Azure machine learning](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a><a name="register-model"></a> Model register
[Werk ruimte](#workspace)  >  **Model register**

Met het **model register** kunt u alle modellen in uw Azure machine learning-werk ruimte bijhouden.

Modellen worden geïdentificeerd op naam en versie. Telkens wanneer u een model met dezelfde naam registreert als een bestaande, wordt ervan uitgegaan dat het een nieuwe versie is. De versie wordt verhoogd en het nieuwe model wordt geregistreerd onder dezelfde naam.

Wanneer u het model registreert, kunt u extra labels voor meta gegevens opgeven en vervolgens de tags gebruiken wanneer u naar modellen zoekt.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

U kunt een geregistreerd model dat wordt gebruikt door een actieve implementatie niet verwijderen.

Zie voor een voor beeld van het registreren van een model [een afbeeldings classificatie model trainen met Azure machine learning](tutorial-train-models-with-aml.md).


### <a name="environments"></a>Omgevingen

[Werk ruimte](#workspace)  >  **Omgevingen**

Een [omgeving](concept-environments.md) is de inkapseling van de omgeving waar de training of het scoren van uw machine learning model gebeurt. De omgeving bevat de Python-pakketten, omgevings variabelen en software-instellingen rond uw trainings-en Score scripts.

Voor code voorbeelden, zie de sectie omgevingen beheren van het [gebruik van omgevingen](how-to-use-environments.md#manage-environments).

### <a name="experiments"></a>Experimenten

[Werk ruimte](#workspace)  >  **Experimenten**

Een experiment is een groepering van veel uitvoeringen van een opgegeven script. Deze behoort altijd tot een werk ruimte. Wanneer u een uitvoering verzendt, geeft u een naam op voor het experiment. Informatie over de uitvoering is opgeslagen onder dat experiment. Als de naam niet bestaat wanneer u een experiment verzendt, wordt automatisch een nieuw experiment gemaakt.

Zie [zelf studie: uw eerste model trainen](tutorial-1st-experiment-sdk-train.md)voor een voor beeld van het gebruik van een experiment.

### <a name="runs"></a>Wordt uitgevoerd

[Werk ruimte](#workspace)  >  [Experimenten](#experiments)  >  **Uitvoeren**

Een uitvoering is één uitvoering van een trainings script. Een experiment bevat normaal gesp roken meerdere uitvoeringen.

Azure Machine Learning registreert alle uitvoeringen en slaat de volgende informatie op in het experiment:

* Meta gegevens over de uitvoering (tijds tempel, duur, enzovoort)
* Metrische gegevens die door het script worden geregistreerd
* Uitvoer bestanden die door het experiment worden verzameld of expliciet door u worden geüpload
* Een moment opname van de map die uw scripts bevat vóór de uitvoering

U produceert een uitvoering wanneer u een script voor het trainen van een model verzendt. Een run kan nul of meer onderliggende uitvoeringen hebben. De uitvoering op het hoogste niveau kan bijvoorbeeld twee onderliggende uitvoeringen hebben, waarvan elk een eigen onderliggend item kan hebben.

### <a name="run-configurations"></a>Configuraties uitvoeren

[Werk ruimte](#workspace)  >  [Experimenten](#experiments)  >  [Uitvoeren](#runs)  >  **Configuratie uitvoeren**

Een uitvoerings configuratie is een set instructies die definieert hoe een script moet worden uitgevoerd in een opgegeven Compute-doel. De configuratie bevat een breed scala aan gedrags definities, zoals of u een bestaande python-omgeving wilt gebruiken of een Conda-omgeving wilt gebruiken die is gebaseerd op een specificatie.

Een uitvoerings configuratie kan worden opgeslagen in een bestand in de map die uw trainings script bevat.   Of kan worden geconstrueerd als een in-memory-object en worden gebruikt voor het verzenden van een run.

Zie bijvoorbeeld configuraties [selecteren en een compute-doel gebruiken om uw model te trainen](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Schattingen

Om model training met populaire Frameworks mogelijk te maken, kunt u met de klasse Estimator eenvoudig uitvoer configuraties bouwen. U kunt een algemene [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainings scripts te verzenden die gebruikmaken van een door u gekozen trainings raamwerk (zoals scikit-leren).

Voor PyTorch-, tensor flow-en Chainer-taken biedt Azure Machine Learning ook de geraamde [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -schattingen om het gebruik van deze frameworks te vereenvoudigen.

Raadpleeg voor meer informatie de volgende artikelen:

* [Train ml-modellen met schattingen](how-to-train-ml-models.md).
* [Train Pytorche diepe Learning-modellen op schaal met Azure machine learning](how-to-train-pytorch.md).
* [Tensor flow-modellen trainen en registreren op schaal met Azure machine learning](how-to-train-tensorflow.md).
* [Keten modellen trainen en registreren op schaal met Azure machine learning](how-to-train-ml-models.md).

### <a name="snapshots"></a>Momentopnamen

[Werk ruimte](#workspace)  >  [Experimenten](#experiments)  >  [Uitvoeren](#runs)  >  **Moment opname**

Wanneer u een uitvoering verzendt, comprimeert Azure Machine Learning de map die het script bevat als een zip-bestand en verzendt het naar het Compute-doel. Het zip-bestand wordt vervolgens geëxtraheerd en het script wordt uitgevoerd. Azure Machine Learning slaat het zip-bestand ook als een moment opname op als onderdeel van de uitvoerings record. Iedereen met toegang tot de werk ruimte kan bladeren in een uitvoerings record en de moment opname downloaden.


### <a name="logging"></a>Logboekregistratie

Wanneer u uw oplossing ontwikkelt, gebruikt u de Azure Machine Learning python-SDK in uw python-script om wille keurige gegevens te registreren. Na de uitvoering voert u een query uit op de metrische gegevens om te bepalen of de uitvoering het model heeft geproduceerd dat u wilt implementeren.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-tracking en-integratie

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Dit werkt met uitvoeringen die zijn verzonden met behulp van een Estimator, ML-pijp lijn of script uitvoering. Het werkt ook voor uitvoeringen die zijn verzonden vanuit de SDK of Machine Learning CLI.

Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

## <a name="deployment"></a>Implementatie

U implementeert een [geregistreerd model](#register-model) als een service-eind punt. U hebt de volgende onderdelen nodig:

* **Omgeving**. Deze omgeving omvat de afhankelijkheden die nodig zijn voor het uitvoeren van uw model.
* **Score code**. Met dit script worden aanvragen geaccepteerd, worden de aanvragen met behulp van het model gescoord en worden de resultaten geretourneerd.
* **Configuratie**afleiding. De configuratie voor afwijzen specificeert de omgeving, het invoer script en andere onderdelen die nodig zijn om het model als een service uit te voeren.

Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over deze onderdelen.

### <a name="endpoints"></a>Eindpunten

[Werk ruimte](#workspace)  >  **Eind punten**

Een eind punt is een instantie van uw model in een webservice die kan worden gehost in de Cloud of een IoT-module voor geïntegreerde implementaties van apparaten.

#### <a name="web-service-endpoint"></a>Webservice-eind punt

Bij het implementeren van een model als een webservice kan het eind punt worden geïmplementeerd op Azure Container Instances, Azure Kubernetes service of Fpga's. U maakt de service vanuit uw model, script en de bijbehorende bestanden. Deze worden in een basis container installatie kopie geplaatst, die de uitvoerings omgeving voor het model bevat. De afbeelding heeft een HTTP-eind punt met gelijke taak verdeling dat Score aanvragen ontvangt die naar de webservice worden verzonden.

U kunt Application Insights telemetrie of model-telemetrie inschakelen om uw webservice te bewaken. De telemetriegegevens zijn alleen toegankelijk voor u.  Deze wordt opgeslagen in uw Application Insights-en opslag account-exemplaren.

Als u automatisch schalen hebt ingeschakeld, wordt uw implementatie automatisch door Azure geschaald.

Voor een voor beeld van het implementeren van een model als een webservice raadpleegt u [een installatie kopie classificatie model implementeren in azure container instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>IoT-module-eind punten

Een geïmplementeerd IoT-module-eind punt is een docker-container die uw model en het bijbehorende script of de gekoppelde toepassing en eventuele extra afhankelijkheden bevat. U implementeert deze modules met behulp van Azure IoT Edge op edge-apparaten.

Als u bewaking hebt ingeschakeld, verzamelt Azure telemetriegegevens van het model in de module Azure IoT Edge. De telemetriegegevens zijn alleen toegankelijk voor u en worden opgeslagen in uw opslag account-exemplaar.

Azure IoT Edge zorgt ervoor dat de module wordt uitgevoerd en controleert het apparaat waarop het wordt gehost.
. 
## <a name="automation"></a>Automatisering

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

De [Azure machine learning cli](reference-azure-machine-learning-cli.md) is een uitbrei ding van Azure CLI, een platformoverschrijdende opdracht regel interface voor het Azure-platform. Deze uitbrei ding bevat opdrachten om uw machine learning activiteiten te automatiseren.

### <a name="ml-pipelines"></a>ML-pijp lijnen

U gebruikt [machine learning pijp lijnen](concept-ml-pipelines.md) voor het maken en beheren van werk stromen die samen een machine learning fasen opdoen. Een pijp lijn kan bijvoorbeeld bestaan uit gegevens voorbereiding, model training, model implementatie en fase ring/scores. Elke fase kan meerdere stappen omvatten, die elk zonder toezicht kunnen worden uitgevoerd in verschillende reken doelen. 

U kunt de stappen van de pijp lijn herbruikbaresen en uitvoeren zonder de vorige stappen opnieuw uit te voeren als de uitvoer van deze stappen niet is gewijzigd. U kunt bijvoorbeeld een model opnieuw trainen zonder de stappen voor het voorbereiden van de kosten voor het opnieuw uitvoeren van gegevens als de gegevens niet zijn gewijzigd. Door pijp lijnen kunnen gegevens wetenschappers ook samen werken terwijl ze werken op verschillende gebieden van een machine learning werk stroom.

## <a name="interacting-with-machine-learning"></a>Interactie met machine learning

> [!IMPORTANT]
> De hulpprogram ma's die zijn gemarkeerd (preview) zijn momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

+  Communiceer met de service in een python-omgeving met de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Communiceer met de service in een wille keurige R-omgeving met de [Azure machine learning SDK voor R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (preview).
+ Gebruik [Azure machine learning Designer (preview)](concept-designer.md) om de werk stroom stappen uit te voeren zonder code te schrijven. (Een [Enter prise-werk ruimte](concept-workspace.md#upgrade)) is vereist voor het gebruik van Designer.)
+ Gebruik [Azure machine learning cli](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) voor Automation.
+ De [Many Models Solution Accelerator](https://aka.ms/many-models) (preview) heeft Azure Machine Learning als basis en stelt u in staat om honderden, of zelfs duizenden machine Learning-modellen, te trainen, te gebruiken en te beheren.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met Azure Machine Learning raadpleegt u:

* [Wat is Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)
* [Zelf studie (deel 1): een model trainen](tutorial-train-models-with-aml.md)
