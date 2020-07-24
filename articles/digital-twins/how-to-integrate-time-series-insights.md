---
title: Integreren met Time Series Insights
titleSuffix: Azure Digital Twins
description: Zie gebeurtenis routes van Azure Digital Apparaatdubbels instellen op Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131596"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Digitale Apparaatdubbels integreren met Azure Time Series Insights

In deze Naslag informatie wordt uitgelegd hoe u Azure Digital Apparaatdubbels integreert met [Azure time series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Met deze oplossing kunt u historische gegevens over uw IoT-oplossing verzamelen en analyseren. Azure Digital Apparaatdubbels is een uitstekende oplossing voor het inbrengen van gegevens in Time Series Insights, omdat u hiermee meerdere gegevensstreams kunt correleren en uw gegevens gestandaardiseerd kunt maken voordat u deze naar Time Series Insights verzendt. 

## <a name="solution-architecture"></a>Architectuur voor de oplossing

U gaat time series Insights koppelen aan Azure Digital Apparaatdubbels via het onderstaande pad.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Een weer gave van Azure-Services in een end-to-end-scenario, markeren Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Vereisten

* U hebt een Azure Digital Apparaatdubbels-instantie nodig die u een paar keer dubbele informatie kunt bijwerken om te zien dat de gegevens die in Time Series Insights worden bijgehouden. 
    * Als u er nog geen hebt, volgt u de zelf studie over Azure Digital Apparaatdubbels [*: Verbind een end-to-end oplossing*](./tutorial-end-to-end.md) om een Azure Digital apparaatdubbels-exemplaar en een virtueel IOT-apparaat in te stellen voor het genereren van twee wijzigingen.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Een route maken en filteren op dubbele update meldingen

Azure Digital Apparaatdubbels-instanties kunnen [dubbele update gebeurtenissen](how-to-interpret-event-data.md) verzenden wanneer de status van een twee is bijgewerkt. U gaat een route maken waarmee deze update gebeurtenissen worden doorgestuurd naar een Event Hub voor verdere verwerking.

De zelf studie over Azure Digital Apparaatdubbels [*: verbinding maken met een end-to-end-oplossing*](./tutorial-end-to-end.md) door een scenario waarin een thermo meter wordt gebruikt voor het bijwerken van een temperatuur kenmerk dat is gekoppeld aan de dubbele plek van een kamer. In dit patroon worden de dubbele updates gebruikt in plaats van telemetrie over te sturen van een IoT-apparaat. Dit biedt u de flexibiliteit om de onderliggende gegevens bron te wijzigen zonder dat u uw Time Series Insights logica hoeft bij te werken.

1. Maak een Event Hub-naam ruimte, die gebeurtenissen ontvangt van uw Azure Digital Apparaatdubbels-exemplaar. U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: Quick Start [*: een event hub maken met behulp van Azure Portal*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Maak een Event Hub.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Maak een [autorisatie regel](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) met machtigingen voor verzenden en ontvangen.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Maak een eind punt om uw event grid-onderwerp aan Azure Digital Apparaatdubbels te koppelen.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Maak een route in azure Digital Apparaatdubbels om dubbele update gebeurtenissen naar uw eind punt te verzenden. Met het filter in deze route worden alleen dubbele update berichten door gegeven aan uw eind punt.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Een Azure-functie maken 

Vervolgens gaat u een door Event Hubs geactiveerde functie maken in een nieuwe functie-app, uw functie-app uit de end-to-end zelf studie ([*zelf studie: een end-to-end oplossing verbinden*](./tutorial-end-to-end.md)). Met deze functie worden deze updates van het oorspronkelijke formulier geconverteerd als JSON-patch-documenten naar JSON-objecten die alleen bijgewerkte en toegevoegde waarden van uw apparaatdubbels bevatten.

Zie [*Azure Event hubs trigger voor Azure functions*](../azure-functions/functions-bindings-event-hubs-trigger.md)voor meer informatie over het gebruik van Event hubs met Azure functions.

Vervang de functie code door de volgende code in de gepubliceerde functie-app.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

Hier verzendt de functie vervolgens de JSON-objecten die worden gemaakt naar een tweede Event Hub, waarmee u verbinding maakt met Time Series Insights.

## <a name="send-telemetry-to-an-event-hub"></a>Telemetrie verzenden naar een Event Hub

U gaat nu een tweede Event Hub maken en uw functie zo configureren dat de uitvoer naar die Event Hub wordt gestreamd. Deze Event Hub wordt vervolgens verbonden met Time Series Insights.

### <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt de onderstaande Azure CLI-instructies gebruiken of de Azure Portal: Quick Start [*: een event hub maken met behulp van Azure Portal*](../event-hubs/event-hubs-create.md).

1. Bereid uw Event Hub-naam ruimte en de naam van de resource groep voor met eerdere versies 

2. Een Event Hub maken
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Een [autorisatie regel](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) maken met machtigingen voor verzenden en ontvangen
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Uw functie configureren

U moet een omgevings variabele in uw functie-app van eerder instellen, die uw Event Hub connection string bevat.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Stel de Time Series Insights Event Hub in connection string

1. De [Event hub-Connection String](../event-hubs/event-hubs-get-connection-string.md) ophalen voor de autorisatie regels die u hierboven hebt gemaakt voor de time series Insights hub:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. Maak in uw functie-app een app-instelling die uw connection string bevat:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Stel de Apparaatdubbels-Event Hub in connection string

1. De [Event hub-Connection String](../event-hubs/event-hubs-get-connection-string.md) ophalen voor de autorisatie regels die u hierboven hebt gemaakt voor de apparaatdubbels hub.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Maak in uw functie-app een app-instelling die uw connection string bevat:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Een Time Series Insights-exemplaar maken en verbinden

Vervolgens stelt u een Time Series Insights-exemplaar in om de gegevens van uw tweede Event Hub te ontvangen. Zie [ *zelf studie: een Azure time series INSIGHTS Gen2 payg-omgeving instellen* voor meer informatie over dit proces.](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. Begin met het maken van een Time Series Insights resource in het Azure Portal. 
    1. Selecteer de prijs categorie **payg (preview)** .
    2. U moet een tijd reeks-ID voor deze omgeving kiezen. De tijd reeks-ID mag Maxi maal drie waarden zijn die u gaat gebruiken om te zoeken naar uw gegevens in Time Series Insights. Voor deze zelf studie kunt u **$dtId**gebruiken. Meer informatie over het selecteren van een ID-waarde in [*Best practices voor het kiezen van een time series-id*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="De portal voor het maken van een Time Series Insights-omgeving. De prijs categorie PAYG (preview) is geselecteerd en de eigenschaps naam van de tijd reeks-ID is $dtId":::

2. Selecteer **volgende: gebeurtenis bron** en selecteer uw event hubs informatie hierboven. U moet ook een nieuwe Event Hubs Consumer groep maken.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="De portal voor het maken van een Time Series Insights-omgevings gebeurtenis bron. U maakt een gebeurtenis bron met de Event Hub informatie hierboven. U maakt ook een nieuwe consumenten groep.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Beginnen met het verzenden van IoT-gegevens naar Azure Digital Apparaatdubbels

Als u wilt beginnen met het verzenden van gegevens naar Time Series Insights, moet u beginnen met het bijwerken van de digitale twee eigenschappen met het wijzigen van gegevens waarden. Gebruik de opdracht [AZ DT dubbele update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Als u de end-to-end-zelf studie gebruikt om te helpen bij het instellen van de omgeving, kunt u beginnen met het verzenden van gesimuleerde IoT-gegevens door het `DeviceSimulator` project uit te voeren vanuit de zelf studie over Azure Digital apparaatdubbels [*: Verbind een end-to-end oplossing*](tutorial-end-to-end.md). De instructies zijn te vinden in de sectie [*simulatie configureren en uitvoeren*](tutorial-end-to-end.md#configure-and-run-the-simulation) van de zelf studie.

## <a name="visualize-your-data-in-time-series-insights"></a>Uw gegevens visualiseren in Time Series Insights

Gegevens moeten nu worden gestroomd naar uw Time Series Insights-exemplaar, die klaar zijn om te worden geanalyseerd. Volg de onderstaande stappen om de gegevens in te verkennen.

1. Open uw Time Series Insights-exemplaar in de Azure Portal. Ga naar de URL van Time Series Insights Explorer die wordt weer gegeven in het overzicht.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Klik op de URL van Time Series Insights Explorer op het tabblad Overzicht van uw Time Series Insights omgeving":::

2. In de Explorer ziet u de drie apparaatdubbels die aan de linkerkant worden weer gegeven. Klik op **thermostat67**, selecteer **patch_value**en klik op **toevoegen**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Klik op * * thermostat67 * *, selecteer * * Tempe ratuur * * en klik op * * toevoegen * *":::

3. U ziet nu de oorspronkelijke temperatuur aflezingen van uw Thermo staat, zoals hieronder wordt weer gegeven. Diezelfde temperatuur meting wordt bijgewerkt voor room21 en floor1, en u kunt deze gegevens stromen op elkaar visualiseren.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="De initiële temperatuur gegevens worden in een grafiek in de TSI-Verkenner gegrafeerd. Het is een regel met wille keurige waarden tussen 68 en 85":::

4. Als u toestaat dat de simulatie veel langer wordt uitgevoerd, ziet uw visualisatie er ongeveer als volgt uit:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="De gegevens over de Tempe ratuur van elke dubbele kleur worden in drie parallelle lijnen van verschillende kleuren genoteerd.":::

## <a name="next-steps"></a>Volgende stappen

De digitale apparaatdubbels worden standaard opgeslagen als een platte hiërarchie in Time Series Insights, maar ze kunnen worden verrijkt met model gegevens en een hiërarchie met meerdere niveaus voor de organisatie. Lees voor meer informatie over dit proces: 

* [*Zelf studie: een model definiëren en Toep assen*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

U kunt aangepaste logica schrijven om deze informatie automatisch te verstrekken met behulp van het model en de grafiek gegevens die al zijn opgeslagen in azure Digital Apparaatdubbels. Zie de volgende verwijzingen voor meer informatie over het beheren, bijwerken en ophalen van gegevens uit de apparaatdubbels-grafiek:

* [*Instructies: een digitale twee richtings beheer beheren*](./how-to-manage-twin.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](./how-to-query-graph.md)