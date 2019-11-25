---
title: 'Tutorial: Stream Analytics at the edge - Azure IoT Edge'
description: In this tutorial, you deploy Azure Stream Analytics as a module to an IoT Edge device
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7fbbe32efcedd4fa2635db1cc21f7ce98557515b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452531"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Tutorial: Deploy Azure Stream Analytics as an IoT Edge module

Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. Met Azure IoT Edge kunt u de logica van [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) verplaatsen naar het apparaat zelf. Door telemetriestromen te verwerken kunt u de hoeveelheid geüploade gegevens verkleinen en tijd besparen bij het reageren op inzichten waarvoor een actie kan worden uitgevoerd.

Azure IoT Edge en Azure Stream Analytics zijn geïntegreerd, zodat u een Azure Stream Analytics-taak kunt maken in Azure Portal en deze vervolgens zonder aanvullende code kunt implementeren als een IoT Edge-module.  

Azure Stream Analytics provides a richly structured query syntax for data analysis, both in the cloud and on IoT Edge devices. For more information, see [Azure Stream Analytics documentation](../stream-analytics/stream-analytics-edge.md).

Met de Stream Analytics-module in deze zelfstudie wordt gedurende 30 seconden (lopende telling) de gemiddelde temperatuur gemeten. Wanneer het gemiddelde de 70 bereikt, wordt met de module voor het apparaat een waarschuwing verzonden dat er actie moet worden ondernomen. In dit geval bestaat deze actie uit het opnieuw instellen van de gesimuleerde temperatuursensor. In een productieomgeving kunt u deze functie gebruiken om een computer uit te schakelen of om voorzorgsmaatregelen te nemen wanneer de temperatuur een gevaarlijk niveau bereikt. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Stream Analytics-taak maken om gegevens te verwerken.
> * Verbinding maken tussen de nieuwe Azure Stream Analytics-taak en andere IoT Edge-modules.
> * De Azure Stream Analytics-taak implementeren op een IoT Edge-apparaat vanuit Azure Portal.

<center>

