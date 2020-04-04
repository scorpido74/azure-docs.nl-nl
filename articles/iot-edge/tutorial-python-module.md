---
title: Aangepaste Python-module maken - Azure IoT Edge | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met Python-code maakt en deze implementeert op een Edge-apparaat.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f474ec121f444f5f0c41272f5d87a7f8abfadb8d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657056"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Zelfstudie: Een Python IoT Edge-module ontwikkelen en implementeren voor Linux-apparaten

Gebruik Visual Studio Code om Python-code te ontwikkelen en te implementeren op een Linux-apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie u een IoT Edge-module maken en implementeren die sensorgegevens filtert op het IoT Edge-apparaat dat u in de quickstart hebt ingesteld. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge Python-module te maken.
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossingsbereik

Deze zelfstudie laat zien hoe je een module in **Python** ontwikkelen met behulp van **Visual Studio Code**en hoe je deze implementeert op een **Linux-apparaat.** IoT Edge ondersteunt geen Python-modules voor Windows-apparaten.

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van Python-modules naar Linux:

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![VS Code for Python modules gebruiken op Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![VS Code for Python modules gebruiken op Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, had u de vorige zelfstudie moeten doorlopen om uw ontwikkelomgeving in te stellen voor de ontwikkeling van Linux-containers: [IoT Edge-modules ontwikkelen voor Linux-apparaten.](tutorial-develop-for-linux.md) Door het invullen van die tutorial, moet u de volgende voorwaarden op zijn plaats:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IoT Edge](quickstart-linux.md)
* Een containerregister, zoals [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd om Linux-containers uit te voeren.

Als u een IoT Edge-module in Python wilt ontwikkelen, installeert u de volgende aanvullende vereisten op uw ontwikkelingsmachine:

* [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) voor Visual Studio Code.
* [Python.](https://www.python.org/downloads/)
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) voor de installatie van Python-pakketten (over het algemeen opgenomen in uw Python-installatie).

>[!Note]
>Zorg ervoor dat uw map `bin` zich op het pad voor uw platform bevindt. Normaal gesproken is dat `~/.local/` voor UNIX en macOS of `%APPDATA%\Python` voor Windows.

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen maakt u een IoT Edge Python-module met behulp van Visual Studio Code en de Azure IoT-hulpprogramma's.

### <a name="create-a-new-project"></a>Een nieuw project maken

Gebruik de VS-code om een Python-oplossingssjabloon te maken waar u bovenop bouwen.

1. Selecteer in Visual Studio Code **View** > **Terminal** om de geïntegreerde VS Code-terminal te openen.

1. Selecteer**Opdrachtpalet** **weergeven** > om het opdrachtpalet VS-code te openen.

1. Voer in het opdrachtpalet de opdracht **Azure: Sign in** in en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u al bent aangemeld, kunt u deze stap overslaan.

1. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen en geef de volgende informatie om uw oplossing te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **Python-module**. |
   | Een modulenaam opgeven | Geef de module de naam **PythonModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/pythonmodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor de containeropslagplaats opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

1. Open in de VS-codeverkenner het **.env-bestand.**
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla het .env-bestand op.

### <a name="select-your-target-architecture"></a>Selecteer uw doelarchitectuur

Momenteel kan Visual Studio Code Python-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u bij elke oplossing target, omdat de container voor elk architectuurtype anders is gebouwd en deze wordt uitgevoerd. De standaard is Linux AMD64.

1. Open het opdrachtpalet en zoek naar **Azure IoT Edge: Stel standaarddoelplatform in voor edge-oplossing**of selecteer het sneltoetspictogram in de zijbalk onder aan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. Voor deze tutorial gebruiken we een Virtuele Ubuntu-machine als het IoT Edge-apparaat, dus houdt de standaard **amd64**.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Elke sjabloon bevat voorbeeldcode, die gesimuleerde sensorgegevens van de **Module SimulatedTemperatureSensor** neemt en deze naar de IoT-hub leidt. In deze sectie voegt u de code toe waarmee de **PythonModule** wordt uitgebreid om de berichten te analyseren voordat u ze verzendt.

1. Open in de VS Code-verkenner **modules** > **PythonModule** > **main.py**.

2. Importeer bovenaan het **main.py**-bestand de **JSON**-bibliotheek:

    ```python
    import json
    ```

3. Voeg de variabelen **TEMPERATURE_THRESHOLD** en **TWIN_CALLBACKS** toe onder de globale tellers. De temperatuurdrempel bepaalt de waarde die de gemeten temperatuur van de machine moet overschrijden voordat de gegevens naar de IoT-hub worden verzonden.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Vervang de **functie input1_listener** door de volgende code:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )

        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Werk de **luisteraars** bij om ook twee updates te beluisteren.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Sla het bestand main.py op.

7. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

8. Voeg de moduledubbel **PythonModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onderaan de sectie **moduleContent** in, na de moduledubbel **$edgeHub**:

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-python-module/module-twin.png)

9. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>Bouw en duw je module

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de PythonModule die berichten uitfiltert waar de gerapporteerde machinetemperatuur binnen de aanvaardbare limieten ligt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de geïntegreerde VS-codeterminal door **View** > **Terminal te**selecteren .

1. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver vanuit uw Azure-containerregister. U deze waarden ophalen uit de sectie **Toegangssleutels** van uw register in de Azure-portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

1. Klik in de VS-codeverkenner met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **de Oplossing IoT Edge bouwen en pushen**.

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

Gebruik de Visual Studio Code explorer en de Azure IoT Tools-extensie om het moduleproject te implementeren op uw IoT Edge-apparaat. U hebt al een implementatiemanifest voorbereid op uw scenario, het **bestand deployment.json** in de config-map. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de visual studiocodeverkenner de sectie **Azure IoT Hub-apparaten** uit om uw lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Klik op de knop Vernieuwen. U ziet de nieuwe **PythonModule** draaien samen met de **Module SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub.**

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in de verkenner voor Visual Studio Code met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Ingebouwde gebeurteniseindpunt starten.**

2. Bekijk de berichten die aankomen op uw IoT Hub. Het kan even duren voordat de berichten binnenkomen. Het IoT Edge-apparaat moet zijn nieuwe implementatie ontvangen en alle modules starten. Vervolgens wachten de wijzigingen die we hebben aangebracht in de PythonModule-code totdat de temperatuur van de machine 25 graden is voordat we berichten verzenden. Het voegt ook het berichttype **Alert** toe aan berichten die die temperatuurdrempel bereiken.

## <a name="edit-the-module-twin"></a>De moduletwee bewerken

We gebruikten de PythonModule module twin in het implementatiemanifest om de temperatuurdrempel op 25 graden in te stellen. U de moduletwee gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Vouw in Visual Studio Code de details onder uw IoT Edge-apparaat uit om de lopende modules te bekijken.

2. Klik met de rechtermuisknop op **PythonModule** en selecteer **Moduletwin bewerken**.

3. Zoek **Temperatuurdrempel** in de gewenste eigenschappen. Verander de waarde in een nieuwe temperatuur 5 graden tot 10 graden hoger dan de laatst gemelde temperatuur.

4. Sla het dubbele modulebestand op.

5. Klik met de rechtermuisknop ergens in het dubbele bewerkingsvenster van de module en selecteer **Moduletwin bijwerken**.

6. Monitor de binnenkomende device-to-cloud berichten. U ziet de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders u de lokale configuraties en de Azure-resources verwijderen die u in dit artikel hebt gebruikt om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, u meer te weten komen over [het ontwikkelen van uw eigen IoT Edge-modules](module-development.md) of hoe u modules ontwikkelen met Visual Studio [Code.](how-to-vs-code-develop-module.md) Zie [IoT Edge-modulevoorbeelden](https://github.com/Azure/iotedge/tree/master/edge-modules) en [IoT Python SDK-samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)voor voorbeelden van IoT Edge- modules, waaronder de gesimuleerde temperatuurmodule.

U doorgaan naar de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen Azure-cloudservices te implementeren om gegevens aan de rand te verwerken en te analyseren.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
