---
title: Zelfstudie - Azure Machine Learning implementeren op een apparaat met Azure IoT Edge
description: In deze zelfstudie maakt u een Azure Machine Learning-model en implementeert u het als module naar een randapparaat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76773004"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Zelfstudie: Azure Machine Learning implementeren als een IoT Edge-module (preview)

Gebruik Azure Notebooks om een machine learning-module te ontwikkelen en te implementeren op een Linux-apparaat met Azure IoT Edge.
U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. Deze zelfstudie laat u stapsgewijs zien hoe u een Azure Machine Learning-module kunt implementeren waarmee wordt voorspeld wanneer een apparaat mislukt op basis van gesimuleerde machinetemperatuurgegevens. Zie [Azure Machine Learning-documentatie](../machine-learning/how-to-deploy-and-where.md)voor meer informatie over Azure Machine Learning op IoT Edge.

>[!NOTE]
>Azure Machine Learning-modules in Azure IoT Edge zijn in de openbare preview.

De Azure Machine Learning-module die u in deze zelfstudie maakt, leest de omgevingsgegevens die door uw apparaat zijn gegenereerd en markeert de berichten als afwijkend of niet.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Azure Machine Learning-module maken
> * Een module-container naar een Azure-containerregister zenden
> * Een Azure Machine Learning-module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Een Azure IoT Edge-apparaat:

* U een Virtuele Azure-machine als Een IoT Edge-apparaat gebruiken door de stappen in de quickstart voor [Linux](quickstart-linux.md)te volgen.
* De Azure Machine Learning-module biedt geen ondersteuning voor Windows-containers.
* De Azure Machine Learning-module biedt geen ondersteuning voor ARM-processors.

Cloudresources:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een Azure Machine Learning-werkruimte. Volg de instructies in [Gebruik de Azure-portal om aan de slag te gaan met Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) om er een te maken en te leren hoe u deze gebruiken.
  * Noteer de naam van de werkruimte, de resourcegroep en de abonnements-id. Deze waarden zijn allemaal beschikbaar in het werkruimteoverzicht in de Azure-portal. U gebruikt deze waarden later in de zelfstudie om een Azure-notitieblok te verbinden met uw werkruimtebronnen.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning-module maken en implementeren

