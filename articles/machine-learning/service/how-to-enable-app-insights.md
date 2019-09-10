---
title: Azure-toepassing Insights instellen voor het bewaken van ML-modellen
titleSuffix: Azure Machine Learning service
description: Webservices die met Azure Machine Learning service zijn geïmplementeerd, bewaken met behulp van Azure-toepassing Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: ff9261e2720ed80fc96131cea69d2985c8e0883e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858756"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Uw Azure Machine Learning-modellen met Application Insights bewaken

In dit artikel leert u hoe u Azure Application Insights instellen voor uw Azure Machine Learning-service. Application Insights biedt u de mogelijkheid om te controleren:
* Tarieven, reactietijden en foutpercentages aanvragen.
* Afhankelijkheidsrelaties, reactietijden en foutpercentages.
* Uitzonderingen.

[Meer informatie over Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Vereisten

* Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer nog vandaag de [gratis of betaalde versie van de Azure Machine Learning Service](https://aka.ms/AMLFree).

* Een Azure Machine Learning-werkruimte en een lokale map met uw scripts en de Azure Machine Learning-SDK voor Python geïnstalleerd. Zie voor meer informatie over het verkrijgen van deze vereisten, [het configureren van een ontwikkelomgeving](how-to-configure-environment.md).
* Een getrainde machine learning-model worden toegepast op Azure Kubernetes Service (AKS) of Azure Container exemplaar (ACI). Als u niet hebt, raadpleegt u de [Train installatiekopie classificeringsmodel](tutorial-train-models-with-aml.md) zelfstudie.


## <a name="use-sdk-to-configure"></a>SDK gebruiken om te configureren 

### <a name="update-a-deployed-service"></a>Een geïmplementeerde service bijwerken
1. Identificeren van de service in uw werkruimte. De waarde voor `ws` is de naam van uw werkruimte.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Bijwerken van uw service en Application Insights inschakelen. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Aangepaste logboektraceringen in uw service
Als u wilt dat aangepaste logtraceringen, volgt u het proces van de standaard voor AKS of ACI in de [implementeren en waar](how-to-deploy-and-where.md) document. Gebruik vervolgens de volgende stappen uit:

1. Het scoring-bestand bijwerken door toevoeging van afdrukken instructies.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. De serviceconfiguratie van de bijwerken.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bouw een installatiekopie en implementeer deze op [AKS](how-to-deploy-to-aks.md) of [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Bijhouden van wijzigingen in Python uitschakelen

Als u wilt uitschakelen Application Insights, gebruik de volgende code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>Portal gebruiken om te configureren

U kunt in- en uitschakelen van Application Insights in Azure portal.

1. In de [Azure-portal](https://portal.azure.com), opent u uw werkruimte.

1. Op de **implementaties** tabblad, selecteert u de service waar u Application Insights inschakelen.

   [![Lijst met services op het tabblad implementaties](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selecteer **Bewerken**.

   [![Knop bewerken](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **geavanceerde instellingen**, selecteer de **diagnose van AppInsights inschakelen** selectievakje.

   [![Het selectievakje is ingeschakeld voor het inschakelen van diagnostische gegevens](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selecteer **Update** aan de onderkant van het scherm om de wijzigingen toe te passen. 

### <a name="disable"></a>Uitschakelen
1. In de [Azure-portal](https://portal.azure.com), opent u uw werkruimte.
1. Selecteer **implementaties**, selecteert u de service en selecteer **bewerken**.

   [![Gebruik de knop bewerken](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **geavanceerde instellingen**, schakel de **diagnose van AppInsights inschakelen** selectievakje. 

   [![Het selectievakje is uitgeschakeld voor het inschakelen van diagnostische gegevens](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selecteer **Update** aan de onderkant van het scherm om de wijzigingen toe te passen. 
 

## <a name="evaluate-data"></a>Gegevens evalueren
Van uw service-gegevens worden opgeslagen in uw Application Insights-account, in dezelfde resourcegroep bevinden als uw Azure Machine Learning-service.
Om dit te bekijken:
1. Ga naar de service Machine Learning-werkruimte in de [Azure-portal](https://portal.azure.com) en klik op de koppeling van Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selecteer de **overzicht** tabblad om te bekijken van een set van metrische gegevens voor uw service.

   [![Overzicht](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Als u wilt zoeken in uw aangepaste traceringen, selecteer **Analytics**.
4. Selecteer in de schemasectie **traceringen**. Selecteer vervolgens **uitvoeren** uw query uit te voeren. Gegevens moeten worden weergegeven in een tabelindeling en moet worden toegewezen aan uw aangepaste aanroepen in de scoring-bestand. 

   [![Aangepaste traceringen](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Zie voor meer informatie over het gebruik van Application Insights, [wat is Application Insights?](../../azure-monitor/app/app-insights-overview.md).


## <a name="example-notebook"></a>Voorbeeld van de notebook

De [Enable-app-Insights-in-production-service. ipynb-](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook demonstreert de concepten in dit artikel. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen
U kunt ook gegevens verzamelen over uw modellen in productie. Lees het artikel [verzamelen van gegevens voor modellen in productie](how-to-enable-data-collection.md). 

Lees ook de [Azure monitor voor containers](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json).
