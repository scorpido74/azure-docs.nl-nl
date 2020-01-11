---
title: Zelf studie-Azure Machine Learning implementeren op een apparaat met behulp van Azure IoT Edge
description: In deze zelf studie maakt u een Azure Machine Learning model en implementeert u het als een module op een edge-apparaat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 47a84e11149d9c54d335fe09f3c56532f2aaf58b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862863"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Zelfstudie: Azure Machine Learning implementeren als een IoT Edge-module (preview)

Gebruik Azure Notebooks om een machine learning module te ontwikkelen en te implementeren op een Linux-apparaat met Azure IoT Edge. 

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. Deze zelfstudie laat u stapsgewijs zien hoe u een Azure Machine Learning-module kunt implementeren waarmee wordt voorspeld wanneer een apparaat mislukt op basis van gesimuleerde machinetemperatuurgegevens. Zie [Azure machine learning documentatie](../machine-learning/how-to-deploy-and-where.md)voor meer informatie over Azure Machine Learning op IOT Edge.

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

* U kunt een virtuele machine van Azure als IoT Edge apparaat gebruiken door de stappen in de Quick start voor [Linux](quickstart-linux.md)te volgen.
* De module Azure Machine Learning biedt geen ondersteuning voor Windows-containers.
* De Azure Machine Learning-module biedt geen ondersteuning voor ARM-processors.

Cloudresources:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een Azure Machine Learning-werkruimte. Volg de instructies in [het Azure Portal gebruiken om aan de slag te gaan met Azure machine learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) om er een te maken en te leren hoe u deze kunt gebruiken.
   * Noteer de naam van de werk ruimte, de resource groep en de abonnements-ID. Deze waarden zijn allemaal beschikbaar in het overzicht van de werk ruimte in de Azure Portal. U gebruikt deze waarden later in de zelf studie om een Azure-notebook te verbinden met uw werkruimte resources. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning module maken en implementeren

