---
title: Gegevens verzamelen over uw productiemodellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van Azure Machine Learning-invoermodelgegevens in Azure Blob-opslag.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 44acc81df9eb6dc6a6af28b5b0f4730aa93adffc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475442"
---
# <a name="collect-data-for-models-in-production"></a>Gegevens verzamelen voor modellen in productie

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> De Azure Machine Learning Monitoring SDK wordt binnenkort buiten gebruik gesteld. De SDK is nog steeds geschikt voor ontwikkelaars die momenteel de SDK gebruiken om gegevensdrift in modellen te controleren. Maar voor nieuwe klanten raden we u aan de vereenvoudigde gegevensbewaking te gebruiken [met Application Insights.](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)

In dit artikel ziet u hoe u invoermodelgegevens verzamelt van Azure Machine Learning. Het laat ook zien hoe u de invoergegevens implementeert in een AKS-cluster (Azure Kubernetes Service) en de uitvoergegevens opslaat in Azure Blob-opslag.

Zodra verzameling is ingeschakeld, helpen de gegevens die u verzamelt u:

* [Monitor gegevensdriften](how-to-monitor-data-drift.md) als productiegegevens uw model binnenkomen.

* Neem betere beslissingen over wanneer u uw model moet omscholen of optimaliseren.

* Train uw model om met de verzamelde gegevens.

## <a name="what-is-collected-and-where-it-goes"></a>Wat wordt verzameld en waar het naartoe gaat

De volgende gegevens kunnen worden verzameld:

* Modelleer invoergegevens van webservices die zijn geïmplementeerd in een AKS-cluster. Spraakaudio, afbeeldingen en video worden *niet* verzameld.
  
* Modelvoorspellingen met behulp van productie-invoergegevens.

>[!NOTE]
> Vooraggregatie en voorberekeningen van deze gegevens maken momenteel geen deel uit van de incassoservice.

De uitvoer wordt opgeslagen in Blob-opslag. Omdat de gegevens worden toegevoegd aan blob-opslag, u uw favoriete hulpmiddel kiezen om de analyse uit te voeren.

Het pad naar de uitvoergegevens in de blob volgt de volgende syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> In versies van de Azure Machine Learning SDK voor Python eerder dan `designation` versie 0.1.0a16 wordt het argument genoemd `identifier`. Als u uw code met een eerdere versie hebt ontwikkeld, moet u deze dienovereenkomstig bijwerken.

## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://aka.ms/AMLFree) voordat u begint.

- Er moet een AzureMachine Learning-werkruimte, een lokale map met uw scripts en de Azure Machine Learning SDK voor Python worden geïnstalleerd. Zie [Een ontwikkelomgeving configureren](how-to-configure-environment.md)voor meer informatie over het installeren ervan.

- U hebt een getraind machinelearningmodel nodig om te worden geïmplementeerd op AKS. Als u geen model hebt, raadpleegt u de zelfstudie van het [classificatiemodel Treinafbeelding.](tutorial-train-models-with-aml.md)

- Je hebt een AKS-cluster nodig. Zie Hoe te implementeren [en waar](how-to-deploy-and-where.md)te worden geïmplementeerd voor informatie over het maken van een en implementeren.

