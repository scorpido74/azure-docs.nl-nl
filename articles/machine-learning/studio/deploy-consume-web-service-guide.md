---
title: Implementatie en verbruik
titleSuffix: Azure Machine Learning Studio (classic)
description: U kunt Azure Machine Learning Studio (klassiek) gebruiken om machine learning werk stromen en modellen als webservices te implementeren. Deze webservices kunnen vervolgens worden gebruikt voor het aanroepen van de machine learning modellen van toepassingen via internet om voor spellingen in realtime of in batch modus uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: bf169a9b7064bd83caf0c9f8ec6dd6dd55894d67
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493018"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Webservices Azure Machine Learning Studio (klassiek): implementatie en verbruik

U kunt Azure Machine Learning Studio (klassiek) gebruiken om machine learning werk stromen en modellen als webservices te implementeren. Deze webservices kunnen vervolgens worden gebruikt voor het aanroepen van de machine learning modellen van toepassingen via internet om voor spellingen in realtime of in batch modus uit te voeren. Omdat de webservices actief zijn, kunt u deze aanroepen vanuit verschillende programmeer talen en platformen, zoals .NET en Java, en vanuit toepassingen, zoals Excel.

De volgende secties bevatten koppelingen naar instructies, code en documentatie om aan de slag te gaan.

## <a name="deploy-a-web-service"></a>Een webservice implementeren

### <a name="with-azure-machine-learning-studio-classic"></a>Met Azure Machine Learning Studio (klassiek)

De Studio-Portal en de Microsoft Azure Machine Learning web services-Portal helpen u bij het implementeren en beheren van een webservice zonder code te schrijven.

De volgende koppelingen bevatten algemene informatie over het implementeren van een nieuwe webservice:

* Zie [een nieuwe webservice implementeren](publish-a-machine-learning-web-service.md)voor een overzicht van het implementeren van een nieuwe webservice op basis van Azure Resource Manager.
* Zie [een Azure machine learning-webservice implementeren](publish-a-machine-learning-web-service.md)voor een overzicht van het implementeren van een webservice.
* Voor een volledig overzicht van het maken en implementeren van een webservice, begint u met [zelf studie 1: krediet risico voors pellen](tutorial-part1-credit-risk.md).
* Zie voor specifieke voor beelden voor het implementeren van een webservice:

  * [Zelf studie 3: een model voor krediet Risico's implementeren](tutorial-part3-credit-risk-deploy.md)
  * [Een webservice implementeren in meerdere regio's](/azure/machine-learning/studio/publish-a-machine-learning-web-service#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Met Web Services resource provider Api's (Azure Resource Manager-Api's)

Met de klassieke versie van Azure Machine Learning Studio resource provider voor webservices kunt u webservices implementeren en beheren door gebruik te maken van REST API-aanroepen. Zie voor meer informatie de referentie voor [machine learning-webservice (rest)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Met Power shell-cmdlets

Met de klassieke versie van Azure Machine Learning Studio resource provider voor webservices kunnen webservices worden geïmplementeerd en beheerd met behulp van Power shell-cmdlets.

Als u de cmdlets wilt gebruiken, moet u zich eerst vanuit de Power shell-omgeving aanmelden bij uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Zie [Azure PowerShell gebruiken met Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md)als u niet bekend bent met het aanroepen van Power shell-opdrachten die zijn gebaseerd op Resource Manager.

Als u uw voorspellende experiment wilt exporteren, gebruikt u [deze voorbeeld code](https://github.com/ritwik20/AzureML-WebServices). Nadat u het exe-bestand van de code hebt gemaakt, kunt u het volgende typen:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Als u de toepassing uitvoert, wordt een JSON-sjabloon voor de webservice gemaakt. Als u de sjabloon wilt gebruiken om een webservice te implementeren, moet u de volgende informatie toevoegen:

* Naam en sleutel van het opslag account

    U kunt de naam en sleutel van het opslag account ophalen uit de [Azure Portal](https://portal.azure.com/).
* Toezeggings plan-ID

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

Raadpleeg de volgende artikelen en voorbeeld code voor aanvullende informatie:

* Naslag informatie voor [Azure machine learning Studio (klassieke)-cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) op MSDN
* Voorbeeld [scenario](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) op github

## <a name="consume-the-web-services"></a>De webservices gebruiken

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Vanuit de gebruikers interface van Azure Machine Learning-webservices (testen)

U kunt uw webservice testen vanuit de Azure Machine Learning-webservices Portal. Dit omvat het testen van de aanvraag-antwoord service (RR'S) en BES-interfaces (batch Execution Service).

* [Een nieuwe webservice implementeren](publish-a-machine-learning-web-service.md)
* [Een Azure Machine Learning-webservice implementeren](publish-a-machine-learning-web-service.md)
* [Zelf studie 3: een model voor krediet Risico's implementeren](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Vanuit Excel

U kunt een Excel-sjabloon downloaden die de webservice gebruikt:

* [Een Azure Machine Learning-webservice gebruiken vanuit Excel](consuming-from-excel.md)
* [Excel-invoeg toepassing voor Azure Machine Learning-webservices](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Van een REST-gebaseerde client

Azure Machine Learning-webservices zijn REST Api's. U kunt deze Api's van verschillende platformen gebruiken, zoals .NET, Python, R, Java, enzovoort. De pagina **verbruik** voor uw webservice op de [Microsoft Azure machine learning Web Services-portal](https://services.azureml.net) bevat voorbeeld code waarmee u aan de slag kunt gaan. Zie [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Azure Machine Learning-webservice gebruiken) voor meer informatie.
