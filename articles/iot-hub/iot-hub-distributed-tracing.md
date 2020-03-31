---
title: Correlatie-id's toevoegen aan IoT-berichten w/distributed tracing (pre)
description: Meer informatie over het gebruik van de gedistribueerde traceringsmogelijkheid om IoT-berichten te traceren in de Azure-services die door uw oplossing worden gebruikt.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: efee34ddfb2b2f6090d5dc8c43647c7ee1c53ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562425"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Azure IoT-berichten van apparaat naar cloud traceren met gedistribueerde tracering (voorbeeld)

Microsoft Azure IoT Hub ondersteunt momenteel gedistribueerde tracering als [voorbeeldfunctie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

IoT Hub is een van de eerste Azure-services die gedistribueerde tracering ondersteunt. Naarmate meer Azure-services ondersteuning bieden voor gedistribueerde tracering, u IoT-berichten traceren in de Azure-services die betrokken zijn bij uw oplossing. Zie Gedistribueerd traceren voor een achtergrond over gedistribueerde [tracering.](../azure-monitor/app/distributed-tracing.md)

Het inschakelen van gedistribueerde tracering voor IoT Hub geeft u de mogelijkheid om:

- Controleer nauwkeurig de stroom van elk bericht via IoT Hub met behulp van [tracecontext.](https://github.com/w3c/trace-context) Deze traceringscontext bevat correlatie-id's waarmee u gebeurtenissen van één component correleren met gebeurtenissen uit een andere component. Het kan worden toegepast voor een subset of alle IoT-apparaatberichten met behulp van [device twin.](iot-hub-devguide-device-twins.md)
- Log de traceringscontext automatisch in [bij diagnostische logboeken van Azure Monitor](iot-hub-monitor-resource-health.md).
- Meet en begrijp de berichtstroom en latentie van apparaten naar IoT Hub en routeringseindpunten.
- Ga na over hoe u gedistribueerde tracering wilt implementeren voor de niet-Azure-services in uw IoT-oplossing.

In dit artikel gebruikt u de [Azure IoT-apparaat SDK voor C](iot-hub-device-sdk-c-intro.md) met gedistribueerde tracering. Voor de andere SDK's is nog steeds ondersteuning voor gedistribueerde tracering aan de gang.

## <a name="prerequisites"></a>Vereisten

- De preview van gedistribueerde tracering wordt momenteel alleen ondersteund voor IoT-hubs die zijn gemaakt in de volgende regio's:

  - **Europa - noord**
  - **Azië - zuidoost**
  - **VS - west 2**

- In dit artikel wordt ervan uitgegaan dat u bekend bent met het verzenden van telemetrieberichten naar uw IoT-hub. Controleer of u de [telemetrie C Quickstart verzenden](quickstart-send-telemetry-c.md)hebt voltooid.

- Registreer een apparaat met uw IoT-hub (stappen beschikbaar in elke Quickstart) en noteer de verbindingstekenreeks.

- Installeer de meest recente versie van [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>IoT-hub configureren

In deze sectie configureert u een IoT-hub om gedistribueerde traceringskenmerken (correlatie-id's en tijdstempels) te registreren.

1. Navigeer naar uw IoT-hub in de [Azure-portal.](https://portal.azure.com/)

1. Schuif in het linkerdeelvenster voor uw IoT-hub omlaag naar de sectie **Controle** en klik op **Diagnostische instellingen**.

1. Als diagnostische instellingen nog niet zijn ingeschakeld, klikt u **op Diagnostische gegevens inschakelen**. Als u diagnostische instellingen al hebt ingeschakeld, klikt u op **Diagnostische instelling toevoegen**.

1. Voer in het veld **Naam** een naam in voor een nieuwe diagnostische instelling. Bijvoorbeeld **DistributedTracingSettings**.

1. Kies een of meer van de volgende opties die bepalen waar de logboekregistratie wordt verzonden:

    - **Archiveren naar een opslagaccount:** Configureer een opslagaccount om de logboekregistratiegegevens te bevatten.
    - **Streamen naar een gebeurtenishub:** configureer een gebeurtenishub om de logboekregistratiegegevens te bevatten.
    - **Verzenden naar Logboekanalyse:** een werkruimte voor logboekanalyse configureren om de logboekgegevens te bevatten.

1. Selecteer **in** de sectie Logboek de bewerkingen waarvoor u logboekgegevens wilt registreren.

    Zorg ervoor dat u **DistributedTracing**opneemt en een **retentie configureert** voor het aantal dagen dat de logboekregistratie moet worden behouden. Logboekretentie heeft wel invloed op de opslagkosten.

    ![Schermafbeelding van de categorie DistributedTracing voor De diagnostische instellingen van IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klik **op Opslaan** voor de nieuwe instelling.

1. (Optioneel) Als u wilt zien dat de berichten naar verschillende plaatsen stromen, stelt u [routeringsregels](iot-hub-devguide-messages-d2c.md)in op ten minste twee verschillende eindpunten .

Zodra de logboekregistratie is ingeschakeld, registreert IoT Hub een logboek wanneer in een van de volgende situaties een bericht met geldige traceringseigenschappen wordt aangetroffen:

- De berichten komen binnen bij de gateway van IoT Hub.
- Het bericht wordt verwerkt door de IoT Hub.
- Het bericht wordt doorgestuurd naar aangepaste eindpunten. Routering moet zijn ingeschakeld.

Zie [Distributed tracing in IoT Hub diagnostische logboeken](iot-hub-monitor-resource-health.md#distributed-tracing-preview)voor meer informatie over deze logboeken en hun schema's.

## <a name="set-up-device"></a>Apparaat instellen

In deze sectie bereidt u een ontwikkelomgeving voor op gebruik met de [Azure IoT C SDK.](https://github.com/Azure/azure-iot-sdk-c) Vervolgens wijzigt u een van de voorbeelden om gedistribueerde tracering in te schakelen op de telemetrieberichten van uw apparaat.

Deze instructies zijn voor het bouwen van het voorbeeld op Windows. Zie Voor andere omgevingen de C SDK of [Voorverpakte C SDK compileren voor platformspecifieke ontwikkeling.](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development) [Compile the C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile)

### <a name="clone-the-source-code-and-initialize"></a>De broncode klonen en initialiseren

1. Installeer ['Desktopontwikkeling met C++' workload](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) voor Visual Studio 2019. Visual Studio 2017 en 2015 worden ook ondersteund.

1. CMake [installeren](https://cmake.org/). Zorg ervoor dat `PATH` het `cmake -version` in uw door te typen van een opdracht prompt.

1. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository te klonen:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

1. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten `azure-iot-sdk-c` uit de map uit:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Als `cmake` u uw C++ compiler niet vinden, u buildfouten krijgen tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Het voorbeeld van telemetrie verzenden bewerken om gedistribueerde tracering in te schakelen

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Download het voorbeeld op GitHub</a>

1. Gebruik een editor `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` om het bronbestand te openen.

1. Zoek de declaratie van de `connectionString` constante:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Vervang de waarde `connectionString` van de constante door de apparaatverbindingstekenreeks waar u een notitie van hebt gemaakt in het [register een apparaatsectie](./quickstart-send-telemetry-c.md#register-a-device) van de Quickstart Verzenden van [telemetrie](./quickstart-send-telemetry-c.md).

1. Wijzig `MESSAGE_COUNT` de `5000`definitie in :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Zoek de coderegel `IoTHubDeviceClient_LL_SetConnectionStatusCallback` die aanroept om een terugbelfunctie voor verbindingsstatus te registreren voordat de berichtlus wordt verzonden. Voeg code toe onder die `IoTHubDeviceClient_LL_EnablePolicyConfiguration` regel, zoals hieronder wordt weergegeven, om gedistribueerde tracering voor het apparaat in te schakelen:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    De `IoTHubDeviceClient_LL_EnablePolicyConfiguration` functie maakt beleidsregels mogelijk voor specifieke IoTHub-functies die zijn geconfigureerd via [apparaattweelingen.](./iot-hub-devguide-device-twins.md) Zodra `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` de coderegel hierboven is ingeschakeld, weerspiegelt het traceringsgedrag van het apparaat de gedistribueerde traceringswijzigingen die op de apparaattweeling zijn aangebracht.

1. Als u de voorbeeld-app wilt laten werken zonder al uw quota op te nemen, voegt u een vertraging van één seconde toe aan het einde van de berichtlus verzenden:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compileren en uitvoeren

1. Navigeer naar de *iothub_ll_telemetry_sample* projectmap van`azure-iot-sdk-c/cmake`de cmake-map ( ) die u eerder hebt gemaakt en compileer het voorbeeld:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Voer de toepassing uit. Het apparaat verzendt telemetrie ter ondersteuning van gedistribueerde tracering.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Houd de app draaiende. Bekijk eventueel het bericht dat naar IoT Hub wordt verzonden door naar het consolevenster te kijken.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Tijdelijke oplossing voor externe klanten

Het is **niet triviaal** om een voorbeeld te bekijken van de gedistribueerde traceringsfunctie zonder de C SDK te gebruiken. Deze aanpak wordt dus niet aanbevolen.

Ten eerste moet u alle primitieve nit-protocol-primitieven van iot-hubin uw berichten implementeren door de dev-gids [IoT Hub-berichten te maken en te lezen.](iot-hub-devguide-messages-construct.md) Bewerk vervolgens de protocoleigenschappen in de MQTT/AMQP-berichten die u wilt toevoegen `tracestate` als **systeemeigenschap.** Met name:

* Voeg voor MQTT toe aan `%24.tracestate=timestamp%3d1539243209` `1539243209` het berichtonderwerp, waar moet worden vervangen door de aanmaaktijd van het bericht in de unix-tijdstempelindeling. Zie bijvoorbeeld de implementatie [in de C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Voor AMQP, `key("tracestate")` `value("timestamp=1539243209")` toevoegen en als bericht annotatie. Zie [hier](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)voor een referentie-implementatie.

Als u het percentage berichten met deze eigenschap wilt beheren, implementeert u logica om naar door de cloud geïnitieerde gebeurtenissen te luisteren, zoals dubbele updates.

## <a name="update-sampling-options"></a>Bemonsteringsopties bijwerken 

Als u het percentage berichten wilt wijzigen dat vanuit de cloud moet worden getraceerd, moet u de apparaattweeling bijwerken. U dit op meerdere manieren uitvoeren, waaronder de JSON-editor in portal en de IoT Hub-service SDK. De volgende onderafdelingen geven voorbeelden.

### <a name="update-using-the-portal"></a>Bijwerken met behulp van de portal

1. Navigeer naar uw IoT-hub in [Azure-portal](https://portal.azure.com/)en klik vervolgens op **IoT-apparaten**.

1. Klik op uw apparaat.

1. Zoek naar **Gedistribueerde tracering inschakelen (voorbeeld)** en selecteer **Inschakelen**.

    ![Gedistribueerde tracering inschakelen in Azure-portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Kies een **samplingrate** tussen 0% en 100%.

1. Klik op **Opslaan**.

1. Wacht een paar seconden en druk op **Vernieuwen**en als het apparaat wordt herkend, verschijnt er een synchronisatiepictogram met een vinkje.

1. Ga terug naar het consolevenster voor de telemetriebericht-app. U ziet berichten die `tracestate` worden verzonden met in de toepassingseigenschappen.

    ![Status traceren](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Optioneel) Wijzig de bemonsteringsfrequentie in een andere waarde en observeer `tracestate` de frequentiewijziging die berichten in de toepassingseigenschappen bevatten.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Bijwerken met Azure IoT Hub voor VS-code

1. Installeer VS-code en installeer vervolgens vanaf [hier](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)de nieuwste versie van Azure IoT Hub voor VS-code.

1. Open VS-code en [stel de verbindingstekenreeks van IoT Hub in](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Vouw het apparaat uit en zoek naar **de instelling Voor gedistribueerde tracering (Voorbeeld).** Klik eronder op **Gedistribueerde traceringsinstelling (Voorbeeld)** bijwerken van subknooppunt.

    ![Gedistribueerde tracering inschakelen in Azure IoT Hub-extensie](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Selecteer in het pop-upvenster **Inschakelen**en druk op Enter om 100 als bemonsteringsfrequentie te bevestigen.

    ![Bemonsteringsmodus bijwerken](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Samplingrate bijwerken](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Bulkupdate voor meerdere apparaten

Als u de configuratie van de gedistribueerde traceringsbemonstering voor meerdere apparaten wilt bijwerken, gebruikt u [automatische apparaatconfiguratie](iot-hub-auto-device-config.md). Zorg ervoor dat u dit dubbele schema volgt:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Elementnaam | Vereist | Type | Beschrijving |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Ja | Geheel getal | Twee moduswaarden worden momenteel ondersteund om de bemonstering in- en uit te schakelen. `1`is aan `2` en is uitgeschakeld. |
| `sampling_rate` | Ja | Geheel getal | Deze waarde is een percentage. Alleen waarden `0` `100` van naar (inclusief) zijn toegestaan.  |

## <a name="query-and-visualize"></a>Query's en visualiseren

Als u alle traces wilt zien die zijn vastgelegd door een IoT-hub, voert u een query uit in het logboekarchief dat u hebt geselecteerd in diagnostische instellingen. Deze sectie loopt door een paar verschillende opties.

### <a name="query-using-log-analytics"></a>Query met Logboekanalyse

Als u Logboekanalyse hebt ingesteld [met diagnostische logboeken,](../azure-monitor/platform/resource-logs-collect-storage.md) `DistributedTracing` zoekt u naar logboeken in de categorie. In deze query worden bijvoorbeeld alle geregistreerde traces weergegeven:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Voorbeeldlogboeken zoals weergegeven door Log Analytics:

| TimeGenerated | OperationName | Categorie | Niveau | CorrelationId | DurationMs | Eigenschappen |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | GedistribueerdTraceering | Informatief | 00-8cd869a412459a25f5f3131223344-0144d2590acd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticioTHubIngress | GedistribueerdTraceering | Informatief | 00-8cd869a412459a25f5f313131223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590acd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | GedistribueerdTraceering | Informatief | 00-8cd869a412459a25f5f313131223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590acd909"} |

Zie Diagnostische logboeken voor [Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview)voor meer informatie over de verschillende typen logboeken.

### <a name="application-map"></a>Toepassingskaart

Als u de stroom van IoT-berichten wilt visualiseren, stelt u de voorbeeld-app Toepassingskaart in. De voorbeeld-app stuurt de gedistribueerde traceringslogboeken naar [toepassingskaart](../application-insights/app-insights-app-map.md) met behulp van een Azure-functie en een gebeurtenishub.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Download het voorbeeld op GitHub</a>

Deze afbeelding hieronder toont gedistribueerde tracering in App Map met drie routeringseindpunten:

![IoT-gedistribueerde tracering in app-kaart](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Informatie over Azure IoT-gedistribueerde tracering

### <a name="context"></a>Context

Veel IoT-oplossingen, waaronder onze eigen [referentiearchitectuur](https://aka.ms/iotrefarchitecture) (alleen In het Engels), volgen over het algemeen een variant van de [microservice-architectuur.](https://docs.microsoft.com/azure/architecture/microservices/) Naarmate een IoT-oplossing complexer wordt, gebruikt u uiteindelijk een tiental of meer microservices. Deze microservices zijn mogelijk wel of niet afkomstig van Azure. Het kan een uitdaging worden om aan te geven waar IoT-berichten vallen of vertragen. U hebt bijvoorbeeld een IoT-oplossing die gebruik maakt van 5 verschillende Azure-services en 1500 actieve apparaten. Elk apparaat verzendt 10 device-to-cloud berichten per seconde (voor een totaal van 15.000 berichten per seconde), maar u merkt dat uw web-app slechts 10.000 berichten per seconde ziet. Waar is het probleem? Hoe vind je de dader?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Gedistribueerd traceringspatroon in microservicearchitectuur

Als u de stroom van een IoT-bericht over verschillende services wilt reconstrueren, moet elke service een *correlatie-ID* verspreiden die het bericht op unieke wijze identificeert. Eenmaal verzameld in een gecentraliseerd systeem, kunnen correlatie-id's u de berichtenstroom zien. Deze methode wordt het [gedistribueerde traceringspatroon](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing)genoemd.

Om een bredere adoptie voor gedistribueerde tracering te ondersteunen, draagt Microsoft bij aan het [W3C-standaardvoorstel voor gedistribueerde tracering.](https://w3c.github.io/trace-context/)

### <a name="iot-hub-support"></a>Ondersteuning voor IoT Hub

Eenmaal ingeschakeld, volgt de ondersteuning voor distributed tracing voor IoT Hub deze stroom:

1. Er wordt een bericht gegenereerd op het IoT-apparaat.
1. Het IoT-apparaat beslist (met hulp van de cloud) dat dit bericht moet worden toegewezen met een traceringscontext.
1. De SDK `tracestate` voegt een toe aan de eigenschap message application, die de tijdstempel voor het maken van berichten bevat.
1. Het IoT-apparaat stuurt het bericht naar IoT Hub.
1. Het bericht komt binnen bij de IoT hub gateway.
1. IoT Hub zoekt `tracestate` naar de eigenschappen van de berichttoepassing en controleert of deze in de juiste indeling is.
1. Als dat het zo is, `trace-id` genereert IoT `span-id` Hub een wereldwijd uniek voor het bericht, `DiagnosticIoTHubD2C`een voor de hop, en registreert deze in diagnostische logboeken van Azure Monitor onder de bewerking .
1. Zodra de verwerking van het bericht `span-id` is voltooid, genereert `trace-id` IoT `DiagnosticIoTHubIngress`Hub een andere en registreert deze samen met de bestaande onder de bewerking .
1. Als routering is ingeschakeld voor het bericht, schrijft IoT Hub `span-id` het naar `trace-id` het `DiagnosticIoTHubEgress`aangepaste eindpunt en registreert een andere met hetzelfde onder de categorie .
1. De bovenstaande stappen worden herhaald voor elk gegenereerd bericht.

## <a name="public-preview-limits-and-considerations"></a>Limieten en overwegingen voor openbare preview

- Voorstel voor W3C Trace Context standaard is momenteel een werkend ontwerp.
- Momenteel is de enige ontwikkelingstaal die wordt ondersteund door client SDK C.
- Cloud-to-device twin-mogelijkheden zijn niet beschikbaar voor [de basislaag van IoT Hub.](iot-hub-scaling.md#basic-and-standard-tiers) IoT Hub wordt echter nog steeds aangemeld bij Azure Monitor als er een goed samengestelde traceringscontextkop wordt weergegeven.
- Om een efficiënte werking te garanderen, zal IoT Hub een gaspedaal opleggen aan de mate van logboekregistratie die kan optreden als onderdeel van gedistribueerde tracering.

## <a name="next-steps"></a>Volgende stappen

- Zie [Microservice-architectuurpatroon: distributed tracing voor](https://microservices.io/patterns/observability/distributed-tracing.html)meer informatie over het algemene gedistribueerde traceringspatroon in microservices.
- Zie [IoT-apparaten op schaal configureren en bewaken](iot-hub-auto-device-config.md)als u de configuratie wilt instellen om gedistribueerde traceringsinstellingen op een groot aantal apparaten toe te passen.
- Zie Wat is Azure Monitor voor meer informatie over Azure [Monitor?](../azure-monitor/overview.md)