- [Stel uw omgeving in](how-to-configure-environment.md) en installeer de [Azure Machine Learning Monitoring SDK.](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen

U gegevensverzameling inschakelen, ongeacht het model dat u implementeert via Azure Machine Learning of andere hulpprogramma's.

Als u het verzamelen van gegevens wilt inschakelen, moet u:

1. Open het scorebestand.

1. Voeg de [volgende code](https://aka.ms/aml-monitoring-sdk) boven aan het bestand toe:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declareer uw variabelen voor `init` het verzamelen van gegevens in uw functie:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* is een optionele parameter. U hoeft het niet te gebruiken als uw model het niet nodig heeft. Gebruik van *CorrelationId* helpt u gemakkelijker in kaart te brengen met andere gegevens, zoals *LoanNumber* of *CustomerId.*
    
    De parameter *Identifier* wordt later gebruikt voor het bouwen van de mapstructuur in uw blob. U het gebruiken om ruwe gegevens te onderscheiden van verwerkte gegevens.

1. Voeg de volgende regels `run(input_df)` code toe aan de functie:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Het verzamelen van gegevens wordt *niet* automatisch ingesteld op **true** wanneer u een service implementeert in AKS. Werk uw configuratiebestand bij, zoals in het volgende voorbeeld:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    U Application Insights ook inschakelen voor servicebewaking door deze configuratie te wijzigen:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Zie Hoe u een nieuwe afbeelding wilt implementeren en waar u een nieuwe afbeelding wilt maken en het machine learning-model wilt [implementeren.](how-to-deploy-and-where.md)

Als u al een service hebt met de afhankelijkheden die zijn geïnstalleerd in uw omgevingsbestand en het scorebestand, schakelt u het verzamelen van gegevens in door de volgende stappen te volgen:

1. Ga naar [Azure Machine Learning](https://ml.azure.com).

1. Open uw werkruimte.

1. Selecteer **Implementaties** > **Selecteer service** > **Bewerken**.

   ![De service bewerken](././media/how-to-enable-data-collection/EditService.PNG)

1. Selecteer in **Geavanceerde instellingen**de optie **Application Insights-diagnostiek en gegevensverzameling inschakelen.**

1. Selecteer **Bijwerken** om de wijzigingen toe te passen.

## <a name="disable-data-collection"></a>Gegevens verzamelen uitschakelen

U op elk gewenst moment stoppen met het verzamelen van gegevens. Gebruik Python-code of Azure Machine Learning om het verzamelen van gegevens uit te schakelen.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Optie 1 - Gegevensverzameling uitschakelen in Azure Machine Learning

1. Meld u aan bij [Azure Machine Learning](https://ml.azure.com).

1. Open uw werkruimte.

1. Selecteer **Implementaties** > **Selecteer service** > **Bewerken**.

   [![De optie Bewerken selecteren](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. In **Geavanceerde instellingen**u de diagnose en **gegevensverzameling van Application Insights inschakelen.**

1. Selecteer **Bijwerken** om de wijziging toe te passen.

U hebt ook toegang tot deze instellingen in uw werkruimte in [Azure Machine Learning.](https://ml.azure.com)

### <a name="option-2---use-python-to-disable-data-collection"></a>Optie 2 - Python gebruiken om gegevensverzameling uit te schakelen

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Uw gegevens valideren en analyseren

U een hulpmiddel kiezen met uw voorkeur om de gegevens te analyseren die zijn verzameld in uw Blob-opslag.

### <a name="quickly-access-your-blob-data"></a>Snel toegang tot uw blobgegevens

1. Meld u aan bij [Azure Machine Learning](https://ml.azure.com).

1. Open uw werkruimte.

1. Selecteer **Opslag**.

    [![De optie Opslag selecteren](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Volg het pad naar de uitvoergegevens van de blob met deze syntaxis:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Modelgegevens analyseren met Power BI

1. Power [BI-bureaublad](https://www.powerbi.com)downloaden en openen .

1. Selecteer **Gegevens opvragen** en selecteer [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Power BI-blob-instelling](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Voeg de naam van uw opslagaccount toe en voer uw opslagsleutel in. U deze informatie vinden door **Instellingen** > **Toegangstoetsen** in uw blob te selecteren.

1. Selecteer de **modelgegevenscontainer** en selecteer **Bewerken**.

    [![Power BI Navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klik in de queryeditor onder de kolom **Naam** en voeg uw opslagaccount toe.

1. Voer uw modelpad in het filter in. Als u alleen bestanden van een bepaald jaar of maand wilt bekijken, vouwt u het filterpad uit. Als u bijvoorbeeld alleen naar gegevens van maart wilt kijken, gebruikt u dit filterpad:

   /modeldata/\<subscriptionid\<\<>/ resourcegroupname>/\<workspacename>/ webservicename\<>/ modelname>/\<modelversion>/\<designation>/year\<>/3

1. Filter de gegevens die voor u relevant zijn op basis **van naamwaarden.** Als u voorspellingen en invoer hebt opgeslagen, moet u voor elk query een query maken.

1. Selecteer de neerwaartse dubbele pijlen naast de kolomkop **Inhoud** om de bestanden te combineren.

    [![Power BI-inhoud](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecteer **OK**. De gegevens preloads.

    [![Power BI-combinatiebestanden](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecteer **Sluiten en toepassen**.

1. Als u invoer en voorspellingen hebt toegevoegd, worden uw tabellen automatisch geordend op **RequestId-waarden.**

1. Begin met het bouwen van uw aangepaste rapporten op uw modelgegevens.

### <a name="analyze-model-data-using-azure-databricks"></a>Modelgegevens analyseren met Azure Databricks

1. Maak een [Azure Databricks-werkruimte](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Ga naar uw Databricks-werkruimte.

1. Selecteer **Gegevens uploaden**in uw werkruimte Databricks .

    [![De optie Gegevensstenen uploaden selecteren](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecteer **Nieuwe tabel maken** en selecteer Andere **gegevensbronnen** > **azure blob-opslagtabel** > **maken in notitieblok**.

    [![Databricks tabel creatie](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Werk de locatie van uw gegevens bij. Hier volgt een voorbeeld:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks-installatie](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Volg de stappen op de sjabloon om uw gegevens weer te geven en te analyseren.
