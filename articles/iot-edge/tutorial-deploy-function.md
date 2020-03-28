---
title: 'Zelfstudie: Azure-functies implementeren als modules - Azure IoT Edge'
description: In deze zelfstudie ontwikkelt u een Azure-functie als een IoT Edge-module en implementeert u die functie op een Edge-apparaat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1f4b3387375b52b28600590a099884277f3de63e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943036"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Zelfstudie: Azure-functies implementeren als IoT Edge-modules

U kunt Azure-functies gebruiken voor het implementeren van code die uw bedrijfslogica rechtstreeks op uw Azure IoT Edge-apparaten implementeert. In deze zelfstudie leert u hoe u een Azure-functie maakt en implementeert die sensorgegevens op het gesimuleerde IoT Edge-apparaat filtert. U gebruikt het gesimuleerde IoT Edge-apparaat dat u hebt gemaakt in de snelstarts over het implementeren van Azure IoT Edge op een gesimuleerd apparaat in [Windows](quickstart.md) of [Linux](quickstart-linux.md). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een Azure-functie te maken.
> * VS Code en Docker gebruiken om een Docker-installatiekopie te maken en deze te publiceren naar een containerregister.
> * De module vanuit het containerregister implementeren op uw IoT Edge-apparaat.
> * Gefilterde gegevens weergeven.

<center>

