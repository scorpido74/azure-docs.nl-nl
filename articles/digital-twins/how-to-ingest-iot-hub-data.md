---
title: Telemetrie opnemen vanuit IoT Hub
titleSuffix: Azure Digital Twins
description: Zie telemetrie-berichten van apparaten inslikken van IoT Hub.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725848"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetrie opnemen in azure Digital Apparaatdubbels

Azure Digital Apparaatdubbels wordt gestuurd met gegevens van IoT-apparaten en andere bronnen. Een algemene bron voor de apparaatgegevens die in azure Digital Apparaatdubbels moet worden gebruikt, is [IOT hub](../iot-hub/about-iot-hub.md).

Tijdens de preview wordt het proces voor het opnemen van gegevens in azure Digital Apparaatdubbels ingesteld op het instellen van een externe reken resource, zoals een [Azure-functie](../azure-functions/functions-overview.md), waarmee de gegevens worden ontvangen en de [DigitalTwins-api's](how-to-use-apis-sdks.md) worden gebruikt om de eigenschappen of telemetrie-gebeurtenissen op [digitale apparaatdubbels](concepts-twins-graph.md) dienovereenkomstig in te stellen. 

In dit document vindt u instructies voor het schrijven van een Azure-functie waarmee telemetrie kan worden opgenomen in IoT Hub.

## <a name="example-telemetry-scenario"></a>Voor beeld van telemetrie-scenario

In deze procedure wordt beschreven hoe u berichten van IoT Hub naar Azure Digital Apparaatdubbels verzendt met behulp van een Azure-functie. Er zijn veel mogelijke configuraties en overeenkomende strategieën die u kunt gebruiken, maar het voor beeld voor dit artikel bevat de volgende onderdelen:
* Een thermo meter-apparaat in IoT Hub met een bekende apparaat-ID.
* Een digitale dubbele om het apparaat te vertegenwoordigen met een overeenkomende ID
* Een digitale dubbele representatie van een kamer

> [!NOTE]
> In dit voor beeld wordt een eenvoudige ID-overeenkomst gebruikt tussen de apparaat-ID en de bijbehorende digitale dubbele ID, maar het is mogelijk om meer geavanceerde toewijzingen van het apparaat aan de dubbele waarde toe te voegen (zoals bij een toewijzings tabel).

Wanneer een temperatuur telemetrie-gebeurtenis wordt verzonden door het Thermo meter-apparaat, moet de eigenschap *Tempe ratuur* van de *kamer* dubbele worden bijgewerkt. Als u dit wilt doen, wijst u vanuit een telemetrie-gebeurtenis op een apparaat toe aan een eigenschaps-Setter op het digitale dubbele. U gebruikt topologie gegevens uit het [dubbele diagram](concepts-twins-graph.md) om de *kamer* dubbele te vinden en vervolgens kunt u de eigenschap van het dubbele instellen. In andere gevallen willen gebruikers mogelijk een eigenschap instellen op de overeenkomende dubbele waarde (in dit voor beeld is dit het dubbele met de ID van *123*). Azure Digital Apparaatdubbels biedt u veel flexibiliteit om te bepalen hoe telemetrie-gegevens worden toegewezen in apparaatdubbels. 

Dit scenario wordt beschreven in een diagram hieronder:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Een IoT Hub apparaat verstuurt temperatuur telemetrie via IoT Hub, Event Grid of systeem onderwerpen naar een Azure-functie, waarmee een temperatuur eigenschap wordt bijgewerkt op apparaatdubbels in azure Digital Apparaatdubbels." border="false":::

## <a name="prerequisites"></a>Vereisten

Voordat u verder gaat met dit voor beeld, moet u de volgende vereisten volt ooien.
1. Maak een IoT-hub. Zie de sectie *een IOT hub maken* van [deze IOT hub Snelstartgids](../iot-hub/quickstart-send-telemetry-cli.md) voor instructies.
2. Maak ten minste één Azure-functie voor het verwerken van gebeurtenissen van IoT Hub. Zie [How-to: een Azure-functie instellen voor het verwerken van gegevens](how-to-create-azure-function.md) voor het bouwen van een eenvoudige Azure-functie waarmee verbinding kan worden gemaakt met Azure Digital Apparaatdubbels en Azure Digital apparaatdubbels API-functies kunt aanroepen. De rest van deze procedure wordt gebaseerd op deze functie.
3. Stel een gebeurtenis bestemming in voor hub-gegevens. Ga in het [Azure Portal](https://portal.azure.com/)naar uw IOT hub-exemplaar. Maak onder *gebeurtenissen*een abonnement voor uw Azure-functie. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure Portal: een gebeurtenis abonnement toevoegen":::

4. Vul op de pagina *gebeurtenis abonnement maken* de velden als volgt in:
   * Geef onder DETAILS van het *gebeurtenis abonnement*een naam op voor het abonnement dat u wilt
   * Kies onder *gebeurtenis typen*het *apparaat telemetrie* als het gebeurtenis type waarop moet worden gefilterd
      - Voeg filters toe aan andere gebeurtenis typen, indien gewenst.
   * Selecteer onder *Details van eind punt*uw Azure-functie als een eind punt

## <a name="create-an-azure-function-in-visual-studio"></a>Een Azure-functie maken in Visual Studio

In deze sectie wordt gebruikgemaakt van dezelfde Visual Studio-opstart stappen en Azure function-skelet van [instructies: Stel een Azure-functie in voor het verwerken van gegevens](how-to-create-azure-function.md). Het skelet behandelt verificatie en maakt een service-client, klaar om gegevens te verwerken en Azure Digital Apparaatdubbels-Api's te aanroepen als reactie. 

De kern van de functie skelet is als volgt:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

In de volgende stappen voegt u specifieke code toe voor het verwerken van IoT-telemetrie-gebeurtenissen van IoT Hub.  

## <a name="add-telemetry-processing"></a>Telemetrie-verwerking toevoegen

Telemetrie-gebeurtenissen worden in de vorm van berichten van het apparaat geleverd. De eerste stap bij het toevoegen van de telemetrie-verwerkings code wordt het relevante deel van dit apparaat geëxtraheerd uit de Event Grid gebeurtenis. 

Verschillende apparaten kunnen hun berichten anders structureren, zodat de code voor deze stap afhankelijk is van het verbonden apparaat. 

De volgende code toont een voor beeld van een eenvoudig apparaat dat telemetrie verzendt als JSON. In het voor beeld wordt de apparaat-ID geëxtraheerd van het apparaat dat het bericht heeft verzonden, evenals de waarde voor de Tempe ratuur.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Het doel van deze oefening is het bijwerken van de Tempe ratuur van een *kamer* binnen het dubbele diagram. Dit betekent dat het doel van het bericht niet het digitale element is dat is gekoppeld aan dit apparaat, maar de *lokale ruimte* die het bovenliggende item is. U kunt de bovenliggende dubbele waarde vinden met behulp van de apparaat-ID die u hebt geëxtraheerd uit het telemetrie-bericht met behulp van de bovenstaande code.

U doet dit door de Azure Digital Apparaatdubbels-Api's te gebruiken om toegang te krijgen tot de binnenkomende relaties met het apparaat, dat de dubbele ID heeft (in dit geval is hetzelfde als het apparaat). Vanuit de binnenkomende relatie kunt u de ID van het bovenliggende item vinden met het code fragment hieronder.

In het volgende code fragment ziet u hoe u inkomende relaties van een twee navolgende kunt ophalen:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

Het bovenliggende element van de dubbele locatie bevindt zich in de eigenschap *SourceId* van de relatie.

Het is tamelijk gebruikelijk voor een model van een dubbele waarde waarmee een apparaat slechts één binnenkomende relatie heeft. In dit geval kunt u de eerste (en enige) relatie selecteren die wordt geretourneerd. Als uw modellen meerdere typen relaties hebben, moet u mogelijk extra opgeven om te kiezen uit meerdere binnenkomende relaties. Een veelgebruikte manier om dit te doen is door de relatie te kiezen door `RelationshipName` . 

Zodra u de ID van het bovenliggende element hebt die de *ruimte*bevat, kunt u ' patch ' (updates uitvoeren) die dubbele. Gebruik hiervoor de volgende code:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Volledige Azure-functie code

Als u de code uit de vorige voor beelden gebruikt, is dit de volledige Azure-functie in context:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

De functie van het hulp programma voor het vinden van binnenkomende relaties:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

En de functie van het hulp programma voor het bijwerken van de dubbele:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

U hebt nu een Azure-functie die is ingericht voor het lezen en interpreteren van de scenario gegevens die afkomstig zijn van IoT Hub.

## <a name="debug-azure-function-apps-locally"></a>Lokaal fouten opsporen in azure function-apps

U kunt fouten opsporen in azure functions met een Event Grid trigger lokaal. Zie [Debug Event grid trigger lokaal](../azure-functions/functions-debug-event-grid-trigger-local.md)voor meer informatie hierover.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het inkomen en uitkomen van gegevens met Azure Digital Apparaatdubbels:
* [Concepten: integratie met andere services](concepts-integration.md)
