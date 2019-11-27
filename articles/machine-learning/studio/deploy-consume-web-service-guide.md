---
title: Implementatie en verbruik
titleSuffix: ML Studio (classic) - Azure
description: U kunt Azure Machine Learning Studio (klassiek) gebruiken om machine learning werk stromen en modellen als webservices te implementeren. Deze webservices kunnen vervolgens worden gebruikt voor het aanroepen van de machine learning-modellen van toepassingen via internet wilt voorspellingen in realtime of in de batchmodus.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 1c97fd4f4d5646b6654f5261abd99372c521c389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228239"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Webservices Azure Machine Learning Studio (klassiek): implementatie en verbruik

U kunt Azure Machine Learning Studio (klassiek) gebruiken om machine learning werk stromen en modellen als webservices te implementeren. Deze webservices kunnen vervolgens worden gebruikt voor het aanroepen van de machine learning-modellen van toepassingen via Internet wilt voorspellingen in realtime of in de batchmodus. Omdat de webservices RESTful zijn, kunt u deze aanroepen van verschillende programmeertalen en platforms, zoals .NET, Java, en toepassingen, zoals Excel.

De volgende secties vindt u koppelingen naar scenario's, code en documentatie waarmee kunt u aan de slag.

## <a name="deploy-a-web-service"></a>Een webservice implementeren

### <a name="with-azure-machine-learning-studio-classic"></a>Met Azure Machine Learning Studio (klassiek)

De Studio-Portal en de Microsoft Azure Machine Learning web services-Portal helpen u bij het implementeren en beheren van een webservice zonder code te schrijven.

De volgende koppelingen bieden algemene informatie over het implementeren van een nieuwe webservice:

* Zie [een nieuwe webservice implementeren](deploy-a-machine-learning-web-service.md)voor een overzicht van het implementeren van een nieuwe webservice op basis van Azure Resource Manager.
* Zie [een Azure machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor een overzicht van het implementeren van een webservice.
* Voor een volledig overzicht van het maken en implementeren van een webservice, begint u met [zelf studie 1: krediet risico voors pellen](tutorial-part1-credit-risk.md).
* Zie voor specifieke voorbeelden die een webservice implementeren:

  * [Zelf studie 3: een model voor krediet Risico's implementeren](tutorial-part3-credit-risk-deploy.md)
  * [Een webservice implementeren in meerdere regio's](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Bij de resourceprovider voor web services API's (Azure Resource Manager-API's)

Met de klassieke versie van Azure Machine Learning Studio resource provider voor webservices kunt u webservices implementeren en beheren door gebruik te maken van REST API-aanroepen. Zie voor meer informatie de referentie voor [machine learning-webservice (rest)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Met PowerShell-cmdlets

Met de klassieke versie van Azure Machine Learning Studio resource provider voor webservices kunnen webservices worden geïmplementeerd en beheerd met behulp van Power shell-cmdlets.

Als u de cmdlets wilt gebruiken, moet u zich eerst vanuit de Power shell-omgeving aanmelden bij uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Zie [Azure PowerShell gebruiken met Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md)als u niet bekend bent met het aanroepen van Power shell-opdrachten die zijn gebaseerd op Resource Manager.

Als u uw voorspellende experiment wilt exporteren, gebruikt u [deze voorbeeld code](https://github.com/ritwik20/AzureML-WebServices). Nadat u het .exe-bestand van de code hebt gemaakt, kunt u het volgende typen:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

De toepassing wordt uitgevoerd, maakt een web-service JSON-sjabloon. Als u de sjabloon wilt een webservice implementeert, moet u de volgende informatie toevoegen:

* Storage-accountnaam en -sleutel

    U kunt de naam en sleutel van het opslag account ophalen uit de [Azure Portal](https://portal.azure.com/).
* Toezegging plan-ID

    U kunt de plan-ID ophalen uit de [Azure machine learning Web Services](https://services.azureml.net) -portal door u aan te melden en te klikken op een naam van een abonnement.

Voeg ze toe aan de JSON-sjabloon als onderliggende elementen van het knoop punt *Eigenschappen* op hetzelfde niveau als het *MachineLearningWorkspace* -knoop punt.

Hier volgt een voorbeeld:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Zie de volgende artikelen en voorbeeldcode voor meer informatie:

* Naslag informatie voor [Azure machine learning Studio (klassieke)-cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) op MSDN
* Voorbeeld [scenario](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) op github

## <a name="consume-the-web-services"></a>De webservices gebruiken

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Vanuit de Azure Machine Learning-webservices UI (testen)

U kunt uw webservice van Azure Machine Learning-webserviceportal testen. Dit omvat de Request Response-service (RRS) testen en interfaces voor batchuitvoeringsservice (BES).

* [Een nieuwe webservice implementeren](deploy-a-machine-learning-web-service.md)
* [Een Azure Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md)
* [Zelf studie 3: een model voor krediet Risico's implementeren](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Vanuit Excel

U kunt een Excel-sjabloon die de webservice verbruikt downloaden:

* [Een Azure Machine Learning-webservice gebruiken vanuit Excel](consuming-from-excel.md)
* [Excel-invoeg toepassing voor Azure Machine Learning-webservices](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Van een REST-client

Azure Machine Learning-webservices zijn RESTful API's. U kunt deze Api's van verschillende platformen gebruiken, zoals .NET, Python, R, Java, enzovoort. De pagina **verbruik** voor uw webservice op de [Microsoft Azure machine learning Web Services-portal](https://services.azureml.net) bevat voorbeeld code waarmee u aan de slag kunt gaan. Zie [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Azure Machine Learning-webservice gebruiken) voor meer informatie.
