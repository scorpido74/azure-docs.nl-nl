---
title: Inzicht in het Azure IoT Hub-identiteitsregister | Microsoft Documenten
description: Ontwikkelaarshandleiding - beschrijving van het IoT Hub-identiteitsregister en hoe u het gebruiken om uw apparaten te beheren. Bevat informatie over het importeren en exporteren van apparaatidentiteiten in bulk.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.openlocfilehash: ccb840caea5d28975daaf8cbf6f0d4985bdf006d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499142"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Inzicht in het identiteitsregister in uw IoT-hub

Elke IoT-hub heeft een identiteitsregister dat informatie opslaat over de apparaten en modules die zijn toegestaan om verbinding te maken met de IoT-hub. Voordat een apparaat of module verbinding kan maken met een IoT-hub, moet er een vermelding voor dat apparaat of module in het identiteitsregister van de IoT-hub staan. Een apparaat of module moet ook verifiëren met de IoT-hub op basis van referenties die zijn opgeslagen in het identiteitsregister.

Het apparaat of de module-id die in het identiteitsregister is opgeslagen, is hoofdlettergevoelig.

Op een hoog niveau is het identiteitsregister een REST-geschikte verzameling van apparaat- of moduleidentiteitsbronnen. Wanneer u een vermelding in het identiteitsregister toevoegt, maakt IoT Hub een set bronnen per apparaat, zoals de wachtrij met in-flight cloud-to-device-berichten.

Gebruik het identiteitsregister wanneer dat nodig is:

* Inrichten van apparaten of modules die verbinding maken met uw IoT-hub.
* Bedien toegang per apparaat/per module tot het apparaat of de modulegerichte eindpunten van uw hub.

> [!NOTE]
> * Het identiteitsregister bevat geen toepassingsspecifieke metagegevens.
> * Module identiteit en module twin is in openbare preview. Onderstaande functie wordt ondersteund op module-identiteit wanneer deze algemeen beschikbaar is.
>

## <a name="identity-registry-operations"></a>Registerbewerkingen voor identiteit

In het identiteitsregister van IoT Hub worden de volgende bewerkingen gepubliceerd:

* Apparaat- of module-identiteit maken
* Apparaat- of module-identiteit bijwerken
* Apparaat- of module-identiteit ophalen met id
* Apparaat- of module-identiteit verwijderen
* Lijst tot 1000 identiteiten
* Apparaatidentiteiten exporteren naar Azure blob-opslag
* Apparaatidentiteiten importeren uit Azure blob-opslag

