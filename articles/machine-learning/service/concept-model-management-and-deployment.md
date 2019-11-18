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
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c77e36b5fbeb1ecacd42352c8c52cfd4b617d0e6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123598"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: model beheer, implementatie en bewaking met Azure Machine Learning

In dit artikel vindt u informatie over het gebruik van Azure Machine Learning voor het beheren van de levens cyclus van uw modellen. Azure Machine Learning maakt gebruik van een MLOps-benadering (Machine Learning Operations), waarmee de kwaliteit en consistentie van uw machine learning oplossingen worden verbeterd. 

Azure Machine Learning biedt de volgende MLOps mogelijkheden:

- **ML projecten vanaf elke locatie implementeren**
- **Bewaak de bewakende toepassingen voor operationele en ml gerelateerde problemen** -vergelijkt model invoer tussen training en interferentie, verken specifieke metrische gegevens en geef bewaking en waarschuwingen op uw ml-infra structuur.
- **Leg de gegevens vast die nodig zijn voor het tot stand brengen van een end-to-end-audittrail van de milliliters levenscyclus**, waaronder het publiceren van modellen, waarom wijzigingen worden aangebracht en wanneer modellen zijn geïmplementeerd of gebruikt voor productie.
- **Automatiseer de end-to-end ml-levens cyclus met Azure machine learning en Azure DevOps** voor vaak bijgewerkte modellen, test nieuwe modellen en implementeer voortdurend nieuwe ml-modellen naast uw andere toepassingen en services.

Bekijk de volgende video voor meer informatie over de concepten achter MLOps en hoe deze van toepassing zijn op Azure Machine Learning.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>ML projecten vanaf elke locatie implementeren

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Uw trainings proces omzetten in een reproduceer bare pijp lijn
Gebruik ML-pijp lijnen van Azure Machine Learning om alle stappen in uw model trainings proces samen te voegen, van gegevens voorbereiding tot het uitpakken van de functie voor het afstemmen van de model evaluatie.

Zie [ml-pijp lijnen](concept-ml-pipelines.md)voor meer informatie.

### <a name="register-and-track-ml-models"></a>Modellen voor MILLILITERs registreren en volgen

Met model registratie kunt u uw modellen in de Azure-Cloud opslaan en versie, in uw werk ruimte. Het model register maakt het eenvoudig om uw getrainde modellen te organiseren en bij te houden.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.
 
Geregistreerde modellen worden geïdentificeerd op naam en versie. Telkens wanneer die u een model met dezelfde naam als een bestaande resourcegroep registreren in het register wordt het versienummer verhoogd. U kunt ook aanvullende metagegevenstags opgeven tijdens de registratie die kan worden gebruikt bij het zoeken naar voor modellen. Azure Machine Learning ondersteunt elk model dat kan worden geladen met python 3.5.2 of hoger.

> [!TIP]
> U kunt ook modellen die zijn getraind buiten Azure Machine Learning registreren.

