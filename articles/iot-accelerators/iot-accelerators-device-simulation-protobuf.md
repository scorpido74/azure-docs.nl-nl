---
title: Protocolbuffers gebruiken met apparaatsimulatie - Azure| Microsoft Documenten
description: In deze handleiding leert u hoe u Protocolbuffers gebruiken om telemetrie te serialiseren die wordt verzonden vanaf de apparaatsimulatieoplossingsversneller.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom:
- mvc
- amqp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c49745b30d2c4acc115a72af095f3e941dc4d509
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683993"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Telemetrie serialiseren met protocolbuffers

Protocol Buffers (Protobuf) is een binaire serialisatie-indeling voor gestructureerde gegevens. Protobuf is ontworpen om eenvoud en prestaties te benadrukken met als doel kleiner en sneller te zijn dan XML.

Apparaatsimulatie ondersteunt de **proto3-versie** van de taal van de protocolbuffers.

Omdat Protobuf gecompileerde code nodig heeft om de gegevens te serialiseren, moet u een aangepaste versie van apparaatsimulatie bouwen.

De stappen in deze handleiding laten u zien hoe u:

1. Een ontwikkelomgeving voorbereiden
1. Opgeven met de Protobuf-indeling in een apparaatmodel
1. Uw Protobuf-indeling definiëren
1. Protobuf-klassen genereren
1. Lokaal testen

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt volgen, moet u het volgende volgen:

* Visual Studio Code. U [Visual Studio Code voor Mac, Linux en Windows](https://code.visualstudio.com/download)downloaden.
* .NET Core. U [.NET Core voor Mac, Linux en Windows](https://www.microsoft.com/net/download)downloaden.
* Postman. Je [Postman voor Mac, windows of Linux](https://www.getpostman.com/apps)downloaden.
* Een [IoT-hub die is geïmplementeerd voor uw Azure-abonnement.](../iot-hub/iot-hub-create-through-portal.md) U hebt de verbindingstekenreeks van de IoT-hub nodig om de stappen in deze handleiding te voltooien. U de verbindingstekenreeks ophalen via de Azure-portal.
* Een [Cosmos DB-database die is geïmplementeerd in uw Azure-abonnement](../cosmos-db/create-sql-api-dotnet.md#create-account) dat de SQL-API gebruikt en die is geconfigureerd voor [een sterke consistentie.](../cosmos-db/manage-account.md) U hebt de verbindingstekenreeks van de Cosmos DB-database nodig om de stappen in deze handleiding uit te voeren. U de verbindingstekenreeks ophalen via de Azure-portal.
* Een [Azure-opslagaccount dat is geïmplementeerd voor uw Azure-abonnement.](../storage/common/storage-account-create.md) U hebt de verbindingstekenreeks van het opslagaccount nodig om de stappen in deze handleiding uit te voeren. U de verbindingstekenreeks ophalen via de Azure-portal.

## <a name="prepare-your-development-environment"></a>Uw ontwikkelomgeving voorbereiden

Voltooi de volgende taken om uw ontwikkelomgeving voor te bereiden:

* Download de bron voor de microservice van apparaatsimulatie.
* Download de bron voor de microservice van de opslagadapter.
* Voer de microservice van de opslagadapter lokaal uit.

De instructies in dit artikel gaan ervan uit dat u Windows gebruikt. Als u een ander besturingssysteem gebruikt, moet u mogelijk een aantal bestandspaden en opdrachten aanpassen aan uw omgeving.

### <a name="download-the-microservices"></a>De microservices downloaden

Download en rits de [Remote Monitoring Microservices](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) van GitHub uit naar een geschikte locatie op uw lokale machine. Deze repository bevat de microservice van de opslagadapter die u nodig hebt voor deze how-to.

Download en rits de microservice van de [apparaatsimulatie](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) van GitHub uit naar een geschikte locatie op uw lokale machine.

### <a name="run-the-storage-adapter-microservice"></a>De microservice van de opslagadapter uitvoeren

Open in Visual Studio Code de map **remote-monitoring-services-dotnet-master\storage-adapter.** Klik **op de knoppen Herstellen** om onopgeloste afhankelijkheden op te lossen.

Open het bestand **.vscode/launch.json** en wijs de Cosmos DB-verbindingstekenreeks toe aan de **\_\_\_documentdb-verbindingsvariabele PCS-opslagadapter.**

> [!NOTE]
> Wanneer u de microservice lokaal op uw machine uitvoert, moet er nog steeds een Cosmos DB-exemplaar in Azure correct werken.

Als u de microservice voor de opslagadapter lokaal wilt uitvoeren, klikt u op **Foutopsporing van de foutopsporing van \> defoutopsporing debuggen.**

Het **venster Terminal** in Visual Studio Code toont de uitvoer van de <http://127.0.0.1:9022/v1/status>uitvoerende microservice, inclusief een URL voor de statuscontrole van de webservice: . Wanneer u naar dit adres navigeert, moet de status "OK: Levend en wel" zijn.

Laat de microservice van de opslagadapter draaien in dit geval van Visual Studio Code terwijl u de volgende stappen uitvoert.

## <a name="define-your-device-model"></a>Uw apparaatmodel definiëren

Open de **apparaatsimulatie-dotnet-mastermap** die u van GitHub hebt gedownload in een nieuw exemplaar van Visual Studio Code. Klik **op de knoppen Herstel** om onopgeloste afhankelijkheden op te lossen.

In deze handleiding maakt u een nieuw apparaatmodel voor een assettracker:

1. Maak een nieuw apparaatmodelbestand genaamd **assettracker-01.json** in de map **Services\data\devicemodels.**

1. Definieer de apparaatfunctionaliteit in het apparaatmodel **assettracker-01.json-bestand.** De telemetriesectie van een Protobuf-apparaatmodel moet:

   * Vermeld de naam van de klasse Protobuf die u voor uw apparaat genereert. In de volgende sectie ziet u hoe u deze klasse genereren.
   * Geef Protobuf op als berichtindeling.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Script apparaatgedrag maken

Schrijf het gedragsscript dat bepaalt hoe uw apparaat zich gedraagt. Zie [Een geavanceerd gesimuleerd apparaat maken](iot-accelerators-device-simulation-advanced-device.md)voor meer informatie.

## <a name="define-your-protobuf-format"></a>Uw Protobuf-indeling definiëren

Wanneer u een apparaatmodel hebt en de berichtindeling hebt bepaald, u een **protobestand** maken. In het **proto-bestand** voegt u het:

* Een `csharp_namespace` die overeenkomt met de eigenschap **ClassName** in uw apparaatmodel.
* Een bericht voor elke gegevensstructuur om te serialiseren.
* Een naam en type voor elk veld in het bericht.

1. Maak een nieuw bestand genaamd **assettracker.proto** in de map **Services\Models\Protobuf\proto.**

1. Definieer de syntaxis, naamruimte en het berichtschema in het **protobestand** als volgt:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

De `=1` `=2` markeringen op elk element geven een unieke tag op die het veld in de binaire codering gebruikt. Nummers 1-15 vereisen een byte minder om te coderen dan hogere getallen.

## <a name="generate-the-protobuf-class"></a>De Protobuf-klasse genereren

wanneer u een **proto-bestand** hebt, is de volgende stap het genereren van de klassen die nodig zijn om berichten te lezen en te schrijven. Om deze stap te voltooien, heb je de **Protoc** Protobuf compiler nodig.

1. [Download de Protobuf compiler van GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Voer de compiler uit en geef de bronmap, de doelmap en de naam van uw **proto-bestand** op. Bijvoorbeeld:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Met deze opdracht genereert u een **Assettracker.cs** bestand in de map **Services\Models\Protobuf.**

## <a name="test-protobuf-locally"></a>Test Protobuf lokaal

In deze sectie test u het assettracker-apparaat dat u in de vorige secties hebt gemaakt, lokaal.

### <a name="run-the-device-simulation-microservice"></a>De microservice voor apparaatsimulatie uitvoeren

Open het **bestand .vscode/launch.json** en wijs uw:

* De tekenreeks voor de Verbinding van IoT-hub met de **\_\_pcs-iothub-verbindingstekenreeks.**
* Tekenreeks van de opslagaccountverbinding met de variabele **VOOR AZURE-opslagaccount\_\_\_van PCS.**
* Cosmos DB-verbindingstekenreeks met de **documentdb-verbindingsvariabele\_\_\_PCS-opslagadapter.**

Open het **bestand WebService/Properties/launchSettings.json** en wijs uw:

* De tekenreeks voor de Verbinding van IoT-hub met de **\_\_pcs-iothub-verbindingstekenreeks.**
* Tekenreeks van de opslagaccountverbinding met de variabele **VOOR AZURE-opslagaccount\_\_\_van PCS.**
* Cosmos DB-verbindingstekenreeks met de **documentdb-verbindingsvariabele\_\_\_PCS-opslagadapter.**

Open het **bestand WebService\appsettings.ini** en wijzig de instellingen als volgt:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>De oplossing configureren om uw nieuwe apparaatmodelbestanden op te nemen

Standaard worden uw nieuwe apparaatmodel JSON- en JS-bestanden niet gekopieerd naar de ingebouwde oplossing. Je moet ze expliciet opnemen.

Voeg een vermelding toe aan het **services\services.csproj-bestand** voor elk bestand dat u wilt opnemen. Bijvoorbeeld:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Als u de microservice lokaal wilt uitvoeren, klikt u op **Foutopsporing van \> defoutopsporing debuggen debuggen.**

Het **venster Terminal** in Visual Studio Code toont de uitvoer van de hardloopmicroservice.

Laat de microservice voor apparaatsimulatie draaien in dit geval van Visual Studio Code terwijl u de volgende stappen uitvoert.

### <a name="set-up-a-monitor-for-device-events"></a>Een monitor instellen voor apparaatgebeurtenissen

In deze sectie gebruikt u de Azure CLI om een gebeurtenismonitor in te stellen om de telemetrie weer te geven die is verzonden vanaf de apparaten die zijn verbonden met uw IoT-hub.

Het volgende script gaat ervan uit dat de naam van uw **IoT-hub apparaatsimulatie-test**is.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Laat de gebeurtenismonitor draaien terwijl u de gesimuleerde apparaten test.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Een simulatie maken met het apparaattype assettracker

In deze sectie gebruikt u de tool Postman om de microservice voor apparaatsimulatie te vragen om een simulatie uit te voeren met behulp van het apparaattype asset tracker. Postman is een tool waarmee u REST-verzoeken naar een webservice sturen.

Postman instellen:

1. Open postbode op je lokale machine.

1. Klik ** \> op Bestand importeren**. Klik vervolgens op **Bestanden kiezen**.

1. Selecteer **Azure IoT Device Simulation\_solution accelerator.postman collection** en **Azure\_IoT Device Simulation solution accelerator.postman environment** en klik op **Openen**.

1. Vouw de **azure IoT Device Simulation-oplossingsversneller** uit om de aanvragen weer te geven die u verzenden.

1. Klik **op Geen omgeving** en selecteer Azure **IoT Device Simulation solution accelerator**.

U hebt nu een verzameling en omgeving geladen in uw Postman-werkruimte die u gebruiken om te communiceren met de microservice voor apparaatsimulatie.

Ga als het gaat om het configureren en uitvoeren van de simulatie:

1. Selecteer in de postmanverzameling de optie **Simulatie van activatrackers maken** en klik op **Verzenden**. Met deze aanvraag worden vier exemplaren van het gesimuleerde apparaattype assettracker gebruikt.

1. De uitvoer van gebeurtenismonitor in het Azure CLI-venster toont de telemetrie van de gesimuleerde apparaten.

Als u de simulatie wilt stoppen, selecteert u de **simulatieaanvraag stoppen** in Postman en klikt u op **Verzenden**.

### <a name="clean-up-resources"></a>Resources opschonen

U de twee lokaal draaiende microservices stoppen in hun visual studiocode-instanties **(Foutopsporing \> stoppen met foutopsporing).**

Als u de IoT Hub- en Cosmos DB-exemplaren niet meer nodig hebt, verwijdert u deze uit uw Azure-abonnement om onnodige kosten te voorkomen.

## <a name="iot-hub-support"></a>Ondersteuning voor IoT-hub

Veel IoT Hub-functies ondersteunen niet native Protobuf of andere binaire indelingen. U bijvoorbeeld niet routeren op basis van de payload van berichten omdat IoT Hub de payload van het bericht niet kan verwerken. U echter een route op basis van berichtkoppen.

## <a name="next-steps"></a>Volgende stappen

Nu heb je geleerd hoe je apparaatsimulatie aanpassen om Protobuf te gebruiken om telemetrie te verzenden, de volgende stap is om nu te leren om [een aangepaste afbeelding naar de cloud te implementeren.](iot-accelerators-device-simulation-deploy-image.md)
