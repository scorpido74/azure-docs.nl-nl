---
title: 'Zelf studie: gegevens opslaan met SQL-module met behulp van Azure IoT Edge'
description: Deze zelf studie laat zien hoe u gegevens lokaal op uw IoT Edge apparaat opslaat met een SQL Server module
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c16fca06950ea06b80f2e27d6fb845f5d0d282c0
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665130"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Zelfstudie: gegevens opslaan aan de rand met SQL Server-databases

Implementeer een SQL Server-module om gegevens op te slaan op een Linux-apparaat met Azure IoT Edge.

Gebruik Azure IoT Edge en SQL Server voor het opslaan en opvragen van gegevens aan de rand. Azure IoT Edge heeft basisopslagmogelijkheden waarmee berichten in cache worden geplaatst als een apparaat offline is, en worden doorgestuurd wanneer de verbinding weer tot stand is gebracht. Mogelijk wilt u echter meer geavanceerde opslagmogelijkheden, zoals het lokaal kunnen opvragen van gegevens. Uw IoT Edge-apparaten kunnen lokale data bases gebruiken om complexere computing uit te voeren zonder dat er een verbinding met IoT Hub hoeft te worden onderhouden. 

Dit artikel bevat instructies voor het implementeren van een SQL Server-database op een IoT Edge-apparaat. Azure Functions, dat op het IoT Edge-apparaat wordt uitgevoerd, structureert de binnenkomende gegevens en verzendt deze naar de database. De stappen in dit artikel kunnen ook worden toegepast op andere databases die in containers werken, zoals MySQL of PostgreSQL.

In deze zelfstudie leert u het volgende: 

> [!div class="checklist"]
> * Visual Studio Code gebruiken om een Azure-functie te maken
> * Een SQL-database op uw IoT Edge-apparaat implementeren
> * Gebruik Visual Studio Code om modules te bouwen en deze op uw IoT Edge-apparaat te implementeren
> * Gegenereerde gegevens weergeven

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, moet u de vorige zelf studie hebben door lopen om uw ontwikkel omgeving in te stellen voor Linux-container ontwikkeling: [ontwikkel IOT Edge-modules voor Linux-apparaten](tutorial-develop-for-linux.md). Door deze zelf studie te volt ooien, moet u aan de volgende vereisten voldoen: 

