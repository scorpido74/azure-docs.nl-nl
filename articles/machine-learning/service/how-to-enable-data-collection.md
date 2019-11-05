---
title: Gegevens verzamelen in uw productie modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van Azure Machine Learning invoer model gegevens in een Azure Blob-opslag.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 845d271c60762177ea88912f2100f3b47aedde46
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489985"
---
# <a name="collect-data-for-models-in-production"></a>Gegevens verzamelen voor modellen in productie
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Deze SDK wordt binnenkort buiten gebruik gesteld. Deze SDK is nog steeds geschikt voor ontwikkel aars die gegevens drift in modellen bewaken, maar de meeste ontwikkel aars moeten de vereenvoudigde [gegevens bewaking met Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)gebruiken. 

In dit artikel leert u hoe u invoer model gegevens kunt verzamelen van Azure Machine Learning die u in azure Kubernetes-cluster (AKS) hebt geïmplementeerd in een Azure Blob-opslag. 

Als u deze gegevens hebt verzameld, kunt u het volgende doen:
* [Gegevens drift bewaken](how-to-monitor-data-drift.md) naarmate productie gegevens uw model binnenkomen

* Betere beslissingen nemen wanneer u uw model opnieuw traint of optimaliseert

* Train uw model opnieuw met de verzamelde gegevens

## <a name="what-is-collected-and-where-does-it-go"></a>Wat wordt er verzameld en waar gaat het?

De volgende gegevens kunnen worden verzameld:
* Model **invoer** gegevens van webservices die zijn geïmplementeerd in azure Kubernetes cluster (AKS) (spraak, afbeeldingen en video worden **niet** verzameld) 
  
* Model voorspellingen met behulp van invoer gegevens voor productie.

> [!Note]
> Vóór het samen voegen of vooraf berekenen van deze gegevens maken geen deel uit van de service.   

De uitvoer wordt opgeslagen in een Azure-Blob. Omdat de gegevens worden toegevoegd aan een Azure-Blob, kunt u vervolgens uw favoriete hulp programma kiezen om de analyse uit te voeren. 

Het pad naar de uitvoer gegevens in de BLOB volgt de volgende syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> In versies van de SDK vóór het `0.1.0a16` het argument `designation` de naam `identifier`heeft. Als uw code is ontwikkeld met een eerdere versie, moet u de update dienovereenkomstig bijwerken.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

- Een Azure Machine Learning-werk ruimte, een lokale map met uw scripts en de Azure Machine Learning SDK voor python geïnstalleerd. Meer informatie over hoe u deze vereisten kunt ophalen met behulp van het document [een ontwikkel omgeving configureren](how-to-configure-environment.md) .

- Een getraind machine learning model dat moet worden geïmplementeerd in azure Kubernetes service (AKS). Als u er nog geen hebt, raadpleegt u de zelf studie over het [classificatie Model Train image](tutorial-train-models-with-aml.md) .

- Een Azure Kubernetes-service cluster. Zie voor meer informatie over het maken en implementeren van een account de [instructies document implementeren en](how-to-deploy-and-where.md) gebruiken.

