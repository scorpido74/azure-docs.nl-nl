---
title: C# IoT Edge-module voor Azure Data Box Edge | Microsoft Documenten
description: Meer informatie over het ontwikkelen van een C# IoT Edge-module die kan worden geïmplementeerd op uw Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942546"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Een C# IoT Edge-module ontwikkelen om bestanden op Data Box Edge te verplaatsen

In dit artikel u een IoT Edge-module maken voor implementatie met uw Data Box Edge-apparaat. Azure Data Box Edge is een opslagoplossing waarmee u gegevens kunt verwerken en via een netwerk kunt verzenden naar Azure.

U Azure IoT Edge-modules met uw Data Box Edge gebruiken om de gegevens te transformeren terwijl deze naar Azure worden verplaatst. De module die in dit artikel wordt gebruikt, implementeert de logica om een bestand van een lokaal aandeel naar een cloudshare op uw Data Box Edge-apparaat te kopiëren.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Maak een containerregister om uw modules op te slaan en te beheren (Docker-afbeeldingen).
> * Maak een IoT Edge-module die u wilt implementeren op uw Data Box Edge-apparaat.


## <a name="about-the-iot-edge-module"></a>Informatie over de IoT Edge-module

Uw Data Box Edge-apparaat kan IoT Edge-modules implementeren en uitvoeren. Edge-modules zijn in wezen Docker-containers die een specifieke taak uitvoeren, zoals het innemen van een bericht van een apparaat, het transformeren van een bericht of het verzenden van een bericht naar een IoT-hub. In dit artikel maakt u een module die bestanden van een lokaal aandeel kopieert naar een cloudshare op uw Data Box Edge-apparaat.

1. Bestanden worden naar het lokale aandeel op uw Data Box Edge-apparaat geschreven.
2. De bestandsgebeurtenisgenerator maakt een bestandsgebeurtenis voor elk bestand dat naar het lokale aandeel is geschreven. De bestandsgebeurtenissen worden ook gegenereerd wanneer een bestand wordt gewijzigd. De bestandsgebeurtenissen worden vervolgens verzonden naar IoT Edge Hub (in IoT Edge-runtime).
3. De aangepaste IoT Edge-module verwerkt de bestandsgebeurtenis om een bestandsgebeurtenisobject te maken dat ook een relatief pad voor het bestand bevat. De module genereert een absoluut pad met behulp van het relatieve bestandspad en kopieert het bestand van het lokale aandeel naar het cloudaandeel. De module verwijdert vervolgens het bestand uit het lokale aandeel.

