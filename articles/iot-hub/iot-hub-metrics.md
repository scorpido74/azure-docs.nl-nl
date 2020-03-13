---
title: Metrische gegevens gebruiken voor het bewaken van Azure IoT Hub | Microsoft Docs
description: Hoe u Azure IoT Hub metrische gegevens kunt gebruiken om de algehele status van uw IoT-hubs te beoordelen en te controleren.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284432"
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
|d2c.telemetry.ingress.allProtocol|Verzend pogingen voor telemetrie-berichten|Count|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|None|
|d2c.telemetry.ingress.success|Verzonden telemetriegegevens|Count|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|None|
|C2D. commands. OUTuitgang. complete. geslaagd|C2D-bericht leveringen voltooid|Count|Totaal|Aantal bezorgingen van Cloud-naar-apparaat-berichten voltooid door het apparaat|None|
|c2d.commands.egress.abandon.success|C2D-berichten zijn afgebroken|Count|Totaal|Aantal Cloud-naar-apparaat-berichten die zijn afgebroken door het apparaat|None|
|c2d.commands.egress.reject.success|Geweigerde C2D-berichten|Count|Totaal|Aantal Cloud-naar-apparaat-berichten dat door het apparaat is geweigerd|None|
|C2DMessagesExpired|C2D-berichten verlopen (preview-versie)|Count|Totaal|Aantal verlopen Cloud-naar-apparaat-berichten|None|
|devices.totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|None|
|devices.connectedDevices.allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|None|
|d2c.telemetry.egress.success|Route ring: telemetrie-berichten|Count|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|None|
|d2c.telemetry.egress.dropped|Route ring: telemetrie-berichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|None|
|d2c.telemetry.egress.orphaned|Route ring: telemetriegegevens van zwevende berichten |Count|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |None|
|d2c.telemetry.egress.invalid|Route ring: telemetrie-berichten incompatibel|Count|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|None|
|d2c.telemetry.egress.fallback|Route ring: berichten worden bezorgd bij terugval|Count|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|None|
|d2c.endpoints.egress.eventHubs|Route ring: berichten worden bezorgd bij Event hub|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|None|
|d2c.endpoints.latency.eventHubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|None|
|d2c.endpoints.egress.serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|None|
|d2c.endpoints.latency.serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|None|
|d2c.endpoints.egress.serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|None|
|d2c.endpoints.latency.serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|None|
|D2C. endpoints. uitgangs punt. builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen).|None|
|d2c.endpoints.latency.builtIn.events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen).|None|
|d2c.endpoints.egress.storage|Route ring: berichten worden bezorgd bij de opslag|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|None|
|d2c.endpoints.latency.storage|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|None|
|D2C. endpoints. out. storage. bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|None|
|d2c.endpoints.egress.storage.blobs|Route ring: blobs die aan de opslag worden geleverd|Count|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|None|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Count|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer (https://aka.ms/ioteventgrid).|ResourceID<br/>Daardoor<br/>EventType|
|EventGridLatency|Event Grid latentie (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|ResourceID<br/>EventType|
|d2c.twin.read.success|Geslaagde dubbele Lees bewerkingen van apparaten|Count|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|None|
|D2C. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van apparaten|Count|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|None|
|d2c.twin.read.size|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|None|
|d2c.twin.update.success|Geslaagde dubbele updates van apparaten|Count|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|None|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Count|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|None|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|None|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Count|Totaal|Het aantal voltooide direct-methode aanroepen.|None|
|C2D. methods. failure|Mislukte directe aanroepen van methode|Count|Totaal|Het aantal mislukte direct-methode aanroepen.|None|
|C2D. methods. requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|None|
|C2D. methods. responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|None|
|c2d.twin.read.success|Geslaagde dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|None|
|c2d.twin.read.failure|Mislukte dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|None|
|c2d.twin.read.size|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|None|
|c2d.twin.update.success|Geslaagde dubbele updates van back-end|Count|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|None|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Count|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|None|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|None|
|twinQueries.success|Geslaagde dubbele query's|Count|Totaal|Het aantal geslaagde dubbele query's.|None|
|twinQueries.failure|Mislukte dubbele query's|Count|Totaal|Het aantal mislukte dubbele query's.|None|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|None|
|jobs.createTwinUpdateJob.success|Geslaagde creatie van dubbele update taken|Count|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|None|
|jobs.createTwinUpdateJob.failure|Kan geen dubbele update taken uitvoeren|Count|Totaal|Het aantal mislukte het maken van dubbele update taken.|None|
|jobs.createDirectMethodJob.success|Geslaagde creatie van methode aanroep taken|Count|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|None|
|jobs.createDirectMethodJob.failure|Kan geen aanroepen van methode aanroep taken uitvoeren|Count|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|None|
|jobs.listJobs.success|Geslaagde aanroepen naar lijst taken|Count|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|None|
|jobs.listJobs.failure|Mislukte aanroepen naar lijst taken|Count|Totaal|Het aantal mislukte aanroepen naar lijst taken.|None|
|jobs.cancelJob.success|Voltooide taak annuleringen|Count|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|None|
|jobs.cancelJob.failure|Mislukte taak annuleringen|Count|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|None|
|jobs.queryJobs.success|Geslaagde taak query's|Count|Totaal|Het aantal geslaagde aanroepen naar query taken.|None|
|jobs.queryJobs.failure|Mislukte taak query's|Count|Totaal|Het aantal mislukte aanroepen naar query taken.|None|
|Jobs. voltooid|Voltooide taken|Count|Totaal|Het aantal voltooide taken.|None|
|Jobs. mislukt|Mislukte taken|Count|Totaal|Het aantal mislukte taken.|None|
|d2c.telemetry.ingress.sendThrottle|Aantal beperkings fouten|Count|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|None|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Count|Average|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|None|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|None|
|deviceDataUsageV2|Totaal gebruik van apparaatgegevens (preview-versie)|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|None|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Count|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|None|
|connectedDeviceCount|Verbonden apparaten (preview-versie)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|None|
|configuraties|Metrische configuratie gegevens|Count|Totaal|Metrische gegevens voor configuratie bewerkingen|None|

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Hub metrische gegevens hebt weer gegeven, volgt u deze koppeling voor meer informatie over het beheren van Azure IoT Hub:

* [Diagnostische logboeken instellen](iot-hub-monitor-resource-health.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)

* [AI implementeren op edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
