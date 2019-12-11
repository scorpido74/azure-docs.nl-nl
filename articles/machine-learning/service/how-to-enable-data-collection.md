---
title: Gegevens verzamelen in uw productie modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van Azure Machine Learning invoer model gegevens in Azure Blob-opslag.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 4ef1249a601334cc198662b90da95623247190e7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978182"
---
# <a name="collect-data-for-models-in-production"></a>Verzamelen van gegevens voor modellen in productie

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> De Azure Machine Learning monitoring-SDK wordt binnenkort ingetrokken. De SDK is nog steeds geschikt voor ontwikkel aars die momenteel de SDK gebruiken om gegevens drift in modellen te bewaken. Voor nieuwe klanten raden wij u echter aan de vereenvoudigde [gegevens bewaking met Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights)te gebruiken.

In dit artikel wordt uitgelegd hoe u invoer model gegevens van Azure Machine Learning kunt verzamelen. Ook wordt uitgelegd hoe u de invoer gegevens implementeert in een AKS-cluster (Azure Kubernetes service) en de uitvoer gegevens opslaat in Azure Blob-opslag.

Zodra de verzameling is ingeschakeld, kunt u met de gegevens die u verzamelt, het volgende doen:

* [Bewaak gegevens drift](how-to-monitor-data-drift.md) als productie gegevens uw model binnenkomen.

* Neem betere beslissingen over wanneer u uw model opnieuw traint of optimaliseert.

* Train uw model opnieuw met de verzamelde gegevens.

## <a name="what-is-collected-and-where-it-goes"></a>Wat wordt verzameld en waar het gaat

De volgende gegevens kunnen worden verzameld:

* Model invoer gegevens van webservices die zijn geïmplementeerd in een AKS-cluster. Voice audio, afbeeldingen en video worden *niet* verzameld.
  
* Model voorspellingen met invoergegevens van de productie.

>[!NOTE]
> Preaggregatie en voor berekeningen op deze gegevens maken momenteel geen deel uit van de verzamelings service.

De uitvoer wordt opgeslagen in Blob Storage. Omdat de gegevens worden toegevoegd aan de Blob-opslag, kunt u uw favoriete hulp programma kiezen om de analyse uit te voeren.

Het pad naar de uitvoergegevens in de blob met de volgende deze syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> In versies van de Azure Machine Learning SDK voor python ouder dan versie 0.1.0 A16 heeft het argument `designation` de naam `identifier`. Als u uw code hebt ontwikkeld met een eerdere versie, moet u deze dienovereenkomstig bijwerken.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.

- Er moet een AzureMachine Learning-werk ruimte, een lokale map met uw scripts en de Azure Machine Learning SDK voor python zijn geïnstalleerd. Zie [How to configure a Development Environment (een ontwikkel omgeving configureren](how-to-configure-environment.md)) voor meer informatie over het installeren van deze.

- U hebt een getraind machine learning-model nodig om te worden geïmplementeerd op AKS. Als u geen model hebt, raadpleegt u de zelf studie over het [classificatie Model Train image](tutorial-train-models-with-aml.md) .

- U hebt een AKS-cluster nodig. Voor informatie over hoe u een maakt en implementeert, raadpleegt [u hoe u implementeert en waar](how-to-deploy-and-where.md).