![Hoe de Azure IoT Edge-module werkt op Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Zodra het bestand zich in het cloudaandeel bevindt, wordt het automatisch geüpload naar uw Azure Storage-account.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of u over het volgende beschikt:

- Een Data Box Edge-apparaat dat wordt uitgevoerd.

    - Het apparaat heeft ook een bijbehorende IoT Hub-bron.
    - Het apparaat heeft Edge compute-rol geconfigureerd.
    Ga voor meer informatie naar [Compute configureren](data-box-edge-deploy-configure-compute.md#configure-compute) voor uw Data Box Edge.

- De volgende ontwikkelingsbronnen:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [De extensie C# voor Visual Studio Code (van OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Azure IoT Edge-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Mogelijk moet u een account aanmaken om de software te downloaden en te installeren.

## <a name="create-a-container-registry"></a>Een containerregister maken

Een Azure-containerregister is een persoonlijk Docker-register in Azure waar u uw persoonlijke installatiekopieën van de Docker-container kunt opslaan en beheren. De twee populaire Docker-registerservices die beschikbaar zijn in de cloud zijn Azure Container Registry en Docker Hub. In dit artikel wordt gebruik gebruikt voor het containerregister.

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
2. Selecteer **Een resource > containers > containerregister maken.** Klik **op Maken**.
3. Geef op:

   1. Een unieke **registernaam** binnen Azure die 5 tot 50 alfanumerieke tekens bevat.
   2. Kies een **abonnement**.
   3. Maak nieuwe of kies een bestaande **resourcegroep**.
   4. Selecteer een **locatie**. We raden aan dat deze locatie dezelfde is als die is gekoppeld aan de Data Box Edge-bron.
   5. Stel de **Gebruiker met beheerdersrechten** in op **Inschakelen**.
   6. Stel de SKU in op **Basic.**

      ![Containerregister maken](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Selecteer **Maken**.
5. Nadat het containerregister is gemaakt, bladert u ernaartoe en selecteert u **Toegangssleutels**.

    ![Toegangssleutels ophalen](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Kopieer de waarden voor **Aanmeldingsserver**, **Gebruikersnaam** en **wachtwoord**. U gebruikt deze waarden later om de Docker-afbeelding in uw register te publiceren en de registerreferenties toe te voegen aan de runtime van Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Een IoT Edge-moduleproject creëren

Met de volgende stappen wordt een IoT Edge-moduleproject gemaakt op basis van de .NET Core 2.1 SDK. Het project maakt gebruik van Visual Studio Code en de Azure IoT Edge-extensie.

### <a name="create-a-new-solution"></a>Een nieuwe oplossing maken

Maak een C#-oplossingssjabloon die u met uw eigen code kunt aanpassen.

1. Selecteer in Visual Studio Code **het opdrachtpalet > weergeven** om het opdrachtpalet VS-code te openen.
2. Voer in het opdrachtpalet de opdracht **Azure: Sign in** in en voer deze uit. Volg vervolgens de instructies om u aan te melden bij uw Azure-account. Als u al bent aangemeld, kunt u deze stap overslaan.
3. Voer in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken:

    1. Selecteer de map waarin u de oplossing wilt maken.
    2. Geef een naam op voor de oplossing of houd de standaardnaam **EdgeSolution** aan.
    
        ![Nieuwe oplossing maken 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Kies **C# Module** als modulesjabloon.
    4. Vervang de standaardmodulenaam door de naam die u wilt toewijzen, in dit geval is dit **FileCopyModule.**
    
        ![Nieuwe oplossing maken 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Geef het containerregister op dat u in de vorige sectie hebt gemaakt als de afbeeldingsopslagplaats voor de eerste module. Vervang **localhost:5000** door de gekopieerde waarde voor de aanmeldingsserver.

        De laatste snaar ziet eruit als. `<Login server name>/<Module name>` In dit voorbeeld is `mycontreg2.azurecr.io/filecopymodule`de tekenreeks: .

        ![Nieuwe oplossing maken 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Ga naar **Bestand > Map openen**.

    ![Nieuwe oplossing maken 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Blader en wijs de **EdgeSolution-map** aan die u eerder hebt gemaakt. Het VENSTER VS-code laadt uw IoT Edge-oplossingswerkruimte met zijn vijf componenten op het hoogste niveau. U bewerkt de map **.vscode,** **.gitignore-bestand,** **.env-bestand** en de **deployment.template.json** in dit artikel niet.
    
    Het enige onderdeel dat u wijzigt, is de map modules. Deze map heeft de C#-code voor uw module en Docker-bestanden om uw module als containerafbeelding te bouwen.

    ![Nieuwe oplossing maken 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in de VS-codeverkenner **modules > FileCopyModule > Program.cs**.
2. Voeg boven aan de **naamruimte van FileCopyModule**het volgende toe met instructies voor typen die later worden gebruikt. **Microsoft.Azure.Devices.Client.Transport.Mqtt** is een protocol om berichten naar IoT Edge Hub te verzenden.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Voeg de variabele **InputFolderPath** en **OutputFolderPath** toe aan de klasse Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Voeg onmiddellijk na de vorige stap de klasse **FileEvent** toe om de berichttekst te definiëren.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. In de **methode Init**maakt en configureert de code een **object ModuleClient.** Met dit object kan de module verbinding maken met de lokale Azure IoT Edge-runtime met behulp van het MQTT-protocol om berichten te verzenden en te ontvangen. De verbindingsreeks die in de methode Init wordt gebruikt, wordt door de IoT Edge-runtime aan de module geleverd. De code registreert een FileCopy-terugroep om berichten van een IoT Edge-hub te ontvangen via het **ingang1-eindpunt.** Vervang de **Init-methode** door de volgende code.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Verwijder de code voor **pipemessage-methode** en voeg in plaats daarvan de code voor **FileCopy**in.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub.
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. Sla dit bestand op.
8. U ook [een bestaand codevoorbeeld](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) voor dit project downloaden. Vervolgens u het bestand valideren dat u hebt opgeslagen tegen het **program.cs** bestand in dit voorbeeld.

## <a name="build-your-iot-edge-solution"></a>Uw eigen IoT Edge-oplossing bouwen

In de vorige sectie hebt u een IoT Edge-oplossing gemaakt en code toegevoegd aan de FileCopyModule om bestanden van lokaal aandeel naar het cloudaandeel te kopiëren. Nu moet u de oplossing bouwen als een containerinstallatiekopie en deze naar het containerregister pushen.

1. Ga in VSCode naar Terminal > New Terminal om een nieuwe geïntegreerde Visual Studio Code-terminal te openen.
2. Meld u aan bij Docker door de volgende opdracht in te voeren in de geïntegreerde terminal.

    `docker login <ACR login server> -u <ACR username>`

    Gebruik de inlogserver en gebruikersnaam die u hebt gekopieerd uit uw containerregister.

    ![IoT Edge-oplossing bouwen en pushen](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Wanneer u om een wachtwoord wordt gevraagd, geeft u het wachtwoord op. U de waarden voor de aanmeldingsserver, gebruikersnaam en wachtwoord ook ophalen uit de **toegangssleutels** in uw containerregister in de Azure-portal.
 
3. Zodra de referenties zijn geleverd, u uw moduleafbeelding naar uw Azure-containerregister pushen. Klik in de VS Code Explorer met de rechtermuisknop op het bestand **module.json** en selecteer **IoT Edge-oplossing bouwen en pushen.**

    ![IoT Edge-oplossing bouwen en pushen](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Wanneer u Visual Studio Code vertelt om uw oplossing te bouwen, voert deze twee opdrachten uit in de geïntegreerde terminal: docker build en docker push. Met deze twee opdrachten wordt uw code gebouwd, wordt de CSharpModule.dll in een container opgeslagen en wordt de code vervolgens naar het containerregister gepusht dat u hebt opgegeven toen u de oplossing initialiseerde.

    U wordt gevraagd om het moduleplatform te kiezen. Selecteer *amd64* die overeenkomt met Linux.

    ![Platform selecteren](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Alleen de Linux modules worden ondersteund.

    Mogelijk ziet u de volgende waarschuwing die u negeren:

    *Program.cs(77,44): waarschuwing CS1998: Deze async-methode mist 'wachten' operators en zal synchroon worden uitgevoerd. Overweeg de operator 'wachten' te gebruiken om niet-blokkerende API-aanroepen af te wachten, of 'wacht op Task.Run(...)' om CPU-gebonden werk te doen aan een achtergrondthread.*

4. U kunt het volledige adres van de containerinstallatiekopie, inclusief de tag, zien in de geïntegreerde terminal van VS Code. Het afbeeldingsadres is opgebouwd uit informatie in het module.json-bestand met de indeling `<repository>:<version>-<platform>`. Voor dit artikel, moet `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`het eruit zien .

## <a name="next-steps"></a>Volgende stappen

Zie de stappen in [Een module toevoegen](data-box-edge-deploy-configure-compute.md#add-a-module)als u deze module wilt implementeren en uitvoeren op De rand van het gegevensvak.