* Een gratis of reguliere [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een AMD64 [Linux-apparaat met Azure IOT Edge](quickstart-linux.md).
  * ARM-apparaten, zoals Raspberry Pis, kunnen niet worden uitgevoerd SQL Server. Als u SQL wilt gebruiken op een ARM-apparaat, kunt u zich registreren om [Azure SQL database Edge](https://azure.microsoft.com/services/sql-database-edge/) in de preview-versie te proberen. 
* Een container register, zoals [Azure container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio-code](https://code.visualstudio.com/) die is geconfigureerd met de [Azure IOT-hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) is geconfigureerd voor het uitvoeren van Linux-containers.

In deze zelf studie wordt een Azure Functions module gebruikt voor het verzenden van gegevens naar de SQL Server. Als u een IoT Edge module wilt ontwikkelen met Azure Functions, installeert u de volgende aanvullende vereisten op uw ontwikkel computer: 

* [C#-extensie voor Visual Studio Code (van OmniSharp) voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Een functieproject maken

Om gegevens naar een database te verzenden hebt u een module nodig die de gegevens correct kan structureren en vervolgens in een tabel opslaat. 

### <a name="create-a-new-project"></a>Een nieuw project maken

De volgende stappen laten zien hoe u een IoT-Edge-functie maakt met behulp van Visual Studio Code en de Azure IoT Edge-hulpprogramma's.

1. Open Visual Studio Code.

2. Selecteer **View** > **Command Palette** en open het VS Code-opdrachtpalet.

3. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken: 

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in, bijvoorbeeld **SqlSolution**, of accepteer de standaardinstelling. |
   | Modulesjabloon selecteren | Kies **Azure Functions - C#** . |
   | Een modulenaam opgeven | Noem uw module **sqlFunction**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de laatste stap. Vervang **localhost:5000** door de waarde van de aanmeldingsserver uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke teken reeks ziet eruit als \<register naam\>. azurecr.io/sqlfunction. |

   In het VS Code-venster wordt de werkruimte van de IoT Edge-oplossing geladen. 
   
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

1. Open in VS Code Explorer **modules** > **sqlFunction** > **sqlFunction.cs**.

2. Vervang de volledige inhoud van het bestand door de volgende code:

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
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
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

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
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

3. Vervang in regel 35 de tekenreeks **\<sql-verbindingsreeks\>** door de volgende tekenreeks. De eigenschap **Data Source** verwijst naar de SQL Server container, die nog niet bestaat, maar die u maakt met de naam **SQL** in de volgende sectie. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Sla het bestand **sqlFunction.cs** op. 

5. Open het bestand **sqlFunction.csproj**.

6. Zoek de groep pakket verwijzingen en voeg een nieuwe toe voor het insluiten van SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Sla het bestand **sqlFunction.csproj** op.

## <a name="add-the-sql-server-container"></a>De SQL Server-container toevoegen

Een [distributiemanifest](module-composition.md) declareert welke modules de IoT Edge-runtime op uw IoT Edge-apparaat zal installeren. U hebt de code voor het maken van een aangepaste functie module in de vorige sectie gegeven, maar de SQL Server module is al in de Azure Marketplace gemaakt en beschikbaar. U hoeft alleen aan de IoT Edge-runtime op te geven dat het deze module moet opnemen, en die vervolgens op uw apparaat te configureren. 

1. Open in Visual Studio code het opdracht palet door > **opdracht** **venster weer geven** te selecteren.

2. In het opdracht palet typt en voert u de opdracht uit **Azure IOT Edge: IOT Edge module toevoegen**. Geef in het opdracht palet de volgende informatie op om een nieuwe module toe te voegen: 

   | Veld | Waarde | 
   | ----- | ----- |
   | Implementatiesjabloonbestand selecteren | In het opdracht palet wordt het bestand Deployment. sjabloon. json in de map van de huidige oplossing gemarkeerd. Selecteer dat bestand.  |
   | Modulesjabloon selecteren | Selecteer **module in azure Marketplace**. |

3. Zoek en selecteer **SQL Server module**op de Marketplace Azure IOT Edge-module. 

4. Wijzig de module naam in **SQL**, alle kleine letters. Deze naam komt overeen met de container naam die is gedeclareerd in de connection string in het bestand sqlFunction.cs. 

5. Selecteer **importeren** om de module toe te voegen aan uw oplossing. 

6. Open in de map Solution het bestand **Deployment. sjabloon. json** . 

7. Ga naar de sectie **modules**. U ziet nu drie modules. De module *SimulatedTemperatureSensor* is standaard opgenomen in nieuwe oplossingen en levert test gegevens die met uw andere modules kunnen worden gebruikt. De module *sqlFunction* is de module die u in eerste instantie hebt gemaakt en bijgewerkt met nieuwe code. Ten slotte is de module *SQL* geïmporteerd vanuit Azure Marketplace. 

   >[!Tip]
   >De module SQL Server wordt geleverd met een standaard wachtwoordset in de omgevings variabelen van het implementatie manifest. Telkens wanneer u een SQL Server-container in een productieomgeving maakt, moet u [het standaardwachtwoord van de systeembeheerder](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)wijzigen.

8. Sluit het bestand **Deployment. Temp late. json** .

## <a name="build-your-iot-edge-solution"></a>Uw eigen IoT Edge-oplossing bouwen

In de voorgaande secties hebt u een oplossing met één module gemaakt en er vervolgens nog een toegevoegd aan de distributiemanifestsjabloon. De SQL Server-module wordt openbaar door micro soft gehost, maar u moet de code in de functie-module container plaatsen. In deze sectie bouwt u de oplossing, maakt u container installatie kopieën voor de sqlFunction-module en pusht u de installatie kopie naar het container register. 

1. Open de in Visual Studio Code geïntegreerde terminal door **View** > **Terminal** te selecteren.  

1. Meld u aan bij uw containerregister in Visual Studio Code, zodat u uw installatiekopieën naar uw register kunt pushen. Gebruik dezelfde Azure Container Registry-referenties (ACR) die u hebt toegevoegd aan het. env-bestand. Voer de volgende opdracht in de geïntegreerde terminal in:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Mogelijk wordt een beveiligings waarschuwing weer gegeven waarin wordt aanbevolen de para meter---Password-stdin te gebruiken. Hoewel buiten het bestek van dit artikel, wordt u deze best practice aangeraden. Zie voor meer informatie de koppeling naar de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) . 

2. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**. 

Wanneer u Visual Studio code voor het bouwen van uw oplossing vertelt, neemt het eerst de informatie in de implementatie sjabloon en genereert een implementatie. JSON-bestand in een nieuwe map met de naam **config**. Vervolgens worden er twee opdrachten uitgevoerd in de geïntegreerde terminal: `docker build` en `docker push`. Met deze twee opdrachten wordt de code gebouwd, wordt de module opgeslagen in een container, en wordt de code vervolgens naar het containerregister gepusht dat u hebt opgegeven toen u de oplossing initialiseerde. 

U kunt controleren of de sqlFunction-module naar het container register is gepusht. Ga in het Azure Portal naar het container register. Selecteer **opslag** plaatsen en zoek naar **sqlFunction**. De andere twee modules, SimulatedTemperatureSensor en SQL, worden niet naar het container register gepusht omdat u al naar de opslag plaatsen van de micro soft-registers verwijst.

## <a name="deploy-the-solution-to-a-device"></a>De oplossing implementeren op een apparaat

U kunt modules op een apparaat instellen via de IoT Hub, maar u hebt ook toegang tot uw IoT Hub en apparaten via Visual Studio Code. In dit gedeelte stelt u de toegang tot uw IoT Hub in en gebruikt u VS Code om uw oplossing op uw IoT Edge-apparaat te implementeren. 

1. Vouw in VS Code Explorer de sectie **Azure IoT Hub Devices** uit. 

2. Klik met de rechtermuisknop op het doelapparaat van uw implementatie en selecteer **Implementatie maken voor één apparaat**. 

3. Ga in de bestandsverkenner naar de map **config** in uw oplossing en kies **deployment.amd64**. Klik op **Edge-distributiemanifest selecteren**. 

   Gebruik het bestand Deployment. Temp late. json niet als een implementatie manifest.

Als de implementatie is geslaagd, wordt er een bevestigingsbericht weergegeven in de VS Code-uitvoer. 

Vernieuw de status van uw apparaat in de sectie Azure IoT Hub-apparaten van VS Code. De nieuwe modules worden weergegeven en beginnen gedurende de volgende minuten met rapporteren terwijl de containers worden geïnstalleerd en gestart. U kunt ook controleren of alle modules op uw apparaat actief zijn. Voer op uw IoT Edge-apparaat de volgende opdracht uit om de status van de modules te bekijken. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>De SQL-database maken

Wanneer u het distributiemanifest op uw apparaat toepast, krijgt u drie modules die worden uitgevoerd. De SimulatedTemperatureSensor-module genereert gesimuleerde omgevings gegevens. De module sqlFunction neemt de gegevens en deelt deze in voor een database. Deze sectie leidt u door het instellen van de SQL-database voor het opslaan van de temperatuurgegevens. 

Voer de volgende opdrachten uit op uw IoT Edge-apparaat. Met deze opdrachten wordt verbinding gemaakt met de **sql**-module die wordt uitgevoerd op uw apparaat en worden er een database en een tabel gemaakt voor de opslag van de temperatuurgegevens die naar de module worden verzonden. 

1. Gebruik een opdrachtregelprogramma op uw IoT Edge-apparaat om verbinding te maken met uw database. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Open het SQL-opdrachthulpprogramma.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Uw database maken: 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Definieer uw tabel.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

U kunt het dockerbestand van uw SQL Server aanpassen om uw SQL Server automatisch in te stellen om te worden geïmplementeerd op meerdere IoT Edge-apparaten. Zie het [Microsoft SQL Server-containerdemo-project](https://github.com/twright-msft/mssql-node-docker-demo-app)voor meer informatie. 

## <a name="view-the-local-data"></a>De lokale gegevens bekijken

Nadat uw tabel is gemaakt, begint de module sqlFunction gegevens op te slaan in een lokale SQL Server 2017-database op uw IoT Edge-apparaat. 

Voer vanuit het SQL-opdrachthulpprogramma de volgende opdracht uit om uw opgemaakte tabelgegevens te bekijken: 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Inhoud van lokale database bekijken](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat. 

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Functions-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer informatie krijgen over het [ontwikkelen van Azure Functions met Azure IoT Edge voor Visual Studio Code](how-to-develop-csharp-function.md). 

Als u aan de rand een andere opslag methode wilt proberen, lees dan meer informatie over het gebruik van Azure Blob Storage op IoT Edge. 

> [!div class="nextstepaction"]
> [Gegevens opslaan aan de rand met Azure Blob Storage op IoT Edge](how-to-store-data-blob.md)