- [Stel uw omgeving](how-to-configure-environment.md) in en installeer de [Azure machine learning monitoring-SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen

U kunt gegevens verzameling inschakelen, ongeacht het model dat u via Azure Machine Learning of andere hulpprogram ma's implementeert.

Als u gegevens verzameling wilt inschakelen, moet u het volgende doen:

1. Open het scoring-bestand.

1. Voeg de [met de volgende code](https://aka.ms/aml-monitoring-sdk) aan de bovenkant van het bestand:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declareer de variabelen van uw gegevens verzameling in uw `init` functie:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* is een optionele para meter. U hoeft deze niet te gebruiken als uw model dit niet nodig heeft. Het gebruik van *CorrelationId* helpt u gemakkelijker te koppelen met andere gegevens, zoals *LoanNumber* of *CustomerId*.
    
    De *id* -para meter wordt later gebruikt voor het maken van de mapstructuur in uw blob. U kunt deze gebruiken om onbewerkte gegevens te onderscheiden van verwerkte gegevens.

1. Voeg de volgende regels code aan de `run(input_df)` functie:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Het verzamelen van gegevens wordt *niet* automatisch ingesteld op **True** wanneer u een service in AKS implementeert. Werk uw configuratie bestand bij, zoals in het volgende voor beeld:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    U kunt ook Application Insights voor service bewaking inschakelen door deze configuratie te wijzigen:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Zie [implementeren en waar](how-to-deploy-and-where.md)als u een nieuwe installatie kopie wilt maken en het machine learning model wilt implementeren.

Als u al een service hebt met de afhankelijkheden die zijn geïnstalleerd in uw omgevings bestand en Score bestand, schakelt u het verzamelen van gegevens in door de volgende stappen uit te voeren:

1. Ga naar [Azure machine learning](https://ml.azure.com).

1. Open de werkruimte.

1. Selecteer **implementaties** > **Selecteer Service** > **bewerken**.

   ![De service bewerken](media/how-to-enable-data-collection/EditService.PNG)

1. Selecteer in **Geavanceerde instellingen**de optie **model gegevens verzameling inschakelen**.

    [gegevens verzameling ![selecteren](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   U kunt ook **Diagnostische gegevens over AppInsights inschakelen** selecteren om de status van uw service bij te houden.

1. Selecteer **bijwerken** om de wijzigingen toe te passen.

## <a name="disable-data-collection"></a>Gegevens verzamelen uitschakelen

U kunt het verzamelen van gegevens op elk gewenst moment stoppen. Gebruik python-code of Azure Machine Learning om het verzamelen van gegevens uit te scha kelen.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Optie 1: het verzamelen van gegevens in Azure Machine Learning uitschakelen

1. Meld u aan bij [Azure machine learning](https://ml.azure.com).

1. Open de werkruimte.

1. Selecteer **implementaties** > **Selecteer Service** > **bewerken**.

   [![Selecteer de optie bewerken](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. Schakel in **Geavanceerde instellingen**het **verzamelen van model gegevens inschakelen**uit.

    [het selectie vakje voor het verzamelen van gegevens ![wissen](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Selecteer **Update** de wijziging toepassen.

U kunt deze instellingen ook openen in uw werk ruimte in [Azure machine learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Optie 2: gebruik python om het verzamelen van gegevens uit te scha kelen

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Valideer en analyseer uw gegevens

U kunt een hulp programma kiezen van uw voor keur voor het analyseren van de gegevens die in uw Blob-opslag zijn verzameld.

### <a name="quickly-access-your-blob-data"></a>Snel toegang krijgen tot uw BLOB-gegevens

1. Meld u aan bij [Azure machine learning](https://ml.azure.com).

1. Open de werkruimte.

1. Selecteer **Opslag**.

    [de opslag optie ![selecteren](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Volg het pad naar de uitvoer gegevens van de blob met de volgende syntaxis:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Model gegevens analyseren met behulp van Power BI

1. Down load en open [Power bi Desktop](https://www.powerbi.com).

1. Selecteer **gegevens ophalen** en selecteer [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [Setup van Power BI BLOB ![](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Naam van uw opslagaccount toevoegen en voer de opslagsleutel van uw. U kunt deze informatie vinden door **instellingen** > **toegangs sleutels** in uw BLOB te selecteren.

1. Selecteer de container **model gegevens** en selecteer **bewerken**.

    [![Power BI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klik in de query-editor op onder de kolom **naam** en voeg uw opslag account toe.

1. Voer het pad naar het model in het filter in. Als u alleen wilt zoeken naar bestanden van een bepaald jaar of per maand, vouwt u het pad naar het filter uit. Als u bijvoorbeeld alleen in maart gegevens wilt bekijken, gebruikt u dit pad naar het filter:

   /modeldata/\<subscriptionid >/\<resourcegroupname >/\<workspacenaam >/\<webservicenaam >/\<modelnaam >/\<modelversion >/\<aanwijzing >/\<jaar >/3

1. Filter de gegevens die relevant zijn voor u op basis van **naam** waarden. Als u voor spellingen en invoer hebt opgeslagen, moet u een query maken voor elke.

1. Selecteer de dubbele pijl-omlaag **naast de kolomkop om de bestanden** te combi neren.

    [Power BI inhoud ![](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecteer **OK**. De gegevens worden geladen.

    [![Power BI het combi neren van bestanden](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecteer **sluiten en Toep assen**.

1. Als u invoer en voor spellingen hebt toegevoegd, worden uw tabellen automatisch gerangschikt op basis van **aanvraag** -waarden.

1. Start het bouwen van uw aangepaste rapporten op gegevens van uw model.

### <a name="analyze-model-data-using-azure-databricks"></a>Model gegevens analyseren met behulp van Azure Databricks

1. Maak een [Azure Databricks-werk ruimte](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Ga naar uw Databricks-werkruimte.

1. Selecteer **gegevens uploaden**in uw Databricks-werk ruimte.

    [![optie Databricks-Upload gegevens selecteren](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecteer **nieuwe tabel maken** en selecteer **andere gegevens bronnen** > **Azure Blob Storage** > **tabel in notitie blok maken**.

    [![Databricks-tabel maken](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Werk de locatie van uw gegevens bij. Hier volgt een voorbeeld:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [Setup van ![Databricks](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Volg de stappen in de sjabloon om uw gegevens weer te geven en te analyseren.
