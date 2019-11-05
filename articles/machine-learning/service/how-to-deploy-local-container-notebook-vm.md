---
title: Modellen implementeren voor reken instanties
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning modellen als een webservice met behulp van reken instanties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: bb187826250b3edc9ac3d9e36a243d75819a45b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496879"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Een model implementeren om reken instanties te Azure Machine Learning

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Reken instanties zijn alleen beschikbaar voor werk ruimten met een regio **Noord-Centraal VS** of **UK-Zuid**.
>Als uw werk ruimte zich in een andere regio bevindt, kunt u in plaats daarvan een VM van een [notebook](concept-compute-instance.md#notebookvm) blijven maken en gebruiken.  U kunt met behulp van de stappen in dit artikel een model implementeren op een reken instantie of een notebook-VM.

Meer informatie over het gebruik van Azure Machine Learning voor het implementeren van een model als een webservice op uw Azure Machine Learning Reken exemplaar. Reken instanties gebruiken als aan een van de volgende voor waarden wordt voldaan:

- U moet uw model snel implementeren en valideren.
- U test een model dat wordt ontwikkeld.

> [!TIP]
> Het implementeren van een model van een Jupyter Notebook op een compute-exemplaar, is een _lokale implementatie_op een webservice op dezelfde VM. In dit geval is de ' lokale ' computer het reken exemplaar. Zie [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md)voor meer informatie over implementaties.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werk ruimte waarbij een reken instantie wordt uitgevoerd. Zie [Setup Environment and Workspace](tutorial-1st-experiment-sdk-setup.md)(Engelstalig) voor meer informatie.

## <a name="deploy-to-the-compute-instances"></a>Implementeren in de reken instanties

Een voor beeld van een notebook dat lokale implementaties bevat, is opgenomen in uw reken exemplaar. Gebruik de volgende stappen om het notitie blok te laden en het model te implementeren als een webservice op de VM:

1. Selecteer uw Azure Machine Learning Reken instanties in [Azure machine learning Studio](https://ml.azure.com).

1. Open de `samples-*`-submap en open vervolgens `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Als het notitie blok eenmaal is geopend, voert u het uit.

    ![Scherm afbeelding van de actieve lokale service op notebook](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. In het notitie blok worden de URL en de poort weer gegeven waarop de service wordt uitgevoerd. Bijvoorbeeld `https://localhost:6789`. U kunt ook de cel met `print('Local service port: {}'.format(local_service.port))` uitvoeren om de poort weer te geven.

    ![Scherm afbeelding van de actieve lokale service poort](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Als u de service van een reken instantie wilt testen, gebruikt u de `https://localhost:<local_service.port>` URL. Als u wilt testen vanaf een externe client, haalt u de open bare URL op van de service die wordt uitgevoerd op het reken exemplaar. De open bare URL kan worden bepaald met behulp van de volgende formule. 
    * VM van notebook: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Reken instantie: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 
    
    Bijvoorbeeld: 
    * VM van notebook: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Reken instantie: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Test de service

Gebruik de volgende code om voorbeeld gegevens naar de actieve service te verzenden. Vervang de waarde van `service_url` door de URL van de vorige stap:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning webservices beveiligen met SSL](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)