---
title: Zelf studie-zelf studie voor aangepaste Java-modules met Azure IoT Edge
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met Java-code maakt en deze implementeert op een Edge-apparaat.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d2c781c1ee09e4e70d119b59e6c02e0440d5bf54
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701859"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Zelf studie: een Java IoT Edge-module ontwikkelen voor Linux-apparaten

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de implementatie Azure IoT Edge op een gesimuleerd apparaat in [Linux](quickstart-linux.md) Quick Start. In deze zelfstudie leert u het volgende:    

> [!div class="checklist"]
> * Visual Studio Code gebruiken om een ​​IoT Edge Java-module te maken op basis van het Azure IoT Edge maven-sjabloonpakket en Azure IoT Java-apparaat-SDK.
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.


De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossings bereik

In deze zelf studie wordt gedemonstreerd hoe u een module in **Java** ontwikkelt met **Visual Studio code**en hoe u deze implementeert op een **Linux-apparaat**. IoT Edge biedt geen ondersteuning voor Java-modules voor Windows-apparaten. 

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van Java-modules: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![VS code gebruiken voor Java-modules op Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux-ARM32** | ![VS code gebruiken voor Java-modules op Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules voor Linux-apparaten](tutorial-develop-for-linux.md). Door een van deze zelf studies te volt ooien, moet u aan de volgende vereisten voldoen: 

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IOT Edge](quickstart-linux.md)
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-code](https://code.visualstudio.com/) die is geconfigureerd met de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) is geconfigureerd voor het uitvoeren van Linux-containers.

Als u een IoT Edge module wilt ontwikkelen in Java, installeert u de volgende aanvullende vereisten op uw ontwikkel computer: 

* [Java-uitbreidingspakket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) voor Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks); en [stel de omgevingsvariabele `JAVA_HOME` in](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) om te verwijzen naar uw JDK-installatie.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Een module project maken
Met de volgende stappen maakt u een ​​IoT Edge-moduleproject op basis van het Azure IoT Edge maven-sjabloonpakket en de Azure IoT Java-apparaat-SDK. U kunt het project maken met behulp van Visual Studio Code en de Azure IoT-hulpprogramma's.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een Java-oplossingssjabloon die u met uw eigen code kunt aanpassen. 

1. Selecteer in Visual Studio Code **View** > **Command Palette** om het VS Code-opdrachtpalet te openen. 

2. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **Java Module**. |
   | Waarde opgeven voor groupId | Geef een waarde op voor de groeps-id of accepteer de standaardwaarde **com.edgemodule**. |
   | Een modulenaam opgeven | Geef de module de naam **JavaModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/javamodule. |
 
   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-java-module/repository.png)
   
Als dit de eerste keer is dat u een Java-module maakt, kan het enkele minuten duren om maven-pakketten te downloaden. Vervolgens wordt de werkruimte van de IoT Edge-oplossing geladen in het VS Code-venster. De werkruimte voor de oplossing bevat vijf onderdelen op het hoogste niveau. De map **modules** bevat de Java-code voor uw module evenals Docker-bestanden voor het bouwen van uw module als een containerinstallatiekopie. In het bestand **\.env** worden uw referenties voor het containerregister opgeslagen. Het bestand **deployment.template.json** bevat de gegevens die de IoT Edge-runtime gebruikt om modules op een apparaat te implementeren. En het bestand **deployment.debug.template.json** bevat de foutopsporingsversie van modules. In deze zelfstudie gaan we niet de map **\.vscode** of het bestand **\.gitignore** bewerken. 

Als u geen containerregister hebt opgegeven bij het maken van uw oplossing, maar de standaardwaarde localhost:5000 hebt geaccepteerd, is er geen \.env-bestand gemaakt. 

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. 

1. Open in VS Code Explorer het .env-bestand. 
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister. 
3. Sla dit bestand op. 

### <a name="select-your-target-architecture"></a>Selecteer uw doel architectuur

Momenteel kan Visual Studio code Java-modules ontwikkelen voor Linux AMD64-en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u wilt richten op elke oplossing, omdat de container is gebouwd en anders wordt uitgevoerd voor elk type architectuur. De standaard waarde is Linux AMD64. 

1. Open het opdracht palet en zoek naar **Azure IOT Edge: Stel het standaard doel platform voor de rand oplossing**in of selecteer het snelkoppelings pictogram in de zijbalk aan de onderkant van het venster. 