Al deze bewerkingen kunnen gebruik maken van optimistische gelijktijdigheid, zoals gespecificeerd in [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> De enige manier om alle identiteiten in het identiteitsregister van een IoT-hub op te halen, is door de functie [Exporteren](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) te gebruiken.

Een IoT Hub-identiteitsregister:

* Bevat geen metagegevens van toepassingen.
* Kan worden benaderd als een woordenboek, met behulp van de **deviceId** of **moduleId** als de sleutel.
* Ondersteunt geen expressieve query's.

Een IoT-oplossing heeft doorgaans een aparte oplossingsspecifieke winkel met toepassingsspecifieke metagegevens. De oplossingsspecifieke winkel in een slimme bouwoplossing registreert bijvoorbeeld de ruimte waarin een temperatuursensor wordt ingezet.

> [!IMPORTANT]
> Gebruik het identiteitsregister alleen voor apparaatbeheer en inrichtingsbewerkingen. Hoge doorvoerbewerkingen bij uitvoering mogen niet afhankelijk zijn van het uitvoeren van bewerkingen in het identiteitsregister. Als u bijvoorbeeld de verbindingsstatus van een apparaat controleert voordat u een opdracht verzendt, wordt dit geen ondersteund patroon. Controleer de [beperkingspercentages](iot-hub-devguide-quotas-throttling.md) voor het identiteitsregister en het [heartbeatpatroon van](iot-hub-devguide-identity-registry.md#device-heartbeat) het apparaat.

## <a name="disable-devices"></a>Apparaten uitschakelen

U apparaten uitschakelen door de **statuseigenschap** van een identiteit in het identiteitsregister bij te werken. Normaal gesproken gebruikt u deze eigenschap in twee scenario's:

* Tijdens een inrichting van orkestratieproces. Zie [Apparaatinrichting](iot-hub-devguide-identity-registry.md#device-provisioning)voor meer informatie .

* Als u om welke reden dan ook denkt dat een apparaat is gecompromitteerd of ongeautoriseerd is geworden.

Deze functie is niet beschikbaar voor modules.

## <a name="import-and-export-device-identities"></a>Identiteiten van apparaten importeren en exporteren

Gebruik asynchrone bewerkingen op het eindpunt van de [IoT Hub-bronprovider](iot-hub-devguide-endpoints.md) om apparaatidentiteiten in bulk te exporteren vanuit het identiteitsregister van een IoT-hub. Exporten zijn langlopende taken die een door de klant geleverde blobcontainer gebruiken om apparaatidentiteitsgegevens op te slaan die uit het identiteitsregister worden gelezen.

Gebruik asynchrone bewerkingen op het eindpunt van de [IoT Hub-bronprovider](iot-hub-devguide-endpoints.md) om apparaatidentiteiten in bulk te importeren in het identiteitsregister van een IoT-hub. Importen zijn langlopende taken die gegevens in een door de klant geleverde blobcontainer gebruiken om apparaatidentiteitsgegevens in het identiteitsregister te schrijven.

Zie [REST API's van IoT Hub-resourceprovider voor](/rest/api/iothub/iothubresource)meer informatie over de API's voor import en export. Zie [Bulkbeheer van iot-hub-apparaatidentiteiten](iot-hub-bulk-identity-mgmt.md)voor meer informatie over het uitvoeren van import- en exporttaken.

Apparaatidentiteiten kunnen ook worden geëxporteerd en geïmporteerd vanuit een IoT Hub via de Service API via de [REST API](/rest/api/iothub/service/jobclient/createimportexportjob) of een van de IoT Hub [Service SDKs.](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks)

## <a name="device-provisioning"></a>Inrichting van apparaten

De apparaatgegevens die een bepaalde IoT-oplossing opslaat, zijn afhankelijk van de specifieke vereisten van die oplossing. Maar een oplossing moet minimaal apparaatidentiteiten en verificatiesleutels opslaan. Azure IoT Hub bevat een identiteitsregister waarmee waarden voor elk apparaat kunnen worden opgeslagen, zoals id's, verificatiesleutels en statuscodes. Een oplossing kan andere Azure-services gebruiken, zoals tabelopslag, blobopslag of Cosmos DB om extra apparaatgegevens op te slaan.

*Apparaatinrichting* is het proces van het toevoegen van de eerste apparaatgegevens aan de winkels in uw oplossing. Als u een nieuw apparaat wilt inschakelen om verbinding te maken met uw hub, moet u een apparaat-id en sleutels toevoegen aan het identiteitsregister van de IoT Hub. Als onderdeel van het inrichtingsproces moet u mogelijk apparaatspecifieke gegevens initialiseren in andere oplossingsarchieven. U de Azure IoT Hub Device Provisioning Service ook gebruiken om zero-touch, just-in-time provisioning voor een of meer IoT-hubs mogelijk te maken zonder menselijke tussenkomst. Zie voor meer informatie de documentatie van de [inrichtingsservice](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Apparaatheartbeat

Het IoT Hub-identiteitsregister bevat een veld dat **connectionState**wordt genoemd. Gebruik alleen het **veld connectionState** tijdens de ontwikkeling en het foutopsporing. IoT-oplossingen mogen het veld niet opvragen tijdens de looptijd. Vraag bijvoorbeeld niet naar het veld **verbindingStaat** om te controleren of een apparaat is verbonden voordat u een cloud-naar-apparaatbericht of een sms verzendt. We raden u aan zich te abonneren op de [ **verbinding met** ](iot-hub-event-grid.md#event-types) het apparaat op gebeurtenisgrid om meldingen te ontvangen en de status van de apparaatverbinding te controleren. Gebruik deze [zelfstudie](iot-hub-how-to-order-connection-state-events.md) voor meer informatie over het integreren van door apparaten verbonden en apparaatverbroken van IoT Hub in uw IoT-oplossing.

Als uw IoT-oplossing moet weten of een apparaat is aangesloten, u het *hartslagpatroon*implementeren.
In het heartbeatpatroon verzendt het apparaat ten minste eenmaal per vaste tijd berichten van apparaat naar cloud (bijvoorbeeld ten minste eenmaal per uur). Daarom, zelfs als een apparaat geen gegevens heeft om te verzenden, verzendt het nog steeds een leeg apparaat-naar-cloud-bericht (meestal met een eigenschap die het identificeert als een hartslag). Aan de servicezijde houdt de oplossing een kaart bij met de laatste hartslag die voor elk apparaat is ontvangen. Als de oplossing niet binnen de verwachte tijd van het apparaat een heartbeatbericht ontvangt, gaat deze ervan uit dat er een probleem is met het apparaat.

Een complexere implementatie kan de informatie van [Azure Monitor](../azure-monitor/index.yml) en Azure [Resource Health](../service-health/resource-health-overview.md) bevatten om apparaten te identificeren die proberen verbinding te maken of te communiceren, maar mislukken, controleer de controle Monitor [met diagnostische](iot-hub-monitor-resource-health.md) handleiding. Controleer bij het implementeren van het heartbeatpatroon [iot-hubquota en -gashendels](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Als een IoT-oplossing de verbindingsstatus alleen gebruikt om te bepalen of cloud-naar-apparaatberichten moeten worden verzonden en berichten niet worden uitgezonden naar grote sets apparaten, u overwegen het eenvoudigere *korte verlooptijdpatroon* te gebruiken. Dit patroon bereikt hetzelfde resultaat als het onderhouden van een register van de apparaatverbinding met behulp van het heartbeatpatroon, terwijl het efficiënter is. Als u berichtbevestigingen aanvraagt, kan IoT Hub u op de hoogte stellen welke apparaten berichten kunnen ontvangen en welke niet.

## <a name="device-and-module-lifecycle-notifications"></a>Meldingen over de levenscyclus van apparaten en modules

IoT Hub kan uw IoT-oplossing op de hoogte stellen wanneer een identiteit wordt gemaakt of verwijderd door levenscyclusmeldingen te verzenden. Hiervoor moet uw IoT-oplossing een route maken en de gegevensbron gelijk stellen aan *DeviceLifecycleEvents* of *ModuleLifecycleEvents.* Standaard worden er geen levenscyclusmeldingen verzonden, dat wil zeggen dat dergelijke routes niet bestaan. Het meldingsbericht bevat eigenschappen en hoofdtekst.

Eigenschappen: Eigenschappen van het berichtensysteem zijn vooraf gekoppeld aan het `$` symbool.

Meldingsbericht voor apparaat:

| Name | Waarde |
| --- | --- |
|$content type | application/json |
|$iothub in de wachtrij |  Het tijdstip waarop de melding is verzonden |
|$iothub-berichtbron | deviceLifecycleEvents |
|$content-codering | utf-8 |
|opType | **deviceidentity** of **deleteDeviceIdentity** maken |
|hubNaam | Naam van IoT-hub |
|deviceId | ID van het apparaat |
|operationTimestamp | ISO8601-tijdstempel van de werking |
|iothub-bericht-schema | deviceLifecycleNotification deviceLifecycleNotification deviceLifecycleNotification deviceLifecycle |

Hoofdtekst: Deze sectie is in JSON-formaat en vertegenwoordigt de tweeling van de gemaakte apparaatidentiteit. Bijvoorbeeld:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Meldingsbericht voor module:

| Name | Waarde |
| --- | --- |
$content type | application/json |
$iothub in de wachtrij |  Het tijdstip waarop de melding is verzonden |
$iothub-berichtbron | moduleLifecycleEvents |
$content-codering | utf-8 |
opType | **createModuleIdentity** of **deleteModuleIdentity** |
hubNaam | Naam van IoT-hub |
moduleId | ID van de module |
operationTimestamp | ISO8601-tijdstempel van de werking |
iothub-bericht-schema | moduleLifecycleNotification |

Hoofdtekst: Deze sectie is in JSON-formaat en vertegenwoordigt de tweeling van de gemaakte module-identiteit. Bijvoorbeeld:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Eigenschappen van apparaatidentiteit

Apparaatidentiteiten worden weergegeven als JSON-documenten met de volgende eigenschappen:

| Eigenschap | Opties | Beschrijving |
| --- | --- | --- |
| deviceId |vereist, alleen-lezen op updates |Een hoofdlettergevoelige tekenreeks (tot 128 tekens lang) van ASCII 7-bits `- . + % _ # * ? ! ( ) , = @ $ '`alfanumerieke tekens plus bepaalde speciale tekens: . |
| generationId |vereist, alleen-lezen |Een IoT-hub gegenereerde, casegevoelige string tot 128 tekens lang. Deze waarde wordt gebruikt om apparaten met dezelfde **deviceId**te onderscheiden, wanneer ze zijn verwijderd en opnieuw zijn gemaakt. |
| etag etag |vereist, alleen-lezen |Een tekenreeks die een zwakke ETag voor de identiteit van het apparaat vertegenwoordigt, volgens [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |optioneel |Een samengesteld object met verificatiegegevens en beveiligingsmaterialen. |
| auth.symkey |optioneel |Een samengesteld object met een primaire en een secundaire sleutel, opgeslagen in de base64-indeling. |
| status |vereist |Een toegangsindicator. Kan worden **ingeschakeld** of **uitgeschakeld**. Als **ingeschakeld,** is het apparaat toegestaan om verbinding te maken. Als dit apparaat **is uitgeschakeld,** heeft dit apparaat geen toegang tot een apparaatgericht eindpunt. |
| statusReden |optioneel |Een tekenreeks van 128 tekens die de reden voor de identiteitsstatus van het apparaat opslaat. Alle UTF-8 tekens zijn toegestaan. |
| statusUpdateTime |alleen-lezen |Een tijdelijke indicator met de datum en tijd van de laatste statusupdate. |
| verbindingStaat |alleen-lezen |Een veld dat de verbindingsstatus aangeeft: **Verbonden** of **losgekoppeld**. Dit veld vertegenwoordigt de IoT Hub-weergave van de status van de apparaatverbinding. **Belangrijk**: Dit veld mag alleen worden gebruikt voor ontwikkelings-/debuggingdoeleinden. De verbindingsstatus wordt alleen bijgewerkt voor apparaten die MQTT of AMQP gebruiken. Ook is het gebaseerd op pings op protocolniveau (MQTT-pings of AMQP-pings), en kan het een maximale vertraging van slechts 5 minuten hebben. Om deze redenen kunnen er fout-positieven zijn, zoals apparaten die zijn gerapporteerd als verbonden, maar die zijn losgekoppeld. |
| verbindingStateUpdatedTime |alleen-lezen |Een tijdelijke indicator met de datum en de laatste keer dat de verbindingsstatus is bijgewerkt. |
| laatsteActivityTime |alleen-lezen |Een tijdelijke indicator met de datum en de laatste keer dat het apparaat verbinding heeft gemaakt, ontvangen of verzonden. |

> [!NOTE]
> De verbindingsstatus kan alleen de IoT Hub-weergave van de status van de verbinding weergeven. Updates voor deze status kunnen worden vertraagd, afhankelijk van de netwerkomstandigheden en configuraties.

> [!NOTE]
> Momenteel ondersteunt de apparaat-SDKs `+` `#` geen de tekens en tekens in de **deviceId.**

## <a name="module-identity-properties"></a>Eigenschappen van moduleidentiteit

Moduleidentiteiten worden weergegeven als JSON-documenten met de volgende eigenschappen:

| Eigenschap | Opties | Beschrijving |
| --- | --- | --- |
| deviceId |vereist, alleen-lezen op updates |Een hoofdlettergevoelige tekenreeks (tot 128 tekens lang) van ASCII 7-bits `- . + % _ # * ? ! ( ) , = @ $ '`alfanumerieke tekens plus bepaalde speciale tekens: . |
| moduleId |vereist, alleen-lezen op updates |Een hoofdlettergevoelige tekenreeks (tot 128 tekens lang) van ASCII 7-bits `- . + % _ # * ? ! ( ) , = @ $ '`alfanumerieke tekens plus bepaalde speciale tekens: . |
| generationId |vereist, alleen-lezen |Een IoT-hub gegenereerde, casegevoelige string tot 128 tekens lang. Deze waarde wordt gebruikt om apparaten met dezelfde **deviceId**te onderscheiden, wanneer ze zijn verwijderd en opnieuw zijn gemaakt. |
| etag etag |vereist, alleen-lezen |Een tekenreeks die een zwakke ETag voor de identiteit van het apparaat vertegenwoordigt, volgens [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |optioneel |Een samengesteld object met verificatiegegevens en beveiligingsmaterialen. |
| auth.symkey |optioneel |Een samengesteld object met een primaire en een secundaire sleutel, opgeslagen in de base64-indeling. |
| status |vereist |Een toegangsindicator. Kan worden **ingeschakeld** of **uitgeschakeld**. Als **ingeschakeld,** is het apparaat toegestaan om verbinding te maken. Als dit apparaat **is uitgeschakeld,** heeft dit apparaat geen toegang tot een apparaatgericht eindpunt. |
| statusReden |optioneel |Een tekenreeks van 128 tekens die de reden voor de identiteitsstatus van het apparaat opslaat. Alle UTF-8 tekens zijn toegestaan. |
| statusUpdateTime |alleen-lezen |Een tijdelijke indicator met de datum en tijd van de laatste statusupdate. |
| verbindingStaat |alleen-lezen |Een veld dat de verbindingsstatus aangeeft: **Verbonden** of **losgekoppeld**. Dit veld vertegenwoordigt de IoT Hub-weergave van de status van de apparaatverbinding. **Belangrijk**: Dit veld mag alleen worden gebruikt voor ontwikkelings-/debuggingdoeleinden. De verbindingsstatus wordt alleen bijgewerkt voor apparaten die MQTT of AMQP gebruiken. Ook is het gebaseerd op pings op protocolniveau (MQTT-pings of AMQP-pings), en kan het een maximale vertraging van slechts 5 minuten hebben. Om deze redenen kunnen er fout-positieven zijn, zoals apparaten die zijn gerapporteerd als verbonden, maar die zijn losgekoppeld. |
| verbindingStateUpdatedTime |alleen-lezen |Een tijdelijke indicator met de datum en de laatste keer dat de verbindingsstatus is bijgewerkt. |
| laatsteActivityTime |alleen-lezen |Een tijdelijke indicator met de datum en de laatste keer dat het apparaat verbinding heeft gemaakt, ontvangen of verzonden. |

> [!NOTE]
> Momenteel ondersteunt de apparaat-SDKs `+` `#` geen de tekens en tekens in de **deviceId** en **moduleId.**

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere referentieonderwerpen in de IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eindpunten die elke IoT-hub blootlegt voor run-time- en beheerbewerkingen.

* [Beperking en quota](iot-hub-devguide-quotas-throttling.md) beschrijven de quota en beperkingsgedragingen die van toepassing zijn op de IoT Hub-service.

* [Azure IoT-apparaat en service-SDK's](iot-hub-devguide-sdks.md) bevat de verschillende taal-SDK's die u gebruiken wanneer u zowel apparaat- als service-apps ontwikkelt die met IoT Hub werken.

* [IoT Hub-querytaal](iot-hub-devguide-query-language.md) beschrijft de querytaal die u gebruiken om informatie op te halen uit IoT Hub over uw apparaattweeling en taken.

* [IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het Identiteitsregister van De IoT Hub gebruiken, bent u mogelijk geïnteresseerd in de volgende onderwerpen voor IoT Hub-ontwikkelaars:

* [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

* [Apparaattweelingen gebruiken om status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)

* [Een directe methode op een apparaat aanroepen](iot-hub-devguide-direct-methods.md)

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Zie de volgende IoT Hub-zelfstudie om een aantal concepten uit te proberen die in dit artikel worden beschreven:

* [Aan de slag met Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Zie: 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)