![Diagram - Zelfstudiearchitectuur: functiemodule fase en implementeren](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure Functions-modules in Azure IoT Edge zijn in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

De Azure-functie die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. De functie verzendt gegevens alleen upstream naar Azure IoT Hub wanneer de temperatuur boven een opgegeven drempelwaarde komt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, had u de vorige zelfstudie moeten doorlopen om uw ontwikkelomgeving in te stellen voor de ontwikkeling van Linux-containers: [IoT Edge-modules ontwikkelen voor Linux-apparaten.](tutorial-develop-for-linux.md) Door het invullen van die tutorial, moet u de volgende voorwaarden op zijn plaats:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IoT Edge](quickstart-linux.md)
* Een containerregister, zoals [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd om Linux-containers uit te voeren.

Als u een IoT Edge-module wilt ontwikkelen in met Azure-functies, installeert u de volgende aanvullende vereisten op uw ontwikkelingsmachine:

* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [De .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Een functieproject maken

De Azure IoT-hulpprogramma's voor Visual Studio Code die u hebt geïnstalleerd als een van de vereiste onderdelen bieden niet alleen beheermogelijkheden, maar ook enkele codesjablonen. In deze sectie gebruikt u Visual Studio Code om een IoT Edge-oplossing te maken waarin een Azure-functie wordt gebruikt.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een C#-functieoplossingssjabloon die u met uw eigen code aanpassen.

1. Open Visual Studio Code op uw ontwikkelcomputer.

2. Open het opdrachtpalet VS-code door **Opdrachtpalet weergeven** > **te**selecteren .

3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Volg de aanwijzingen in het opdrachtpalet om uw oplossing te maken.

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor uw oplossing in, bijvoorbeeld **FunctionSolution**, of accepteer de standaardinstelling. |
   | Modulesjabloon selecteren | Kies **Azure Functions - C#**. |
   | Een modulenaam opgeven | Geef de module de naam **CSharpFunction**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de laatste stap. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. De uiteindelijke tekenreeks ziet er ongeveer als volgt uit: \<registernaam\>.azurecr.io/CSharpFunction. |

   ![Opslagplaats voor Docker-installatiekopieën opgeven](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

1. Open in VS Code Explorer het .env-bestand.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Selecteer uw doelarchitectuur

Momenteel kan Visual Studio Code C-modules ontwikkelen voor Linux AMD64- en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u bij elke oplossing target, omdat de container voor elk architectuurtype anders is gebouwd en deze wordt uitgevoerd. De standaard is Linux AMD64.

1. Open het opdrachtpalet en zoek naar **Azure IoT Edge: Stel standaarddoelplatform in voor edge-oplossing**of selecteer het sneltoetspictogram in de zijbalk onder aan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. Voor deze tutorial gebruiken we een Virtuele Ubuntu-machine als het IoT Edge-apparaat, dus houdt de standaard **amd64**.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

Laten we wat extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat u ze doorstuurt naar IoT Hub.

1. Open in Visual Studio Code **de modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Vervang de inhoud van het bestand **CSharpFunction.cs** door de volgende code. Deze code ontvangt telemetrie over omgevings- en machinetemperatuur en stuurt het bericht alleen door naar IoT Hub als de machinetemperatuur hoger is dan een opgegeven drempelwaarde.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

1. Sla het bestand op.

## <a name="build-your-iot-edge-solution"></a>Uw eigen IoT Edge-oplossing bouwen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en de **CSharpFunctie** gewijzigd om berichten met gerapporteerde machinetemperaturen onder de aanvaardbare drempelwaarde uit te filteren. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

In deze sectie verstrekt u de referenties voor uw containerregister voor de tweede keer (de eerste was in het **.env-bestand** van uw IoT Edge-oplossing) door u lokaal aan te melden vanaf uw ontwikkelingsmachine, zodat Visual Studio Code afbeeldingen naar uw register kan pushen.

1. Open de geïntegreerde VS-codeterminal door **View** > **Terminal te**selecteren .

2. Meld u aan bij uw containerregister door de volgende opdracht in te voeren in de geïntegreerde terminal. Gebruik de gebruikersnaam en de aanmeldingsserver die u eerder hebt gekopieerd uit het Azure-containerregister.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Wanneer u om het wachtwoord wordt gevraagd, plakt u het wachtwoord (het is niet zichtbaar in het terminalvenster) voor uw containerregister en drukt u op **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. Klik in de VS-codeverkenner met de rechtermuisknop op het bestand deployment.template.json en selecteer **de Oplossing IoT Edge bouwen en pushen**.

Wanneer u Visual Studio Code vertelt om uw oplossing te bouwen, neemt deze eerst de informatie in de implementatiesjabloon en genereert u een deployment.json-bestand in een nieuwe map met de naam **config.** Vervolgens draait het twee commando's `docker build` `docker push`in de geïntegreerde terminal: en . De opdracht Build bouwt uw code en containeriseert de functies. Vervolgens duwt de pushopdracht de code naar het containerregister dat u hebt opgegeven toen u de oplossing initieerde.

## <a name="view-your-container-image"></a>De containerinstallatiekopie weergeven

In Visual Studio Code wordt een succesbericht weergegeven wanneer de containerinstallatiekopie naar het containerregister is doorgezet. Als u zelf wilt bevestigen dat de bewerking is geslaagd, kunt u de installatiekopie in het register bekijken.

1. Blader in de Azure-portal naar het Azure-containerregister.
2. Selecteer **Opslagplaatsen**.
3. U zou nu de opslagplaats **csharpfunction** in de lijst moeten zien. Selecteer deze opslagplaats om meer details te bekijken.
4. In de sectie **Tags** zou u de tag **0.0.1-amd64** moeten zien. Deze tag bevat de versie en het platform van de installatiekopie die u hebt gemaakt. Deze waarden zijn ingesteld in het bestand module.json in de map CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>De oplossing implementeren en uitvoeren

U kunt de Azure-portal gebruiken om uw functiemodule op een IoT Edge-apparaat te implementeren, zoals u hebt gedaan in de quickstarts. U kunt modules ook vanuit Visual Studio Code implementeren en bewaken. In de volgende secties worden de Azure IoT-hulpprogramma's voor VS Code gebruikt die wordt vermeld in de vereisten. Installeer de extensie nu, als u dat nog niet hebt gedaan.

1. Vouw in VS Code Explorer de sectie **Azure IoT Hub Devices** uit.

2. Klik met de rechtermuisknop op de naam van het IoT Edge-apparaat en selecteer **Implementatie voor één apparaat maken**.

3. Blader naar de oplossingsmap die de **CSharpFunction** bevat. Open de map config, selecteer het bestand **deployment.json** en kies **Edge-distributiemanifest selecteren**.

4. Vernieuw de sectie **Azure IoT Hub-apparaten**. U ziet de nieuwe **CSharpFunction** lopen samen met de **Module SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub.** Het kan even duren voordat de nieuwe modules worden weergegeven. Het IoT Edge-apparaat moet de nieuwe implementatiegegevens ophalen van IoT Hub, de nieuwe containers starten en de status weer melden bij IoT Hub.

   ![Geïmplementeerde modules in VS Code weergeven](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

U alle berichten zien die bij uw IoT-hub binnenkomen door **Azure IoT Hub: Start Monitoring Ingebouwde gebeurteniseindpunt** in het opdrachtpalet.

U kunt de weergave ook filteren om alle berichten te zien die bij de IoT-hub binnenkomen vanuit een specifiek apparaat. Klik met de rechtermuisknop op het apparaat in de sectie **Azure IoT Hub-apparaten** en selecteer **Ingebouwde gebeurteniseindpunt starten**.

Als u het bewaken van berichten wilt stoppen, voert u de opdracht **Azure IoT Hub: Stop Monitoring Ingebouwde gebeurteniseindpunt** in het opdrachtpalet uit.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-functiemodule gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, u meer te weten komen over het [ontwikkelen met Azure IoT Edge voor Visual Studio Code.](how-to-vs-code-develop-module.md)

Ga verder met de volgende zelfstudies om te leren hoe Azure IoT Edge u verder kan helpen bij het omzetten van uw gegevens in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Gemiddelden zoeken met een zwevend venster in Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
