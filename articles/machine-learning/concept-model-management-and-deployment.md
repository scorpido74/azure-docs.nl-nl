---
title: 'MLOps: ML-modelbeheer'
titleSuffix: Azure Machine Learning
description: 'Meer informatie over modelbeheer met Azure Machine Learning (MLOps). Implementeer, beheer en monitor uw modellen om ze continu te verbeteren. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 7857d11c625911cd1b49dfcf0e0d612fc6a3871e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314299"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: modelbeheer, implementatie en bewaking met Azure Machine Learning

In dit artikel vindt u meer informatie over het gebruik van Azure Machine Learning om de levenscyclus van uw modellen te beheren. Azure Machine Learning maakt gebruik van een Machine Learning Operations (MLOps) benadering. MLOps verbetert de kwaliteit en consistentie van uw machine learning-oplossingen. 

## <a name="what-is-mlops"></a>Wat is MLOps?

Machine Learning Operations (MLOps) is gebaseerd op [DevOps](https://azure.microsoft.com/overview/what-is-devops/) principes en praktijken die de efficiëntie van workflows verhogen. Bijvoorbeeld continue integratie, levering en implementatie. MLOps past deze principes toe op het machine learning-proces, met als doel:

* Sneller experimenteren en ontwikkelen van modellen
* Snellere implementatie van modellen in productie
* Kwaliteitscontrole

Azure Machine Learning biedt de volgende MLOps-mogelijkheden:

- **Maak reproduceerbare ML-pijplijnen.** Met Machine Learning-pijplijnen u herhaalbare en herbruikbare stappen definiëren voor uw gegevensvoorbereidings-, trainings- en scoringsprocessen.
- **Maak herbruikbare softwareomgevingen** voor het trainen en implementeren van modellen.
- **Registreer, verpak en implementeer modellen vanaf elke locatie.** U ook de bijbehorende metagegevens bijhouden die nodig zijn om het model te gebruiken.
- **Leg de governancegegevens vast voor de end-to-end ML-levenscyclus.** De geregistreerde informatie kan bestaan uit wie modellen publiceert, waarom wijzigingen zijn aangebracht en wanneer modellen zijn geïmplementeerd of gebruikt in de productie.
- **Melding en waarschuwing over gebeurtenissen in de ML-levenscyclus**. Bijvoorbeeld het voltooien van experimenten, modelregistratie, modelimplementatie en detectie van gegevensdrift.
- **Monitor ML-toepassingen op operationele en ML-gerelateerde problemen.** Vergelijk modelingangen tussen training en gevolgtrekking, verken modelspecifieke statistieken en geef monitoring en waarschuwingen op uw ML-infrastructuur.
- **Automatiseer de end-to-end ML-levenscyclus met Azure Machine Learning en Azure Pipelines.** Met behulp van pijplijnen u modellen vaak bijwerken, nieuwe modellen testen en voortdurend nieuwe ML-modellen uitrollen naast uw andere toepassingen en services.

## <a name="create-reproducible-ml-pipelines"></a>Reproduceerbare ML-pijplijnen maken

Gebruik ML-pijplijnen van Azure Machine Learning om alle stappen die betrokken zijn bij uw modeltrainingsproces samen te voegen.

Een ML-pijplijn kan stappen bevatten van gegevensvoorbereiding tot functieextractie tot hyperparametertuning tot modelevaluatie. Zie [ML-pijplijnen](concept-ml-pipelines.md)voor meer informatie.

Als u de [Designer](concept-designer.md) gebruikt om uw ML-pijplijnen te maken, u op elk gewenst moment klikken op de **...** rechtsboven op de designerpagina en vervolgens **Kloon**selecteren. Als u uw pijplijn kloont, u uw pijplijnontwerp herhalen zonder uw oude versies te verliezen.  

## <a name="create-reusable-software-environments"></a>Maak herbruikbare softwareomgevingen

Met Azure Machine Learning-omgevingen u de softwareafhankelijkheden van uw projecten volgen en reproduceren terwijl deze evolueren. Met omgevingen u ervoor zorgen dat builds reproduceerbaar zijn zonder handmatige softwareconfiguraties.

Omgevingen beschrijven de pip- en Conda-afhankelijkheden voor uw projecten en kunnen worden gebruikt voor zowel training als implementatie van modellen. Zie [Wat zijn Azure Machine Learning-omgevingen](concept-environments.md)voor meer informatie.

## <a name="register-package-and-deploy-models-from-anywhere"></a>Modellen overal registreren, verpakken en implementeren

### <a name="register-and-track-ml-models"></a>ML-modellen registreren en bijhouden

Met modelregistratie u uw modellen opslaan en versien in de Azure-cloud, in uw werkruimte. Het modelregister maakt het eenvoudig om uw getrainde modellen te organiseren en bij te houden.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die deel uitmaken van uw model. Als u bijvoorbeeld een model hebt dat in meerdere bestanden is opgeslagen, u deze als één model registreren in uw Azure Machine Learning-werkruimte. Na registratie u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

Geregistreerde modellen worden geïdentificeerd met naam en versie. Telkens wanneer u een model registreert met dezelfde naam als een bestaand model, wordt de versie in het register verhoogd. Extra metadata tags kunnen worden verstrekt tijdens de registratie. Deze tags worden vervolgens gebruikt bij het zoeken naar een model. Azure Machine Learning ondersteunt elk model dat kan worden geladen met Python 3.5.2 of hoger.

> [!TIP]
> U ook modellen registreren die buiten Azure Machine Learning zijn getraind.

U een geregistreerd model dat wordt gebruikt in een actieve implementatie niet verwijderen.
Zie voor meer informatie het gedeelte registermodel van [Deploy-modellen](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Profielmodellen

Azure Machine Learning kan u helpen inzicht te krijgen in de CPU- en geheugenvereisten van de service die wordt gemaakt wanneer u uw model implementeert. Profilering test de service die uw model uitvoert en retourneert informatie zoals het CPU-gebruik, geheugengebruik en reactielatentie. Het biedt ook een CPU en geheugen aanbeveling op basis van het gebruik van de bron.
Zie het profielgedeelte van [Deploy-modellen voor](how-to-deploy-and-where.md#profilemodel)meer informatie .

### <a name="package-and-debug-models"></a>Pakket- en foutopsporingsmodellen

Voordat u een model in productie neemt, wordt het verpakt in een Docker-afbeelding. In de meeste gevallen vindt het maken van afbeeldingen automatisch plaats op de achtergrond tijdens de implementatie. U de afbeelding handmatig opgeven.

Als u problemen ondervindt met de implementatie, u deze implementeren op uw lokale ontwikkelomgeving voor het oplossen en opsporen van fouten.

Zie [Implementaties implementeren en](how-to-deploy-and-where.md#registermodel) [probleemoplossing voor](how-to-troubleshoot-deployment.md)meer informatie.

### <a name="convert-and-optimize-models"></a>Modellen converteren en optimaliseren

Het converteren van uw model naar [Open Neural Network Exchange](https://onnx.ai) (ONNX) kan de prestaties verbeteren. Gemiddeld kan het omzetten naar ONNX een 2x prestatieverhoging opleveren.

Zie het artikel [ML-modellen maken en versnellen](concept-onnx.md) voor meer informatie over ONNX met Azure Machine Learning.

### <a name="use-models"></a>Modellen gebruiken

Getrainde machine learning-modellen worden geïmplementeerd als webservices in de cloud of lokaal. U ook modellen implementeren op Azure IoT Edge-apparaten. Implementaties gebruiken CPU, GPU of field-programmeerbare gate arrays (FPGA) voor inferencing. U ook modellen van Power BI gebruiken.

Wanneer u een model gebruikt als webservice of IoT Edge-apparaat, levert u de volgende items:

* Het model(en) dat wordt gebruikt om gegevens te scoren die naar de service/apparaat zijn verzonden.
* Een invoerscript. Dit script accepteert aanvragen, gebruikt het model(en) om de gegevens te scoren en retourneert een antwoord.
* Een Azure Machine Learning-omgeving die de pip- en Conda-afhankelijkheden beschrijft die vereist zijn door het model(en) en het invoerscript.
* Eventuele extra elementen zoals tekst, gegevens, enz.

U zorgt ook voor de configuratie van het doelimplementatieplatform. Bijvoorbeeld het type VM-familie, het beschikbare geheugen en het aantal cores bij het implementeren naar Azure Kubernetes Service.

Wanneer de afbeelding wordt gemaakt, worden ook onderdelen toegevoegd die vereist zijn door Azure Machine Learning. Er zijn bijvoorbeeld assets nodig om de webservice uit te voeren en te communiceren met IoT Edge.

#### <a name="batch-scoring"></a>Batchscore
Batchscores worden ondersteund via ML-pijplijnen. Zie [Batchvoorspellingen over big data voor](how-to-use-parallel-run-step.md)meer informatie.

#### <a name="real-time-web-services"></a>Realtime webservices

U uw modellen gebruiken in **webservices** met de volgende rekendoelen:

* Azure Container Instance
* Azure Kubernetes Service
* Lokale ontwikkelingsomgeving

Als u het model als webservice wilt implementeren, moet u de volgende items opgeven:

* Het model of ensemble van modellen.
* Afhankelijkheden die nodig zijn om het model te gebruiken. Bijvoorbeeld een script dat aanvragen accepteert en het model, conda-afhankelijkheden, enz.
* Implementatieconfiguratie die beschrijft hoe en waar het model moet worden geïmplementeerd.

Zie [Modellen implementeren voor](how-to-deploy-and-where.md)meer informatie.

#### <a name="controlled-rollout"></a>Gecontroleerde implementatie

Wanneer u wordt geïmplementeerd in Azure Kubernetes Service, u gecontroleerde implementatie gebruiken om de volgende scenario's in te schakelen:

* Meerdere versies van een eindpunt voor een implementatie maken
* Voer A/B-tests uit door verkeer naar verschillende versies van het eindpunt te routeren.
* Schakel tussen eindpuntversies door het verkeerspercentage in eindpuntconfiguratie bij te werken.

Zie [Gecontroleerde uitrol van ML-modellen](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)voor meer informatie.

#### <a name="iot-edge-devices"></a>IoT Edge-apparaten

U modellen met IoT-apparaten gebruiken via **Azure IoT Edge-modules.** IoT Edge-modules worden geïmplementeerd op een hardwareapparaat, waardoor gevolgtrekking of modelscore op het apparaat mogelijk is.

Zie [Modellen implementeren voor](how-to-deploy-and-where.md)meer informatie.

### <a name="analytics"></a>Analyse

Microsoft Power BI ondersteunt het gebruik van machine learning-modellen voor gegevensanalyse. Zie [Azure Machine Learning-integratie in Power BI (voorbeeld) voor](https://docs.microsoft.com/power-bi/service-machine-learning-integration)meer informatie.

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Leg de governancegegevens vast die nodig zijn voor het vastleggen van de end-to-end ML-levenscyclus

Azure ML biedt u de mogelijkheid om het end-to-end controlespoor van al uw ML-assets bij te houden met behulp van metagegevens.

- Azure ML [integreert met Git](how-to-set-up-training-targets.md#gitintegration) om informatie bij te houden over welke repository / branch / commit uw code vandaan komt.
- [Azure ML-gegevenssets](how-to-create-register-datasets.md) helpen u bij het bijhouden, profileren en versiegegevens.
- [Met de interpreteerbaarheid](how-to-machine-learning-interpretability.md) u uw modellen uitleggen, voldoen aan de naleving van de regelgeving en begrijpen hoe modellen tot een resultaat voor bepaalde input komen.
- Azure ML Run-geschiedenis slaat een momentopname op van de code, gegevens en gegevens die worden gebruikt om een model te trainen.
- Het Azure ML-modelregister legt alle metagegevens vast die aan uw model zijn gekoppeld (welk experiment het heeft getraind, waar het wordt geïmplementeerd, als de implementaties in orde zijn).
- [Met integratie met Azure Event Grid](concept-event-grid-integration.md) u reageren op gebeurtenissen in de ML-levenscyclus. Modelregistratie, implementatie, datadrift en trainingsgebeurtenissen (run).For example, model registration, deployment, data drift, and training (run) events.

> [!TIP]
> Hoewel bepaalde informatie over modellen en gegevenssets automatisch wordt vastgelegd, u aanvullende informatie toevoegen met behulp van __tags.__ Wanneer u op zoek bent naar geregistreerde modellen en gegevenssets in uw werkruimte, u tags als filter gebruiken.
>
> Het koppelen van een gegevensset aan een geregistreerd model is een optionele stap. Zie [de](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py) referentie modelklasse voor informatie over het verwijzen naar een gegevensset bij het registreren van een gegevensset.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Meldingen, automatiseren en waarschuwen voor gebeurtenissen in de ML-levenscyclus
Azure ML publiceert belangrijke gebeurtenissen op Azure EventGrid, die kunnen worden gebruikt om gebeurtenissen in de ML-levenscyclus te melden en te automatiseren. Zie [dit document](how-to-use-event-grid.md)voor meer informatie.


## <a name="monitor-for-operational--ml-issues"></a>Monitor voor operationele & ML-problemen

Met monitoring u begrijpen welke gegevens naar uw model worden verzonden en welke voorspellingen deze retourneren.

Deze informatie helpt u te begrijpen hoe uw model wordt gebruikt. De verzamelde invoergegevens kunnen ook nuttig zijn bij het trainen van toekomstige versies van het model.

Zie [Modelgegevensverzameling inschakelen](how-to-enable-data-collection.md)voor meer informatie.

## <a name="retrain-your-model-on-new-data"></a>Uw model omscholen op nieuwe gegevens

Vaak wilt u uw model valideren, bijwerken of zelfs helemaal opnieuw trainen, terwijl u nieuwe informatie ontvangt. Soms is het ontvangen van nieuwe gegevens een verwacht onderdeel van het domein. Andere keren, zoals besproken in [Detect data drift (preview) op datasets,](how-to-monitor-datasets.md)kunnen modelprestaties verslechteren in het licht van zaken als wijzigingen in een bepaalde sensor, wijzigingen in natuurlijke gegevens, zoals seizoenseffecten of functies die verschuiven in hun relatie tot andere functies. 

Er is geen universeel antwoord op "Hoe weet ik of ik me moet omscholen?" maar Azure ML-gebeurtenis- en bewakingstools die eerder zijn besproken, zijn goede uitgangspunten voor automatisering. Zodra u hebt besloten om te scholen, moet u: 

- Uw gegevens vooraf verwerken met een herhaalbaar, geautomatiseerd proces
- Train je nieuwe model
- Vergelijk de output van uw nieuwe model met die van uw oude model
- Vooraf gedefinieerde criteria gebruiken om te kiezen of u uw oude model wilt vervangen 

Een thema van de bovenstaande stappen is dat uw omscholing moet worden geautomatiseerd, niet ad hoc. [Azure Machine Learning-pijplijnen](concept-ml-pipelines.md) zijn een goed antwoord voor het maken van werkstromen met betrekking tot gegevensvoorbereiding, training, validatie en implementatie. Lees [Modellen omscholen met Azure Machine Learning designer (preview)](how-to-retrain-designer.md) om te zien hoe pijplijnen en de Azure Machine Learning-ontwerper passen in een omscholingsscenario. 

## <a name="automate-the-ml-lifecycle"></a>Automatiseer de ML-levenscyclus 

U GitHub en Azure Pipelines gebruiken om een continu integratieproces te maken dat een model traint. In een typisch scenario start de Azure Pipeline een trainingsrun wanneer een gegevenswetenschapper een wijziging in de Git-repo voor een project controleert. De resultaten van de run kunnen vervolgens worden geïnspecteerd om de prestatiekenmerken van het getrainde model te zien. U ook een pijplijn maken die het model als webservice implementeert.

Met de [Azure Machine Learning-extensie](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) u eenvoudiger werken met Azure Pipelines. Het biedt de volgende verbeteringen aan Azure Pipelines:

* Hiermee u werkruimteselectie selecteren bij het definiëren van een serviceverbinding.
* Hiermee kunnen releasepijplijnen worden geactiveerd door getrainde modellen die zijn gemaakt in een trainingspijplijn.

Zie de volgende koppelingen voor meer informatie over het gebruik van Azure Pipelines met Azure Machine Learning:

* [Continue integratie en implementatie van ML-modellen met Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning MLOps](https://aka.ms/mlops) repository.
* [Azure Machine Learning MLOpsPython-repository.](https://github.com/Microsoft/MLOpspython)

U Azure Data Factory ook gebruiken om een pijplijn voor het innemen van gegevens te maken die gegevens voorbereidt op gebruik met training. Zie [Pijplijn gegevens inname voor](how-to-cicd-data-ingestion.md)meer informatie .

## <a name="next-steps"></a>Volgende stappen

Meer informatie door de volgende bronnen te lezen en te verkennen:

+ [Hoe & waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md) met Azure Machine Learning

+ [Zelfstudie: Een afbeeldingsclassificatiemodel implementeren in ACI](tutorial-deploy-models-with-aml.md).

+ [End-to-end MLOps voorbeelden repo](https://github.com/microsoft/MLOps)

+ [CI/CD van ML-modellen met Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Clients maken die [een geïmplementeerd model verbruiken](how-to-consume-web-service.md)

+ [Machine learning at scale](/azure/architecture/data-guide/big-data/machine-learning-at-scale) (Machine learning op schaal)

+ [Azure AI-referentiearchitecturen & best practices rep](https://github.com/microsoft/AI)
