---
title: Gegevens verzamelen in uw productie modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van Azure Machine Learning invoermodel gegevens in een Azure Blob-opslag.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 3c747f25b92d9f165bfeb4468a0e263f102976f9
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350571"
---
# <a name="collect-data-for-models-in-production"></a>Verzamelen van gegevens voor modellen in productie

>[!IMPORTANT]
> Deze SDK wordt binnenkort buiten gebruik gesteld en wordt vervangen door vereenvoudigde [gegevens bewaking met Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In dit artikel leert u hoe u invoer model gegevens kunt verzamelen van Azure Machine Learning die u in azure Kubernetes-cluster (AKS) hebt geïmplementeerd in een Azure Blob-opslag. 

Eenmaal is ingeschakeld, wordt deze gegevens die u verzamelt helpt u:
* [Gegevens drift bewaken](how-to-monitor-data-drift.md) naarmate productie gegevens uw model binnenkomen

* Neem betere beslissingen op het opnieuw trainen of het model optimaliseren

* Opnieuw trainen van uw model met de verzamelde gegevens

## <a name="what-is-collected-and-where-does-it-go"></a>Wat wordt er verzameld en waar gaat het?

De volgende gegevens kunnen worden verzameld:
* Model **invoer** gegevens van webservices die zijn geïmplementeerd in Azure Kubernetes Cluster (AKS) (spraak, afbeeldingen en video zijn **niet** verzameld) 
  
* Model voorspellingen met invoergegevens van de productie.

> [!Note]
> Vooraf aggregatie of vooraf berekeningen op deze gegevens zijn niet deel uit van de service op dit moment.   

De uitvoer wordt opgeslagen in een Azure-Blob. Omdat de gegevens in een Azure-Blob wordt toegevoegd, kunt u vervolgens uw favoriete hulpprogramma voor de analyse uitvoeren. 

Het pad naar de uitvoergegevens in de blob met de volgende deze syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

- Een Azure Machine Learning-werk ruimte, een lokale map met uw scripts en de Azure Machine Learning SDK voor python geïnstalleerd. Informatie over het verkrijgen van deze vereisten met behulp van de [het configureren van een ontwikkelomgeving](how-to-configure-environment.md) document.

- Een getraind model voor machine learning om te worden geïmplementeerd naar Azure Kubernetes Service (AKS). Als u niet hebt, raadpleegt u de [classificeringsmodel voor de installatiekopie van de trein](tutorial-train-models-with-aml.md) zelfstudie.

- Een Azure Kubernetes Service-cluster. Zie voor meer informatie over het maken en implementeren op een de [implementeren en waar](how-to-deploy-and-where.md) document.

- [Instellen van uw omgeving](how-to-configure-environment.md) en installeer de [Monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen
Het verzamelen van gegevens kan worden ingeschakeld, ongeacht het model dat wordt geïmplementeerd via Azure Machine Learning of andere hulpprogram ma's. 

Als u wilt inschakelen, moet u naar:

1. Open het scoring-bestand. 

1. Voeg de [met de volgende code](https://aka.ms/aml-monitoring-sdk) aan de bovenkant van het bestand:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Declareer de variabelen van uw gegevens verzameling in uw `init()` functie:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* is een optionele parameter, u hoeft niet instellen als uw model niet worden vereist. Met een correlationId in plaats, helpt u om eenvoudiger met andere gegevens. (Voor beelden zijn: LoanNumber, CustomerId, enz.)
    
    *Id* wordt later gebruikt voor het bouwen van de mappenstructuur in uw Blob, kan worden gebruikt om te delen 'raw' gegevens ten opzichte van "verwerkt".

3.  Voeg de volgende regels code aan de `run(input_df)` functie:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Verzamelen van gegevens is **niet** automatisch ingesteld op **waar** wanneer u een service in AKS implementeert, dus u moet uw configuratiebestand bijwerken zoals: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    Application Insights voor het bewaken van de service kan ook worden ingeschakeld door deze configuratie te wijzigen:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Een nieuwe installatiekopie maken en implementeren van de service, Zie de [implementeren en waar](how-to-deploy-and-where.md) document.


Als u al een service met de afhankelijkheden die zijn geïnstalleerd hebt uw **omgevingsbestand** en **scoring-bestand**, verzamelen van gegevens door inschakelen:

1. Ga naar [Azure Portal](https://portal.azure.com).

1. Open de werkruimte.

1. Ga naar **implementaties** -> **Selecteer service** -> **bewerken**.

   ![Service bewerken](media/how-to-enable-data-collection/EditService.PNG)

1. In **geavanceerde instellingen**, schakel **inschakelen modelgegevensverzameling**. 

    [![het verzamelen van gegevens controleren](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In dit venster, u kunt ook 'Diagnose van Appinsights inschakelen' om bij te houden van de status van uw service.  

1. Selecteer **Update** de wijziging toepassen.


## <a name="disable-data-collection"></a>Gegevens verzamelen uitschakelen
U kunt stoppen met het verzamelen van gegevens elk gewenst moment. Python-code of de Azure-portal gebruiken om te verzamelen van gegevens uitschakelen.

+ Optie 1 - uitschakelen in Azure portal: 
  1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

  1. Open de werkruimte.

  1. Ga naar **implementaties** -> **Selecteer service** -> **bewerken**.

     [![Optie bewerken](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **geavanceerde instellingen**, schakel **inschakelen modelgegevensverzameling**. 

     [![Schakel het verzamelen van gegevens](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selecteer **Update** de wijziging toepassen.

  U kunt deze instellingen ook gebruiken op de [landings pagina van de werk ruimte (preview)](https://ml.azure.com).

+ Optie 2: Python gebruiken om te verzamelen van gegevens uitschakelen:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Uw gegevens valideren en te analyseren
U kunt een hulpprogramma van uw voorkeur voor het analyseren van de gegevens die zijn verzameld in uw Azure-Blob. 

Snel toegang krijgt tot de gegevens van uw blob:
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Open de werkruimte.
1. Klik op **opslag**.

    [![Opslag](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Volg het pad naar de uitvoergegevens in de blob met de volgende syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Modelgegevens via Power BI analyseren

1. [Power bi Desktop](https://www.powerbi.com) downloaden en openen

1. Selecteer **gegevens ophalen** en klikt u op [ **Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![PBI-Blob-instellingen](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Naam van uw opslagaccount toevoegen en voer de opslagsleutel van uw. U kunt deze informatie vinden in van de blob **instellingen** >> toegangssleutels. 

1. Selecteer de container **modeldata** en klikt u op **bewerken**. 

    [![PBI-Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. In de query-editor, klikt u op onder de kolom 'Name' en voeg uw Storage-account 1. Model-pad in het filter. Opmerking: als u kijken alleen bestanden van een bepaald jaar of maand wilt, alleen vouwt u het filterpad. Bijvoorbeeld, u kijkt gewoon in maart gegevens: / modeldata/abonnements-id > / resourcegroupname > / Werkruimtenaam > / webservicename > / modelname > / modelversion > / id > / jaar > / 3

1. Filter de gegevens die relevant is voor u op basis van **naam**. Als u opgeslagen **voorspellingen** en **invoer** , moet u een query per Maak.

1. Klik op de dubbele pijl gereserveerd de **inhoud** kolom de bestanden worden gecombineerd. 

    [![PBI-inhoud](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klik op OK en de gegevens worden geladen.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. U kunt nu klikken op **sluiten en toepassen** .

1.  Als u een invoer- en voorspellingen uw tabellen automatisch wordt toegevoegd correleren met **RequestId**.

1. Start het bouwen van uw aangepaste rapporten op gegevens van uw model.


### <a name="analyzing-model-data-using-databricks"></a>Analyseren van gegevens van het model met behulp van Databricks

1. Maak een [Databricks-werkruimte](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Ga naar uw Databricks-werkruimte. 

1. Selecteer in uw databricks werkruimte **gegevens uploaden**.

    [![DB uploaden](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Nieuwe tabel maken en selecteer **andere gegevensbronnen** Azure Blob Storage -> Create Table in de Notebook ->.

    [![DB-tabel](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. De locatie van uw gegevens worden bijgewerkt. Hier volgt een voorbeeld:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Met DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Volg de stappen op de sjabloon om te kunnen weergeven en analyseren van uw gegevens. 

## <a name="example-notebook"></a>Voorbeeld van de notebook

De [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) notebook concepten in dit artikel laat zien.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
