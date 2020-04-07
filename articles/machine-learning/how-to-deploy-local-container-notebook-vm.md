---
title: Modellen implementeren om instanties te berekenen
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen als webservice met rekeninstanties.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 09164580b8bdb249fc12d14e827ad799d51cab34
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756589"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Een model implementeren in compute-exemplaren van Azure Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van Azure Machine Learning om een model te implementeren als webservice op uw Azure Machine Learning-rekenexemplaar. Compute-instanties gebruiken als een van de volgende voorwaarden waar is:

- U moet uw model snel implementeren en valideren.
- U test een model dat in ontwikkeling is.

> [!TIP]
> Het implementeren van een model van een Jupyter-notitieblok op een rekeninstantie naar een webservice op dezelfde VM is een _lokale implementatie._ In dit geval is de 'lokale' computer de rekeninstantie. Zie Modellen implementeren met [Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie over implementaties.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte met een rekeninstantie die wordt uitgevoerd. Zie [Omgeving en werkruimte instellen](tutorial-1st-experiment-sdk-setup.md)voor meer informatie.

## <a name="deploy-to-the-compute-instances"></a>Implementeren in de rekeninstanties

Een voorbeeldnotitieblok dat lokale implementaties demonstreert, is opgenomen in uw rekeninstantie. Gebruik de volgende stappen om het notitieblok te laden en het model te implementeren als webservice op de VM:

1. Selecteer in [Azure Machine Learning-studio](https://ml.azure.com)uw Computer Learning-rekeninstanties van Azure Machine Learning.

1. Open `samples-*` de submap en `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`open . Eenmaal geopend, voer het notitieblok uit.

    ![Schermafbeelding van de lokale service die wordt uitgevoerd op notitieblok](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Het notitieblok geeft de URL en poort weer waarop de service wordt uitgevoerd. Bijvoorbeeld `https://localhost:6789`. U de cel `print('Local service port: {}'.format(local_service.port))` met de poort ook uitvoeren.

    ![Schermafbeelding van de lopende lokale servicepoort](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Als u de service wilt testen `https://localhost:<local_service.port>` vanuit een rekeninstantie, gebruikt u de URL. Als u wilt testen vanaf een externe client, krijgt u de openbare URL van de service die wordt uitgevoerd op de rekeninstantie. De openbare URL kan worden bepaald met behulp van de volgende formule; 
    * Notebook VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Rekeninstantie: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Bijvoorbeeld: 
    * Notebook VM:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Rekeninstantie:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Test de service

Als u voorbeeldgegevens wilt verzenden naar de lopende service, gebruikt u de volgende code. Vervang de `service_url` waarde van de URL van de vorige stap:

> [!NOTE]
> Wanneer u een implementatie op de rekeninstantie verifiëren, wordt de verificatie gemaakt met Azure Active Directory. De aanroep naar `interactive_auth.get_authentication_header()` de voorbeeldcode verifieert u met AAD en retourneert een koptekst die vervolgens kan worden gebruikt om te verifiëren naar de service op de rekeninstantie. Zie [Verificatie instellen voor Azure Machine Learning-resources en -werkstromen voor](how-to-setup-authentication.md#interactive-authentication)meer informatie.
>
> Wanneer u een implementatie op Azure Kubernetes Service of Azure Container Instances wilt verifiëren, wordt een andere verificatiemethode gebruikt. Zie Verificatie instellen [voor Azure Machine Learning-resources en -werkstromen voor](how-to-setup-authentication.md#web-service-authentication)meer informatie.

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie](how-to-troubleshoot-deployment.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning-modellen controleren met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)