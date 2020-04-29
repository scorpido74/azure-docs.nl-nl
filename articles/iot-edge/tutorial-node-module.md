---
title: Zelf studie de node. js-module ontwikkelen voor Linux-Azure IoT Edge | Microsoft Docs
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met Node.js-code maakt en deze implementeert op een Edge-apparaat
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 71b22bf9bf040abcdf513a4f8baa916930c8972e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76772210"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Zelf studie: een node. js-IoT Edge module ontwikkelen en implementeren voor Linux-apparaten

Gebruik Visual Studio code voor het ontwikkelen van node. js-code en implementeer deze op een Linux-apparaat met Azure IoT Edge.

U kunt IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de snelstarts. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge Node.js-module te maken
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register
> * De module implementeren op uw IoT Edge-apparaat
> * Gegenereerde gegevens weergeven

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheidgegevens die worden gecommuniceerd naar en opgeslagen in de cloud te reduceren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossings bereik

In deze zelf studie wordt gedemonstreerd hoe u een module in **node. js** kunt ontwikkelen met **Visual Studio code**en hoe u deze implementeert op een **Linux-apparaat**. IoT Edge biedt geen ondersteuning voor node. js-modules voor Windows-apparaten.

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van node. js-modules:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![VS-code gebruiken voor node. js-modules op Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![VS-code gebruiken voor node. js-modules op Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules voor Linux-apparaten](tutorial-develop-for-linux.md). Door een van deze zelf studies te volt ooien, moet u aan de volgende vereisten voldoen:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IOT Edge](quickstart-linux.md)
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-code](https://code.visualstudio.com/) die is geconfigureerd met de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) is geconfigureerd voor het uitvoeren van Linux-containers.

Als u een IoT Edge module wilt ontwikkelen in node. js, installeert u de volgende aanvullende vereisten op uw ontwikkel computer:

* [Node. js en NPM](https://nodejs.org). Het NPM-pakket wordt gedistribueerd met Node.js. Dit betekent dat NMP automatisch wordt geïnstalleerd op de computer wanneer u Node.js downloadt.

## <a name="create-a-module-project"></a>Een module project maken

De volgende stappen laten zien hoe u een IoT-Edge Node.js-module maakt met behulp van Visual Studio Code en de Azure IoT-hulpprogramma's.

### <a name="create-a-new-project"></a>Een nieuw project maken

Gebruik **NPM** om een Node.js-oplossingssjabloon te maken waarop u verder kunt bouwen.

1. In Visual Studio code selecteert u**Geïntegreerde Terminal** **weer geven** > om de VS code geïntegreerde Terminal te openen.

2. Voer in de geïntegreerde terminal de volgende opdracht in om **yeoman** en de generator voor de Node.js Azure IoT Edge-module te installeren:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecteer**opdracht palet** **weer geven** > om het VS code-opdracht palet te openen.

4. Typ in het opdrachtpalet de opdracht **Azure: Sign in** en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u zich al hebt aangemeld, kunt u deze stap overslaan.

5. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **Node.js Module**. |
   | Een modulenaam opgeven | Geef de module de naam **NodeModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/nodemodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor de containeropslagplaats opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

1. Open het **. env** -bestand in de VS code Explorer.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Selecteer uw doel architectuur

Momenteel kunt u met Visual Studio code node. js-modules ontwikkelen voor Linux AMD64-en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u wilt richten op elke oplossing, omdat de container is gebouwd en anders wordt uitgevoerd voor elk type architectuur. De standaard waarde is Linux AMD64.

1. Open het opdracht palet en zoek naar **Azure IOT Edge: Stel het standaard doel platform voor de rand oplossing**in of selecteer het snelkoppelings pictogram in de zijbalk aan de onderkant van het venster.

2. Selecteer in het opdracht palet de doel architectuur in de lijst met opties. Voor deze zelf studie gebruiken we een virtuele machine van Ubuntu als het IoT Edge-apparaat, zodat de standaard **amd64**wordt bewaard.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Elke sjabloon wordt geleverd met voorbeeld code, waarmee gesimuleerde sensor gegevens van de **SimulatedTemperatureSensor** -module worden opgehaald en doorgestuurd naar IOT hub. Voeg in deze sectie code toe om de berichten te analyseren met NodeModule voordat u ze verzendt.

1. Open **modules** > **NodeModule** > **app. js**in de VS code Explorer.

2. Voeg een variabele voor de temperatuurdrempel toe onder de vereiste knooppuntmodules. De temperatuurdrempel bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar IoT Hub worden verzonden.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Vervang de hele functie `PipeMessage` door de functie `FilterMessage`.

    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Vervang in functie `client.on()` de functienaam `pipeMessage` door `filterMessage`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Kopieer het volgende codefragment in de functieaanroep `client.open()`, na `client.on()` binnen de `else`-instructie. Deze functie wordt aangeroepen wanneer de gewenste eigenschappen zijn bijgewerkt.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Sla het bestand app.js op.

7. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

8. Voeg de moduledubbel NodeModule toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie `moduleContent` in, na de moduledubbel `$edgeHub`:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-node-module/module-twin.png)

9. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>Uw module bouwen en pushen

In de vorige sectie hebt u een IoT Edge oplossing gemaakt en code toegevoegd aan de NodeModule waarmee berichten worden gefilterd waarbij de gerapporteerde machine temperatuur binnen de aanvaard bare limieten valt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de VS code geïntegreerde Terminal door **weer gave** > -**Terminal**te selecteren.

1. Meld u aan bij docker door de volgende opdracht in te voeren in de Terminal. Meld u aan met de gebruikers naam, het wacht woord en de aanmeldings server vanuit uw Azure container Registry. U kunt deze waarden ophalen uit de sectie **toegangs sleutels** van het REGI ster in de Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het `--password-stdin`gebruik van te gebruiken. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de [koppeling naar docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. Klik in de VS code Explorer met de rechter muisknop op het bestand **Deployment. sjabloon. json** en selecteer **Build and push IOT Edge Solution**.

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing **config** met het volledige implementatie manifest, gebaseerd op de gegevens in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` de container installatie kopie gemaakt op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` de opslag plaats voor de installatie kopie naar het container register pusht.

## <a name="deploy-modules-to-device"></a>Modules implementeren op het apparaat

Gebruik Visual Studio code Explorer en de uitbrei ding Azure IoT tools om het module project te implementeren op uw IoT Edge-apparaat. U hebt al een implementatie manifest voor bereid voor uw scenario, het bestand **Deployment. json** in de map Config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in Visual Studio code Explorer de sectie **Azure IOT Hub-apparaten** uit om uw lijst met IOT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Klik op de knop Vernieuwen. De nieuwe **NodeModule** die worden uitgevoerd, worden samen met de **SimulatedTemperatureSensor** -module en de **$edgeAgent** en **$edgeHub**weer geven.

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in Visual Studio code Explorer met de rechter muisknop op de naam van uw IoT Edge apparaat en selecteer controle van het **ingebouwde gebeurtenis-eind punt starten**.

2. Bekijk de berichten die op uw IoT Hub arriveren. Het kan even duren voordat de berichten binnenkomen. Het IoT Edge apparaat moet zijn nieuwe implementatie ontvangen en alle modules starten. Vervolgens worden de wijzigingen die we hebben aangebracht in de NodeModule-code gewacht totdat de computer temperatuur 25 graden bereikt voordat berichten worden verzonden. Het bericht type **waarschuwing** wordt ook toegevoegd aan berichten die de drempel waarde voor de Tempe ratuur bereiken.

## <a name="edit-the-module-twin"></a>De module twee bewerken

We hebben de NodeModule-module twee maal in het implementatie manifest gebruikt om de drempel waarde voor de Tempe ratuur op 25 graden in te stellen. U kunt de module twee gebruiken om de functionaliteit te wijzigen zonder dat u de module code hoeft bij te werken.

1. Vouw in Visual Studio code de details onder uw IoT Edge apparaat uit om de actieve modules weer te geven.

2. Klik met de rechter muisknop op **NodeModule** en selecteer **module bewerken**.

3. Zoek **TemperatureThreshold** in de gewenste eigenschappen. Wijzig de waarde in een nieuwe Tempe ratuur van vijf graden tot 10 graden hoger dan de laatste gerapporteerde Tempe ratuur.

4. Sla het dubbele bestand van de module op.

5. Klik met de rechter muisknop op een wille keurige plaats in het deel venster module dubbel bewerken en selecteer **Update module**.

6. Bewaak de binnenkomende apparaat-naar-Cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuur drempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer te weten komen over [het ontwikkelen van uw eigen IOT Edge modules](module-development.md) of het [ontwikkelen van modules met Visual Studio code](how-to-vs-code-develop-module.md). Zie voor voor beelden van IoT Edge modules, met inbegrip van de gesimuleerde temperatuur module [IOT Edge module](https://github.com/Azure/iotedge/tree/master/edge-modules)-voor beelden.

U kunt door gaan met de volgende zelf studies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)stream Analytics
> [Machine Learning](tutorial-deploy-machine-learning.md)machine learning
> [Custom Vision service](tutorial-deploy-custom-vision.md)
