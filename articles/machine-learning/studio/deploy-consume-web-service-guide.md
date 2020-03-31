---
title: Implementatie en verbruik
titleSuffix: ML Studio (classic) - Azure
description: U Azure Machine Learning Studio (klassiek) gebruiken om machine learning-workflows en -modellen te implementeren als webservices. Deze webservices kunnen vervolgens worden gebruikt om de machine learning-modellen van toepassingen via het internet te bellen om voorspellingen in realtime of in batchmodus te doen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204389"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klassieke) Web Services: implementatie en verbruik

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

U Azure Machine Learning Studio (klassiek) gebruiken om machine learning-workflows en -modellen te implementeren als webservices. Deze webservices kunnen vervolgens worden gebruikt om de machine learning-modellen van toepassingen via het internet aan te roepen om voorspellingen in realtime of in batchmodus te doen. Omdat de webservices RESTful zijn, u ze bellen vanuit verschillende programmeertalen en platforms, zoals .NET en Java, en vanuit toepassingen, zoals Excel.

De volgende secties bieden koppelingen naar walkthroughs, code en documentatie om u op weg te helpen.

## <a name="deploy-a-web-service"></a>Een webservice implementeren

### <a name="with-azure-machine-learning-studio-classic"></a>Met Azure Machine Learning Studio (klassiek)

De Studio-portal (klassieke) portal en de Microsoft Azure Machine Learning Web Services-portal helpen u bij het implementeren en beheren van een webservice zonder code te schrijven.

De volgende koppelingen geven algemene informatie over het implementeren van een nieuwe webservice:

* Zie [Een nieuwe webservice](deploy-a-machine-learning-web-service.md)implementeren voor een overzicht over het implementeren van een nieuwe webservice die is gebaseerd op Azure Resource Manager.
* Zie [Een Webservice](deploy-a-machine-learning-web-service.md)azure Machine Learning implementeren voor een walkthrough over het implementeren van een webservice.
* Voor een volledige walkthrough over het maken en implementeren van een webservice, begint u met [Zelfstudie 1: Kredietrisico voorspellen](tutorial-part1-credit-risk.md).
* Zie voor specifieke voorbeelden die een webservice implementeren:

  * [Zelfstudie 3: Kredietrisicomodel implementeren](tutorial-part3-credit-risk-deploy.md)
  * [Een webservice implementeren in meerdere regio's](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Api's van webservicesbronprovider (Azure Resource Manager API's)

De Azure Machine Learning Studio (classic) resource provider voor webservices maakt implementatie en beheer van webservices mogelijk met rest API-aanroepen. Zie de rest-referentie [(Machine Learning Web Service) voor](/rest/api/machinelearning/index) meer informatie.

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Met PowerShell-cmdlets

De Azure Machine Learning Studio (classic) resource provider voor webservices maakt implementatie en beheer van webservices mogelijk met PowerShell-cmdlets.

Als u de cmdlets wilt gebruiken, moet u zich eerst aanmelden bij uw Azure-account vanuit de PowerShell-omgeving met behulp van de [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Zie [Azure PowerShell gebruiken met Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md)als u niet bekend bent met het aanroepen van PowerShell-opdrachten die zijn gebaseerd op Resource Beheer.

Als u uw voorspellende experiment wilt exporteren, gebruikt u [deze voorbeeldcode](https://github.com/ritwik20/AzureML-WebServices). Nadat u het .exe-bestand van de code hebt gemaakt, u het type typen:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Als u de toepassing uitvoert, wordt een JSON-sjabloon voor webservice gemaakt. Als u de sjabloon wilt gebruiken om een webservice te implementeren, moet u de volgende gegevens toevoegen:

* Naam en sleutel van het opslagaccount

    U de naam en de sleutel van het opslagaccount ophalen via de [Azure-portal.](https://portal.azure.com/)
* Toezeggingsplan-ID

    U de plan-id ophalen via de [Azure Machine Learning Web Services-portal](https://services.azureml.net) door u aan te melden en op een plannaam te klikken.

Voeg ze toe aan de JSON-sjabloon als kinderen van het knooppunt *Eigenschappen* op hetzelfde niveau als het knooppunt *MachineLearningWorkspace.*

Hier volgt een voorbeeld:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Zie de volgende artikelen en voorbeeldcode voor meer informatie:

* [Azure Machine Learning Studio (klassieke) cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) referentie op MSDN
* Voorbeeld [van walkthrough](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) op GitHub

## <a name="consume-the-web-services"></a>De webservices gebruiken

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Vanuit de Azure Machine Learning Web Services-gebruikersinterface (Testen)

U uw webservice testen via de Azure Machine Learning Web Services-portal. Dit omvat het testen van de RRS- en Batch Execution-service (BES)-interfaces.This includes testing the Request-Response service (RRS) and Batch Execution service (BES) interfaces.

* [Een nieuwe webservice implementeren](deploy-a-machine-learning-web-service.md)
* [Een Azure Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md)
* [Zelfstudie 3: Kredietrisicomodel implementeren](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Vanuit Excel

U een Excel-sjabloon downloaden die de webservice verbruikt:

* [Een Azure Machine Learning-webservice gebruiken vanuit Excel](consuming-from-excel.md)
* [Excel-invoegtoepassing voor Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Van een rest-gebaseerde klant

Azure Machine Learning Web Services zijn RESTful API's. U deze API's van verschillende platforms gebruiken, zoals .NET, Python, R, Java, enz. De pagina **Verbruik** voor uw webservice op de [Microsoft Azure Machine Learning Web Services-portal](https://services.azureml.net) heeft voorbeeldcode waarmee u aan de slag. Zie [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Azure Machine Learning-webservice gebruiken) voor meer informatie.
