---
title: Metrische gegevens gebruiken voor het bewaken van Azure IoT Hub | Microsoft Docs
description: Hoe u Azure IoT Hub metrische gegevens kunt gebruiken om de algehele status van uw IoT-hubs te beoordelen en te controleren.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 808320f89c4dbeca835fc5a710ea1566199f6884
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791840"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>IoT Hub metrische gegevens begrijpen

IoT Hub metrische gegevens geven u informatie over de status van de Azure IoT-resources in uw Azure-abonnement. Met IoT Hub metrische gegevens kunt u de algemene status van de IoT Hub-service en de aangesloten apparaten beoordelen. Aan de hand van deze gebruikers gerichte statistieken kunt u zien wat er gebeurt met uw IoT-hub en kunt u de hoofd oorzaak van problemen analyseren zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Metrische gegevens zijn standaard ingeschakeld. U kunt IoT Hub metrische gegevens van de Azure Portal weer geven.

> [!NOTE]
> U kunt IoT Hub metrische gegevens gebruiken om informatie weer te geven over IoT Plug en Play-apparaten die zijn verbonden met uw IoT Hub. IoT Plug en Play-apparaten maken deel uit van de [IOT-Plug en Play open bare preview](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub metrische gegevens weer geven

1. Maak een IoT-hub. U vindt instructies over het maken van een IoT-hub in de [telemetrie van een apparaat verzenden naar IOT hub](quickstart-send-telemetry-dotnet.md) Guide.

2. Open de Blade van uw IoT-hub. Klik vervolgens op **metrische gegevens**.
   
    ![Scherm afbeelding waarin de optie metrische gegevens zich op IoT Hub resource pagina bevindt](./media/iot-hub-metrics/enable-metrics-1.png)

3. Op de Blade metrische gegevens kunt u de metrische gegevens voor uw IoT-hub bekijken en aangepaste weer gaven van uw metrische gegevens maken. 
   
    ![Scherm afbeelding met de pagina metrische gegevens voor IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. U kunt ervoor kiezen om uw metrische gegevens te verzenden naar een Event Hubs-eind punt of een Azure Storage-account door te klikken op **Diagnostische instellingen**en vervolgens **Diagnostische instelling toevoegen**.

   ![Scherm opname van de knop met Diagnostische instellingen](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metrische gegevens en hoe u deze kunt gebruiken

IoT Hub biedt diverse metrische gegevens om u een overzicht te geven van de status van uw hub en het totale aantal verbonden apparaten. U kunt informatie uit meerdere metrische gegevens combi neren om een grotere afbeelding van de status van de IoT-hub te tekenen. In de volgende tabel worden de metrische gegevens voor elke IoT-hub beschreven en wordt uitgelegd hoe elke metriek de algehele status van de IoT-hub heeft.

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|RoutingDeliveries | Bezorgings pogingen routeren (preview) | Count | Totaal |Dit is de metrische gegevens van de route ring. Gebruik de dimensies om de leverings status voor een bepaald eind punt of voor een specifieke routerings bron te identificeren.| ResourceID<br>Daardoor<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*Meer informatie over dimensies [**vindt u hier**](#dimensions)*. |
|RoutingDeliveryLatency| Routerings latentie (preview-versie) | Milliseconden | Average |Dit is de metrische gegevens van de bezorgings latentie van de route ring. Gebruik de dimensies om de latentie voor een bepaald eind punt of voor een specifieke routerings bron te identificeren.| ResourceID<br>RoutingSource,<br>EndpointType,<br>EndpointName<br>*Meer informatie over dimensies [**vindt u hier**](#dimensions)*.|
|RoutingDataSizeInBytesDelivered| Grootte van bezorgings gegevens in bytes (preview-versie)| Bytes | Totaal |Het totale aantal bytes dat wordt gerouteerd door IoT Hub naar het aangepaste eind punt en het ingebouwde eind punt. Gebruik de dimensies om gegevens grootte te identificeren die naar een bepaald eind punt of voor een specifieke routerings bron worden doorgestuurd.| ResourceID<br>RoutingSource,<br>EndpointType<br>EndpointName<br>*Meer informatie over dimensies [**vindt u hier**](#dimensions)*.|
|D2C. telemetrie.<br>allProtocol|Verzend pogingen voor telemetrie-berichten|Count|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Geen|
|D2C. telemetrie.<br>voltooid|Verzonden telemetriegegevens|Count|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Geen|
|C2D. commands. OUTuitgang.<br>voltooid. geslaagd|C2D-bericht leveringen voltooid|Count|Totaal|Aantal bezorgingen van Cloud-naar-apparaat-berichten voltooid door het apparaat|Geen|
|C2D. commands. OUTuitgang.<br>abandon. geslaagd|C2D-berichten zijn afgebroken|Count|Totaal|Aantal Cloud-naar-apparaat-berichten die zijn afgebroken door het apparaat|Geen|
|C2D. commands. OUTuitgang.<br>afwijzen. geslaagd|Geweigerde C2D-berichten|Count|Totaal|Aantal Cloud-naar-apparaat-berichten dat door het apparaat is geweigerd|Geen|
|C2DMessagesExpired|C2D-berichten verlopen (preview-versie)|Count|Totaal|Aantal verlopen Cloud-naar-apparaat-berichten|Geen|
|apparaten. totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|apparaten. connectedDevices.<br>allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|D2C. telemetrie. OUTuitgang.<br>voltooid|Route ring: telemetrie-berichten|Count|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen|
|D2C. telemetrie. OUTuitgang.<br>minder|Route ring: telemetrie-berichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Geen|
|D2C. telemetrie. OUTuitgang.<br>zwevende|Route ring: telemetriegegevens van zwevende berichten |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring omdat deze niet overeenkomen met een routerings query wanneer terugval route is uitgeschakeld.|Geen|
|D2C. telemetrie. OUTuitgang.<br>Ongeldig|Route ring: telemetrie-berichten incompatibel|Count|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Een bericht is niet compatibel met een eind punt wanneer IOT hub het bericht aan een eind punt probeert af te leveren en mislukt met een niet-tijdelijke fout. Ongeldige berichten worden niet opnieuw geprobeerd. Deze waarde omvat geen nieuwe pogingen.|Geen|
|D2C. telemetrie. OUTuitgang.<br>terugval|Route ring: berichten worden bezorgd bij terugval|Count|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Geen|
|D2C. endpoints. OUTuitgang.<br>Event hubs|Route ring: berichten worden bezorgd bij Event hub|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan aangepaste eind punten van het type Event hub. Dit omvat geen berichten routes naar het ingebouwde eind punt (gebeurtenissen).|Geen|
|D2C. endpoints. latentie.<br>Event hubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen bericht binnenkomend naar IoT Hub en het bericht wordt binnengebracht in aangepaste eind punten van het type Event hub. Dit omvat geen berichten routes naar het ingebouwde eind punt (gebeurtenissen).|Geen|
|D2C. endpoints. OUTuitgang.<br>serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Geen|
|D2C. endpoints. latentie.<br>serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een Service Bus wachtrij-eind punt.|Geen|
|D2C. endpoints. OUTuitgang.<br>serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Geen|
|D2C. endpoints. latentie.<br>serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een eind punt van een Service Bus onderwerp.|Geen|
|D2C. endpoints. OUTuitgang.<br>builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen) en terugval route.|Geen|
|D2C. endpoints. latentie.<br>builtIn. Events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in het ingebouwde eind punt (berichten/gebeurtenissen) en de terugval route.|Geen|
|D2C. endpoints. OUTuitgang.<br>opslag|Route ring: berichten worden bezorgd bij de opslag|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Geen|
|D2C. endpoints. latentie.<br>opslag|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een opslag eindpunt.|Geen|
|D2C. endpoints. OUTuitgang.<br>opslag. bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Geen|
|D2C. endpoints. OUTuitgang.<br>opslag. blobs|Route ring: blobs die aan de opslag worden geleverd|Count|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Geen|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Count|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer ( https://aka.ms/ioteventgrid) .|ResourceID<br/>Daardoor<br/>EventType|
|EventGridLatency|Event Grid latentie (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|ResourceID<br/>EventType|
|D2C. dubbele. lezen. geslaagd|Geslaagde dubbele Lees bewerkingen van apparaten|Count|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Geen|
|D2C. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van apparaten|Count|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Geen|
|D2C. dubbele. Lees. grootte|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het aantal gestarte dubbele Lees bewerkingen voor alle geslaagde apparaten.|Geen|
|D2C. dubbele. update. geslaagd|Geslaagde dubbele updates van apparaten|Count|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Count|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Geen|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|De totale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Count|Totaal|Het aantal voltooide direct-methode aanroepen.|Geen|
|C2D. methods. failure|Mislukte directe aanroepen van methode|Count|Totaal|Het aantal mislukte direct-methode aanroepen.|Geen|
|C2D. methods. requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het aantal voltooide direct-methode aanvragen.|Geen|
|C2D. methods. responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het aantal voltooide direct-methode reacties.|Geen|
|C2D. dubbele. lezen. geslaagd|Geslaagde dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|C2D. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|C2D. dubbele. Lees. grootte|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|C2D. dubbele. update. geslaagd|Geslaagde dubbele updates van back-end|Count|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Geen|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Count|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Geen|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|De totale grootte van alle geslaagde back-end-geïnitieerde twee-updates.|Geen|
|twinQueries. geslaagd|Geslaagde dubbele query's|Count|Totaal|Het aantal geslaagde dubbele query's.|Geen|
|twinQueries. failure|Mislukte dubbele query's|Count|Totaal|Het aantal mislukte dubbele query's.|Geen|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het totaal van de resultaat grootte van alle geslaagde dubbele query's.|Geen|
|Jobs. createTwinUpdateJob.<br>voltooid|Geslaagde creatie van dubbele update taken|Count|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Geen|
|Jobs. createTwinUpdateJob.<br>veroorzaakt|Kan geen dubbele update taken uitvoeren|Count|Totaal|Het aantal mislukte het maken van dubbele update taken.|Geen|
|Jobs. createDirectMethodJob.<br>voltooid|Geslaagde creatie van methode aanroep taken|Count|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Geen|
|Jobs. createDirectMethodJob.<br>veroorzaakt|Kan geen aanroepen van methode aanroep taken uitvoeren|Count|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Geen|
|Jobs. listJobs. geslaagd|Geslaagde aanroepen naar lijst taken|Count|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Geen|
|Jobs. listJobs. failure|Mislukte aanroepen naar lijst taken|Count|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Geen|
|Jobs. cancelJob. geslaagd|Voltooide taak annuleringen|Count|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen|
|Jobs. cancelJob. failure|Mislukte taak annuleringen|Count|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen|
|Jobs. queryJobs. geslaagd|Geslaagde taak query's|Count|Totaal|Het aantal geslaagde aanroepen naar query taken.|Geen|
|Jobs. queryJobs. failure|Mislukte taak query's|Count|Totaal|Het aantal mislukte aanroepen naar query taken.|Geen|
|Jobs. voltooid|Voltooide taken|Count|Totaal|Het aantal voltooide taken.|Geen|
|Jobs. mislukt|Mislukte taken|Count|Totaal|Het aantal mislukte taken.|Geen|
|D2C. telemetrie.<br>sendThrottle|Aantal beperkings fouten|Count|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Geen|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Count|Average|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|Geen|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|deviceDataUsageV2|Totaal gebruik van apparaatgegevens (preview-versie)|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Count|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|connectedDeviceCount|Verbonden apparaten (preview-versie)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|configuraties|Metrische configuratie gegevens|Count|Totaal|Het totale aantal ruwe verwerkingen dat is uitgevoerd voor apparaatconfiguratie en IoT Edge implementatie op een set doel apparaten. Dit omvat ook het aantal bewerkingen dat het apparaat dubbel of de module heeft gewijzigd vanwege deze configuraties.|Geen|

### <a name="dimensions"></a>Dimensies
Met dimensies kunt u meer informatie over de metrische gegevens vaststellen. Sommige para meters voor route ring bieden informatie per eind punt. De volgende tabel bevat mogelijke waarden voor deze dimensies.

|Dimensie|Waarden|
|---|---|
|ResourceID|uw IoT Hub-resource|
|Resultaat|voltooid<br>veroorzaakt|
|RoutingSource|Apparaat-berichten<br>Dubbele wijzigings gebeurtenissen<br>Levenscyclus gebeurtenissen van apparaat|
|EndpointType|Event hubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>ingebouwd<br>blobStorage|
|FailureReasonCategory|Ongeldig<br>minder<br>zwevende<br>null|
|EndpointName|naam van eind punt|

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Hub metrische gegevens hebt weer gegeven, volgt u deze koppeling voor meer informatie over het beheren van Azure IoT Hub:

* [Diagnostische logboeken instellen](iot-hub-monitor-resource-health.md)

* [Meer informatie over het oplossen van problemen met bericht routering](troubleshoot-message-routing.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
