---
title: 'Zelf studie: C#-module voor Linux ontwikkelen met behulp van Azure IoT Edge'
description: In deze zelf studie wordt uitgelegd hoe u een IoT Edge module maakt met C#-code en deze implementeert op een Linux IoT Edge-apparaat.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 53e1863f6f3421a6d8df9112f463f16443cff93e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78943052"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Zelf studie: een C# IoT Edge-module ontwikkelen voor Linux-apparaten

Gebruik Visual Studio code om C#-code te ontwikkelen en te implementeren op een Linux-apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de quickstarts over het implementeren van Azure IoT Edge op een gesimuleerd apparaat in [Windows](quickstart.md) of [Linux](quickstart-linux.md). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een IoT Edge-module te maken op basis van de .NET Core 2.1 SDK.
> * Visual Studio Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar het register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossings bereik

In deze zelf studie wordt gedemonstreerd hoe u een module in **C#** ontwikkelt met **Visual Studio code** en deze implementeert op een **Linux-apparaat**. Als u modules voor Windows-apparaten ontwikkelt, gaat u in plaats daarvan naar [een C# IOT Edge-module ontwikkelen voor Windows-apparaten](tutorial-csharp-module-windows.md) .

Gebruik de volgende tabel om inzicht te krijgen in de mogelijkheden voor het ontwikkelen en implementeren van C#-modules voor Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![C#-modules voor LinuxAMD64 in VS code](./media/tutorial-c-module/green-check.png) | ![C#-modules voor LinuxAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![C#-modules voor LinuxARM32 in VS code](./media/tutorial-c-module/green-check.png) | ![C#-modules voor LinuxARM64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Ondersteuning voor Linux ARM64-apparaten is beschikbaar in de [open bare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Zie voor meer informatie [ARM64-modules ontwikkelen en fouten opsporen IOT Edge in Visual Studio code (preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen, [een IOT Edge module te ontwikkelen voor een Linux-apparaat](tutorial-develop-for-linux.md). Nadat u deze zelf studie hebt voltooid, moet u de volgende vereisten al hebben:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IOT Edge](quickstart-linux.md).
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-code](https://code.visualstudio.com/) die is geconfigureerd met de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) is geconfigureerd voor het uitvoeren van Linux-containers.

Voor het volt ooien van deze zelf studies moet u de volgende aanvullende vereisten op uw ontwikkel computer voorbereiden:

* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Een module project maken

Met de volgende stappen maakt u een IoT Edge module project voor C# met behulp van Visual Studio code en de uitbrei ding Azure IoT tools. Zodra u een project sjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C#-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. In Visual Studio code selecteert u**opdracht palet** **weer geven** > om het VS code-opdracht palet te openen.

2. Voer in het opdrachtpalet de opdracht **Azure: Sign in** in en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u al bent aangemeld, kunt u deze stap overslaan.

3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in of accepteer de standaardnaam **EdgeSolution**. |
   | Modulesjabloon selecteren | Kies **C# Module**. |
   | Een modulenaam opgeven | Geef de module de naam **CSharpModule**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de naam die u in de laatste stap hebt opgegeven. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/csharpmodule. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **toegangs sleutels** van uw Azure container Registry.

1. Open het **. env** -bestand in de VS code Explorer.
2. Werk de velden met de **gebruikers naam** en het **wacht woord** bij vanuit uw Azure container Registry.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Selecteer uw doel architectuur

Momenteel kan Visual Studio code C#-modules ontwikkelen voor Linux AMD64-en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u wilt richten op elke oplossing, omdat de container is gebouwd en anders wordt uitgevoerd voor elk type architectuur. De standaard waarde is Linux AMD64.

1. Open het opdracht palet en zoek naar **Azure IOT Edge: Stel het standaard doel platform voor de rand oplossing**in of selecteer het snelkoppelings pictogram in de zijbalk aan de onderkant van het venster.

2. Selecteer in het opdracht palet de doel architectuur in de lijst met opties. Voor deze zelf studie gebruiken we een virtuele machine van Ubuntu als het IoT Edge-apparaat, zodat de standaard **amd64**wordt bewaard.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in de VS code Explorer **modules** > **CSharpModule** > **Program.cs**.

2. Voeg bovenaan de naamruimte **CSharpModule** drie **using**-instructies toe voor typen die later worden gebruikt:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Voeg de variabele **temperatureThreshold** toe aan de klasse **Program**. Deze variabele bepaalt de waarde die de gemeten temperatuur moet overschrijden voordat de gegevens naar de IoT-hub worden verzonden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Voeg de klassen **MessageBody**, **Machine** en **Ambient** toe aan de klasse **Program**. Deze klassen bepalen het verwachte schema voor de hoofdtekst van inkomende berichten.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

5. Zoek de functie **init** . Met deze functie wordt een **ModuleClient** -object gemaakt en geconfigureerd, waarmee de module verbinding kan maken met de lokale Azure IOT Edge runtime om berichten te verzenden en te ontvangen. Nadat de **ModuleClient** is gemaakt, leest de code de waarde van **temperatureThreshold** uit de gewenste eigenschappen van de moduledubbel. De code registreert een aanroep om berichten van een IoT Edge-hub te ontvangen via het eindpunt **input1**. Vervang de methode **SetInputMessageHandlerAsync** door een nieuwe en voeg een methode **SetDesiredPropertyUpdateCallbackAsync** toe voor updates van de gewenste eigenschappen. Om deze wijziging aan te brengen, moet u de laatste regel van de methode **Init** vervangen door de volgende code:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Voeg de methode **onDesiredPropertiesUpdate** toe aan de klasse **Program**. Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. Vervang de methode **PipeMessage** door de methode **FilterMessages**. Deze methode wordt aangeroepen wanneer de module een bericht ontvangt van de IoT Edge-hub. Berichten die temperaturen onder de drempelwaarde die via de moduledubbel is ingesteld, worden hierdoor gefilterd. Ook wordt de eigenschap **BerichtType** toegevoegd aan het bericht met de waarde ingesteld op **Alarm**.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Sla het bestand Program.cs op.

9. Open in VS Code Explorer het bestand **deployment.template.json** in de werkruimte van de IoT Edge-oplossing.

10. Voeg de moduledubbel **CSharpModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie **modulesContent** in, na de moduledubbel **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-csharp-module/module-twin.png)

11. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>Uw module bouwen en pushen

In de vorige sectie hebt u een IoT Edge oplossing gemaakt en code toegevoegd aan de CSharpModule. Met de nieuwe code worden berichten gefilterd waarbij de gerapporteerde machine temperatuur binnen de toegestane limieten valt. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Open de VS code geïntegreerde Terminal door **weer gave** > -**Terminal**te selecteren.

1. Meld u aan bij docker door de volgende opdracht in te voeren in de Terminal. Meld u aan met de gebruikers naam, het wacht woord en de aanmeldings server vanuit uw Azure container Registry. U kunt deze waarden ophalen uit de sectie **toegangs sleutels** van het REGI ster in de Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het `--password-stdin`gebruik van te gebruiken. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de [koppeling naar docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. Klik in de VS code Explorer met de rechter muisknop op het bestand **Deployment. sjabloon. json** en selecteer **Build and push IOT Edge Solution**.

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing **config** met het volledige implementatie manifest, gebaseerd op de gegevens in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` de container installatie kopie gemaakt op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` de opslag plaats voor de installatie kopie naar het container register pusht.

## <a name="deploy-and-run-the-solution"></a>De oplossing implementeren en uitvoeren

Gebruik Visual Studio code Explorer en de uitbrei ding Azure IoT tools om het module project te implementeren op uw IoT Edge-apparaat. U hebt al een implementatie manifest voor bereid voor uw scenario, het bestand **Deployment. json** in de map Config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in Visual Studio code Explorer de sectie **Azure IOT Hub-apparaten** uit om uw lijst met IOT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Selecteer het bestand **deployment.json** in de **configuratiemap** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Klik op de knop Vernieuwen. De nieuwe **CSharpModule** die worden uitgevoerd, worden samen met de **SimulatedTemperatureSensor** -module en de **$edgeAgent** en **$edgeHub**weer geven.  

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U kunt de status van uw IoT Edge-apparaat bekijken via de sectie **Azure IoT Hub Devices** van de Visual Studio Code explorer. Vouw de details van uw apparaat uit voor een overzicht van de modules die worden geïmplementeerd en uitgevoerd.

1. Klik in Visual Studio code Explorer met de rechter muisknop op de naam van uw IoT Edge apparaat en selecteer controle van het **ingebouwde gebeurtenis-eind punt starten**.

2. Bekijk de berichten die op uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten binnenkomen, omdat het IoT Edge apparaat de nieuwe implementatie moet ontvangen en alle modules kan starten. Vervolgens worden de wijzigingen die we hebben aangebracht in de CModule-code gewacht totdat de computer temperatuur 25 graden bereikt voordat berichten worden verzonden. Het bericht type **waarschuwing** wordt ook toegevoegd aan berichten die de drempel waarde voor de Tempe ratuur bereiken.

   ![Berichten weer geven die binnenkomen op IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De module twee bewerken

We hebben de CSharpModule-module twee maal in het implementatie manifest gebruikt om de drempel waarde voor de Tempe ratuur op 25 graden in te stellen. U kunt de module twee gebruiken om de functionaliteit te wijzigen zonder dat u de module code hoeft bij te werken.

1. Vouw in Visual Studio code de details onder uw IoT Edge apparaat uit om de actieve modules weer te geven.

2. Klik met de rechter muisknop op **CSharpModule** en selecteer **module bewerken**.

3. Zoek **TemperatureThreshold** in de gewenste eigenschappen. Wijzig de waarde in een nieuwe Tempe ratuur van vijf graden tot 10 graden hoger dan de laatste gerapporteerde Tempe ratuur.

4. Sla het dubbele bestand van de module op.

5. Klik met de rechter muisknop op een wille keurige plaats in het deel venster module dubbel bewerken en selecteer **Update module**.

6. Bewaak de binnenkomende apparaat-naar-Cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuur drempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en de Azure-resources die u in dit artikel hebt gebruikt, verwijderen om kosten te voor komen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer te weten komen over [het ontwikkelen van uw eigen IOT Edge modules](module-development.md) of het [ontwikkelen van modules met Visual Studio code](how-to-vs-code-develop-module.md). Zie voor voor beelden van IoT Edge modules, met inbegrip van de gesimuleerde temperatuur module [IOT Edge module](https://github.com/Azure/iotedge/tree/master/edge-modules)-voor beelden.

U kunt door gaan met de volgende zelf studies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)stream Analytics
> [Machine Learning](tutorial-deploy-machine-learning.md)machine learning
> [Custom Vision service](tutorial-deploy-custom-vision.md)
