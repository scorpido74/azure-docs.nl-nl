---
title: 'MLOps: ML-modellen beheren, implementeren & bewaken'
titleSuffix: Azure Machine Learning service
description: 'Meer informatie over het gebruik van Azure Machine Learning service voor MLOps: uw modellen implementeren, beheren en bewaken om ze continu te verbeteren. U kunt de modellen die u met Azure Machine Learning-Service is getraind op uw lokale computer of uit andere bronnen kunt implementeren.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 2f3c3532637bef041ad1983b7573837dd0f29211
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860609"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Beheren, implementeren en bewaken van modellen met Azure Machine Learning-Service

In dit artikel vindt u informatie over het gebruik van Azure Machine Learning-service voor het beheren van de levens cyclus van uw modellen. Azure Machine Learning maakt gebruik van een MLOps-benadering (Machine Learning Operations), waarmee de kwaliteit en consistentie van uw machine learning oplossingen worden verbeterd. 

Azure Machine Learning-service biedt de volgende MLOps mogelijkheden:

- **ML projecten vanaf elke locatie implementeren**
- **Bewaak de bewakende toepassingen voor operationele en ml gerelateerde problemen** -vergelijkt model invoer tussen training en interferentie, verken specifieke metrische gegevens en geef bewaking en waarschuwingen op uw ml-infra structuur.
- **Leg de gegevens vast die nodig zijn voor het tot stand brengen van een end-to-end-audittrail van de milliliters levenscyclus**, waaronder het publiceren van modellen, waarom wijzigingen worden aangebracht en wanneer modellen zijn geïmplementeerd of gebruikt voor productie.
- **Automatiseer de end-to-end ml-levens cyclus met Azure machine learning en Azure DevOps** voor vaak bijgewerkte modellen, test nieuwe modellen en implementeer voortdurend nieuwe ml-modellen naast uw andere toepassingen en services.

Bekijk de volgende video voor meer informatie over de concepten achter MLOps en hoe deze van toepassing zijn op de Azure Machine Learning service.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>ML projecten vanaf elke locatie implementeren

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Uw trainings proces omzetten in een reproduceer bare pijp lijn
Gebruik ML-pijp lijnen van Azure Machine Learning om alle stappen in uw model trainings proces samen te voegen, van gegevens voorbereiding tot het uitpakken van de functie voor het afstemmen van de model evaluatie.

Zie [ml-pijp lijnen](concept-ml-pipelines.md)voor meer informatie.

### <a name="register-and-track-ml-models"></a>Modellen voor MILLILITERs registreren en volgen

Met model registratie kunt u uw modellen in de Azure-Cloud opslaan en versie, in uw werk ruimte. Het model register maakt het eenvoudig om uw getrainde modellen te organiseren en bij te houden.

> [!TIP]
> Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in uw Azure Machine Learning-werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.
 
Geregistreerde modellen worden geïdentificeerd op naam en versie. Telkens wanneer die u een model met dezelfde naam als een bestaande resourcegroep registreren in het register wordt het versienummer verhoogd. U kunt ook aanvullende metagegevenstags opgeven tijdens de registratie die kan worden gebruikt bij het zoeken naar voor modellen. De Azure Machine Learning-service ondersteunt elk model dat kan worden geladen met python 3.5.2 of hoger.

> [!TIP]
> U kunt ook modellen die zijn getraind buiten de Azure Machine Learning-service registreren.

