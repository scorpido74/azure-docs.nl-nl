---
title: 'Tutorial: Deploy Azure Machine Learning to a device - Azure IoT Edge'
description: In this tutorial, you create an Azure Machine Learning model, then deploy it as a module to an edge device
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f1bd4ce5b701652318679f3277bc7c9109fa529
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457635"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Zelfstudie: Azure Machine Learning implementeren als een IoT Edge-module (preview)

Use Azure Notebooks to develop a machine learning module and deploy it to a Linux device running Azure IoT Edge. 

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. Deze zelfstudie laat u stapsgewijs zien hoe u een Azure Machine Learning-module kunt implementeren waarmee wordt voorspeld wanneer een apparaat mislukt op basis van gesimuleerde machinetemperatuurgegevens. For more information about Azure Machine Learning on IoT Edge, see [Azure Machine Learning documentation](../machine-learning/service/how-to-deploy-to-iot.md).

De Azure Machine Learning-module die u in deze zelfstudie maakt, leest de omgevingsgegevens die door uw apparaat zijn gegenereerd en markeert de berichten als afwijkend of niet.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Machine Learning-module maken
> * Een module-container naar een Azure-containerregister zenden
> * Een Azure Machine Learning-module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven

>[!NOTE]
>Azure Machine Learning-modules in Azure IoT Edge zijn in de openbare preview.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md).
* The Azure Machine Learning module doesn't support Windows containers.
* The Azure Machine Learning module doesn't support ARM processors.

Cloudresources:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een Azure Machine Learning-werkruimte. Follow the instructions in [Use the Azure portal to get started with Azure Machine Learning](../machine-learning/service/quickstart-get-started.md) to create one and learn how to use it.
   * Make a note of the workspace name, resource group, and subscription ID. These values are all available on the workspace overview in the Azure portal. You'll use these values later in the tutorial to connect an Azure notebook to your workspace resources. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Create and deploy Azure Machine Learning module