2. Selecteer in het opdracht palet de doel architectuur in de lijst met opties. Voor deze zelf studie gebruiken we een virtuele machine van Ubuntu als het IoT Edge-apparaat, zodat de standaard **amd64**wordt bewaard. 

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in de VS Code explorer **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Voeg bovenaan het bestand de volgende code toe om nieuwe klassen waarnaar wordt verwezen, te importeren.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Voeg de volgende definitie toe aan de klasse **App**. Met deze variabele wordt een drempelwaarde voor temperatuur ingesteld. De gemeten machinetemperatuur wordt niet bij IoT Hub gerapporteerd totdat deze waarde wordt overschreden. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Vervang de execute-methode van **MessageCallbackMqtt** door de volgende code. Deze methode wordt aangeroepen wanneer de module een MQTT-bericht ontvangt van de IoT Edge-hub. Berichten die temperaturen onder de drempelwaarde die via de moduledubbel is ingesteld, worden hierdoor gefilterd.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Voeg de volgende twee statische binnenste klassen toe aan de klasse **App**. Met deze klassen wordt de variabele tempThreshold bijgewerkt wanneer de gewenste eigenschap van de moduledubbel verandert. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Voeg de volgende regels toe aan de module **main**, na **client.open()** , om u te abonneren op de updates van de moduledubbel.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Sla het App.java-bestand op.

8. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

9. Voeg de moduledubbel **JavaModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onderaan de sectie **moduleContent** in, na de moduledubbel **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-java-module/module-twin.png)

10. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>Uw module bouwen en pushen

In het vorige gedeelte hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **JavaModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare limiet is. Nu gaat u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen. 

1. Open de met VS Code geïntegreerde terminal door **View** > **Terminal** te selecteren.

1. Meld u aan bij docker door de volgende opdracht in te voeren in de Terminal. Meld u aan met de gebruikers naam, het wacht woord en de aanmeldings server vanuit uw Azure container Registry. U kunt deze waarden ophalen uit de sectie **toegangs sleutels** van het REGI ster in de Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het gebruik van `--password-stdin`. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de [koppeling naar docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing **config** met het volledige implementatie manifest, gebaseerd op de gegevens in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` uitgevoerd om de container installatie kopie te bouwen op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` uitgevoerd om de opslag plaats van de installatie kopie naar het container register te pushen.

## <a name="deploy-modules-to-device"></a>Modules implementeren op het apparaat

Gebruik Visual Studio code Explorer en de uitbrei ding Azure IoT tools om het module project te implementeren op uw IoT Edge-apparaat. U hebt al een implementatie manifest voor bereid voor uw scenario, het bestand **Deployment. json** in de map Config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in Visual Studio code Explorer de sectie **Azure IOT Hub-apparaten** uit om uw lijst met IOT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Klik op de knop Vernieuwen. De nieuwe **JavaModule** die worden uitgevoerd, worden samen met de **SimulatedTemperatureSensor** -module en de **$edgeAgent** en **$edgeHub**weer geven.  

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in Visual Studio code Explorer met de rechter muisknop op de naam van uw IoT Edge apparaat en selecteer controle van het **ingebouwde gebeurtenis-eind punt starten**.

2. Bekijk de berichten die op uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten binnenkomen, omdat het IoT Edge apparaat de nieuwe implementatie moet ontvangen en alle modules kan starten. Vervolgens worden de wijzigingen die we hebben aangebracht in de JavaModule-code gewacht totdat de computer temperatuur 25 graden bereikt voordat berichten worden verzonden. Het bericht type **waarschuwing** wordt ook toegevoegd aan berichten die de drempel waarde voor de Tempe ratuur bereiken. 

## <a name="edit-the-module-twin"></a>De module twee bewerken

We hebben de JavaModule-module twee maal in het implementatie manifest gebruikt om de drempel waarde voor de Tempe ratuur op 25 graden in te stellen. U kunt de module twee gebruiken om de functionaliteit te wijzigen zonder dat u de module code hoeft bij te werken.

1. Vouw in Visual Studio code de details onder uw IoT Edge apparaat uit om de actieve modules weer te geven. 

2. Klik met de rechter muisknop op **JavaModule** en selecteer **module bewerken**. 

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
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [machine learning](tutorial-deploy-machine-learning.md) [
> Custom Vision service](tutorial-deploy-custom-vision.md)
