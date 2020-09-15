---
title: Apparaten automatisch beheren met DPS
titleSuffix: Azure Digital Twins
description: Zie een geautomatiseerd proces voor het inrichten en buiten gebruik stellen van IoT-apparaten in azure Digital Apparaatdubbels met behulp van de Device Provisioning Service (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efc507cb69b3368a2102b6de0b905657d5806ef2
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561428"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Apparaten in azure Digital Apparaatdubbels automatisch beheren met behulp van de Device Provisioning Service (DPS)

In dit artikel leert u hoe u Azure Digital Apparaatdubbels integreert met de [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md).

Met de oplossing die in dit artikel wordt beschreven, kunt u het proces voor het **_inrichten_** en **_buiten gebruik_** stellen van IOT Hub apparaten in azure Digital apparaatdubbels met behulp van Device Provisioning Service automatiseren. 

Zie de [sectie *levens cyclus van apparaten* ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) in de documentatie van IOT hub Apparaatbeheer voor meer informatie over de fase van het _inrichten_ en de _buiten gebruiks telling_ en voor een beter begrip van de set algemene Apparaatbeheer die gemeen schappelijk zijn voor alle zakelijke IOT-projecten.

## <a name="prerequisites"></a>Vereisten

Voordat u het inrichten kunt instellen, moet u een **Azure Digital apparaatdubbels-exemplaar** hebben dat modellen en apparaatdubbels bevat. Dit exemplaar moet ook worden ingesteld met de mogelijkheid om digitale dubbele informatie bij te werken op basis van gegevens. 

Als u deze instelling nog niet hebt ingesteld, kunt u deze maken met behulp van de Azure Digital Apparaatdubbels- [*zelf studie: verbinding maken met een end-to-end oplossing*](tutorial-end-to-end.md). In deze zelf studie wordt u begeleid bij het instellen van een Azure Digital Apparaatdubbels-exemplaar met modellen en apparaatdubbels, een verbonden Azure- [IOT hub](../iot-hub/about-iot-hub.md)en verschillende [Azure-functies](../azure-functions/functions-overview.md) voor het door geven van de gegevens stroom.

U hebt de volgende waarden later in dit artikel nodig bij het instellen van uw exemplaar. Als u deze waarden opnieuw wilt verzamelen, gebruikt u de onderstaande koppelingen voor instructies.
* Azure Digital Twins-exemplaar **_hostnaam_** ([beschikbaar in de portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure Event Hubs connection string **_Connection String_** ([Zoeken in portal](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

In dit voor beeld wordt ook een **device Simulator** gebruikt die het inrichten met behulp van de Device Provisioning Service bevat. De Device Simulator bevindt zich hier: [Azure Digital apparaatdubbels-en IOT hub Integration](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)-voor beeld. Down load het voorbeeld project op uw machine door te navigeren naar de voorbeeld koppeling en de knop *zip downloaden* te selecteren onder de titel. De gedownloade map uitpakken.

De Device Simulator is gebaseerd op **Node.js**, versie 10.0. x of hoger. [*Uw ontwikkel omgeving voorbereiden*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) bevat informatie over het installeren van Node.js voor deze zelf studie over Windows of Linux.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In de onderstaande afbeelding ziet u de architectuur van deze oplossing met behulp van Azure Digital Apparaatdubbels met Device Provisioning Service. Het toont zowel de inrichting van het apparaat als de buiten gebruiks telling van de stroom.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Een weer gave van een apparaat en verschillende Azure-Services in een end-to-end-scenario. Gegevens stromen terug en tussen een thermo staat-apparaat en DPS. Gegevens stromen ook van DPS naar IoT Hub en naar Azure Digital Apparaatdubbels via een Azure-functie met het label ' toewijzing '. Gegevens van een hand matige actie voor het verwijderen van een apparaat stromen via IoT Hub > Event Hubs > Azure Functions > Azure Digital Apparaatdubbels.":::

Dit artikel is onderverdeeld in twee secties:
* [*Apparaat automatisch inrichten met Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Apparaat automatisch buiten gebruik stellen met IoT Hub levenscyclus gebeurtenissen*](#auto-retire-device-using-iot-hub-lifecycle-events)

Zie de afzonderlijke secties verderop in dit artikel voor een diep gaande uitleg van elke stap in de architectuur.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Apparaat automatisch inrichten met Device Provisioning Service

In deze sectie voegt u Device Provisioning Service toe aan Azure Digital Apparaatdubbels om apparaten automatisch in te richten via het onderstaande pad. Dit is een uittreksel van de volledige architectuur die [eerder](#solution-architecture)is weer gegeven.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Inrichtings stroom: een fragment van het diagram van de oplossings architectuur, met cijfers voor de secties van de stroom. Gegevens stromen terug en tussen een thermo staat-apparaat en DPS (1 voor Device > DPS en 5 voor DPS >-apparaat). Gegevens stromen ook van DPS naar IoT Hub (4) en naar Azure Digital Apparaatdubbels (3) via een Azure-functie met het label ' toewijzing ' (2).":::

Hier volgt een beschrijving van de proces stroom:
1. Apparaat neemt contact op met het DPS-eind punt, waarbij identificatie gegevens worden door gegeven om de identiteit ervan te bewijzen.
2. DPS valideert de identiteit van het apparaat door de registratie-ID en-sleutel te valideren op basis van de registratie lijst en een [Azure-functie](../azure-functions/functions-overview.md) aan te roepen om de toewijzing uit te voeren.
3. Met de functie Azure maakt u een nieuwe [dubbele](concepts-twins-graph.md) in azure Digital apparaatdubbels voor het apparaat.
4. Met DPS wordt het apparaat geregistreerd bij een IoT-hub en wordt de gewenste twee status van het apparaat ingevuld.
5. De IoT-hub retourneert informatie over de apparaat-ID en de gegevens van de IoT hub-verbinding met het apparaat. Het apparaat kan nu verbinding maken met de IoT-hub.

In de volgende secties worden de stappen beschreven voor het instellen van deze apparaat stroom voor automatische inrichting.

### <a name="create-a-device-provisioning-service"></a>Een Device Provisioning Service maken

Wanneer een nieuw apparaat wordt ingericht met behulp van Device Provisioning Service, kan een nieuw station voor dat apparaat worden gemaakt in azure Digital Apparaatdubbels.

Maak een Device Provisioning service-exemplaar dat wordt gebruikt voor het inrichten van IoT-apparaten. U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: Quick Start [*: Stel de IOT hub Device Provisioning Service in met de Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Met de volgende Azure CLI-opdracht wordt een Device Provisioning-Service gemaakt. U moet een naam, resource groep en regio opgeven. De opdracht kan worden uitgevoerd in [Cloud shell](https://shell.azure.com)of lokaal als u de Azure cli [op uw computer hebt geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

```azurecli
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Een Azure-functie maken

Vervolgens maakt u een door een HTTP-aanvraag geactiveerde functie binnen een functie-app. U kunt de functie-app die u in de end-to-end zelf studie hebt gemaakt, gebruiken ([*zelf studie: een end-to-end-oplossing verbinden*](tutorial-end-to-end.md)) of uw eigen.

Deze functie wordt gebruikt door de Device Provisioning Service in een [aangepast toewijzings beleid](../iot-dps/how-to-use-custom-allocation-policies.md) dat een nieuw apparaat inricht. Zie [*Azure HTTP-aanvraag trigger voor Azure functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)voor meer informatie over het gebruik van HTTP-aanvragen met Azure functions.

Voeg in uw functie-app-project een nieuwe functie toe. Voeg ook een nieuw NuGet-pakket toe aan het project: `Microsoft.Azure.Devices.Provisioning.Service` .

Plak de volgende code in het zojuist gemaakte functie code bestand.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Sla het bestand op en publiceer vervolgens de functie-app opnieuw. Zie het gedeelte [*de app publiceren*](tutorial-end-to-end.md#publish-the-app) van de end-to-end-zelf studie voor instructies voor het publiceren van de functie-app.

### <a name="configure-your-function"></a>Uw functie configureren

Vervolgens moet u de omgevings variabelen in uw functie-app van eerder instellen, met daarin de verwijzing naar het Azure Digital Apparaatdubbels-exemplaar dat u hebt gemaakt. Als u de end-to-end zelf studie hebt gebruikt ([*zelf studie: een end-to-end oplossing verbinden*](tutorial-end-to-end.md)), is de instelling al geconfigureerd.

Voeg de instelling toe met deze Azure CLI-opdracht:

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Zorg ervoor dat de machtigingen en de rol van beheerde identiteits toewijzing correct zijn geconfigureerd voor de functie-app, zoals beschreven in de sectie [*machtigingen toewijzen aan de functie-app*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) in de end-to-end zelf studie.

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>Inschrijving voor Device Provisioning maken

Vervolgens moet u een registratie in Device Provisioning Service maken met behulp van een **aangepaste toewijzings functie**. Volg de instructies om dit te doen in de secties [*de inschrijving maken*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) en [*unieke Apparaatinstellingen afleiden*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) van het artikel van de Device Provisioning Services over het aangepaste toewijzings beleid.

Terwijl u deze stroom doorloopt, koppelt u de registratie aan de functie die u zojuist hebt gemaakt door tijdens de stap de functie te selecteren om te **selecteren hoe u apparaten aan hubs wilt toewijzen**. Nadat de inschrijving is gemaakt, worden de registratie naam en de primaire of secundaire SAS-sleutel later gebruikt om de Device Simulator voor dit artikel te configureren.

### <a name="set-up-the-device-simulator"></a>De Device Simulator instellen

In dit voor beeld wordt een Device Simulator gebruikt die inrichting bevat met behulp van de Device Provisioning Service. De Device Simulator bevindt zich hier: [Azure Digital apparaatdubbels-en IOT hub Integration](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)-voor beeld. Als u het voor beeld nog niet hebt gedownload, gaat u nu naar de voorbeeld koppeling en selecteert u de knop voor het downloaden van de *zip* onder de titel. De gedownloade map uitpakken.

Open een opdracht venster en navigeer naar de gedownloade map en vervolgens naar de map *device-Simulator* . Installeer de afhankelijkheden voor het project met behulp van de volgende opdracht:

```cmd
npm install
```

Kopieer vervolgens het bestand *. env. sjabloon* naar een nieuw bestand met de naam *. env*en vul de volgende instellingen in:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Sla het bestand op en sluit het.

### <a name="start-running-the-device-simulator"></a>Starten met het uitvoeren van de Device Simulator

Open in de map *device-Simulator* in het opdracht venster de Device Simulator met de volgende opdracht:

```cmd
node .\adt_custom_register.js
```

U ziet het apparaat dat wordt geregistreerd en verbonden met IoT Hub en vervolgens begint met het verzenden van berichten.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Opdrachtvenster weer geven van apparaatregistratie en verzenden van berichten":::

### <a name="validate"></a>Valideren

Als gevolg van de stroom die u in dit artikel hebt ingesteld, wordt het apparaat automatisch geregistreerd in azure Digital Apparaatdubbels. Gebruik de volgende [Azure Digital APPARAATDUBBELS cli](how-to-use-cli.md) -opdracht om de dubbele van het apparaat te vinden in het Azure Digital apparaatdubbels-exemplaar dat u hebt gemaakt.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

U ziet het dubbele apparaat dat wordt gevonden in het Azure Digital Apparaatdubbels-exemplaar.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Opdrachtvenster met nieuw gemaakte dubbele":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Apparaat automatisch buiten gebruik stellen met IoT Hub levenscyclus gebeurtenissen

In deze sectie gaat u IoT Hub levenscyclus gebeurtenissen toevoegen aan Azure Digital Apparaatdubbels om apparaten automatisch buiten gebruik te stellen via het onderstaande pad. Dit is een uittreksel van de volledige architectuur die [eerder](#solution-architecture)is weer gegeven.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Apparaat stroom buiten gebruik stellen: een fragment van het diagram van de oplossings architectuur, met nummers die secties van de stroom bevatten. Het Thermo staat-apparaat wordt weer gegeven zonder verbindingen met de Azure-Services in het diagram. Gegevens van een hand matige actie voor het verwijderen van een apparaat stromen via IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Apparaatdubbels (3).":::

Hier volgt een beschrijving van de proces stroom:
1. Een extern of hand matig proces activeert het verwijderen van een apparaat in IoT Hub.
2. IoT Hub het apparaat verwijdert en een [levens cyclus](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) gebeurtenis van het apparaat genereert die wordt doorgestuurd naar een [Event hub](../event-hubs/event-hubs-about.md).
3. Een Azure-functie verwijdert het dubbele van het apparaat in azure Digital Apparaatdubbels.

In de volgende secties worden de stappen beschreven voor het instellen van deze apparaat stroom voor automatische buiten gebruik stellen.

### <a name="create-an-event-hub"></a>Een Event Hub maken

U moet nu een Azure- [Event hub](../event-hubs/event-hubs-about.md)maken, die wordt gebruikt om de gebeurtenissen van de IOT hub levenscyclus te ontvangen. 

Volg de stappen die worden beschreven in de Snelstartgids [*Create a Event hub*](../event-hubs/event-hubs-create.md) , met behulp van de volgende informatie:
* Als u de end-to-end-zelf studie gebruikt ([*zelf studie: een end-to-end oplossing verbinden*](tutorial-end-to-end.md)), kunt u de resource groep die u hebt gemaakt voor de end-to-end zelf studie hergebruiken.
* Noem uw Event Hub *lifecycleevents*of iets anders van uw keuze en onthoud de naam ruimte die u hebt gemaakt. U gebruikt deze bij het instellen van de levenscyclus functie en IoT Hub route in de volgende secties.

### <a name="create-an-azure-function"></a>Een Azure-functie maken

Vervolgens maakt u een door Event Hubs geactiveerde functie binnen een functie-app. U kunt de functie-app die u in de end-to-end zelf studie hebt gemaakt, gebruiken ([*zelf studie: een end-to-end-oplossing verbinden*](tutorial-end-to-end.md)) of uw eigen. 

Noem uw Event Hub trigger *lifecycleevents*en verbind de Event hub trigger met de Event hub die u in de vorige stap hebt gemaakt. Als u een andere Event Hub naam hebt gebruikt, wijzigt u deze in overeenkomstig de naam van de trigger hieronder.

Deze functie maakt gebruik van de gebeurtenis levens cyclus van IoT Hub om een bestaand apparaat buiten gebruik te stellen. Zie [*IOT hub niet-telemetrie-gebeurtenissen*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)voor meer informatie over levenscyclus gebeurtenissen. Zie [*Azure Event hubs trigger voor Azure functions*](../azure-functions/functions-bindings-event-hubs-trigger.md)voor meer informatie over het gebruik van Event hubs met Azure functions.

Voeg in de gepubliceerde functie-app een nieuwe functie klasse van het type *Event hub-trigger*toe en plak de onderstaande code.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Sla het project op en publiceer vervolgens de functie-app opnieuw. Zie het gedeelte [*de app publiceren*](tutorial-end-to-end.md#publish-the-app) van de end-to-end-zelf studie voor instructies voor het publiceren van de functie-app.

### <a name="configure-your-function"></a>Uw functie configureren

Vervolgens moet u de omgevings variabelen in uw functie-app van eerder instellen, met daarin de verwijzing naar het Azure Digital Apparaatdubbels-exemplaar dat u hebt gemaakt en de Event Hub. Als u de end-to-end zelf studie hebt gebruikt ([*zelf studie: een end-to-end oplossing verbinden*](./tutorial-end-to-end.md)), is de eerste instelling al geconfigureerd.

Voeg de instelling toe met deze Azure CLI-opdracht. De opdracht kan worden uitgevoerd in [Cloud shell](https://shell.azure.com)of lokaal als u de Azure cli [op uw computer hebt geïnstalleerd](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Vervolgens moet u de functie omgevings variabele configureren om verbinding te maken met de zojuist gemaakte Event Hub.

```azurecli
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Zorg ervoor dat de machtigingen en de rol van beheerde identiteits toewijzing correct zijn geconfigureerd voor de functie-app, zoals beschreven in de sectie [*machtigingen toewijzen aan de functie-app*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) in de end-to-end zelf studie.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Een IoT Hub route maken voor levenscyclus gebeurtenissen

U moet nu een IoT Hub route instellen om levenscyclus gebeurtenissen van het apparaat te routeren. In dit geval luistert u specifiek naar het verwijderen van het apparaat, aangeduid door `if (opType == "deleteDeviceIdentity")` . Hiermee wordt de verwijdering van het digitale dubbele item geactiveerd en wordt de buiten gebruiks telling van een apparaat en de bijbehorende digitale dubbele items afgerond.

Instructies voor het maken van een IoT Hub route worden beschreven in dit artikel: [*gebruik IOT hub bericht routering om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten*](../iot-hub/iot-hub-devguide-messages-d2c.md). In de sectie *niet-telemetrie-gebeurtenissen* wordt uitgelegd dat u **levenscyclus gebeurtenissen** van het apparaat kunt gebruiken als de gegevens bron voor de route.

De stappen die u moet door lopen voor deze installatie zijn:
1. Een aangepaste IoT Hub Event Hub eind punt maken. Dit eind punt moet gericht zijn op de Event Hub die u hebt gemaakt in de sectie [*een event hub maken*](#create-an-event-hub) .
2. Een route voor *levenscyclus gebeurtenissen* van een apparaat toevoegen. Gebruik het eind punt dat u in de vorige stap hebt gemaakt. U kunt de levens cyclus gebeurtenissen van het apparaat beperken tot het verzenden van gebeurtenissen door de routerings query toe te voegen `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Een route toevoegen":::

Zodra u deze stroom hebt door lopen, is alles zo ingesteld dat apparaten end-to-end buiten gebruik worden gesteld.

### <a name="validate"></a>Valideren

Als u het proces van het buiten gebruik wilt stellen, moet u het apparaat hand matig verwijderen uit IoT Hub.

In de [eerste helft van dit artikel](#auto-provision-device-using-device-provisioning-service)hebt u een apparaat gemaakt in IOT hub en een bijbehorende digitale twee. 

Ga nu naar het IoT Hub en verwijder dat apparaat (u kunt dit doen met een [Azure cli-opdracht](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) of in de [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

Het apparaat wordt automatisch uit Azure Digital Apparaatdubbels verwijderd. 

Gebruik de volgende [Azure Digital APPARAATDUBBELS cli](how-to-use-cli.md) -opdracht om te controleren of het dubbele apparaat in het Azure Digital apparaatdubbels-exemplaar is verwijderd.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

U ziet dat de dubbele van het apparaat niet meer kan worden gevonden in het Azure Digital Apparaatdubbels-exemplaar.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Opdrachtvenster met dubbele niet gevonden":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt niet meer nodig hebt, volgt u deze stappen om ze te verwijderen.

Met de Azure Cloud Shell of lokale Azure CLI kunt u alle Azure-resources in een resource groep verwijderen met de opdracht [AZ Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Hiermee verwijdert u de resource groep. het Azure Digital Apparaatdubbels-exemplaar; de IoT-hub en de registratie van het hub-apparaat; het event grid-onderwerp en de bijbehorende abonnementen; de Event hubs-naam ruimte en beide Azure Functions-apps, inclusief gekoppelde resources zoals opslag.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

```azurecli
az group delete --name <your-resource-group>
```
<!-- 
Next, delete the Azure AD app registration you created for your client app with this command:

```azurecli
az ad app delete --id <your-application-ID>
``` -->

Verwijder vervolgens de voor beeld-map van het project dat u hebt gedownload van uw lokale computer.

## <a name="next-steps"></a>Volgende stappen

De digitale apparaatdubbels die voor de apparaten zijn gemaakt, worden opgeslagen als een platte hiërarchie in azure Digital Apparaatdubbels, maar ze kunnen worden verrijkt met model gegevens en een hiërarchie met meerdere niveaus voor de organisatie. Lees voor meer informatie over dit concept:

* [*Concepten: Digital apparaatdubbels en het dubbele diagram*](concepts-twins-graph.md)

U kunt aangepaste logica schrijven om deze informatie automatisch te verstrekken met behulp van het model en de grafiek gegevens die al zijn opgeslagen in azure Digital Apparaatdubbels. Zie het volgende voor meer informatie over het beheren, bijwerken en ophalen van gegevens uit de apparaatdubbels-grafiek:

* [*Instructies: een digitale twee richtings beheer beheren*](how-to-manage-twin.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)