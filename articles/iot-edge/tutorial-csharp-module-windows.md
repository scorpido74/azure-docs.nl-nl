---
title: Zelf studie C# ontwikkel module voor Windows-Azure IOT Edge | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u een IoT Edge module C# maakt met code en deze implementeert op een Windows IOT edge-apparaat.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: eb2a6933b711804f957056353d7d609dbdbbe5d5
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036451"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Zelfstudie: Een C# IOT Edge-module ontwikkelen voor Windows-apparaten

Gebruik Visual Studio om code C# te ontwikkelen en te implementeren op een Windows-apparaat met Azure IOT Edge. 

U kunt Azure IoT Edge-modules gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw IoT Edge-apparaten implementeert. In deze zelfstudie leert u een IoT Edge-module te maken die sensorgegevens filtert. In deze zelfstudie leert u het volgende:    

> [!div class="checklist"]
> * Gebruik Visual Studio om een IoT Edge module te maken op basis van de C# SDK.
> * Gebruik Visual Studio en docker om een docker-installatie kopie te maken en te publiceren naar het REGI ster.
> * De module implementeren op uw IoT Edge-apparaat.
> * Gegenereerde gegevens weergeven.

De IoT Edge-module die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. Er worden alleen gegevens upstream gezonden als de temperatuur boven een opgegeven drempelwaarde komt. Dit soort analyse is nuttig om de hoeveelheid gegevens te reduceren die worden gecommuniceerd naar en worden opgeslagen in de cloud. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Oplossings bereik

In deze zelf studie wordt gedemonstreerd hoe u **C#** een module ontwikkelt in **Visual Studio 2019**en hoe u deze implementeert op een **Windows-apparaat**. Als u modules voor Linux-apparaten ontwikkelt, gaat u in plaats daarvan naar [een C# IOT Edge module ontwikkelen voor Linux-apparaten](tutorial-csharp-module.md) . 

Gebruik de volgende tabel om inzicht te krijgen in de opties voor het ontwikkelen en implementeren van C-modules op Windows-apparaten: 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64 ontwikkelen** | ![Modules C# ontwikkelen voor WINAMD64 in VS code](./media/tutorial-c-module/green-check.png) | ![Modules C# ontwikkelen voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64-fout opsporing** |   | ![Fouten C# opsporen in modules voor WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen, [een IOT Edge module te ontwikkelen voor een Windows-apparaat](tutorial-develop-for-windows.md). Nadat u deze zelf studie hebt voltooid, moet u de volgende vereisten al hebben: 

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Windows-apparaat met Azure IOT Edge](quickstart.md).
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) geconfigureerd met de extensie [Azure IOT Edge-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) is geconfigureerd om Windows-containers uit te voeren.

> [!TIP]
> Als u Visual Studio 2017 (versie 15,7 of hoger) gebruikt, downloadt en installeert u [Azure IOT Edge-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) voor VS 2017 van de Visual Studio Marketplace

## <a name="create-a-module-project"></a>Een module project maken

Met de volgende stappen maakt u een IoT Edge module-project met behulp van Visual Studio en de uitbrei ding Azure IoT Edge tools. Zodra u een project sjabloon hebt gemaakt, voegt u nieuwe code toe zodat de module berichten filtert op basis van de gerapporteerde eigenschappen. 

### <a name="create-a-new-project"></a>Een nieuw project maken

De Azure IoT Edge-Hulpprogram Ma's bieden project sjablonen voor alle ondersteunde IoT Edge module talen in Visual Studio. Deze sjablonen hebben alle bestanden en code die u nodig hebt om een Working-module te implementeren om IoT Edge te testen. u kunt ook een begin punt geven om de sjabloon aan te passen met uw eigen bedrijfs logica. 

1. Start Visual Studio 2019 en selecteer **Nieuw project maken**.

2. Zoek in het venster Nieuw project **IOT Edge** project en kies het **Azure IOT Edge (Windows amd64)-** project. Klik op **Volgende**. 

   ![Een nieuw Azure IoT Edge project maken](./media/tutorial-csharp-module-windows/new-project.png)

3. In het venster uw nieuwe project configureren wijzigt u de naam van het project en de oplossing in een beschrijvende soort, zoals **CSharpTutorialApp**. Klik op **maken** om het project te maken. 

   ![Een nieuw Azure IoT Edge project configureren](./media/tutorial-csharp-module-windows/configure-project.png)