U kunt een geregistreerd model dat wordt gebruikt in een actieve implementatie, niet verwijderen.
Zie de sectie model registreren van [modellen implementeren](how-to-deploy-and-where.md#registermodel)voor meer informatie.

### <a name="package-and-debug-models"></a>Pakket-en fout opsporings modellen

Voordat u een model in productie implementeert, wordt het verpakt in een docker-installatie kopie. In de meeste gevallen gebeurt het maken van afbeeldingen automatisch op de achtergrond tijdens de implementatie. Voor geavanceerde scenario's kunt u de installatie kopie hand matig opgeven.

Als u problemen met de implementatie ondervindt, kunt u in uw lokale ontwikkel omgeving implementeren voor probleem oplossing en fout opsporing.

Zie [modellen implementeren](how-to-deploy-and-where.md#registermodel) en [implementaties oplossen](how-to-troubleshoot-deployment.md)voor meer informatie.

### <a name="validate-and-profile-models"></a>Validatie-en profiel modellen

Azure Machine Learning kunt profile ring gebruiken om de ideale CPU-en geheugen instellingen te bepalen die moeten worden gebruikt bij het implementeren van uw model. Model validatie treedt op als onderdeel van dit proces en gebruikt gegevens die u voor het profilerings proces opgeeft.

### <a name="convert-and-optimize-models"></a>Modellen converteren en optimaliseren

Als u uw model converteert naar [Open Neural Network Exchange](https://onnx.ai) (ONNX), kunnen de prestaties worden verbeterd. Gemiddeld kan het converteren naar ONNX resulteren in een 2x-prestatie verhoging.

Voor meer informatie over ONNX met Azure Machine Learning raadpleegt u het artikel ' [ml-modellen maken en versnellen](concept-onnx.md) '.

### <a name="use-models"></a>Modellen gebruiken

Getrainde machine learning modellen kunnen worden geïmplementeerd als webservices in de Cloud of lokaal op uw ontwikkel omgeving. U kunt modellen ook implementeren op Azure IoT Edge apparaten. Implementaties kunnen gebruikmaken van CPU, GPU of Field-Programmeer bare poort matrices (FPGA) voor het afvoeren. U kunt ook modellen van Power BI gebruiken.

Wanneer u een model als een webservice of IoT Edge apparaat gebruikt, geeft u de volgende items op:

* De modellen die worden gebruikt voor het bepalen van de gegevens die naar de service/het apparaat worden verzonden.
* Een invoer script. Dit script accepteert aanvragen, maakt gebruik van de model (en) om de gegevens te beoordelen en retourneert een antwoord.
* Een Conda-omgevings bestand waarin de afhankelijkheden worden beschreven die vereist zijn voor de model (len) en het vermeldings script.
* Alle extra assets, zoals tekst, gegevens, enzovoort, die vereist zijn voor de model (len) en het vermeldings script.

Deze assets worden verpakt in een docker-installatie kopie en geïmplementeerd als een webservice of IoT Edge module.

U kunt desgewenst de volgende para meters gebruiken voor het afstemmen van de implementatie:

* Enable GPU: wordt gebruikt om GPU-ondersteuning in te scha kelen in de docker-installatie kopie. De installatie kopie moet worden gebruikt voor Microsoft Azure Services, zoals Azure Container Instances, Azure Kubernetes service, Azure Machine Learning Compute of Azure Virtual Machines.
* Extra stappen voor het docker-bestand: een bestand dat extra docker-stappen bevat die moeten worden uitgevoerd bij het maken van de docker-installatie kopie.
* Basis installatie kopie: een aangepaste installatie kopie die moet worden gebruikt als basis installatie kopie. Als u geen aangepaste installatie kopie gebruikt, wordt de basis installatie kopie verschaft door Azure Machine Learning.

U kunt ook de configuratie van het platform voor doel implementatie opgeven. Bijvoorbeeld, het type van de VM-familie, het beschik bare geheugen en het aantal kernen bij het implementeren naar Azure Kubernetes service.

Wanneer de installatie kopie is gemaakt, worden de onderdelen die zijn vereist door Azure Machine Learning ook toegevoegd. Bijvoorbeeld activa die nodig zijn om de webservice uit te voeren en te communiceren met IoT Edge.

> [!NOTE]
> U kunt de webserver-of IoT Edge onderdelen die in de docker-installatie kopie worden gebruikt, niet wijzigen of wijzigen. Azure Machine Learning gebruikt een webserver configuratie en IoT Edge onderdelen die door micro soft worden getest en ondersteund.

#### <a name="web-service"></a>Webservice

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


## <a name="monitor-for-operational--ml-issues"></a>Controleren op problemen met operationele & ML

Met bewaking kunt u begrijpen welke gegevens worden verzonden naar uw model en welke voor spellingen worden geretourneerd.

Deze informatie helpt u inzicht te krijgen in het gebruik van uw model. De verzamelde invoer gegevens kunnen ook nuttig zijn bij het trainen van toekomstige versies van het model.

Zie [How to Enable model Data Collection](how-to-enable-data-collection.md)(Engelstalig) voor meer informatie.


## <a name="audit-trail-of-the-ml-lifecycle"></a>Audittrail van de levens cyclus van ML

Azure ML biedt u de mogelijkheid om het end-to-end-audittrail van al uw ML-assets bij te houden. Met name:

- Azure ML [integreert met git](how-to-set-up-training-targets.md#gitintegration) om informatie bij te houden over de opslag plaats/vertakking/het door voeren van uw code.
- Met de [Azure ml](how-to-create-register-datasets.md) -gegevens sets kunt u gegevens bijhouden en versie.
- In de geschiedenis van Azure ML run wordt een moment opname van de code, gegevens en reken kracht opgeslagen waarmee een model kan worden getraind.
- In het REGI ster van Azure ML-model worden alle meta gegevens vastgelegd die aan uw model zijn gekoppeld (waarbij experiment wordt getraind, waar het wordt geïmplementeerd als de implementaties in orde zijn).

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

+ [Machine learning at scale](/architecture/data-guide/big-data/machine-learning-at-scale) (Machine learning op schaal)

+ [Azure AI-referentie architecturen & best practices rep](https://github.com/microsoft/AI)
