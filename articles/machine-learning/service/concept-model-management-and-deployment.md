---
title: 'MLOps: ML model beheer'
titleSuffix: Azure Machine Learning
description: 'Meer informatie over model beheer met Azure Machine Learning (MLOps). Implementeer, beheer en bewaak uw modellen om ze continu te verbeteren. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: a56096ffa760bcddd6bd423d1262e57dc1db3e3c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973557"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: model beheer, implementatie en bewaking met Azure Machine Learning

In dit artikel vindt u informatie over het gebruik van Azure Machine Learning voor het beheren van de levens cyclus van uw modellen. Azure Machine Learning maakt gebruik van een MLOps-benadering (Machine Learning Operations). MLOps verbetert de kwaliteit en consistentie van uw machine learning oplossingen. 

Azure Machine Learning biedt de volgende MLOps mogelijkheden:

- **Maak reproduceer bare ml-pijp lijnen**. Met pijp lijnen kunt u Herhaal bare en herbruikbare stappen definiëren voor uw gegevens voorbereiding, training en Score processen.
- **Registreer, verpak en implementeer modellen vanaf elke locatie** en houd gekoppelde meta gegevens bij die vereist zijn voor het gebruik van het model.
- **Leg de governance-gegevens vast die nodig zijn voor het vastleggen van de end-to-end ml levenscyclus**, waaronder wie modellen publiceert, waarom wijzigingen worden aangebracht en wanneer modellen in productie zijn geïmplementeerd of gebruikt.
- **Meld u aan bij gebeurtenissen in de levens cyclus van de ml** , zoals het volt ooien van het experiment, model registratie, model implementatie en detectie van gegevens drift.
- **Bewaak ml-toepassingen voor operationele en ml-gerelateerde problemen**. Vergelijk model invoer tussen training en demijnen, verken specifieke metrische gegevens en geef bewaking en waarschuwingen op uw ML-infra structuur.
- **Automatiseer de end-to-end ml-levens cyclus met Azure machine learning en Azure DevOps** om vaak updates te kunnen uitvoeren, nieuwe modellen te testen en voortdurend nieuwe ml-modellen uit te vouwen naast uw andere toepassingen en services.

## <a name="create-reproducible-ml-pipelines"></a>Reproduceer bare ML-pijp lijnen maken

Gebruik ML-pijp lijnen van Azure Machine Learning om alle stappen in uw model trainings proces samen te voegen.

Een ML-pijp lijn kan stappen bevatten van gegevens voorbereiding voor het uitpakken van de functie voor het afstemmen van de model evaluatie. Zie [ml-pijp lijnen](concept-ml-pipelines.md)voor meer informatie.

## <a name="register-package-and-deploy-models-from-anywhere"></a>Modellen vanaf elke locatie registreren, inpakken en implementeren

### <a name="register-and-track-ml-models"></a>Modellen voor MILLILITERs registreren en volgen

Met model registratie kunt u uw modellen in de Azure-Cloud opslaan en versie, in uw werk ruimte. Het model register maakt het eenvoudig om uw getrainde modellen te organiseren en bij te houden.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

Geregistreerde modellen worden geïdentificeerd op naam en versie. Telkens wanneer die u een model met dezelfde naam als een bestaande resourcegroep registreren in het register wordt het versienummer verhoogd. Er kunnen extra meta gegevenslabels worden gegeven tijdens de registratie. Deze tags worden vervolgens gebruikt bij het zoeken naar een model. Azure Machine Learning ondersteunt elk model dat kan worden geladen met python 3.5.2 of hoger.

> [!TIP]
> U kunt ook modellen die zijn getraind buiten Azure Machine Learning registreren.