In deze sectie converteert u getrainde machine learning model bestanden en naar een Azure Machine Learning-container. Alle onderdelen die vereist zijn voor de Docker-installatiekopie bevinden zich in de [AI-werkset voor Azure IoT Edge Git-repo](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Volg deze stappen om die opslag plaats te uploaden naar Microsoft Azure Notebooks om de container te maken en deze naar Azure Container Registry te pushen.


1. Navigeer naar uw Azure Notebooks projecten. U kunt dit doen vanuit uw Azure Machine Learning-werk ruimte in de [Azure Portal](https://portal.azure.com) of door u aan te melden bij [Microsoft Azure notebooks](https://notebooks.azure.com/home/projects) met uw Azure-account.

2. Selecteer **github opslag plaats uploaden**.

3. Geef de volgende naam op voor de GitHub-opslag plaats: `Azure/ai-toolkit-iot-edge`. Schakel het selectie vakje **openbaar** uit als u uw project privé wilt laten blijven. Selecteer **Importeren**. 

4. Als het importeren is voltooid, gaat u naar het nieuwe project **AI-Toolkit-IOT-Edge** en opent u de map met de **zelf studie voor afwijkings detectie van IOT Edge** . 

5. Controleer of het project actief is. Als dat niet het geval is, selecteert u **uitvoeren op gratis reken kracht**.

   ![Uitvoeren op gratis compute](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Open het **aml_config/config.json** -bestand.

7. Bewerk het configuratie bestand om de waarden voor uw Azure-abonnements-ID, een resource groep in uw abonnement en uw Azure Machine Learning werkruimte naam op te nemen. U kunt al deze waarden ophalen uit de sectie **overzicht** van uw werk ruimte in Azure. 

8. Sla het configuratie bestand op.

9. Open het bestand met de **zelf studie. ipynb van 00-anomalie detectie** .

10. Als u hierom wordt gevraagd, selecteert u de **Python 3,6** -kernel en selecteert u **kernel instellen**.

11. Bewerk de eerste cel in het notitie blok volgens de instructies in de opmerkingen. Gebruik dezelfde resource groep, abonnements-ID en werkruimte naam die u aan het configuratie bestand hebt toegevoegd.

12. Voer de cellen in het notitie blok uit door ze te selecteren en **uitvoeren** te selecteren of op `Shift + Enter`te drukken.

    >[!TIP]
    >Sommige van de cellen in de zelf studie voor afwijkings detectie zijn optioneel, omdat ze resources maken die sommige gebruikers al dan niet kunnen hebben, zoals een IoT Hub. Als u de bestaande resource gegevens in de eerste cel plaatst, ontvangt u fouten als u de cellen uitvoert die nieuwe resources maken, omdat Azure geen dubbele resources maakt. Dit is een prima probleem en u kunt de fouten negeren of deze optionele secties volledig overs Laan. 

Door alle stappen in het notitie blok uit te voeren, hebt u een afwijkend detectie model getraind, gebouwd als een docker-container installatie kopie en gepusht die installatie kopie naar Azure Container Registry. Vervolgens hebt u het model getest en tot slot geïmplementeerd op uw IoT Edge-apparaat. 

## <a name="view-container-repository"></a>Container opslagplaats weer geven

Controleer of de container installatie kopie is gemaakt en opgeslagen in het Azure container Registry dat is gekoppeld aan uw machine learning omgeving. Het notitie blok dat u in de vorige sectie hebt gebruikt, heeft automatisch de container installatie kopie en de register referenties voor uw IoT Edge apparaat verstrekt, maar u moet weten waar deze zijn opgeslagen, zodat u de informatie later kunt vinden. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar de werk ruimte van uw machine learning-service. 

2. In het gedeelte **overzicht** worden de werkruimte details weer gegeven, evenals de bijbehorende resources. Selecteer de **register** waarde. dit moet de naam van uw werk ruimte zijn, gevolgd door wille keurige getallen. 

3. Selecteer **opslag**plaatsen in het container register. Als het goed is, ziet u een opslag plaats met de naam **tempanomalydetection** die is gemaakt door het notitie blok dat u in de vorige sectie hebt uitgevoerd. 

4. Selecteer **tempanomalydetection**. U ziet dat de opslag plaats één tag heeft: **1**. 

   Nu u de register naam, de naam van de opslag plaats en het label kent, weet u het volledige pad naar de installatie kopie van de container. Afbeeldings paden zien eruit als **\<registry_name\>. azurecr.io/tempanomalydetection:1**. U kunt het pad naar de installatiekopie gebruiken om deze container op IoT Edge-apparaten te implementeren. 

5. Selecteer **toegangs sleutels**in het container register. Als het goed is, ziet u een aantal toegangs referenties, inclusief **aanmeldings server** en de **gebruikers naam**en het **wacht woord** voor een gebruiker met beheerders rechten.

   Deze referenties kunnen in het distributiemanifest worden opgenomen om uw IoT Edge-apparaat toegang te geven om containerinstallatiekopieën uit het register op te halen. 

U weet nu waar de Machine Learning container installatie kopie is opgeslagen. In de volgende sectie worden de stappen beschreven voor het weer geven van de container die wordt uitgevoerd als een module op uw IoT Edge-apparaat. 

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

U kunt berichten weergeven die worden gegenereerd vanaf elke IoT Edge-module, en u kunt berichten weergeven die worden bezorgd bij de IoT Edge-hub.

### <a name="view-data-on-your-iot-edge-device"></a>Gegevens weergeven op het IoT Edge-apparaat

Op het IoT Edge-apparaat kunt u de berichten weergeven die worden verzonden vanaf elke afzonderlijke module.

Mogelijk moet u `sudo` gebruiken voor verhoogde machtigingen om `iotedge`-opdrachten uit te voeren. Als u zich afmeldt en opnieuw aanmeldt bij uw apparaat, worden uw machtigingen automatisch bijgewerkt.

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

4. Selecteer de optie **...** en selecteer vervolgens **controle van het ingebouwde gebeurtenis-eind punt starten**.

5. Bekijk de berichten die de tempSensor elke vijf seconden verzendt. De berichttekst bevat een eigenschap genaamd **afwijking** die de machinelearningmodule voorziet van een waarde 'waar' of 'onwaar'. De eigenschap **AzureMLResponse** bevat de waarde ‘OK’ als het model is uitgevoerd.

   ![Reactie van Azure Machine Learning in bericht tekst](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module geïmplementeerd die werd aangedreven door Azure Machine Learning. U kunt doorgaan met elke andere zelfstudie om te leren waarmee Azure IoT Edge u nog meer kan helpen uw gegevens om te zetten in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Afbeeldingen classificeren met Custom Vision Service](tutorial-deploy-custom-vision.md)
