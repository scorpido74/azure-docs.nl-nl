---
title: Zelfstudie - C#-module voor Windows ontwikkelen met Azure IoT Edge
description: In deze zelfstudie ziet u hoe u een IoT Edge-module met C#-code maakt en implementeert op een Windows IoT Edge-apparaat.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4fa3fb17f4eace8d389738fb46267a097610f175
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760416"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Zelfstudie: Een C# IoT Edge-module voor Windows-apparaten ontwikkelen

Gebruik Visual Studio om C#-code te ontwikkelen en te implementeren op een Windows-apparaat met Azure IoT Edge.

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Gebruik Visual Studio om een IoT Edge-module te maken die is gebaseerd op de C# SDK.
> * Gebruik Visual Studio en Docker om een Docker-afbeelding te maken en deze te publiceren in uw register.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossingsbereik

In deze zelfstudie wordt uitgelegd hoe u een module in **C#** ontwikkelen met **Visual Studio 2019** en deze implementeren op een **Windows-apparaat.** Als u modules voor Linux-apparaten ontwikkelt, gaat u in plaats daarvan [naar Het ontwikkelen van een C# IoT Edge-module voor Linux-apparaten.](tutorial-csharp-module.md)

Gebruik de volgende tabel om inzicht te krijgen in uw opties voor het ontwikkelen en implementeren van C#-modules op Windows-apparaten:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 ontwikkelen** | ![C#-modules ontwikkelen voor WinAMD64 in VS Code](./media/tutorial-c-module/green-check.png) | ![Ontwikkel C#modules voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 debug** |   | ![FoutopsporingsC#-modules voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie doorlopen om uw ontwikkelomgeving in te stellen, [Een IoT Edge-module voor een Windows-apparaat ontwikkelen.](tutorial-develop-for-windows.md) Na het voltooien van die zelfstudie, moet u al de volgende voorwaarden:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IoT Edge](quickstart.md).
* Een containerregister, zoals [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) geconfigureerd met de Azure [IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) Tools-extensie.
* [Docker-bureaublad](https://docs.docker.com/docker-for-windows/install/) geconfigureerd voor het uitvoeren van Windows-containers.

> [!TIP]
> Als u Visual Studio 2017 (versie 15.7 of hoger) gebruikt, downloadt en installeert u [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor VS 2017 vanuit de Visual Studio-marktplaats

## <a name="create-a-module-project"></a>Een moduleproject maken

Met de volgende stappen wordt een IoT Edge-moduleproject gemaakt met Behulp van Visual Studio en de azure IoT Edge Tools-extensie. Zodra u een projectsjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van hun gerapporteerde eigenschappen.

### <a name="create-a-new-project"></a>Een nieuw project maken

De Azure IoT Edge Tools bieden projectsjablonen voor alle ondersteunde IoT Edge-moduletalen in Visual Studio. Deze sjablonen bevatten alle bestanden en code die u nodig hebt om een werkende module te implementeren om IoT Edge te testen, of u een startpunt te geven om de sjabloon aan te passen met uw eigen bedrijfslogica.

1. Start Visual Studio 2019 en selecteer **Nieuw project maken**.

2. Zoek naar **IoT Edge** en kies het **Azure IoT Edge -project (Windows AMD64).** Klik op **Volgende**.

   ![Een nieuw Azure IoT Edge-project maken](./media/tutorial-csharp-module-windows/new-project.png)

3. Wijzig de naam van het project en de oplossing voor iets beschrijvends zoals **CSharpTutorialApp**. Klik **op Maken** om het project te maken.

   ![Een nieuw Azure IoT Edge-project configureren](./media/tutorial-csharp-module-windows/configure-project.png)

4. Configureer uw project met de volgende waarden:

   | Veld | Waarde |
   | ----- | ----- |
   | Een sjabloon selecteren | Selecteer **C#-module**. |
   | Moduleprojectnaam | Geef de module de naam **CSharpModule**. |
   | Docker-afbeeldingsopslagplaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf ingevuld vanaf de waarde van de projectnaam module. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/csharpmodule. |

   ![Uw project configureren op doelapparaat, moduletype en containerregister](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selecteer **Toevoegen** om het project te maken.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

Het implementatiemanifest deelt de referenties voor uw containerregister met de runtime van IoT Edge. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **Toegangssleutels** van uw Azure-containerregister.

1. Open het bestand **deployment.template.json** in de oplossingsverkenner van Visual Studio.

2. Zoek de eigenschap **registryCredentials** in de gewenste $edgeAgent gewenste eigenschappen. Het moet uw registeradres automatisch laten invullen van de informatie die u bij het maken van het project hebt opgegeven, en vervolgens moeten gebruikersnaam- en wachtwoordvelden variabele namen bevatten. Bijvoorbeeld:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Open het **.env-bestand** in uw moduleoplossing. (Het is standaard verborgen in de Oplossingsverkenner, dus het kan nodig zijn om de knop **Alle bestanden weergeven** te selecteren om het weer te geven.) Het .env-bestand moet dezelfde gebruikersnaam- en wachtwoordvariabelen bevatten die u in het bestand deployment.template.json hebt gezien.

4. Voeg de **waarden gebruikersnaam** en **wachtwoord** toe uit uw Azure-containerregister.

5. Sla uw wijzigingen op in het .env-bestand.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

De standaardmodulecode ontvangt berichten in een invoerwachtrij en geeft deze door een uitvoerwachtrij. Laten we wat extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat u ze doorstuurt naar IoT Hub. Werk de module zo bij dat de temperatuurgegevens in elk bericht worden geanalyseerd en wordt het bericht alleen naar IoT Hub verzonden als de temperatuur een bepaalde drempel overschrijdt.

1. Open in Visual Studio **CSharpModule** > **Program.cs**.

2. Voeg bovenaan de naamruimte **CSharpModule** drie **using**-instructies toe voor typen die later worden gebruikt:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Voeg de **variabele temperatuurDrempel** toe aan de klasse **Programma** na de variabele teller. De variabele temperatuurDrempel stelt de waarde in die de gemeten temperatuur moet overschrijden voor de gegevens die naar de IoT-hub moeten worden verzonden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Voeg de klassen **MessageBody,** **Machine**en **Ambient** toe aan de klasse **Programma** na de variabele declaratie. Deze klassen bepalen het verwachte schema voor de hoofdtekst van inkomende berichten.

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

5. Zoek de **Init-methode.** Met deze methode wordt een **ModuleClient-object** gemaakt en geconfigureerd, waarmee de module verbinding kan maken met de lokale Azure IoT Edge-runtime om berichten te verzenden en te ontvangen. De code registreert ook een callback om berichten van een IoT Edge-hub te ontvangen via het **ingang1-eindpunt.**

   Vervang de volledige Init-methode door de volgende code:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Deze bijgewerkte Init-methode stelt nog steeds de verbinding met de IoT Edge-runtime in met de ModuleClient, maar voegt ook nieuwe functionaliteit toe. Het leest de gewenste eigenschappen van de moduletwin om de waarde van de **temperatuurdrempel** op te halen. Vervolgens wordt een callback die luistert naar eventuele toekomstige updates van de gewenste eigenschappen van de moduletweeling. Met deze callback u de temperatuurdrempel in de moduletwin op afstand bijwerken en worden de wijzigingen in de module opgenomen.

   De bijgewerkte Init-methode wijzigt ook de bestaande **SetInputMessageHandlerAsync-methode.** In de voorbeeldcode worden binnenkomende berichten op *input1* verwerkt met de *pipemessage-functie,* maar we willen die wijzigen om de functie *Filterberichten* te gebruiken die we in de volgende stappen maken.

6. Voeg een nieuwe **methode onDesiredPropertiesUpdate** toe aan de klasse **Program.** Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

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

7. Verwijder de **voorbeeldmethode PipeMessage** en vervang deze door een nieuwe **methode FilterBerichten.** Deze methode wordt aangeroepen wanneer de module een bericht ontvangt van de IoT Edge-hub. Berichten die temperaturen onder de drempelwaarde die via de moduledubbel is ingesteld, worden hierdoor gefilterd. Ook wordt de eigenschap **BerichtType** toegevoegd aan het bericht met de waarde ingesteld op **Alarm**.

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
                using(var filteredMessage = new Message(messageBytes))
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

9. Open het **bestand deployment.template.json** in uw IoT Edge-oplossing. Dit bestand vertelt de IoT Edge-agent welke modules moeten worden geïmplementeerd, in dit geval **SimulatedTemperatureSensor** en **CSharpModule,** en vertelt de IoT Edge-hub hoe berichten tussen deze modules kunnen worden doorgestuurd.

10. Voeg de moduledubbel **CSharpModule** toe aan het distributiemanifest. Voeg de volgende JSON-inhoud onder aan de sectie **modulesContent** in, na de moduledubbel **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Moduledubbel toevoegen aan implementatiesjabloon](./media/tutorial-csharp-module-windows/module-twin.png)

11. Sla het bestand deployment.template.json op.

## <a name="build-and-push-your-module"></a>Bouw en duw je module

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **CSharpModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare drempelwaarde is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Gebruik de volgende opdracht om je aan te melden bij Docker op je ontwikkelmachine. Gebruik de gebruikersnaam, het wachtwoord en de aanmeldingsserver uit uw Azure-containerregister. U deze waarden ophalen uit de sectie **Toegangssleutels** van uw register in de Azure-portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk ontvangt u een beveiligingswaarschuwing `--password-stdin`waarin het gebruik van . Hoewel die aanbevolen procedures worden aanbevolen voor productiescenario's, valt deze buiten het bereik van deze zelfstudie. Zie de [inlogreferentie voor docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

2. Klik in de Visual Studio-oplossingsverkenner met de rechtermuisknop op de projectnaam die u wilt bouwen. De standaardnaam is **AzureIotEdgeApp1** en aangezien u een Windows-module bouwt, moet de extensie **Windows.Amd64**zijn.

3. Selecteer **IoT-randmodules bouwen en pushen**.

   De opdracht bouwen en pushen start drie bewerkingen. Ten eerste maakt het een nieuwe map in de oplossing genaamd **config** die het volledige implementatiemanifest bevat, opgebouwd uit informatie in de implementatiesjabloon en andere oplossingsbestanden. Ten tweede `docker build` wordt het uitgevoerd om de containerafbeelding te bouwen op basis van het juiste dockerbestand voor uw doelarchitectuur. Vervolgens wordt `docker push` het uitgevoerd om de afbeeldingsopslagplaats naar uw containerregister te duwen.

## <a name="deploy-modules-to-device"></a>Modules implementeren op apparaat

Gebruik de Visual Studio cloud explorer en de Azure IoT Edge Tools-extensie om het moduleproject te implementeren op uw IoT Edge-apparaat. U hebt al een implementatiemanifest voorbereid op uw scenario, het **bestand deployment.json** in de config-map. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is.

1. Vouw in de Visual Studio-cloudexplorer de resources uit om uw lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat dat u wilt ontvangen.

3. Selecteer **Implementatie maken**.

4. Selecteer in de verkenner het **bestand deployment.windows-amd64** in de config-map van uw oplossing.

5. Vernieuw de cloudexplorer om de geïmplementeerde modules onder uw apparaat weer te geven.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart.

U de extensie IoT Edge Tools gebruiken om berichten te bekijken wanneer ze bij uw IoT Hub aankomen.

1. Selecteer in de Visual Studio-cloudexplorer de naam van uw IoT Edge-apparaat.

2. Selecteer in de lijst **Acties** **het eindpunt van ingebouwde gebeurtenis**met ingebouwde gebeurtenis starten .

3. Bekijk de berichten die aankomen op uw IoT Hub. Het kan even duren voordat de berichten binnenkomen, omdat de wijzigingen die we hebben aangebracht in de CSharpModule-code wachten tot de temperatuur van de machine 25 graden bereikt voordat ze berichten verzenden. Het voegt ook het berichttype **Alert** toe aan berichten die die temperatuurdrempel bereiken.

   ![Berichten bekijken die aankomen op IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De moduletwee bewerken

We gebruikten de CSharpModule module twin om de temperatuurdrempel op 25 graden te zetten. U de moduletwee gebruiken om de functionaliteit te wijzigen zonder dat u de modulecode hoeft bij te werken.

1. Open in Visual Studio het **bestand deployment.windows-amd64.json.** (Niet het bestand deployment.template. Als u het implementatiemanifest niet ziet in het config-bestand in de oplossingsverkenner, selecteert u het pictogram **Alle bestanden weergeven** op de werkbalk van de verkenner.)

2. Zoek de CSharpModule twin en verander de waarde van de **parameter temperatureThreshold** naar een nieuwe temperatuur 5 graden tot 10 graden hoger dan de laatst gerapporteerde temperatuur.

3. Sla het **bestand deployment.windows-amd64.json op.**

4. Volg de implementatiestappen opnieuw om het bijgewerkte implementatiemanifest op uw apparaat toe te passen.

5. Monitor de binnenkomende device-to-cloud berichten. U ziet de berichten stoppen totdat de nieuwe temperatuurdrempel is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders u de lokale configuraties en de Azure-resources verwijderen die u in dit artikel hebt gebruikt om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, u meer te weten komen over [het ontwikkelen van uw eigen IoT Edge-modules](module-development.md) of hoe u modules ontwikkelen met Visual [Studio.](how-to-visual-studio-develop-module.md) Zie Voorbeeld van IoT Edge-modules, waaronder de gesimuleerde temperatuurmodule, [iot edge-modulevoorbeelden.](https://github.com/Azure/iotedge/tree/master/edge-modules)

U doorgaan naar de volgende zelfstudies om te leren hoe Azure IoT Edge u kan helpen Azure-cloudservices te implementeren om gegevens aan de rand te verwerken en te analyseren.

> [!div class="nextstepaction"]
> [Functies](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