- [Stel uw omgeving](how-to-configure-environment.md) in en installeer de [controle-SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen
Het verzamelen van gegevens kan worden ingeschakeld, ongeacht het model dat wordt geïmplementeerd via Azure Machine Learning of andere hulpprogram ma's. 

Als u deze wilt inschakelen, moet u het volgende doen:

1. Open het Score bestand. 

1. Voeg de [volgende code](https://aka.ms/aml-monitoring-sdk) toe boven aan het bestand:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declareer de variabelen voor het verzamelen van gegevens in uw `init()`-functie:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* is een optionele para meter. u hoeft deze niet in te stellen als u deze niet nodig hebt voor uw model. Als u een correlationId op locatie hebt, helpt u de toewijzing met andere gegevens te vereenvoudigen. (Voor beelden zijn: LoanNumber, CustomerId, etc.)
    
    De *id* wordt later gebruikt voor het maken van de mapstructuur in uw blob. deze kan worden gebruikt om "onbewerkte" gegevens te delen versus "verwerkt".

3.  Voeg de volgende regels code toe aan de functie `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Het verzamelen van gegevens wordt **niet** automatisch ingesteld op **True** wanneer u een service in AKS implementeert, dus u moet uw configuratie bestand bijwerken zoals: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights voor service bewaking kan ook worden ingeschakeld door deze configuratie te wijzigen:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Als u een nieuwe installatie kopie wilt maken en de service wilt implementeren, raadpleegt u het document [implementeren en](how-to-deploy-and-where.md) gebruiken.


Als u al een service hebt met de afhankelijkheden die zijn geïnstalleerd in uw **omgevings bestand** en **Score bestand**, schakelt u het verzamelen van gegevens in:

1. Ga naar [Azure machine learning Studio](https://ml.azure.com).

1. Open uw werk ruimte.

1. Ga naar **implementaties** -> **Selecteer Service** -> **bewerken**.

   ![Service bewerken](media/how-to-enable-data-collection/EditService.PNG)

1. Schakel in **Geavanceerde instellingen**de optie **model gegevens verzamelen inschakelen**uit. 

    [Gegevens verzameling ![controleren](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In dit venster kunt u er ook voor kiezen om Appinsights Diagnostics in te scha kelen om de status van uw service bij te houden.  

1. Selecteer **bijwerken** om de wijziging toe te passen.


## <a name="disable-data-collection"></a>Gegevens verzameling uitschakelen
U kunt het verzamelen van gegevens op elk gewenst moment stoppen. Gebruik python-code of Azure Machine Learning Studio om het verzamelen van gegevens uit te scha kelen.

+ Optie 1-uitschakelen in Azure Machine Learning studio: 
  1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

  1. Open uw werk ruimte.

  1. Ga naar **implementaties** -> **Selecteer Service** -> **bewerken**.

     [optie voor ![bewerken](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. Schakel in **Geavanceerde instellingen**de optie **model gegevens verzamelen inschakelen**uit. 

     [Gegevens verzameling ![uitschakelen](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selecteer **bijwerken** om de wijziging toe te passen.

  U kunt deze instellingen ook openen in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com).

+ Optie 2: gebruik python om het verzamelen van gegevens uit te scha kelen:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Valideer uw gegevens en Analyseer deze
U kunt elk gewenst hulp programma kiezen voor het analyseren van de verzamelde gegevens in uw Azure-Blob.

Om snel toegang te krijgen tot de gegevens van uw blob:
1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

1. Open uw werk ruimte.
1. Klik op **opslag**.

    [Opslag ![](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Volg het pad naar de uitvoer gegevens in de blob met de volgende syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Model gegevens analyseren via Power BI

1. [Power bi Desktop](https://www.powerbi.com) downloaden en openen

1. Selecteer **gegevens ophalen** en klik op [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [Setup van aan pbi-BLOB ![](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Voeg de naam van uw opslag account toe en voer uw opslag sleutel in. U kunt deze informatie vinden in de **instellingen** van uw blob > > toegangs sleutels. 

1. Selecteer de container **modeldata** en klik op **bewerken**. 

    [![aan pbi-Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klik in de query-editor onder naam kolom en voeg uw opslag account 1 toe. Het pad naar het filter model. Opmerking: als u alleen bestanden van een bepaald jaar of een bepaalde maand wilt bekijken, vouwt u het pad naar het filter uit. Bekijk bijvoorbeeld alleen maart-gegevens:/modeldata/subscriptionid >/resourcegroupname >/WorkspaceName >/webservicename >/modelname >/modelversion >/designation >/Year >/3

1. Filter de gegevens die relevant zijn voor u op basis van de **naam**. Als u voor **spellingen** en **invoer**hebt opgeslagen, moet u een query maken voor elke.

1. Klik op de dubbele pijl afgezien van de kolom **inhoud** om de bestanden te combi neren. 

    [AAN pbi-inhoud ![](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klik op OK om de gegevens vooraf te laden.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. U kunt nu klikken op **sluiten en Toep assen** .

1.  Als u invoer en voor spellingen hebt toegevoegd, worden uw tabellen automatisch gecorreleerd op **aanvraag**-waarde.

1. Begin met het bouwen van uw aangepaste rapporten in uw model gegevens.


### <a name="analyzing-model-data-using-databricks"></a>Model gegevens analyseren met behulp van Databricks

1. Maak een [Databricks-werk ruimte](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Ga naar uw Databricks-werk ruimte. 

1. Selecteer **gegevens uploaden**in uw databricks-werk ruimte.

    [![DB-upload](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Maak een nieuwe tabel en selecteer **andere gegevens bronnen** -> Azure Blob Storage-> tabel maken in notitie blok.

    [![DB-tabel](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Werk de locatie van uw gegevens bij. Hier volgt een voorbeeld:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![met dbsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Volg de stappen in de sjabloon om uw gegevens te bekijken en te analyseren. 

## <a name="example-notebook"></a>Voor beeld van notebook

In de notebook [How-to-use-azureml/Deployment/Enable-Data-Collection-for-models-in-AKS/Enable-Data-Collection-for-models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) worden concepten in dit artikel gedemonstreerd.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