In this section, you convert trained machine learning model files and into an Azure Machine Learning container. Alle onderdelen die vereist zijn voor de Docker-installatiekopie bevinden zich in de [AI-werkset voor Azure IoT Edge Git-repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Follow these steps to upload that repository into Microsoft Azure Notebooks to create the container and push it to Azure Container Registry.


1. Navigate to your Azure Notebooks projects. You can get there from your Azure Machine Learning workspace in the [Azure portal](https://portal.azure.com) or by signing in to [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) with your Azure account.

2. Select **Upload GitHub Repo**.

3. Provide the following GitHub repository name: `Azure/ai-toolkit-iot-edge`. Uncheck the **Public** box if you want to keep your project private. Select **Import**. 

4. Once the import is finished, navigate into the new **ai-toolkit-iot-edge** project and open the **IoT Edge anomaly detection tutorial** folder. 

5. Verify that your project is running. If not, select **Run on Free Compute**.

   ![Run on free compute](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Open the **aml_config/config.json** file.

7. Edit the config file to include the values for your Azure subscription ID, a resource group in your subscription, and your Azure Machine Learning workspace name. You can get all these values from the **Overview** section of your workspace in Azure. 

8. Save the config file.

9. Open the **00-anomaly-detection-tutorial.ipynb** file.

10. When prompted, select the **Python 3.6** kernel then select **Set Kernel**.

11. Edit the first cell in the notebook according to the instructions in the comments. Use the same resource group, subscription ID, and workspace name that you added to the config file.

12. Run the cells in the notebook by selecting them and selecting **Run** or pressing `Shift + Enter`.

    >[!TIP]
    >Some of the cells in the anomaly detection tutorial notebook are optional, because they create resources that some users may or may not have yet, like an IoT Hub. If you put your existing resource information in the first cell, you'll receive errors if you run the cells that create new resources because Azure won't create duplicate resources. This is fine, and you can ignore the errors or skip those optional sections entirely. 

By completing all the steps in the notebook, you trained an anomaly detection model, built it as a Docker container image, and pushed that image to Azure Container Registry. Then, you tested the model and finally deployed it to your IoT Edge device. 

## <a name="view-container-repository"></a>View container repository

Check that your container image was successfully created and stored in the Azure container registry associated with your machine learning environment. The notebook that you used in the previous section automatically provided the container image and the registry credentials to your IoT Edge device, but you should know where they're stored so that you can find the information yourself later. 

1. In the [Azure portal](https://portal.azure.com), navigate to your Machine Learning service workspace. 

2. The **Overview** section lists the workspace details as well its associated resources. Select the **Registry** value, which should be your workspace name followed by random numbers. 

3. In the container registry, select **Repositories**. You should see a repository called **tempanomalydetection** that was created by the notebook you ran in the earlier section. 

4. Select **tempanomalydetection**. You should see that the repository has one tag: **1**. 

   Now that you know the registry name, repository name, and tag, you know the full image path of the container. Image paths look like **\<registry_name\>.azurecr.io/tempanomalydetection:1**. You can use the image path to deploy this container to IoT Edge devices. 

5. In the container registry, select **Access keys**. You should see a number of access credentials, including **Login server** and the **Username**, and **Password** for an admin user.

   These credentials can be included in the deployment manifest to give your IoT Edge device access to pull container images from the registry. 

Now you know where the Machine Learning container image is stored. The next section walks through steps to view the container running as a module on your IoT Edge device. 

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

U kunt berichten weergeven die worden gegenereerd vanaf elke IoT Edge-module, en u kunt berichten weergeven die worden bezorgd bij de IoT Edge-hub.

### <a name="view-data-on-your-iot-edge-device"></a>Gegevens weergeven op het IoT Edge-apparaat

Op het IoT Edge-apparaat kunt u de berichten weergeven die worden verzonden vanaf elke afzonderlijke module.

You may need to use `sudo` for elevated permissions to run `iotedge` commands. Signing out and signing back in to your device automatically updates your permissions.

1. Geef alle modules op het IoT Edge-apparaat weer.

   ```cmd/sh
   iotedge list
   ```

2. Geef de berichten weer die zijn verzonden vanaf een specifiek apparaat. Gebruik de modulenaam uit de uitvoer van de vorige opdracht.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Gegevens weergeven die binnenkomen bij de IoT-hub

U kunt de apparaat-naar-cloud-berichten die de IoT-hub ontvangt, weergeven met behulp van de [Azure IoT Hub Toolkit-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (voorheen Azure IoT Toolkit-extensie).

In de volgende stappen ziet u hoe u Visual Studio Code kunt instellen om apparaat-naar-cloud-berichten te controleren die binnenkomen bij de IoT-hub.

1. Selecteer in Visual Studio Code **IoT Hub-apparaten**.

2. Selecteer in het menu **...** en vervolgens **Set IoT Hub-verbindingsreeksen**.

   ![IoT Hub-verbindingsreeks instellen](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Voer in het tekstvak dat bovenaan de pagina wordt geopend de iothubowner-verbindingsreeks in voor uw IoT Hub. Uw IoT Edge-apparaat moet worden weergegeven in de lijst met IoT Hub-apparaten.

4. Select **...** again then select **Start Monitoring Built-in Event Endpoint**.

5. Bekijk de berichten die de tempSensor elke vijf seconden verzendt. The message body contains a property called **anomaly**, which the machinelearningmodule provides with a true or false value. De eigenschap **AzureMLResponse** bevat de waarde ‘OK’ als het model is uitgevoerd.

   ![Azure Machine Learning response in message body](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module geïmplementeerd die werd aangedreven door Azure Machine Learning. U kunt doorgaan met elke andere zelfstudie om te leren waarmee Azure IoT Edge u nog meer kan helpen uw gegevens om te zetten in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Afbeeldingen classificeren met Custom Vision Service](tutorial-deploy-custom-vision.md)

