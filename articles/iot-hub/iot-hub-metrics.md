---
title: Metrische gegevens gebruiken voor het bewaken van Azure IoT Hub | Microsoft Docs
description: Hoe u Azure IoT Hub metrische gegevens kunt gebruiken om de algehele status van uw IoT-hubs te beoordelen en te controleren.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 20a1ec4b8eb88f5914ce6b27acc8a472e58f5d29
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457094"
---
# <a name="understand-iot-hub-metrics"></a>IoT Hub metrische gegevens begrijpen

IoT Hub metrische gegevens geven u meer informatie over de status van de Azure IoT-resources in uw Azure-abonnement. Met IoT Hub metrische gegevens kunt u de algemene status van de IoT Hub-service en de aangesloten apparaten beoordelen. Gebruikers gerichte statistieken zijn belang rijk omdat ze u helpen te zien wat er gebeurt met uw IoT-hub en problemen met de hoofdmap kunnen veroorzaken zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Metrische gegevens zijn standaard ingeschakeld. U kunt IoT Hub metrische gegevens van de Azure Portal weer geven.

> [!NOTE]
> U kunt IoT Hub metrische gegevens gebruiken om informatie weer te geven over IoT Plug en Play-apparaten die zijn verbonden met uw IoT Hub. IoT Plug en Play-apparaten maken deel uit van de [IOT-Plug en Play open bare preview](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub metrische gegevens weer geven

1. Maak een IoT-hub. U vindt instructies over het maken van een IoT-hub in de [telemetrie van een apparaat verzenden naar IOT hub](quickstart-send-telemetry-dotnet.md) Guide.

2. Open de Blade van uw IoT-hub. Klik vervolgens op **metrische gegevens**.
   
    ![Scherm afbeelding waarin de optie metrische gegevens zich op IoT Hub resource pagina bevindt](./media/iot-hub-metrics/enable-metrics-1.png)

3. Op de Blade metrische gegevens kunt u de metrische gegevens voor uw IoT-hub bekijken en aangepaste weer gaven van uw metrische gegevens maken. 
   
    ![Scherm afbeelding met de pagina metrische gegevens voor IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. U kunt ervoor kiezen om uw metrische gegevens te verzenden naar een Event Hubs-eind punt of een Azure Storage-account door te klikken op **Diagnostische instellingen**en vervolgens **Diagnostische instelling toevoegen**

   ![Scherm opname van de knop met Diagnostische instellingen](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub metrische gegevens en hoe u deze kunt gebruiken

IoT Hub biedt diverse metrische gegevens om u een overzicht te geven van de status van uw hub en het totale aantal verbonden apparaten. U kunt informatie uit meerdere metrische gegevens combi neren om een grotere afbeelding van de status van de IoT-hub te tekenen. In de volgende tabel worden de metrische gegevens voor elke IoT-hub beschreven en wordt uitgelegd hoe elke metriek de algehele status van de IoT-hub heeft.

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Verzend pogingen voor telemetrie-berichten|Aantal|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Geen|
|d2c.telemetry.ingress.success|Berichten over telemetrie verzonden|Aantal|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Geen|
|C2D. commands. OUTuitgang. complete. geslaagd|C2D-bericht leveringen voltooid|Aantal|Totaal|Aantal bezorgingen van Cloud-naar-apparaat-berichten voltooid door het apparaat|Geen|
|c2d.commands.egress.abandon.success|C2D-berichten zijn afgebroken|Aantal|Totaal|Aantal Cloud-naar-apparaat-berichten die zijn afgebroken door het apparaat|Geen|
|c2d.commands.egress.reject.success|Geweigerde C2D-berichten|Aantal|Totaal|Aantal Cloud-naar-apparaat-berichten dat door het apparaat is geweigerd|Geen|
|C2DMessagesExpired|C2D-berichten verlopen (preview-versie)|Aantal|Totaal|Aantal verlopen Cloud-naar-apparaat-berichten|Geen|
|devices.totalDevices|Alle apparaten (afgeschaft)|Aantal|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|devices.connectedDevices.allProtocol|Verbonden apparaten (afgeschaft) |Aantal|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|d2c.telemetry.egress.success|Route ring: telemetrie-berichten|Aantal|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen|
|d2c.telemetry.egress.dropped|Route ring: telemetrie-berichten verwijderd |Aantal|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Geen|
|d2c.telemetry.egress.orphaned|Route ring: telemetriegegevens van zwevende berichten |Aantal|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |Geen|
|d2c.telemetry.egress.invalid|Route ring: telemetrie-berichten incompatibel|Aantal|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|Geen|
|d2c.telemetry.egress.fallback|Route ring: berichten worden bezorgd bij terugval|Aantal|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Geen|
|d2c.endpoints.egress.eventHubs|Route ring: berichten worden bezorgd bij Event hub|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|Geen|
|d2c.endpoints.latency.eventHubs|Routering: berichtlatentie voor Event Hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|Geen|
|d2c.endpoints.egress.serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Geen|
|d2c.endpoints.latency.serviceBusQueues|Routering: berichtlatentie voor Service Bus-wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|Geen|
|d2c.endpoints.egress.serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Geen|
|d2c.endpoints.latency.serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|Geen|
|D2C. endpoints. uitgangs punt. builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen).|Geen|
|d2c.endpoints.latency.builtIn.events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen).|Geen|
|d2c.endpoints.egress.storage|Route ring: berichten worden bezorgd bij de opslag|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Geen|
|d2c.endpoints.latency.storage|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|Geen|
|D2C. endpoints. out. storage. bytes|Routering: aan opslag geleverde gegevens|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Geen|
|d2c.endpoints.egress.storage.blobs|Routering: aan opslag geleverde blobs|Aantal|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Geen|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Aantal|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer (https://aka.ms/ioteventgrid).|ResourceID<br/>Daardoor<br/>EventType|
|EventGridLatency|Event Grid latentie (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|ResourceID<br/>EventType|
|d2c.twin.read.success|Geslaagde dubbele Lees bewerkingen van apparaten|Aantal|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Geen|
|D2C. dubbele. Read. failure|Mislukte dubbele leesbewerkingen van apparaten|Aantal|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Geen|
|d2c.twin.read.size|Antwoordgrootte van dubbele leesbewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|Geen|
|d2c.twin.update.success|Geslaagde dubbele updates van apparaten|Aantal|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Aantal|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Geen|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Aantal|Totaal|Het aantal voltooide direct-methode aanroepen.|Geen|
|C2D. methods. failure|Mislukte aanroepen van de directe methode|Aantal|Totaal|Het aantal mislukte direct-methode aanroepen.|Geen|
|C2D. methods. requestSize|Aanvraaggrootte van aanroepen van de directe methode|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|Geen|
|C2D. methods. responseSize|Antwoordgrootte van aanroepen van de directe methode|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|Geen|
|c2d.twin.read.success|Geslaagde dubbele Lees bewerkingen van back-end|Aantal|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|c2d.twin.read.failure|Mislukte dubbele leesbewerkingen van back-end|Aantal|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|c2d.twin.read.size|Antwoordgrootte van dubbele leesbewerkingen van back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|c2d.twin.update.success|Geslaagde dubbele updates van back-end|Aantal|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Geen|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Aantal|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Geen|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|Geen|
|twinQueries.success|Geslaagde dubbele query's|Aantal|Totaal|Het aantal geslaagde dubbele query's.|Geen|
|twinQueries.failure|Mislukte dubbele query's|Aantal|Totaal|Het aantal mislukte dubbele query's.|Geen|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|Geen|
|jobs.createTwinUpdateJob.success|Geslaagde creatie van dubbele update taken|Aantal|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Geen|
|jobs.createTwinUpdateJob.failure|Mislukte bewerkingen voor het maken van dubbele taken voor bijwerken|Aantal|Totaal|Het aantal mislukte het maken van dubbele update taken.|Geen|
|jobs.createDirectMethodJob.success|Geslaagde creatie van methode aanroep taken|Aantal|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Geen|
|jobs.createDirectMethodJob.failure|Mislukte bewerkingen voor het maken van taken voor het aanroepen van methode|Aantal|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Geen|
|jobs.listJobs.success|Geslaagde aanroepen naar lijst taken|Aantal|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Geen|
|jobs.listJobs.failure|Mislukte aanroepen naar taken op de lijst|Aantal|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Geen|
|jobs.cancelJob.success|Voltooide taak annuleringen|Aantal|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen|
|jobs.cancelJob.failure|Mislukte taakannuleringen|Aantal|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen|
|jobs.queryJobs.success|Geslaagde taak query's|Aantal|Totaal|Het aantal geslaagde aanroepen naar query taken.|Geen|
|jobs.queryJobs.failure|Mislukte taakquery's|Aantal|Totaal|Het aantal mislukte aanroepen naar query taken.|Geen|
|Jobs. voltooid|Voltooide taken|Aantal|Totaal|Het aantal voltooide taken.|Geen|
|Jobs. mislukt|Mislukte taken|Aantal|Totaal|Het aantal mislukte taken.|Geen|
|d2c.telemetry.ingress.sendThrottle|Aantal netwerkbeperkingsfouten|Aantal|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Geen|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Aantal|Average|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|Geen|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|deviceDataUsageV2|Totaal gebruik van apparaatgegevens (preview-versie)|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Aantal|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|connectedDeviceCount|Verbonden apparaten (preview)|Aantal|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|configuraties|Metrische gegevens van configuratie|Aantal|Totaal|Metrische gegevens voor configuratie bewerkingen|Geen|

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Hub metrische gegevens hebt weer gegeven, volgt u deze koppeling voor meer informatie over het beheren van Azure IoT Hub:

* [Controle van bewerkingen](iot-hub-operations-monitoring.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)

* [AI implementeren op edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