U kunt een geregistreerd model dat wordt gebruikt in een actieve implementatie, niet verwijderen.
Zie de sectie model registreren van [modellen implementeren](how-to-deploy-and-where.md#registermodel)voor meer informatie.

### <a name="package-and-debug-models"></a>Pakket-en fout opsporings modellen

Voordat u een model in productie implementeert, wordt het verpakt in een docker-installatie kopie. In de meeste gevallen gebeurt het maken van afbeeldingen automatisch op de achtergrond tijdens de implementatie. U kunt de installatie kopie hand matig opgeven.

Als u problemen met de implementatie ondervindt, kunt u in uw lokale ontwikkel omgeving implementeren voor probleem oplossing en fout opsporing.

Zie [modellen implementeren](how-to-deploy-and-where.md#registermodel) en [implementaties oplossen](how-to-troubleshoot-deployment.md)voor meer informatie.

### <a name="validate-and-profile-models"></a>Validatie-en profiel modellen

Azure Machine Learning kunt profile ring gebruiken om de ideale CPU-en geheugen instellingen te bepalen die moeten worden gebruikt bij het implementeren van uw model. Model validatie treedt op als onderdeel van dit proces en gebruikt gegevens die u voor het profilerings proces opgeeft.

### <a name="convert-and-optimize-models"></a>Modellen converteren en optimaliseren

Als u uw model converteert naar [Open Neural Network Exchange](https://onnx.ai) (ONNX), kunnen de prestaties worden verbeterd. Gemiddeld kan het converteren naar ONNX resulteren in een 2x-prestatie verhoging.

Voor meer informatie over ONNX met Azure Machine Learning raadpleegt u het artikel ' [ml-modellen maken en versnellen](concept-onnx.md) '.

### <a name="use-models"></a>Modellen gebruiken

Getrainde machine learning modellen worden geïmplementeerd als webservices in de Cloud of lokaal. U kunt modellen ook implementeren op Azure IoT Edge apparaten. Implementaties gebruiken CPU, GPU of veld-Programmeer bare poort matrices (FPGA) voor het afvoeren. U kunt ook modellen van Power BI gebruiken.

Wanneer u een model als een webservice of IoT Edge apparaat gebruikt, geeft u de volgende items op:

* De modellen die worden gebruikt voor het bepalen van de gegevens die naar de service/het apparaat worden verzonden.
* Een invoer script. Dit script accepteert aanvragen, maakt gebruik van de model (en) om de gegevens te beoordelen en retourneert een antwoord.
* Een Conda-omgevings bestand waarin de afhankelijkheden worden beschreven die vereist zijn voor de model (len) en het vermeldings script.
* Alle extra assets, zoals tekst, gegevens, enzovoort, die vereist zijn voor de model (len) en het vermeldings script.

U kunt ook de configuratie van het platform voor doel implementatie opgeven. Bijvoorbeeld, het type van de VM-familie, het beschik bare geheugen en het aantal kernen bij het implementeren naar Azure Kubernetes service.

Wanneer de installatie kopie is gemaakt, worden de onderdelen die zijn vereist door Azure Machine Learning ook toegevoegd. Bijvoorbeeld activa die nodig zijn om de webservice uit te voeren en te communiceren met IoT Edge.

#### <a name="batch-scoring"></a>Batchscore
Batch scores worden ondersteund via ML-pijp lijnen. Zie [batch voorspellingen op Big Data](how-to-run-batch-predictions.md)voor meer informatie.

#### <a name="real-time-web-services"></a>Real-time webservices

U kunt uw modellen in **Web Services** gebruiken met de volgende Compute-doelen:

* Azure Container Instance
* Azure Kubernetes Service
* Lokale ontwikkel omgeving

Als u het model als een webservice wilt implementeren, moet u de volgende items opgeven:

* Het model of de ensemble van modellen.
* Afhankelijkheden die zijn vereist voor het gebruik van het model. Bijvoorbeeld een script dat aanvragen accepteert en het model, Conda afhankelijkheden, enzovoort aanroept.
* Implementatie configuratie die beschrijft hoe en waar het model moet worden geïmplementeerd.

Zie [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

#### <a name="iot-edge-devices"></a>Apparaten IoT Edge

U kunt modellen met IoT-apparaten gebruiken via **Azure IOT Edge-modules**. IoT Edge-modules worden op het apparaat geïmplementeerd op een hardwareapparaat, waarmee de functie voor inschakeling of het model kan worden Gescored.

Zie [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

### <a name="analytics"></a>Analyse

Micro soft Power BI ondersteunt het gebruik van machine learning modellen voor gegevens analyse. Zie [Azure machine learning integration in Power bi (preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)voor meer informatie.

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Vastleggen van de governance-gegevens die nodig zijn voor het vastleggen van de end-to-end ML levenscyclus

Azure ML biedt u de mogelijkheid om de end-to-end-audittrail van al uw ML-assets bij te houden. Specifiek:

- Azure ML [integreert met git](how-to-set-up-training-targets.md#gitintegration) om informatie bij te houden over de opslag plaats/vertakking/het door voeren van uw code.
- Met de [Azure ml](how-to-create-register-datasets.md) -gegevens sets kunt u gegevens bijhouden, profiel en versie. 
- In de geschiedenis van Azure ML run wordt een moment opname van de code, gegevens en reken kracht opgeslagen waarmee een model kan worden getraind.
- In het REGI ster van Azure ML-model worden alle meta gegevens vastgelegd die aan uw model zijn gekoppeld (waarbij experiment wordt getraind, waar het wordt geïmplementeerd als de implementaties in orde zijn).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Meldingen, automatiseren en waarschuwen voor gebeurtenissen in de levens cyclus van ML
Azure ML publiceert belang rijke gebeurtenissen in azure EventGrid, die kunnen worden gebruikt voor het melden en automatiseren van gebeurtenissen in de levens cyclus van ML. Raadpleeg [dit document](how-to-use-event-grid.md)voor meer informatie.


## <a name="monitor-for-operational--ml-issues"></a>Controleren op problemen met operationele & ML

Met bewaking kunt u begrijpen welke gegevens worden verzonden naar uw model en welke voor spellingen worden geretourneerd.

Deze informatie helpt u inzicht te krijgen in het gebruik van uw model. De verzamelde invoer gegevens kunnen ook nuttig zijn bij het trainen van toekomstige versies van het model.

Zie [How to Enable model Data Collection](how-to-enable-data-collection.md)(Engelstalig) voor meer informatie.

## <a name="automate-the-ml-lifecycle"></a>De levens cyclus van ML automatiseren 

U kunt GitHub en Azure-pijp lijnen gebruiken om een doorlopend integratie proces te maken dat een model traint. Wanneer een gegevens wetenschapper een wijziging in de Git-opslag plaats voor een project controleert, wordt in een typisch scenario een trainings uitvoering gestart. De resultaten van de uitvoering kunnen vervolgens worden gecontroleerd om de prestatie kenmerken van het getrainde model te bekijken. U kunt ook een pijp lijn maken die het model als een webservice implementeert.

De [uitbrei ding Azure machine learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) maakt het gemakkelijker om te werken met Azure-pijp lijnen. Het biedt de volgende uitbrei dingen voor Azure-pijp lijnen:

* Hiermee schakelt u de werkruimte selectie in bij het definiëren van een service verbinding.
* Maakt het mogelijk om release pijplijnen te activeren door getrainde modellen die zijn gemaakt in een trainings pijplijn.

Voor meer informatie over het gebruik van Azure-pijp lijnen met Azure Machine Learning raadpleegt u de [continue integratie en implementatie van ml-modellen met Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning) -artikel en de [Azure machine learning MLOps](https://aka.ms/mlops) -opslag plaats.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het lezen en verkennen van de volgende resources:

+ [Hoe & implementeren van modellen](how-to-deploy-and-where.md) met Azure machine learning

+ [Zelf studie: een classificatie model voor een installatie kopie implementeren in ACI](tutorial-deploy-models-with-aml.md).

+ [End-to-end MLOps-voor beelden opslag plaats](https://github.com/microsoft/MLOps)

+ [CI/CD van ML modellen met Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning)

+ Clients maken die [een geïmplementeerd model gebruiken](how-to-consume-web-service.md)

+ [Machine learning at scale](/azure/architecture/data-guide/big-data/machine-learning-at-scale) (Machine learning op schaal)

+ [Azure AI-referentie architecturen & best practices rep](https://github.com/microsoft/AI)
