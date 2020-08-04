---
title: Hoe en waar modellen moeten worden geïmplementeerd
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe en waar u uw Azure Machine Learning-modellen kunt implementeren, waaronder Azure Container Instances, de Azure Kubernetes-service, Azure IoT Edge en Programmeer bare poort matrices voor velden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 42205f87e2025fa7f4492cb76aeb44a1fbf46eb6
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542768"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellen implementeren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van uw machine learning model als een webservice in de Azure-Cloud of het Azure IoT Edge van apparaten.

De werkstroom is vrijwel altijd hetzelfde, ongeacht waar u het model implementeert:

1. Registreer het model.
1. Een configuratie voor een afleiding voorbereiden
1. Een invoer script voorbereiden (tenzij u [geen code-implementatie](how-to-deploy-no-code-deployment.md)gebruikt)
1. Implementeer het model op het rekendoel.
1. Test het geïmplementeerde model, ook wel webservice genoemd.

Zie [modellen beheren, implementeren en bewaken met Azure machine learning](concept-model-management-and-deployment.md)voor meer informatie over de concepten die zijn betrokken bij de implementatie werk stroom.


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>Informatie over de service status

Tijdens de implementatie van het model ziet u mogelijk de wijziging van de service status terwijl deze volledig wordt geïmplementeerd.

In de volgende tabel worden de verschillende service statussen beschreven:

| Status van webservice | Beschrijving | Eind status?
| ----- | ----- | ----- |
| Overstappen | De service is in het implementatie proces. | No |
| Niet in orde | De service is geïmplementeerd, maar is momenteel niet bereikbaar.  | No |
| Unschedulable | De service kan op dit moment niet worden geïmplementeerd vanwege een gebrek aan resources. | No |
| Mislukt | De implementatie van de service is mislukt vanwege een fout of een crash. | Yes |
| In orde | De service is in orde en het eind punt is beschikbaar. | Yes |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Batch-deinterferentie
Azure Machine Learning Compute-doelen worden gemaakt en beheerd door Azure Machine Learning. Ze kunnen worden gebruikt voor batch voorspelling van Azure Machine Learning pijp lijnen.

Zie [batch voorspellingen uitvoeren](tutorial-pipeline-batch-scoring-classification.md)voor een overzicht van batch deinterferentie met Azure machine learning compute.

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge afleiding
Ondersteuning voor het implementeren naar de rand is in preview. Zie [Deploy Azure machine learning als een IOT Edge-module](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Web-service bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)