4. Configureer in het venster IoT Edge toepassing en module het project met de volgende waarden: 

   | Veld | Value |
   | ----- | ----- |
   | Selecteer een sjabloon | Selecteer  **C# de module**. | 
   | Naam van module Project | Geef de module de naam **CSharpModule**. | 
   | Docker-afbeeldings opslagplaats | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De container installatie kopie wordt vooraf ingevuld op basis van de waarde van de module Project naam. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br> De uiteindelijke opslagplaats voor de installatiekopie ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/csharpmodule. |

   ![Uw project configureren voor doel apparaat, module type en container register](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Selecteer **OK** om uw wijzigingen toe te passen. 

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

Het implementatie manifest deelt de referenties voor uw container register met de IoT Edge runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen. Gebruik de referenties uit de sectie **toegangs sleutels** van uw Azure container Registry. 

1. Open in Visual Studio Solution Explorer het bestand **Deployment. sjabloon. json** . 

2. Zoek de eigenschap **registryCredentials** in de gewenste eigenschappen van de $edgeAgent. 

3. Werk de eigenschap bij met uw referenties, met de volgende indeling: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Sla het bestand deployment.template.json op. 

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Met de standaard module code worden berichten ontvangen in een invoer wachtrij en door gegeven via een uitvoer wachtrij. We gaan enkele extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat deze naar IoT Hub worden doorgestuurd. Werk de module bij zodat deze de temperatuur gegevens in elk bericht analyseert en alleen het bericht verzendt naar IoT Hub als de Tempe ratuur een bepaalde drempel waarde overschrijdt. 

1. Open **CSharpModule** > **Program.cs**in Visual Studio.

2. Voeg bovenaan de naamruimte **CSharpModule** drie **using**-instructies toe voor typen die later worden gebruikt:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Voeg de variabele **temperatureThreshold** toe aan de klasse **Program** na de variabele Counters. De variabele temperatureThreshold stelt de waarde in die de gemeten Tempe ratuur moet overschrijden voor de gegevens die moeten worden verzonden naar de IoT-hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Voeg de **MessageBody**-, **machine**-en **Ambient** klassen toe aan de **programma** klasse na de variabelen declaraties. Deze klassen bepalen het verwachte schema voor de hoofdtekst van inkomende berichten.

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

5. Zoek de **init** -methode. Met deze methode wordt een **ModuleClient** -object gemaakt en geconfigureerd, waarmee de module verbinding kan maken met de lokale Azure IOT Edge runtime om berichten te verzenden en te ontvangen. De code registreert ook een call back om berichten van een IoT Edge hub te ontvangen via het **input1** -eind punt.

   Vervang de volledige init-methode door de volgende code:
   
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
   
   Deze bijgewerkte init-methode stelt nog steeds de verbinding met de IoT Edge runtime in met de ModuleClient, maar voegt ook nieuwe functionaliteit toe. Hiermee worden de gewenste eigenschappen van de module twee gelezen om de **temperatureThreshold** -waarde op te halen. Vervolgens wordt er een call back gemaakt die luistert naar toekomstige updates voor de gewenste eigenschappen van de module. Met deze retour aanroep kunt u de drempel waarde voor de Tempe ratuur in de module twee maal op afstand bijwerken en de wijzigingen worden opgenomen in de module. 

   De bijgewerkte init-methode wijzigt ook de bestaande methode **SetInputMessageHandlerAsync** . In de voorbeeld code worden inkomende berichten op *input1* verwerkt met de functie *PipeMessage* , maar we willen dat wijzigen om de functie *FilterMessages* te gebruiken die we in de volgende stappen gaan maken. 

6. Voeg een nieuwe **onDesiredPropertiesUpdate** -methode toe aan de klasse **Program** . Deze methode ontvangt updates van de gewenste eigenschappen van de moduledubbel en updatet de variabele **temperatureThreshold** naar dezelfde waarde. Alle modules hebben hun eigen moduledubbel, waardoor u rechtstreeks in de cloud de code kunt configureren die in een module wordt uitgevoerd.

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

7. Verwijder de voor beeld- **PipeMessage** -methode en vervang deze door een nieuwe **FilterMessages** -methode. Deze methode wordt aangeroepen wanneer de module een bericht ontvangt van de IoT Edge-hub. Berichten die temperaturen onder de drempelwaarde die via de moduledubbel is ingesteld, worden hierdoor gefilterd. Ook wordt de eigenschap **BerichtType** toegevoegd aan het bericht met de waarde ingesteld op **Alarm**. 

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
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
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

9. Open het bestand **Deployment. Temp late. json** in uw IOT EDGE-oplossing. Dit bestand vertelt de IoT Edge-agent welke modules er moeten worden geïmplementeerd, in dit geval **SimulatedTemperatureSensor** en **CSharpModule**, en geeft aan de IOT Edge hub berichten ertussen routeren.

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


## <a name="build-and-push-your-module"></a>Uw module bouwen en pushen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de **CSharpModule** om berichten te filteren waarin de gemelde temperatuur van de machine onder de aanvaardbare drempelwaarde is. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen. 

1. Gebruik de volgende opdracht om u aan te melden bij docker op uw ontwikkel computer. Gebruik de gebruikers naam, het wacht woord en de aanmeldings server van uw Azure container Registry. U kunt deze waarden ophalen uit de sectie **toegangs sleutels** van het REGI ster in de Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen het `--password-stdin`gebruik van te gebruiken. Hoewel dat best practice wordt aanbevolen voor productie scenario's, valt het buiten het bereik van deze zelf studie. Zie voor meer informatie de koppeling naar docker- [aanmelding](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. Klik in de Solution Explorer van Visual Studio met de rechter muisknop op de naam van het project dat u wilt bouwen. De standaard naam is **AzureIotEdgeApp1** en omdat u een Windows-module bouwt, moet de extensie **Windows. amd64**zijn. 

3. Selecteer **Build-en Push IOT Edge-modules**. 

   Met de opdracht build en push worden drie bewerkingen gestart. Eerst wordt een nieuwe map gemaakt in de oplossing **config** met het volledige implementatie manifest, gebaseerd op de gegevens in de implementatie sjabloon en andere oplossings bestanden. Ten tweede wordt `docker build` de container installatie kopie gemaakt op basis van de juiste dockerfile voor uw doel architectuur. Vervolgens wordt `docker push` de opslag plaats voor de installatie kopie naar het container register pusht. 

## <a name="deploy-modules-to-device"></a>Modules implementeren op het apparaat

Gebruik de Visual Studio-Cloud Verkenner en de uitbrei ding van de Azure IoT Edge-Hulpprogram Ma's om het module project te implementeren op uw IoT Edge-apparaat. U hebt al een implementatie manifest voor bereid voor uw scenario, het bestand **Deployment. json** in de map Config. U hoeft nu alleen nog maar een apparaat te selecteren dat de implementatie moet ontvangen.

Zorg ervoor dat uw IoT Edge-apparaat actief is. 

1. Vouw in de Visual Studio-Cloud Verkenner de resources uit om de lijst met IoT-apparaten weer te geven. 

2. Klik met de rechter muisknop op de naam van het IoT Edge apparaat waarvan u de implementatie wilt ontvangen. 

3. Selecteer **implementatie maken**.

4. Selecteer in de Verkenner het bestand **Deployment. Windows-amd64** in de map config van uw oplossing. 

5. Vernieuw de Cloud Verkenner om de geïmplementeerde modules onder uw apparaat weer te geven. 

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

Als u het implementatiemanifest op uw IoT Edge-apparaat toepast, verzamelt de IoT Edge-runtime op het apparaat de informatie over de nieuwe implementatie en wordt deze uitgevoerd. Modules die worden uitgevoerd op het apparaat en die niet zijn opgenomen in het implementatiemanifest, worden gestopt. Alle modules die ontbreken op het apparaat worden gestart. 

U kunt de extensie IoT Edge-Hulpprogram Ma's gebruiken om berichten weer te geven die op uw IoT Hub arriveren. 

1. Selecteer in de Visual Studio Cloud Explorer de naam van uw IoT Edge-apparaat. 

2. Selecteer in de lijst **acties** de optie **controle van ingebouwd gebeurtenis-eind punt starten**. 

3. Bekijk de berichten die op uw IoT Hub arriveren. Het kan enige tijd duren voordat de berichten binnenkomen, omdat de wijzigingen die we hebben aangebracht in de CSharpModule-code wachten tot de computer temperatuur 25 graden bereikt voordat berichten worden verzonden. Het bericht type **waarschuwing** wordt ook toegevoegd aan berichten die de drempel waarde voor de Tempe ratuur bereiken. 

   ![Berichten weer geven die binnenkomen op IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>De module twee bewerken

We hebben de CSharpModule-module gebruikt om de drempel waarde voor de Tempe ratuur op 25 graden in te stellen. U kunt de module twee gebruiken om de functionaliteit te wijzigen zonder dat u de module code hoeft bij te werken.

1. Open in Visual Studio het bestand **Deployment. Windows-amd64. json** . (Niet het bestand Deployment. Temp late. Als u het implementatie manifest niet in het configuratie bestand in het Solution Explorer ziet, selecteert u het pictogram **alle bestanden weer geven** in de Explorer-werk balk.)

2. Zoek de CSharpModule-dubbele en wijzig de waarde van de para meter **temperatureThreshold** in een nieuwe Tempe ratuur van vijf graden tot 10 graden hoger dan de laatste gerapporteerde Tempe ratuur. 

3. Sla de **implementatie. Windows-amd64. json** -bestand op.

4. Volg de implementaties tappen opnieuw om het bijgewerkte implementatie manifest op uw apparaat toe te passen. 

5. Bewaak de binnenkomende apparaat-naar-Cloud-berichten. U ziet dat de berichten stoppen totdat de nieuwe temperatuur drempel is bereikt. 

## <a name="clean-up-resources"></a>Resources opschonen 

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat. 

Anders kunt u de lokale configuraties en de Azure-resources die u in dit artikel hebt gebruikt, verwijderen om kosten te voor komen. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een IoT Edge-module gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer te weten komen over [het ontwikkelen van uw eigen IOT Edge modules](module-development.md) of het [ontwikkelen van modules met Visual Studio](how-to-visual-studio-develop-module.md). U kunt door gaan met de volgende zelf studies om te leren hoe Azure IoT Edge u kan helpen bij de implementatie van Azure Cloud Services voor het verwerken en analyseren van gegevens aan de rand.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