![Diagram - Tutorial architecture, stage and deploy ASA job](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* You can use an Azure virtual machine as an IoT Edge device by following the steps in the quickstart for [Linux](quickstart-linux.md) or [Windows devices](quickstart.md).

Cloudresources:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Een Azure Stream Analytics-taak maken

In this section, you create an Azure Stream Analytics job that will do the following steps:
* Receive data from your IoT Edge device.
* Query the telemetry data for values outside a set range.
* Take action on the IoT Edge device based on the query results. 

### <a name="create-a-storage-account"></a>Maak een opslagaccount

Wanneer u een Azure Stream Analytics-taak maakt die op een IoT Edge-apparaat moet worden uitgevoerd, moet deze worden opgeslagen op een manier die door het apparaat kan worden aangeroepen. U kunt een bestaand Azure-opslagaccount gebruiken of een nieuw maken. 

1. In the Azure portal, go to **Create a resource** > **Storage** > **Storage account**. 

1. Geef de volgende waarden op om uw opslagaccount te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Abonnement | Kies het hetzelfde abonnement als uw IoT-hub. |
   | Resourcegroep | Het wordt aangeraden om dezelfde resourcegroep te gebruiken voor alle test-resources die u maakt met de snelstartgidsen en zelfstudies voor IoT Edge, zoals **IoTEdgeResources**. |
   | Naam | Voer een unieke naam in voor het opslagaccount. | 
   | Locatie | Kies een locatie dicht bij u in de buurt. |


1. Keep the default values for the other fields and select **Review + Create**.

1. Review your settings then select **Create**.

### <a name="create-a-new-job"></a>Een nieuwe taak maken

1. In the Azure portal, go to **Create a resource** > **Internet of Things** > **Stream Analytics job**.

1. Geef de volgende waarden op om de taak te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Taaknaam | Geef de taak een naam. Bijvoorbeeld **IoTEdgeJob**. | 
   | Abonnement | Kies het hetzelfde abonnement als uw IoT-hub. |
   | Resourcegroep | Het wordt aangeraden om dezelfde resourcegroep te gebruiken voor alle test-resources die u maakt met de snelstartgidsen en zelfstudies voor IoT Edge, zoals **IoTEdgeResources**. |
   | Locatie | Kies een locatie dicht bij u in de buurt. | 
   | Hostingomgeving | Selecteer **Edge**. |
 
1. Selecteer **Maken**.

### <a name="configure-your-job"></a>Uw taak configureren

Zodra uw Stream Analytics-taak is gemaakt in de Azure-portal, kunt u deze configureren met invoer, uitvoer en een query die moeten worden uitgevoerd op de gegevens die worden doorgegeven. 

Met behulp van de drie elementen invoer, uitvoer en query wordt in deze sectie een taak gemaakt waarbij temperatuurgegevens van het IoT Edge-apparaat worden ontvangen. Deze gegevens worden in een tijdvenster met een lopende telling van 30 seconden geanalyseerd. Als de gemiddelde temperatuur in dit tijdvenster de 70 graden overschrijdt, wordt een waarschuwing naar het IoT Edge-apparaat verzonden. In de volgende sectie, waarin u de taak gaat implementeren, moet u opgeven waar de gegevens precies vandaan komen en waar ze naartoe gaan.  

1. Navigeer naar uw Stream Analytics-taak in de Azure-portal. 

1. Onder **Taaktopologie** selecteert u **Invoer** en vervolgens **Stroominvoer toevoegen**.

   ![Azure Stream Analytics - add input](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Kies **Edge Hub** in de vervolgkeuzelijst.

1. Voer in het deelvenster **Nieuwe invoer** **temperatuur** in als de invoeralias. 

1. Houd de standaardwaarden voor de andere velden aan en selecteer **Opslaan**.

1. Open onder **Taaktopologie** de optie **Uitvoer** en selecteer vervolgens **Toevoegen**.

   ![Azure Stream Analytics - add output](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Kies **Edge Hub** in de vervolgkeuzelijst.

1. Voer in het deelvenster **Nieuwe uitvoer** **waarschuwing** in als de uitvoeralias. 

1. Houd de standaardwaarden voor de andere velden aan en selecteer **Opslaan**.

1. Selecteer **Query** onder **Taaktopologie**.

1. Vervang de standaardtekst door de volgende query. De SQL-code verzendt een opdracht voor opnieuw instellen naar de waarschuwingsuitvoer als de gemiddelde temperatuur van de machine in een tijdvenster van 30 seconden de 70 graden bereikt. De opdracht voor opnieuw instellen is vooraf in de sensor geprogrammeerd als actie die kan worden uitgevoerd. 

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

1. Selecteer **Opslaan**.

### <a name="configure-iot-edge-settings"></a>Instellingen voor IoT Edge configureren

Als u uw Stream Analytics-taak wilt voorbereiden om te worden geïmplementeerd op een IoT Edge-apparaat, moet u de taak aan een container in een opslagaccount koppelen. Wanneer u uw taak gaat implementeren, wordt de taakdefinitie geëxporteerd naar de opslagcontainer. 

1. Under **Configure**, select **Storage account settings** then select **Add storage account**. 

   ![Azure Stream Analytics - add storage account](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Select the **Storage account** that you created at the beginning of this tutorial from the drop-down menu.

1. Selecteer in het veld **Container** de optie **Nieuw maken** en geef een naam op voor de opslagcontainer. 

1. Selecteer **Opslaan**. 

## <a name="deploy-the-job"></a>De taak implementeren

U bent nu klaar om de Azure Stream Analytics-taak te implementeren op uw IoT Edge-apparaat. 

In deze sectie gaat u de wizard **Modules instellen** in de Azure-portal gebruiken om een *distributiemanifest* te maken. Het distributiemanifest is een JSON-bestand waarin alle modules worden beschreven die op een apparaat worden geïmplementeerd, waarin de containerregisters worden beschreven waarin installatiekopieën van de modules worden bewaard, hoe de modules moeten worden beheerd en hoe de modules met elkaar kunnen communiceren. Uw IoT Edge-apparaat haalt het distributiemanifest op uit IoT Hub en gebruikt vervolgens de informatie die zich daarin bevindt, om alle modules die eraan zijn toegewezen te implementeren en te configureren. 

In deze zelfstudie gaat u twee modules implementeren. The first is **SimulatedTemperatureSensor**, which is a module that simulates a temperature and humidity sensor. De tweede is uw Stream Analytics-taak. De sensormodule levert de gegevensstroom aan die door de taakquery wordt geanalyseerd. 

1. Ga in Azure Portal naar uw IoT-hub.

1. Go to **IoT Edge**, and then open the details page for your IoT Edge device.

1. Selecteer **Modules instellen**.  

1. If you previously deployed the SimulatedTemperatureSensor module on this device, it might autopopulate. Als dit niet het geval is, voegt u de module toe met behulp van de volgende stappen:

   1. Klik op **Toevoegen** en selecteer **IoT Edge-module**.
   1. For the name, type **SimulatedTemperatureSensor**.
   1. Voer **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** in als de URI voor de installatiekopie. 
   1. Laat de overige instellingen ongewijzigd en selecteer **Opslaan**.

1. Voeg uw Azure Stream Analytics Edge-taak toe met behulp van de volgende stappen:

   1. Klik op **Toevoegen** en selecteer **Azure Stream Analytics-module**.
   1. Selecteer uw abonnement en de Azure Stream Analytics-taak die u hebt gemaakt. 
   1. Selecteer **Opslaan**.

   Once you save your changes, the details of your Stream Analytics job are published to the storage container that you created. 

1. When the Stream Analytics module is added to the list of modules, select **Configure** to see how it's structured. 

   De URI van de installatiekopie verwijst naar een standaardinstallatiekopie van Azure Stream Analytics. This one image is used for every Stream Analytics module that gets deployed to an IoT Edge device. 

   De moduledubbel wordt geconfigureerd met een gewenste eigenschap genaamd **ASAJobInfo**. De waarde van deze eigenschap verwijst naar de taakdefinitie in uw opslagcontainer. This property is how the Stream Analytics image is configured with your specific job details. 

   By default, the Stream Analytics module takes the same name as the job it's based on. You can change the module name on this page if you like, but it's not necessary. 

1. Close the module configuration page.

1. Noteer de naam van uw Stream Analytics-module omdat u deze in de volgende stap nodig hebt, en selecteer daarna **Volgende** om door te gaan.

1. Vervang de standaardwaarde in **Routes** door de volgende code. Werk alle drie de instanties van _{moduleName}_ bij met de naam van de Azure Stream Analytics-module. 

    ```json
    {
        "routes": {
            "telemetryToCloud": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream",
            "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream",
            "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/SimulatedTemperatureSensor/inputs/control\")",
            "telemetryToAsa": "FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")"
        }
    }
    ```

   De routes die u hier declareert bepalen de gegevensstroom die via het IoT Edge-apparaat gaat. The telemetry data from SimulatedTemperatureSensor are sent to IoT Hub and to the **temperature** input that was configured in the Stream Analytics job. The **alert** output messages are sent to IoT Hub and to the SimulatedTemperatureSensor module to trigger the reset command. 

1. Selecteer **Next**.

1. In the **Review Deployment** step, you can see how the information you provided in the wizard is converted into a JSON deployment manifest. When you're done reviewing the manifest, select **Submit**.

1. Ga terug naar de detailpagina van het apparaat en selecteer vervolgens **Vernieuwen**.  

    You should see the new Stream Analytics module running, along with the IoT Edge agent and IoT Edge hub modules. It may take a few minutes for the information to reach your IoT Edge device, and then for the new modules to start. If you don't see the modules running right away, continue refreshing the page.

    ![SimulatedTemperatureSensor and ASA module reported by device](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Gegevens weergeven

Now you can go to your IoT Edge device to check out the interaction between the Azure Stream Analytics module and the SimulatedTemperatureSensor module.

1. Controleer of alle modules worden uitgevoerd in Docker:

   ```cmd/sh
   iotedge list  
   ```
   <!--
   ![Docker output](./media/tutorial-deploy-stream-analytics/docker_output.png)
   -->
1. Bekijk alle systeemlogboeken en metrische gegevens. Gebruik de naam van de Stream Analytics-module:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. View the reset command affect the SimulatedTemperatureSensor by viewing the sensor logs:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   You can watch the machine's temperature gradually rise until it reaches 70 degrees for 30 seconds. Vervolgens worden met de Stream Analytics-module de beginwaarden ingesteld. De temperatuur van de machine zakt nu weer naar 21 graden. 

   ![Uitvoer van de opdracht in de logboeken van de module opnieuw instellen](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Resources opschonen 

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 
 
[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Stream Analytics-taak geconfigureerd om gegevens van uw IoT Edge-apparaat te analyseren. Vervolgens hebt u deze Azure Stream Analytics-module op een IoT Edge-apparaat geladen om temperatuurstijgingen lokaal te verwerken en erop te reageren, en de samengevoegde gegevensstroom te verzenden naar de cloud. Ga verder met de andere zelfstudies om te zien hoe u met Azure IoT Edge meer oplossingen voor uw bedrijf kunt maken.

> [!div class="nextstepaction"] 
> [Een Azure Machine Learning-model implementeren als een module](tutorial-deploy-machine-learning.md)
