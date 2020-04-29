---
title: 'Zelf studie: Azure functions implementeren als modules-Azure IoT Edge'
description: In deze zelfstudie ontwikkelt u een Azure-functie als een IoT Edge-module en implementeert u die functie op een Edge-apparaat.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: f909ca12ce080fc5d1241bcc649c041361e405a7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80421168"
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

![Diagram-zelf studie architectuur: faseren en functie module implementeren](./media/tutorial-deploy-function/functions-architecture.png)
</center>

De Azure-functie die u maakt in deze zelfstudie filtert de temperatuurgegevens die door uw apparaat worden gegenereerd. De functie verzendt gegevens alleen upstream naar Azure IoT Hub wanneer de temperatuur boven een opgegeven drempelwaarde komt.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules voor Linux-apparaten](tutorial-develop-for-linux.md). Door deze zelf studie te volt ooien, moet u aan de volgende vereisten voldoen:

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een [Linux-apparaat met Azure IOT Edge](quickstart-linux.md)
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-code](https://code.visualstudio.com/) die is geconfigureerd met de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) is geconfigureerd voor het uitvoeren van Linux-containers.

Als u een IoT Edge module wilt ontwikkelen in met Azure Functions, installeert u de volgende aanvullende vereisten op uw ontwikkel computer:

* [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [De .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Een functieproject maken

De Azure IoT-hulpprogramma's voor Visual Studio Code die u hebt geïnstalleerd als een van de vereiste onderdelen bieden niet alleen beheermogelijkheden, maar ook enkele codesjablonen. In deze sectie gebruikt u Visual Studio Code om een IoT Edge-oplossing te maken waarin een Azure-functie wordt gebruikt.

### <a name="create-a-new-project"></a>Een nieuw project maken

Maak een sjabloon voor een C#-functie oplossing die u kunt aanpassen met uw eigen code.

1. Open Visual Studio Code op uw ontwikkelcomputer.

2. Open het opdracht palet VS code door **weer gave** > **opdracht palet**te selecteren.

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

### <a name="select-your-target-architecture"></a>Selecteer uw doel architectuur

Visual Studio code kan momenteel C-modules ontwikkelen voor Linux AMD64-en Linux ARM32v7-apparaten. U moet selecteren welke architectuur u wilt richten op elke oplossing, omdat de container is gebouwd en anders wordt uitgevoerd voor elk type architectuur. De standaard waarde is Linux AMD64.

1. Open het opdracht palet en zoek naar **Azure IOT Edge: Stel het standaard doel platform voor de rand oplossing**in of selecteer het snelkoppelings pictogram in de zijbalk aan de onderkant van het venster.

2. Selecteer in het opdracht palet de doel architectuur in de lijst met opties. Voor deze zelf studie gebruiken we een virtuele machine van Ubuntu als het IoT Edge-apparaat, zodat de standaard **amd64**wordt bewaard.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

We gaan enkele extra code toevoegen, zodat de module de berichten aan de rand verwerkt voordat deze naar IoT Hub worden doorgestuurd.

1. Open in Visual Studio code **modules** > **CSharpFunction** > **CSharpFunction.cs**.

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

In de vorige sectie hebt u een IoT Edge oplossing gemaakt en de **CSharpFunction** gewijzigd om berichten te filteren met de gerapporteerde computer temperaturen onder de aanvaard bare drempel waarde. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

In deze sectie geeft u de referenties voor het container register voor de tweede keer op (de eerste was in het **. env** -bestand van uw IOT EDGE-oplossing) door zich lokaal aan te melden vanaf uw ontwikkel computer zodat Visual Studio code installatie kopieën kan pushen naar uw REGI ster.

1. Open de VS code geïntegreerde Terminal door **weer gave** > -**Terminal**te selecteren.

2. Meld u aan bij uw containerregister door de volgende opdracht in te voeren in de geïntegreerde terminal. Gebruik de gebruikersnaam en de aanmeldingsserver die u eerder hebt gekopieerd uit het Azure-containerregister.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Wanneer u wordt gevraagd het wacht woord op te geven, plakt u het wacht woord (het wordt niet weer gegeven in het Terminal venster) voor het container register en drukt u op **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. Klik in de VS code Explorer met de rechter muisknop op het bestand Deployment. sjabloon. json en selecteer **Build and Push IOT Edge Solution**.

Wanneer u Visual Studio code voor het bouwen van uw oplossing vertelt, neemt het eerst de informatie in de implementatie sjabloon en genereert een implementatie. JSON-bestand in een nieuwe map met de naam **config**. Daarna worden er twee opdrachten uitgevoerd in de geïntegreerde Terminal `docker build` : `docker push`en. De opdracht build bouwt uw code en plaatst de functies. Vervolgens duwt de push-opdracht de code naar het container register dat u hebt opgegeven tijdens het initialiseren van de oplossing.

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

4. Vernieuw de sectie **Azure IoT Hub-apparaten**. De nieuwe **CSharpFunction** die worden uitgevoerd, worden samen met de **SimulatedTemperatureSensor** -module en de **$edgeAgent** en **$edgeHub**weer geven. Het kan even duren voordat de nieuwe modules worden weergegeven. Het IoT Edge-apparaat moet de nieuwe implementatiegegevens ophalen van IoT Hub, de nieuwe containers starten en de status weer melden bij IoT Hub.

   ![Geïmplementeerde modules in VS Code weergeven](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>De gegenereerde gegevens weergeven

U kunt alle berichten weer geven die op uw IoT-hub arriveren door **Azure IOT hub uit te voeren: controle van het ingebouwde gebeurtenis eindpunt starten** in het opdracht palet.

U kunt de weergave ook filteren om alle berichten te zien die bij de IoT-hub binnenkomen vanuit een specifiek apparaat. Klik met de rechter muisknop op het apparaat in de sectie **Azure IOT Hub-apparaten** en selecteer **controle van het ingebouwde gebeurtenis-eind punt starten**.

Als u het controleren van berichten wilt stoppen, voert u de opdracht **Azure IOT hub: Stop de bewaking van het ingebouwde gebeurtenis eindpunt** in het opdracht palet.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-functiemodule gemaakt met code voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer te weten komen over het [ontwikkelen met Azure IOT Edge voor Visual Studio code](how-to-vs-code-develop-module.md).

Ga verder met de volgende zelfstudies om te leren hoe Azure IoT Edge u verder kan helpen bij het omzetten van uw gegevens in bedrijfsinzichten.

> [!div class="nextstepaction"]
> [Gemiddelden zoeken met een zwevend venster in Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
