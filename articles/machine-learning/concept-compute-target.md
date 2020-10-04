---
title: Wat zijn reken doelen?
titleSuffix: Azure Machine Learning
description: Bepaal waar u uw model wilt trainen of implementeren met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: ca23bb49a3592dcc139bcc04875f3867018e158d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707728"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Wat zijn Compute-doelen in Azure Machine Learning? 

Een **Compute-doel** is een aangewezen reken resource/omgeving waar u uw trainings script uitvoert of uw service-implementatie host. Deze locatie kan uw lokale machine of een cloud-gebaseerde reken resource zijn. Met Compute-doelen kunt u op een later tijdstip uw berekenings omgeving wijzigen zonder dat u de code hoeft te wijzigen.  

In een typische levens cyclus voor model ontwikkeling kunt u het volgende doen:
1. Begin met het ontwikkelen en experimenteren van een kleine hoeveelheid gegevens. In deze fase raden wij uw lokale omgeving (lokale computer of Cloud-VM) aan als uw reken doel. 
2. Schaal omhoog naar grotere gegevens of voer gedistribueerde training uit met behulp van een van deze [trainings Compute-doelen](#train).  
3. Zodra uw model klaar is, implementeert u het in een webhost of IoT-apparaat met een van deze doelen voor de [implementatie](#deploy).

De reken resources die u voor uw reken doelen gebruikt, worden aan een [werk ruimte](concept-workspace.md)gekoppeld. Andere reken bronnen dan de lokale computer worden gedeeld door gebruikers van de werk ruimte.

## <a name="training-compute-targets"></a><a name="train"></a> Doelen van de trainings compute
Azure Machine Learning heeft verschillende ondersteuning voor verschillende Compute-doelen. Een typische levens cyclus voor model ontwikkeling begint met het ontwikkelen/experimenteren van een kleine hoeveelheid gegevens. In deze fase raden we u aan om een lokale omgeving te gebruiken. Bijvoorbeeld uw lokale computer of een VM op basis van de Cloud. Wanneer u uw training op grotere gegevens sets schaalt of gedistribueerde trainingen uitvoert, raden we u aan Azure Machine Learning Compute te gebruiken voor het maken van een cluster met één of meerdere knoop punten dat automatisch wordt geschaald wanneer u een uitvoering verzendt. U kunt ook uw eigen reken resource koppelen, maar de ondersteuning voor verschillende scenario's kan variëren zoals hieronder wordt beschreven:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Meer informatie over het [verzenden van een trainings uitvoering naar een compute-doel](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Reken doelen voor demijnen

De volgende reken bronnen kunnen worden gebruikt voor het hosten van uw model implementatie.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Bij het uitvoeren van een dematiging maakt Azure Machine Learning een docker-container die het model host en de bijbehorende resources die nodig zijn om het te gebruiken. Deze container wordt vervolgens gebruikt in een van de volgende implementatie scenario's:

* Als een __webservice__ die wordt gebruikt voor realtime-interferentie. Implementaties van webservices gebruiken een van de volgende Compute-doelen:

    * [Lokale computer](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning-rekeninstantie](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Services](how-to-create-attach-kubernetes.md)
    * Azure Functions (preview-versie). Implementatie naar Azure Functions is alleen afhankelijk van Azure Machine Learning voor het bouwen van de docker-container. Van daaruit wordt geïmplementeerd met behulp van Azure Functions. Zie [een machine learning model implementeren op Azure functions (preview)](how-to-deploy-functions.md)voor meer informatie.

* Als een __batch-interferentie__ -eind punt dat wordt gebruikt om periodiek batches gegevens te verwerken. Voor batch-interferenties wordt [Azure machine learning Compute-Cluster](how-to-create-attach-compute-cluster.md)gebruikt.

* Naar een __IOT-apparaat__ (preview-versie). Implementatie naar een IoT-apparaat is alleen afhankelijk van Azure Machine Learning voor het bouwen van de docker-container. Van daaruit wordt geïmplementeerd met behulp van Azure IoT Edge. Zie [Deploy as a IOT Edge module (preview)](/azure/iot-edge/tutorial-deploy-machine-learning)voor meer informatie.

Meer informatie over [waar en hoe u uw model implementeert in een compute-doel](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning Compute (beheerd)

Een beheerde Compute-resource wordt gemaakt en beheerd door Azure Machine Learning. Deze berekening is geoptimaliseerd voor machine learning workloads. Azure Machine Learning compute-clusters en [reken instanties](concept-compute-instance.md) zijn de enige beheerde reken processen. 

U kunt Azure Machine Learning Compute-exemplaren of reken clusters maken van:
* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md)
* Python-SDK en CLI:
    * [Rekenproces](how-to-create-manage-compute-instance.md)
    * [Reken cluster](how-to-create-attach-compute-cluster.md)
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (preview-versie)
* Resource Manager-sjabloon. Zie de [sjabloon create Azure machine learning Compute](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute)voor een voorbeeld sjabloon.
* Machine learning- [extensie voor de Azure cli](reference-azure-machine-learning-cli.md#resource-management).  

Wanneer u deze reken resources maakt, maakt u automatisch deel uit van uw werk ruimte, in tegens telling tot andere soorten Compute-doelen.


|Mogelijkheid  |Reken cluster  |Rekenproces  |
|---------|---------|---------|
|Cluster met één of meerdere knoop punten     |    **&check;**       |         |
|Automatisch schalen elke keer dat u een uitvoering verzendt     |     **&check;**      |         |
|Automatische Cluster beheer en taak planning     |   **&check;**        |     **&check;**      |
|Ondersteuning voor zowel CPU-als GPU-resources     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Wanneer een berekenings cluster niet actief is, wordt het automatisch geschaald naar 0 knoop punten, zodat u niet betaalt wanneer het niet in gebruik is.  Een reken *instantie*is echter altijd ingeschakeld en wordt niet automatisch geschaald.  U moet [het reken exemplaar stoppen](how-to-create-manage-compute-instance.md#manage) wanneer u het niet gebruikt om extra kosten te voor komen. 

### <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en-groottes

Wanneer u de grootte van een knoop punt selecteert voor een beheerde Compute-resource in Azure Machine Learning, kunt u kiezen uit beschik bare VM-grootten in Azure. Azure biedt tal van grootten voor Linux en Windows voor verschillende werk belastingen. Hier vindt u meer informatie over de verschillende [VM-typen en-grootten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Er zijn enkele uitzonde ringen en beperkingen voor het kiezen van een VM-grootte:
* Sommige VM-reeksen worden niet ondersteund in Azure Machine Learning.
* Sommige VM-reeksen zijn beperkt. Als u een beperkte serie wilt gebruiken, neemt u contact op met de ondersteuning en vraagt u een quotum toename aan te vragen voor de serie. Zie [ondersteunings opties voor Azure](https://azure.microsoft.com/support/options/) voor meer informatie over contact opnemen met de ondersteuning.

Raadpleeg de volgende tabel voor meer informatie over ondersteunde reeksen en beperkingen. 

| **Ondersteunde VM-serie**  | **Beperkingen** |
|------------|------------|
| D | Geen |
| Dv2 | Geen |  
| Dv3 | Geen|
| DSv2 | Geen | 
| DSv3 | Geen|
| FSv2 | Geen | 
| HBv2 | Goedkeuring vereist |  
| HCS | Goedkeuring vereist |  
| M | Goedkeuring vereist |
| NC | Geen |    
| NCsv2 | Goedkeuring vereist |
| NCsv3 | Goedkeuring vereist |  
| NDs | Goedkeuring vereist |
| NDv2 | Goedkeuring vereist |
| NV | Geen |
| NVv3 | Goedkeuring vereist | 


Hoewel Azure Machine Learning deze VM-serie ondersteunt, zijn ze mogelijk niet beschikbaar in alle Azure-regio's. U kunt controleren of er VM-serie beschikbaar is: [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

### <a name="compute-isolation"></a>Reken isolatie

Azure Machine Learning Compute biedt de grootte van virtuele machines die zijn geïsoleerd voor een specifiek hardwaretype en die zijn toegewezen aan één klant. Geïsoleerde virtuele-machine grootten zijn het meest geschikt voor werk belastingen die een hoge mate van isolatie van andere werk belastingen vereisen om redenen die voldoen aan de vereisten voor naleving en regelgeving. Het gebruik van een geïsoleerde grootte garandeert dat uw virtuele machine de enige is die op dat specifieke Server exemplaar wordt uitgevoerd.

De huidige, geïsoleerde virtuele-machine-aanbiedingen omvatten:
* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*RDMA-compatibel

Zie hier voor meer informatie over [isolatie in de open bare Azure-Cloud](https://docs.microsoft.com/azure/security/fundamentals/isolation-choices).

## <a name="unmanaged-compute"></a>Onbeheerde compute

Een niet-beheerd reken doel wordt *niet* beheerd door Azure machine learning. U maakt dit type reken doel buiten Azure Machine Learning en koppelt het vervolgens aan uw werk ruimte. Onbeheerde reken resources kunnen extra stappen vereisen om de prestaties van machine learning werk belastingen te hand haven of te verbeteren.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:
* [Een reken doel gebruiken om uw model te trainen](how-to-set-up-training-targets.md)
* [Uw model implementeren naar een berekenings doel](how-to-deploy-and-where.md)
