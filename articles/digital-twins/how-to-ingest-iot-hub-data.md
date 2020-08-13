---
title: Telemetrie opnemen vanuit IoT Hub
titleSuffix: Azure Digital Twins
description: Zie telemetrie-berichten van apparaten inslikken van IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5209ffb0328e90fb2ca9b91773cbf18dd4ed2916
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163603"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetrie opnemen in azure Digital Apparaatdubbels

Azure Digital Apparaatdubbels wordt gestuurd met gegevens van IoT-apparaten en andere bronnen. Een algemene bron voor de apparaatgegevens die in azure Digital Apparaatdubbels moet worden gebruikt, is [IOT hub](../iot-hub/about-iot-hub.md).

Het proces voor het opnemen van gegevens in azure Digital Apparaatdubbels is het instellen van een externe reken resource, zoals een [Azure-functie](../azure-functions/functions-overview.md), waarmee de gegevens worden ontvangen en de [DigitalTwins-api's](how-to-use-apis-sdks.md) worden gebruikt voor het instellen van eigenschappen of faxgebeurtenissen op [digitale apparaatdubbels](concepts-twins-graph.md) dienovereenkomstig. 

In dit document vindt u instructies voor het schrijven van een Azure-functie waarmee telemetrie kan worden opgenomen in IoT Hub.

## <a name="prerequisites"></a>Vereisten

Voordat u verder gaat met dit voor beeld, moet u de volgende vereisten volt ooien.
* **Een IOT-hub**. Zie de sectie *een IOT hub maken* van [deze IOT hub Snelstartgids](../iot-hub/quickstart-send-telemetry-cli.md) voor instructies.
* **Een Azure-functie** met de juiste machtigingen voor het aanroepen van uw digitale dubbele instantie. Zie [*How-to: een Azure-functie instellen voor het verwerken van gegevens*](how-to-create-azure-function.md) voor instructies. 
* **Een Digital apparaatdubbelse-instantie** die de telemetrie van uw apparaat ontvangt. Zie [ *How to: een Azure Digital apparaatdubbels-exemplaar en-verificatie instellen*](./how-to-set-up-instance-portal.md) 

### <a name="example-telemetry-scenario"></a>Voor beeld van telemetrie-scenario

In deze procedure wordt beschreven hoe u berichten van IoT Hub naar Azure Digital Apparaatdubbels verzendt met behulp van een Azure-functie. Er zijn veel mogelijke configuraties en overeenkomende strategieën die u kunt gebruiken, maar het voor beeld voor dit artikel bevat de volgende onderdelen:
* Een thermo meter-apparaat in IoT Hub met een bekende apparaat-ID.
* Een digitale dubbele om het apparaat te vertegenwoordigen met een overeenkomende ID

> [!NOTE]
> In dit voor beeld wordt een eenvoudige ID-overeenkomst gebruikt tussen de apparaat-ID en de bijbehorende digitale dubbele ID, maar het is mogelijk om meer geavanceerde toewijzingen van het apparaat aan de dubbele waarde toe te voegen (zoals bij een toewijzings tabel).

Wanneer een temperatuur telemetrie-gebeurtenis wordt verzonden door het Thermo meter-apparaat, moet de eigenschap *Tempe ratuur* van het digitale dubbele worden bijgewerkt. Dit scenario wordt beschreven in een diagram hieronder:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Een diagram waarin een stroom diagram wordt weer gegeven. In de grafiek wordt een IoT Hub apparaat een temperatuur telemetrie via IoT Hub naar een Azure-functie verzonden, waarmee een temperatuur eigenschap op een dubbele in azure Digital Apparaatdubbels wordt bijgewerkt." border="false":::

## <a name="add-a-model-and-twin"></a>Een model en dubbele toevoegen

U hebt een dubbele to-update met IoT hub-gegevens nodig.

Het model ziet er als volgt uit:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Als u **dit model wilt uploaden naar uw apparaatdubbels-exemplaar**, opent u de Azure CLI en voert u de volgende opdracht uit:
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Vervolgens moet u **een dubbele maken met behulp van dit model**. Gebruik de volgende opdracht om een dubbele en set 0,0 te maken als aanvankelijke temperatuur waarde.
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

De uitvoer van een geslaagde, dubbele maken-opdracht moet er als volgt uitzien:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In deze sectie wordt gebruikgemaakt van dezelfde Visual Studio-opstart stappen en Azure function-skelet van [*instructies: Stel een Azure-functie in voor het verwerken van gegevens*](how-to-create-azure-function.md). Het skelet behandelt verificatie en maakt een service-client, klaar om gegevens te verwerken en Azure Digital Apparaatdubbels-Api's te aanroepen als reactie. 

In de volgende stappen voegt u specifieke code toe voor het verwerken van IoT-telemetrie-gebeurtenissen van IoT Hub.  

### <a name="add-telemetry-processing"></a>Telemetrie-verwerking toevoegen
    
Telemetrie-gebeurtenissen worden in de vorm van berichten van het apparaat geleverd. De eerste stap bij het toevoegen van de telemetrie-verwerkings code wordt het relevante deel van dit apparaat geëxtraheerd uit de Event Grid gebeurtenis. 

Verschillende apparaten kunnen hun berichten anders structureren, zodat de code voor **deze stap afhankelijk is van het verbonden apparaat.** 

De volgende code toont een voor beeld van een eenvoudig apparaat dat telemetrie verzendt als JSON. Dit voor beeld wordt volledig verkend in [*zelf studie: verbinding maken met een end-to-end-oplossing*](./tutorial-end-to-end.md). Met de volgende code wordt de apparaat-ID gezocht van het apparaat dat het bericht heeft verzonden, evenals de waarde voor de Tempe ratuur.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

In het volgende code voorbeeld wordt de ID en de waarde voor de Tempe ratuur gebruikt en worden deze gebruikgemaakt van patches (waarbij updates worden gemaakt aan) die twee.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Uw Azure-functie code bijwerken

Nu u de code uit de vorige voor beelden kent, opent u Visual Studio en vervangt u de code van uw Azure-functie door deze voorbeeld code.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Verbind uw functie met IoT Hub

1. Stel een gebeurtenis bestemming in voor hub-gegevens. Ga in het [Azure Portal](https://portal.azure.com/)naar uw IOT hub-exemplaar. Maak onder **gebeurtenissen**een abonnement voor uw Azure-functie. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Scherm afbeelding van de Azure Portal die het toevoegen van een gebeurtenis abonnement weergeeft.":::

2. Vul op de pagina **gebeurtenis abonnement maken** de velden als volgt in:
    1. Geef bij **naam**het abonnement een naam zoals u wilt.
    2. Kies **Event grid schema**onder **gebeurtenis schema**.
    3. Kies onder **naam van systeem onderwerp**een unieke naam.
    4. Kies onder **gebeurtenis typen**het **apparaat telemetrie** als het gebeurtenis type waarop moet worden gefilterd.
    5. Selecteer onder **Details van eind punt**uw Azure-functie als een eind punt.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Scherm afbeelding van de Azure Portal waarin de details van het gebeurtenis abonnement worden weer gegeven":::

## <a name="send-simulated-iot-data"></a>Gesimuleerde IoT-gegevens verzenden

Als u de nieuwe functie insluitingen wilt testen, gebruikt u de hand leiding van Device Simulator [*: verbinding maken met een end-to-end-oplossing*](./tutorial-end-to-end.md). Deze zelf studie wordt aangedreven door een voorbeeld project dat is geschreven in C#. De voorbeeld code bevindt zich hier: [Azure Digital apparaatdubbels](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)-voor beelden. U gebruikt het **DeviceSimulator** -project in die opslag plaats.

In de end-to-end zelf studie voert u de volgende stappen uit:
1. [*Het gesimuleerde apparaat bij IoT Hub registreren*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*De simulatie configureren en uitvoeren*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Uw resultaten valideren

Bij het uitvoeren van de bovenstaande Device Simulator wordt de waarde voor de Tempe ratuur van uw digitale-dubbele waarden gewijzigd. Voer in de Azure CLI de volgende opdracht uit om de waarde voor de Tempe ratuur te bekijken.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

De uitvoer moet een temperatuur waarde als volgt bevatten:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Voer herhaaldelijk de query-opdracht uit om de gewijzigde waarde weer te geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het inkomen en uitkomen van gegevens met Azure Digital Apparaatdubbels:
* [*Concepten: integratie met andere services*](concepts-integration.md)