U kunt een geregistreerd model dat wordt gebruikt in een actieve implementatie, niet verwijderen.
Zie de sectie model registreren van [modellen implementeren](how-to-deploy-and-where.md#registermodel)voor meer informatie.

### <a name="package-and-debug-models"></a>Pakket-en fout opsporings modellen

Voordat u een model in productie implementeert, wordt het verpakt in een docker-installatie kopie. In de meeste gevallen gebeurt het maken van afbeeldingen automatisch op de achtergrond tijdens de implementatie. Voor geavanceerde scenario's kunt u de installatie kopie hand matig opgeven.

Als u problemen met de implementatie ondervindt, kunt u in uw lokale ontwikkel omgeving implementeren voor probleem oplossing en fout opsporing.

Zie [modellen implementeren](how-to-deploy-and-where.md#registermodel) en [implementaties oplossen](how-to-troubleshoot-deployment.md)voor meer informatie.

### <a name="validate-and-profile-models"></a>Validatie-en profiel modellen

Azure Machine Learning-service kan profile ring gebruiken om de ideale CPU-en geheugen instellingen te bepalen die moeten worden gebruikt bij het implementeren van uw model. Model validatie treedt op als onderdeel van dit proces en gebruikt gegevens die u voor het profilerings proces opgeeft.

### <a name="convert-and-optimize-models"></a>Modellen converteren en optimaliseren

Als u uw model converteert naar [Open Neural Network Exchange](https://onnx.ai) (ONNX), kunnen de prestaties worden verbeterd. Gemiddeld kan het converteren naar ONNX resulteren in een 2x-prestatie verhoging.

Voor meer informatie over ONNX met Azure Machine Learning-service raadpleegt u het artikel over het [maken en versnellen van ml-modellen](concept-onnx.md) .

### <a name="use-models"></a>Modellen gebruiken

Getrainde machine learning modellen kunnen worden geïmplementeerd als webservices in de Cloud of lokaal op uw ontwikkel omgeving. U kunt modellen ook implementeren op Azure IoT Edge apparaten. Implementaties kunnen gebruikmaken van CPU, GPU of Field-Programmeer bare poort matrices (FPGA) voor het afvoeren. U kunt ook modellen van Power BI gebruiken.

Wanneer u een model als een webservice of IoT Edge apparaat gebruikt, geeft u de volgende items op:

* De modellen die worden gebruikt voor het bepalen van de gegevens die naar de service/het apparaat worden verzonden.
* Een invoer script. Dit script accepteert aanvragen, maakt gebruik van de model (en) om de gegevens te beoordelen en retourneert een antwoord.
* Een Conda-omgevings bestand waarin de afhankelijkheden worden beschreven die vereist zijn voor de model (len) en het vermeldings script.
* Alle extra assets, zoals tekst, gegevens, enzovoort, die vereist zijn voor de model (len) en het vermeldings script.

Deze assets worden verpakt in een docker-installatie kopie en geïmplementeerd als een webservice of IoT Edge module.

U kunt desgewenst de volgende para meters gebruiken voor het afstemmen van de implementatie:

* GPU inschakelen: Wordt gebruikt om GPU-ondersteuning in te scha kelen in de docker-installatie kopie. De installatie kopie moet worden gebruikt voor Microsoft Azure Services, zoals Azure Container Instances, Azure Kubernetes service, Azure Machine Learning Compute of Azure Virtual Machines.
* Extra stappen voor het docker-bestand: Een bestand dat aanvullende docker-stappen bevat die moeten worden uitgevoerd bij het maken van de docker-installatie kopie.
* Basis installatie kopie: Een aangepaste installatie kopie die moet worden gebruikt als basis installatie kopie. Als u geen aangepaste installatie kopie gebruikt, wordt de basis installatie kopie door de Azure Machine Learning-service verschaft.

U kunt ook de configuratie van het platform voor doel implementatie opgeven. Bijvoorbeeld, het type van de VM-familie, het beschik bare geheugen en het aantal kernen bij het implementeren naar Azure Kubernetes service.

Wanneer de installatie kopie wordt gemaakt, worden de onderdelen die vereist zijn voor de Azure Machine Learning-service ook toegevoegd. Bijvoorbeeld activa die nodig zijn om de webservice uit te voeren en te communiceren met IoT Edge.

> [!NOTE]
> U kunt de webserver-of IoT Edge onderdelen die in de docker-installatie kopie worden gebruikt, niet wijzigen of wijzigen. Azure Machine Learning-service gebruikt een webserver configuratie en IoT Edge onderdelen die door micro soft worden getest en ondersteund.

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

#### <a name="iot-edge-devices"></a>IoT Edge-apparaten

U kunt modellen met IoT-apparaten gebruiken via **Azure IOT Edge-modules**. IoT Edge-modules worden op het apparaat geïmplementeerd op een hardwareapparaat, waarmee de functie voor inschakeling of het model kan worden Gescored.

Zie [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

### <a name="analytics"></a>Analyse

Micro soft Power BI ondersteunt het gebruik van machine learning modellen voor gegevens analyse. Zie [Azure machine learning integration in Power bi (preview)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)voor meer informatie.


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Bewaak ML-toepassingen voor operationele en ML gerelateerde problemen

Met bewaking kunt u begrijpen welke gegevens worden verzonden naar uw model en welke voor spellingen worden geretourneerd.

Deze informatie helpt u inzicht te krijgen in het gebruik van uw model. De verzamelde invoer gegevens kunnen ook nuttig zijn bij het trainen van toekomstige versies van het model.

Zie [How to Enable model Data Collection](how-to-enable-data-collection.md)(Engelstalig) voor meer informatie.


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Een end-to-end-audittrail vastleggen voor de levens cyclus van ML

Azure ML biedt u de mogelijkheid om het end-to-end-audittrail van al uw ML-assets bij te houden. Specifiek:

- Azure ML [integreert met git](how-to-set-up-training-targets.md#gitintegration) om informatie bij te houden over de opslag plaats/vertakking/het door voeren van uw code.
- Met de [Azure ml](how-to-create-register-datasets.md) -gegevens sets kunt u gegevens bijhouden en versie.
- In de geschiedenis van Azure ML run wordt een moment opname van de code, gegevens en reken kracht opgeslagen waarmee een model kan worden getraind.
- In het REGI ster van Azure ML-model worden alle meta gegevens vastgelegd die aan uw model zijn gekoppeld (waarbij experiment wordt getraind, waar het wordt geïmplementeerd als de implementaties in orde zijn).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>De end-to-end ML levenscyclus automatiseren 

U kunt GitHub en Azure-pijp lijnen gebruiken om een doorlopend integratie proces te maken dat een model traint. Wanneer een gegevens wetenschapper een wijziging in de Git-opslag plaats voor een project controleert, wordt in een typisch scenario een trainings uitvoering gestart. De resultaten van de uitvoering kunnen vervolgens worden gecontroleerd om de prestatie kenmerken van het getrainde model te bekijken. U kunt ook een pijp lijn maken die het model als een webservice implementeert.

De [uitbrei ding Azure machine learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) maakt het gemakkelijker om te werken met Azure-pijp lijnen. Het biedt de volgende uitbrei dingen voor Azure-pijp lijnen:

* Hiermee schakelt u de werkruimte selectie in bij het definiëren van een service verbinding.
* Maakt het mogelijk om release pijplijnen te activeren door getrainde modellen die zijn gemaakt in een trainings pijplijn.

Voor meer informatie over het gebruik van Azure-pijp lijnen met Azure Machine Learning raadpleegt u de [continue integratie en implementatie van ml-modellen met Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning) -artikel en de [Azure machine learning service MLOps](https://aka.ms/mlops) -opslag plaats.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md) met de Azure Machine Learning-service. Zie [zelf studie voor een voor beeld van de implementatie: Implementeer een installatie kopie classificatie model in](tutorial-deploy-models-with-aml.md)Azure container instances.

Meer informatie over het maken [van een continue integratie en implementatie van ml-modellen met Azure-pijp lijnen](/azure/devops/pipelines/targets/azure-machine-learning). 

Meer informatie over het maken van client toepassingen en-services die [een model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md).
