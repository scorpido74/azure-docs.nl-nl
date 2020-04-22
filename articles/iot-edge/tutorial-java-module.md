---
title: Zelfstudie - Zelfstudie van de Java-module met Azure IoT Edge
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met Java-code maakt en deze implementeert op een Edge-apparaat.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: d8ea58dca8235b6dfc49c14c519dd44dabdf0592
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733087"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Zelfstudie: Een Java IoT Edge-module ontwikkelen voor Linux-apparaten

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de Deploy Azure IoT Edge op een gesimuleerd apparaat in [Linux](quickstart-linux.md) snelstart. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een ​​IoT Edge Java-module te maken op basis van het Azure IoT Edge maven-sjabloonpakket en Azure IoT Java-apparaat-SDK.
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossingsbereik

Deze zelfstudie laat zien hoe je een module in **Java** ontwikkelen met behulp van **Visual Studio Code**en hoe je deze implementeert op een **Linux-apparaat.** IoT Edge biedt geen ondersteuning voor Java-modules voor Windows-apparaten.

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van Java-modules:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![VS Code voor Java-modules gebruiken op Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![VS Code for Java modules gebruiken op Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, had u de vorige zelfstudie moeten doorlopen om uw ontwikkelomgeving in te stellen voor de ontwikkeling van Linux-containers: [IoT Edge-modules ontwikkelen voor Linux-apparaten.](tutorial-develop-for-linux.md) Door het invullen van een van deze tutorials, moet u de volgende voorwaarden op zijn plaats:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IoT Edge](quickstart-linux.md)
* Een containerregister, zoals [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd om Linux-containers uit te voeren.

Als u een IoT Edge-module in Java wilt ontwikkelen, installeert u de volgende aanvullende vereisten op uw ontwikkelingsmachine: 

* [Java-uitbreidingspakket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) voor Visual Studio Code.
* [Java SE Development Kit 10](https://aka.ms/azure-jdks); en [stel de omgevingsvariabele `JAVA_HOME` in](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) om te verwijzen naar uw JDK-installatie.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen maakt u een ​​IoT Edge-moduleproject op basis van het Azure IoT Edge maven-sjabloonpakket en de Azure IoT Java-apparaat-SDK. U kunt het project maken met behulp van Visual Studio Code en de Azure IoT-hulpprogramma's.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een Java-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Selecteer in Visual Studio Code**opdrachtpalet** **weergeven** > om het opdrachtpalet VS-code te openen.

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

Als het de eerste keer is dat u java-module maakt, kan het enkele minuten duren voordat de maven-pakketten zijn gedownload. Wanneer de oplossing klaar is, laadt het VENSTER VS-code uw Werkruimte voor IoT Edge-oplossingen. De oplossingswerkruimte bevat vijf componenten op het hoogste niveau:

* De **map modules** bevat de Java-code voor uw module en de Docker-bestanden om uw module als containerafbeelding te bouwen.
* Het ** \.env-bestand** slaat uw containerregisterreferenties op.
* Het bestand **deployment.template.json** bevat de gegevens die de IoT Edge-runtime gebruikt om modules op een apparaat te implementeren.
* Het **bestand deployment.debug.template.json** slaat de foutopsporingsversie van modules op.
* U bewerkt de ** \.map vscode** of ** \.gitignore** niet in deze zelfstudie.

Als u geen containerregister hebt opgegeven bij het maken van uw oplossing, maar de standaardwaarde localhost:5000 hebt geaccepteerd, is er geen \.env-bestand gemaakt.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

1. Open in VS Code Explorer het .env-bestand.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Selecteer uw doelarchitectuur

Momenteel kan Visual Studio Code Java-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u bij elke oplossing target, omdat de container voor elk architectuurtype anders is gebouwd en deze wordt uitgevoerd. De standaard is Linux AMD64.

1. Open het opdrachtpalet en zoek naar **Azure IoT Edge: Stel standaarddoelplatform in voor edge-oplossing**of selecteer het sneltoetspictogram in de zijbalk onder aan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. Voor deze tutorial gebruiken we een Virtuele Ubuntu-machine als het IoT Edge-apparaat, dus houdt de standaard **amd64**.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. In de VS Code explorer, open **modules** > **JavaModule** > **src** > **belangrijkste** > **java** > **com** > **edgemodule** > **App.java**.

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

6. Voeg de volgende regels toe aan de module **main**, na **client.open()**, om u te abonneren op de updates van de moduledubbel.

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

## <a name="build-and-push-your-module"></a>Bouw en duw je module

In het vorige gedeelte hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **JavaModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare limiet is. Nu gaat u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de geïntegreerde VS-codeterminal door **View** > **Terminal te**selecteren .

1. Meld u aan bij Docker door de volgende opdracht in de terminal in te voeren. Meld u aan met de gebruikersnaam, het wachtwoord en de aanmeldingsserver vanuit uw Azure-containerregister. U deze waarden ophalen uit de sectie **Toegangssleutels** van uw register in de Azure-portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

1. Klik in de VS-codeverkenner met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **de Oplossing IoT Edge bouwen en pushen**.

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, dat is opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

Gebruik de Visual Studio Code explorer en de Azure IoT Tools-extensie om het moduleproject te implementeren op uw IoT Edge-apparaat. U hebt al een implementatiemanifest voorbereid op uw scenario, het **bestand deployment.json** in de config-map. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de visual studiocodeverkenner de sectie **Azure IoT Hub-apparaten** uit om uw lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Klik op de knop Vernieuwen. U ziet de nieuwe **JavaModule** draaien samen met de **SimulatedTemperatureSensor** module en de **$edgeAgent** en **$edgeHub.**  

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in de verkenner voor Visual Studio Code met de rechtermuisknop op de naam van uw IoT Edge-apparaat en selecteer **Ingebouwde gebeurteniseindpunt starten.**

2. Bekijk de berichten die aankomen op uw IoT Hub. Het kan even duren voordat de berichten binnenkomen. Het IoT Edge-apparaat moet zijn nieuwe implementatie ontvangen en alle modules starten. Vervolgens wachten de wijzigingen die we hebben aangebracht in de JavaModule-code tot de temperatuur van de machine 25 graden is voordat we berichten verzenden. Het voegt ook het berichttype **Alert** toe aan berichten die die temperatuurdrempel bereiken.

## <a name="edit-the-module-twin"></a>De moduletwee bewerken

We gebruikten de JavaModule module twin in het implementatiemanifest om de temperatuurdrempel op 25 graden in te stellen. U de moduletwee gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Vouw in Visual Studio Code de details onder uw IoT Edge-apparaat uit om de lopende modules te bekijken.

2. Klik met de rechtermuisknop op **JavaModule** en selecteer **Moduletwin bewerken**.

3. Zoek **Temperatuurdrempel** in de gewenste eigenschappen. Verander de waarde in een nieuwe temperatuur 5 graden tot 10 graden hoger dan de laatst gemelde temperatuur.

4. Sla het dubbele modulebestand op.

5. Klik met de rechtermuisknop ergens in het dubbele bewerkingsvenster van de module en selecteer **Moduletwin bijwerken**.

6. Monitor de binnenkomende device-to-cloud berichten. U ziet de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die ruwe gegevens filtert die door uw IoT Edge-apparaat worden gegenereerd. Wanneer u klaar bent om uw eigen modules te bouwen, u meer te weten komen over [het ontwikkelen van IoT Edge-modules](module-development.md) of hoe u [modules ontwikkelen met Visual Studio Code.](how-to-vs-code-develop-module.md) Zie [IoT Edge-modulevoorbeelden](https://github.com/Azure/iotedge/tree/master/edge-modules) voor codevoorbeelden, inclusief de gesimuleerde temperatuurmodule.

Ga verder naar de volgende zelfstudies om te leren hoe Azure IoT Edge u helpt Azure-cloudservices te implementeren om gegevens aan de rand te verwerken en te analyseren.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