In deze sectie converteert u getrainde machine learning-modelbestanden en in een Azure Machine Learning-container. Alle onderdelen die vereist zijn voor de Docker-installatiekopie bevinden zich in de [AI-werkset voor Azure IoT Edge Git-repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Volg deze stappen om die opslagplaats te uploaden naar Microsoft Azure-notitieblokken om de container te maken en deze naar Azure Container Registry te schuiven.

1. Navigeer naar uw Azure Notebooks-projecten. U er komen vanuit uw Azure Machine Learning-werkruimte in de [Azure-portal](https://portal.azure.com) of door u aan te melden bij [Microsoft Azure-notitieblokken](https://notebooks.azure.com/home/projects) met uw Azure-account.

2. Selecteer **GitHub Repo uploaden**.

3. Geef de volgende GitHub-repository-naam op: `Azure/ai-toolkit-iot-edge`. Schakel het selectievakje **Openbaar** uit als u uw project privé wilt houden. Selecteer **Importeren**.

4. Zodra de import is voltooid, navigeert u naar het nieuwe **ai-toolkit-iot-edge-project** en opent u de **map met de detectiemap voor iot-randanomaliedetectie.**

5. Controleer of uw project wordt uitgevoerd. Als dit niet het zo is, selecteert u **Uitvoeren op vrije gegevens .**

   ![Draaien op gratis compute](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Open het **bestand aml_config/config.json.**

7. Bewerk het config-bestand om de waarden voor uw Azure-abonnements-id, een brongroep in uw abonnement en de naam van uw Azure Machine Learning-werkruimte op te nemen. U al deze waarden ophalen in de sectie **Overzicht** van uw werkruimte in Azure.

8. Sla het config-bestand op.

9. Open het **00-anomalie-detectie-tutorial.ipynb-bestand.**

10. Selecteer de python **3.6-kernel** wanneer u de python 3.6-kernel wilt **instellen.**

11. Bewerk de eerste cel in het notitieblok volgens de instructies in de opmerkingen. Gebruik dezelfde brongroep, abonnements-ID en werkruimtenaam die u aan het config-bestand hebt toegevoegd.

12. Voer de cellen in het notitieblok **Run** uit `Shift + Enter`door ze te selecteren en Uitvoeren of indrukken te selecteren.

    >[!TIP]
    >Sommige cellen in het zelfstudienotitieblok voor anomaliedetectie zijn optioneel, omdat ze bronnen maken die sommige gebruikers al dan niet hebben, zoals een IoT-hub. Als u uw bestaande brongegevens in de eerste cel plaatst, ontvangt u fouten als u de cellen uitvoert die nieuwe bronnen maken omdat Azure geen dubbele resources maakt. Dit is prima, en u de fouten negeren of deze optionele secties volledig overslaan.

Door alle stappen in het notitieblok te voltooien, hebt u een anomaliedetectiemodel getraind, het als een Docker-containerafbeelding gebouwd en die afbeelding naar Azure Container Registry gepusht. Vervolgens hebt u het model getest en uiteindelijk geïmplementeerd op uw IoT Edge-apparaat.

## <a name="view-container-repository"></a>Containeropslagplaats weergeven

Controleer of uw containerafbeelding is gemaakt en opgeslagen in het Azure-containerregister dat is gekoppeld aan uw machine learning-omgeving. Het notitieblok dat u in de vorige sectie hebt gebruikt, heeft automatisch de containerafbeelding en de registerreferenties aan uw IoT Edge-apparaat verstrekt, maar u moet weten waar ze zijn opgeslagen, zodat u de informatie later zelf vinden.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar de werkruimte van uw Machine Learning-service.

2. In de sectie **Overzicht** worden de details van de werkruimte en de bijbehorende bronnen weergegeven. Selecteer de **registerwaarde,** die uw werkruimtenaam moet zijn, gevolgd door willekeurige getallen.

3. Selecteer **repositories**in het containerregister. U ziet een opslagplaats genaamd **tempanomalydetection** die is gemaakt door het notitieblok dat u in de eerdere sectie hebt uitgevoerd.

4. Selecteer **tijdelijke anomaliedetectie**. U moet zien dat de repository één tag heeft: **1**.

   Nu u de naam van het register, de naam van de opslagplaats en de tag kent, kent u het volledige afbeeldingspad van de container. Afbeeldingspaden zien eruit als ** \<\>registry_name .azurecr.io/tempanomalydetection:1**. U kunt het pad naar de installatiekopie gebruiken om deze container op IoT Edge-apparaten te implementeren.

5. Selecteer **Access-sleutels**in het containerregister . U ziet een aantal toegangsreferenties, waaronder **de aanmeldingsserver** en de **gebruikersnaam**en **het wachtwoord** voor een beheerdersgebruiker.

   Deze referenties kunnen in het distributiemanifest worden opgenomen om uw IoT Edge-apparaat toegang te geven om containerinstallatiekopieën uit het register op te halen.

Nu weet u waar de Machine Learning-containerafbeelding is opgeslagen. In de volgende sectie worden stappen doorlopen om de container te bekijken die als een module op uw IoT Edge-apparaat wordt uitgevoerd.

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

U kunt berichten weergeven die worden gegenereerd vanaf elke IoT Edge-module, en u kunt berichten weergeven die worden bezorgd bij de IoT Edge-hub.

### <a name="view-data-on-your-iot-edge-device"></a>Gegevens weergeven op het IoT Edge-apparaat

Op het IoT Edge-apparaat kunt u de berichten weergeven die worden verzonden vanaf elke afzonderlijke module.

Mogelijk moet u `sudo` verhoogde machtigingen gebruiken `iotedge` om opdrachten uit te voeren. Als u zich afmeldt en zich weer aanmeldt bij uw apparaat, worden uw machtigingen automatisch bijgewerkt.

1. Geef alle modules op het IoT Edge-apparaat weer.

   ```cmd/sh
   iotedge list
   ```

2. Geef de berichten weer die zijn verzonden vanaf een specifiek apparaat. Gebruik de modulenaam uit de uitvoer van de vorige opdracht.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Gegevens weergeven die binnenkomen bij de IoT-hub

U de device-to-cloudberichten bekijken die uw IoT-hub ontvangt met de [Azure IoT Hub-extensie voor Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

In de volgende stappen ziet u hoe u Visual Studio Code kunt instellen om apparaat-naar-cloud-berichten te controleren die binnenkomen bij de IoT-hub.

1. Selecteer in Visual Studio Code **IoT Hub-apparaten**.

2. Selecteer in het menu **...** en vervolgens **Set IoT Hub-verbindingsreeksen**.

   ![IoT Hub-verbindingsreeks instellen](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Voer in het tekstvak dat bovenaan de pagina wordt geopend de iothubowner-verbindingsreeks in voor uw IoT Hub. Uw IoT Edge-apparaat moet worden weergegeven in de lijst met IoT Hub-apparaten.

4. Selecteer **...** selecteer opnieuw **Het ingebouwde gebeurteniseindpunt starten**.

5. Bekijk de berichten die de tempSensor elke vijf seconden verzendt. De berichttekst bevat een eigenschap die **anomalie**wordt genoemd, die de machinelearningmodule een ware of valse waarde biedt. De eigenschap **AzureMLResponse** bevat de waarde ‘OK’ als het model is uitgevoerd.

   ![Azure Machine Learning-reactie in berichttekst](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module geïmplementeerd die werd aangedreven door Azure Machine Learning. U kunt doorgaan met elke andere zelfstudie om te leren waarmee Azure IoT Edge u nog meer kan helpen uw gegevens om te zetten in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Afbeeldingen classificeren met Custom Vision Service](tutorial-deploy-custom-vision.md)
