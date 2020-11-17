---
title: Time Series Insights gebruiken om telemetrie van uw Azure IoT Plug and Play-apparaten op te slaan en te analyseren | Microsoft Docs
description: Stel een Time Series Insights-omgeving in en verbind uw IoT Hub om telemetrie van uw IoT Plug and Play-apparaten te bekijken en te analyseren.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422260"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Zelfstudie: Time Series Insights Gen2 maken en er verbinding mee maken om telemetrie van IoT Plug and Play-apparaten op te slaan, te visualiseren en te analyseren

In deze zelfstudie leert u hoe u een [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi)-omgeving (TSI) maakt en correct configureert om te integreren met uw IoT Plug and Play-oplossing. Met TSI kunt u tijdreeksgegevens verzamelen, verwerken, opslaan, visualiseren en er query's op uitvoeren op IoT-schaal (Internet of Things).

Eerst richt u een TSI-omgeving in en verbindt u uw IoT Hub als een streaming gebeurtenisbron. Vervolgens voert u modelsynchronisatie uit om het Time Series-model van uw TSI-omgeving te maken op basis van de [Digital Twins Definition Language](https://github.com/Azure/opendigitaltwins-dtdl)-voorbeeldmodelbestanden (DTDL) die u hebt gebruikt voor de temperatuurregelings- en thermostaatapparaten.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Om de vereiste van het lokaal installeren van de Azure CLI te vermijden, kunt u de Azure Cloud Shell gebruiken om de cloudservices in te stellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Time Series-id selecteren

Terwijl u uw TSI-omgeving inricht, moet u een Time Series-id selecteren. Het is essentieel de juiste Time Series-id te selecteren, aangezien de eigenschap onveranderbaar is en niet kan worden gewijzigd nadat deze is ingesteld. Het kiezen van een Time Series-id is net als het kiezen van een partitiesleutel voor een database. Uw TS-id moet doorgaans het bladknooppunt van uw assetmodel zijn. Met andere woorden: het is meestal raadzaam de id-eigenschap te selecteren van de meest gedetailleerde asset of sensor die telemetrie verzendt.

Als IoT Plug and Play-gebruiker is de relevante vraag bij het selecteren van uw TS-id of er [onderdelen](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) aanwezig zijn in uw apparaatmodellen. 

![TS-id selecteren](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Als u de snelstartgids hebt gevolgd en uw IoT Hub-apparaat de [Thermostaat](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) vertegenwoordigt, gebruikt u `iot-hub-connection-device-id` als uw TS-id.

* Als u een van de zelfstudies hebt gevolgd voor de [Temperatuurregelaar](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) met meerdere onderdelen, gebruikt u een samengestelde sleutel in de onderstaande sectie, geschreven als `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Uw Azure Time Series Insights Gen2-omgeving inrichten

De onderstaande opdracht:

* Maakt een Azure Storage-account voor de [koude opslag](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store) van uw omgeving, ontworpen voor langetermijnretentie en analyse van historische gegevens.
  * Vervang `mytsicoldstore` door een unieke naam voor uw account.
* Maakt een Azure Time Series Insights Gen2-omgeving, inclusief warme opslag met een bewaarperiode van zeven dagen en koude opslag met een oneindige bewaarperiode. 
  * Vervang `my-tsi-env` door een unieke naam voor uw TSI-omgeving 
  * Vervang `my-pnp-resourcegroup` door de naam van de resourcegroep die u tijdens de instelling hebt gebruikt
  * Vervang `my-ts-id-property` door de eigenschapswaarde van uw TS-id op basis van de bovenstaande selectiecriteria

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

U gaat nu de IoT Hub die u eerder hebt gemaakt, configureren als de [gebeurtenisbron](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources) van uw omgeving. Wanneer uw gebeurtenisbron is verbonden, begint TSI gebeurtenissen uit uw hub te indexeren, beginnend met de vroegste gebeurtenis in de wachtrij.

Maak eerst een unieke consumentengroep in uw IoT Hub voor uw TSI-omgeving. Vervang `my-pnp-hub` door de naam van de IoT Hub die u eerder hebt gebruikt.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Verbind de IoT Hub. Vervang `my-pnp-resourcegroup`, `my-pnp-hub` en `my-tsi-env` door uw respectieve waarden.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Ga in de [Azure-portal](https://portal.azure.com) naar uw resourcegroep en selecteer uw zojuist gemaakte Time Series Insights-omgeving. Ga naar de *URL voor Time Series Insights Explorer* die in het exemplarenoverzicht wordt weergegeven.

![Overzichtspagina van portal](./media/tutorial-configure-tsi/view-environment.png)

In de verkenner zou u uw twee thermostaten onder 'Alle hiërarchieën' moeten zien. Vervolgens gaat u uw Time Series-model cureren op basis van uw apparaatmodel.

![Explorer-weergave 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Modelsynchronisatie tussen Digital Twins Definition Language en Time Series Insights Gen2

Vervolgens gaat u uw DTDL-apparaatmodel vertalen naar het assetmodel in Azure Time Series Insights (TSI). Het Time Series-model van TSI is een hulpmiddel voor semantische modellering om gegevens te contextualiseren in TSI. Het Time Series-model heeft drie kernonderdelen:

* [Time Series-modelexemplaren](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Exemplaren zijn virtuele representaties van de tijdreeksen zelf. Exemplaren worden uniek geïdentificeerd door uw TS-id.
* [Time Series-modelhiërarchieën](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). Hiërarchieën organiseren exemplaren door eigenschapsnamen en hun relaties te specificeren.
* [Time Series-modeltypen](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Typen helpen u [variabelen](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) of formules voor het uitvoeren van berekeningen te definiëren. Typen zijn gekoppeld aan een specifiek exemplaar.

> [!NOTE]
> De onderstaande voorbeelden zijn voor de Temperatuurregelaar met meerdere onderdelen.

### <a name="define-your-types"></a>Uw typen definiëren

U kunt beginnen met het opnemen van gegevens in Azure Time Series Insights Gen2 zonder vooraf een model te hebben gedefinieerd. Wanneer telemetrie aankomt, probeert TSI tijdreeksexemplaren automatisch op te lossen op basis van de eigenschapswaarde van de TS-id. Alle exemplaren krijgen het *Standaardtype* toegewezen. U moet handmatig een nieuw Type maken om uw modellen te vertegenwoordigen. In de onderstaande afbeelding ziet u een eenvoudige methode om een DTDL-model en een TS-modeltype te synchroniseren:

![DTDL naar TS-modeltype](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* Uw Digital Twin Model Identifier (DTMI) wordt uw Type-id
* De naam van het Type kan de naam of weergavenaam van het model zijn
* De modelbeschrijving wordt de beschrijving van het Type
* Er wordt ten minste één Type-variabele gemaakt voor elk telemetrieonderdeel dat een numeriek schema heeft. 
  * Er kunnen alleen numerieke gegevenstypen worden gebruikt voor variabelen, maar als een waarde wordt verzonden als een tekenreeks die kan worden geparseerd, bijvoorbeeld `"0"`, kunt u een [conversiefunctie](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) zoals `toDouble` gebruiken
* De naam van de variabele kan de naam of weergavenaam van de telemetrie zijn
* Wanneer u de Time Series-expressie (TSX) van de variabele definieert, raadpleegt u de naam van de verbonden telemetrie en het bijbehorende gegevenstype.

> [!NOTE]
> Dit voorbeeld toont slechts twee variabelen, een geaggregeerde en een numerieke, maar elk Type kan maximaal 100 variabelen hebben. Verschillende variabelen kunnen naar dezelfde telemetriewaarde verwijzen om verschillende berekeningen uit te voeren. Voor de volledige lijst met filters, aggregaties en scalaire functies bekijkt u de documentatie [Syntaxis van Time Series-expressies voor Time Series Insights Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

Open uw gewenste teksteditor en sla de onderstaande JSON op uw lokale station op:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Ga in uw Time Series Insights Explorer naar het tabblad Model door aan de linkerkant op het modelpictogram te klikken. Klik op **Typen** en klik vervolgens op **JSON uploaden**:

![Uploaden](./media/tutorial-configure-tsi/upload-type.png)

Selecteer **Bestand kiezen**, selecteer de JSON die u eerder hebt opgeslagen, en klik op **Uploaden**.

U moet nu het zojuist gedefinieerde type Thermostaat zien.

### <a name="optional---create-a-hierarchy"></a>Optioneel - Een hiërarchie maken

U kunt desgewenst een hiërarchie maken om de twee thermostaatonderdelen te organiseren onder hun bovenliggende apparaat Temperatuurregelaar.

Klik op *Hiërarchieën* en selecteer *Een hiërarchie toevoegen*. Voer `Device Fleet` als de naam in, maak één niveau genaamd `Device Name` en klik vervolgens op *Opslaan*.

![Een hiërarchie toevoegen](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Uw exemplaren aan het juiste type toewijzen

Vervolgens wijzigt u het Type van uw exemplaren en plaatst u ze desgewenst in de hiërarchie

Selecteer het tabblad *Exemplaren* en klik helemaal rechts op het pictogram *Bewerken*.

![Exemplaren bewerken](./media/tutorial-configure-tsi/edit-instance.png)

Klik op het vervolgkeuzemenu Type en selecteer `Thermostat`. 

![Exemplaartype wijzigen](./media/tutorial-configure-tsi/change-type.png)

Als u een hiërarchie hebt gemaakt, selecteert u *Exemplaarvelden* en schakelt u het selectievakje `Device Fleet` in. Voer `Temperature Controller` als de waarde van hun bovenliggende apparaat in en klik vervolgens op *Opslaan*.

![Toewijzen aan hiërarchie](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Herhaal de bovenstaande stappen voor uw tweede Thermostaat.

### <a name="view-your-data"></a>Uw gegevens weergeven

Ga terug naar het grafiekdeelvenster en vouw Alle apparaten en Temperatuurregelaar uit. Klik op thermostaat1, selecteer de variabele `Temperature` en klik vervolgens op *Toevoegen* om de waarde op de grafiek te plaatsen. Doe hetzelfde voor thermostaat2.

![Exemplaartype voor thermostaat2 wijzigen](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Volgende stappen

* Bekijk de documentatie [Azure Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels) voor meer informatie over de verschillende grafiekopties, waaronder intervalgroottes en y-asbesturingselementen.

* Bekijk [dit](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) artikel voor een uitgebreid overzicht van het Time Series-model van uw omgeving.

* [Klik hier](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis) voor meer informatie over de query-API's en de syntaxis van Time Series-expressies.




