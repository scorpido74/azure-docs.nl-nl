---
title: Met metrische gegevens gebruiken om Azure IoT Hub te controleren | Microsoft Documenten
description: Azure IoT Hub-statistieken gebruiken om de algehele status van uw IoT-hubs te beoordelen en te controleren.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284432"
---
# <a name="understand-iot-hub-metrics"></a>Informatie over IoT Hub-statistieken

IoT Hub-statistieken geven u betere gegevens over de status van de Azure IoT-resources in uw Azure-abonnement. Met IoT Hub-statistieken u de algehele status van de IoT Hub-service en de apparaten die erop zijn aangesloten, beoordelen. Gebruikersgerichte statistieken zijn belangrijk omdat ze u helpen te zien wat er aan de hand is met uw IoT-hub en problemen met het veroorzaken van problemen veroorzaken zonder dat u contact hoeft op te nemen met Azure-ondersteuning.

Statistieken zijn standaard ingeschakeld. U IoT Hub-statistieken bekijken vanuit de Azure-portal.

> [!NOTE]
> U IoT Hub-statistieken gebruiken om informatie weer te geven over IoT Plug and Play-apparaten die zijn verbonden met uw IoT-hub. IoT Plug and Play-apparaten maken deel uit van de openbare preview van [IoT Plug and Play.](../iot-pnp/overview-iot-plug-and-play.md)

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub-statistieken weergeven

1. Maak een IoT-hub. U instructies vinden voor het maken van een IoT-hub in de [telemetrie verzenden van een apparaat naar IoT Hub-handleiding.](quickstart-send-telemetry-dotnet.md)

2. Open het blad van uw IoT-hub. Klik van daaruit op **Statistieken**.
   
    ![Schermafbeelding van de optie metrische gegevens op de pagina IoT Hub-bron](./media/iot-hub-metrics/enable-metrics-1.png)

3. Vanuit het metrische blad u de statistieken voor uw IoT-hub bekijken en aangepaste weergaven van uw statistieken maken. 
   
    ![Schermafbeelding van de pagina met statistieken voor IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. U ervoor kiezen om uw metrische gegevens naar een eindpunt van gebeurtenishubs of een Azure Storage-account te verzenden door op **Diagnostische instellingen**te klikken en vervolgens diagnostische instelling toe **te voegen**

   ![Schermafbeelding van de knop Diagnostische instellingen](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub-statistieken en hoe deze te gebruiken

IoT Hub biedt verschillende statistieken om u een overzicht te geven van de status van uw hub en het totale aantal verbonden apparaten. U informatie uit meerdere statistieken combineren om een groter beeld te geven van de status van de IoT-hub. In de volgende tabel worden de statistieken beschreven die elke IoT-hub bijhoudt en hoe elke statistiek zich verhoudt tot de algemene status van de IoT-hub.

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|d2c.telemetrie.ingress.allProtocol|Telemetriebericht verzendt pogingen|Count|Totaal|Aantal telemetrieberichten van apparaat tot cloud die naar uw IoT-hub zijn verzonden|Geen|
|d2c.telemetrie.ingress.succes|Verzonden telemetrieberichten|Count|Totaal|Aantal telemetrieberichten van apparaat tot cloud die naar uw IoT-hub zijn verzonden|Geen|
|c2d.commands.egress.complete.success|C2D-berichtleveringen voltooid|Count|Totaal|Aantal leveringen van berichten in de cloud naar het apparaat die door het apparaat zijn voltooid|Geen|
|c2d.commands.egress.abandon.success|C2D-berichten verlaten|Count|Totaal|Aantal cloud-to-device-berichten dat door het apparaat wordt opgegeven|Geen|
|c2d.commands.egress.reject.success|C2D-berichten afgewezen|Count|Totaal|Aantal cloud-to-device-berichten dat door het apparaat wordt geweigerd|Geen|
|C2D-berichten verlopen|Verlopen C2D-berichten (voorbeeld)|Count|Totaal|Aantal verlopen cloud-to-device-berichten|Geen|
|devices.totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd op uw IoT-hub|Geen|
|devices.connectedDevices.allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|d2c.telemetry.egress.success|Routering: telemetrieberichten geleverd|Count|Totaal|Het aantal keren dat berichten met succes naar alle eindpunten zijn geleverd met behulp van IoT Hub-routering. Als een bericht naar meerdere eindpunten wordt doorgestuurd, neemt deze waarde met één toe voor elke succesvolle levering. Als een bericht meerdere keren op hetzelfde eindpunt wordt bezorgd, wordt deze waarde voor elke succesvolle levering met één verhoogd.|Geen|
|d2c.telemetry.egress.dropped|Routering: telemetrieberichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub-routering vanwege doodlopende eindpunten. Deze waarde telt geen berichten die worden afgeleverd op fallback-route, omdat gedropte berichten daar niet worden bezorgd.|Geen|
|d2c.telemetry.egress.orphaned|Routering: telemetrieberichten verweesd |Count|Totaal|Het aantal keren dat berichten zijn verweesd door de routering van IoT-hub omdat ze niet overeenkomen met routeringsregels (inclusief de terugvalregel). |Geen|
|d2c.telemetry.egress.invalid|Routering: telemetrieberichten onverenigbaar|Count|Totaal|Het aantal keren dat IoT Hub-routering geen berichten kan verzenden vanwege een onverenigbaarheid met het eindpunt. Deze waarde omvat geen nieuwe pogingen.|Geen|
|d2c.telemetry.egress.fallback|Routering: berichten geleverd aan fallback|Count|Totaal|Het aantal keren dat IoT Hub-routering berichten heeft afgeleverd naar het eindpunt dat is gekoppeld aan de terugvalroute.|Geen|
|d2c.endpoints.egress.eventHubs|Routering: berichten geleverd aan Event Hub|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd aan eindpunten van Event Hub.|Geen|
|d2c.endpoints.latency.eventHubs|Routering: berichtlatentie voor gebeurtenishub|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen het binnendringen van berichten naar IoT Hub en het binnendringen van berichten in een eindpunt van Event Hub.|Geen|
|d2c.endpoints.egress.serviceBusQueues|Routering: berichten die worden bezorgd in de wachtrij voor servicebussen|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd bij eindpunten van wachtrijeindpunten van servicebus.|Geen|
|d2c.endpoints.latency.serviceBusQueues|Routering: berichtlatentie voor servicebuswachtrij|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen het binnendringen van berichten naar IoT Hub en telemetriebericht is in een eindpunt van de servicebuswachtrij.|Geen|
|d2c.endpoints.egress.serviceBusTopics|Routering: berichten geleverd aan Service Bus Topic|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd bij eindpunten van onderwerpen in het onderwerp ServiceBus.|Geen|
|d2c.endpoints.latency.serviceBusTopics|Routering: berichtlatentie voor servicebusonderwerp|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen berichtbinnendringen naar IoT Hub en telemetriebericht binnenvallen in een eindpunt van een servicebusonderwerp.|Geen|
|d2c.endpoints.egress.builtIn.events|Routering: berichten die worden bezorgd aan berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd bij het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen|
|d2c.endpoints.latency.builtIn.events|Routering: berichtlatentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen berichtbinnendringen naar IoT Hub en telemetriebericht binnenvallen in het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen|
|d2c.endpoints.egress.storage|Routering: berichten die in de opslag worden bezorgd|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten naar opslageindpunten heeft geleverd.|Geen|
|d2c.endpoints.latency.storage|Routering: berichtlatentie voor opslag|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen berichtbinnendringen naar IoT Hub en telemetrie bericht binnendringen in een opslag eindpunt.|Geen|
|d2c.endpoints.egress.storage.bytes|Routering: gegevens die aan opslag worden geleverd|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub-routering die wordt geleverd aan opslageindpunten.|Geen|
|d2c.endpoints.egress.storage.blobs|Routering: blobs geleverd aan opslag|Count|Totaal|Het aantal keren dat IoT Hub-routering blobs naar opslageindpunten heeft geleverd.|Geen|
|EventGridDeliveries EventGridDeliveries EventGridDeliveries EventGrid|Gebeurtenisgridleveringen(preview)|Count|Totaal|Het aantal IoT Hub-gebeurtenissen dat is gepubliceerd in Event Grid. Gebruik de dimensie Resultaat voor het aantal geslaagde en mislukte aanvragen. Met de dimensie EventTypehttps://aka.ms/ioteventgrid)wordt het type gebeurtenis weergegeven ( .|Resourceid<br/>Resultaat<br/>EventType (EventType)|
|EventGridLatentie|Latentie van gebeurtenisraster (voorbeeld)|Milliseconden|Average|De gemiddelde latentie (milliseconden) vanaf het moment dat de gebeurtenis Iot Hub werd gegenereerd tot het moment waarop de gebeurtenis werd gepubliceerd in Gebeurtenisraster. Dit getal is een gemiddelde tussen alle gebeurtenistypen. Gebruik de dimensie EventType om de latentie van een specifiek type gebeurtenis te zien.|Resourceid<br/>EventType (EventType)|
|d2c.twin.read.success|Succesvolle tweeling leest van apparaten|Count|Totaal|De telling van alle succesvolle apparaat-geïnitieerde tweeling leest.|Geen|
|d2c.twin.read.failure|Mislukte tweeling leest van apparaten|Count|Totaal|De telling van alle mislukte apparaat-geïnitieerde tweeling leest.|Geen|
|d2c.twin.read.size|Reactiegrootte van dubbele leest van apparaten|Bytes|Average|Het gemiddelde, min, en max van alle succesvolle apparaat geïnitieerde tweeling leest.|Geen|
|d2c.twin.update.success|Succesvolle dubbele updates van apparaten|Count|Totaal|De telling van alle succesvolle apparaat-geïnitieerde dubbele updates.|Geen|
|d2c.twin.update.failure|Mislukte dubbele updates van apparaten|Count|Totaal|De telling van alle mislukte apparaat-geïnitieerde dubbele updates.|Geen|
|d2c.twin.update.size|Grootte van dubbele updates van apparaten|Bytes|Average|De gemiddelde, min, en maximale grootte van alle succesvolle apparaat geïnitieerde dubbele updates.|Geen|
|c2d.methods.success|Succesvolle directe methode aanroepingen|Count|Totaal|De telling van alle succesvolle directe methodevraag.|Geen|
|c2d.methods.failure|Mislukte directe aanroepen van methoden|Count|Totaal|De telling van alle mislukte directe methodeaanroepen.|Geen|
|c2d.methods.requestSize|Aanvraaggrootte van directe methode-aanroepingen|Bytes|Average|Het gemiddelde, min en max van alle succesvolle directe methodeaanvragen.|Geen|
|c2d.methods.responseSize|Reactiegrootte van directe aanroepingen van methoden|Bytes|Average|Het gemiddelde, min, en max van alle succesvolle directe methode reacties.|Geen|
|c2d.twin.read.success|Succesvolle tweeling leest van back-end|Count|Totaal|De telling van alle succesvolle back-end-geïnitieerde tweeling leest.|Geen|
|c2d.twin.read.failure|Mislukte tweeling leest van back-end|Count|Totaal|De telling van alle ontbroken back-end-geïnitieerde tweeling leest.|Geen|
|c2d.twin.read.size|Reactiegrootte van tweeling leest van back-end|Bytes|Average|De gemiddelde, min, en max van alle succesvolle back-end-geïnitieerde tweeling leest.|Geen|
|c2d.twin.update.success|Succesvolle dubbele updates van back-end|Count|Totaal|De telling van alle succesvolle back-end-geïnitieerde dubbele updates.|Geen|
|c2d.twin.update.failure|Mislukte dubbele updates van back-end|Count|Totaal|De telling van alle mislukte back-end-geïnitieerde dubbele updates.|Geen|
|c2d.twin.update.size|Grootte van dubbele updates van back-end|Bytes|Average|De gemiddelde, min, en maximale grootte van alle succesvolle back-end-geïnitieerde dubbele updates.|Geen|
|twinQueries.success|Succesvolle dubbele query's|Count|Totaal|De telling van alle succesvolle dubbele query's.|Geen|
|twinQueries.failure|Mislukte dubbele query's|Count|Totaal|De telling van alle mislukte dubbele query's.|Geen|
|twinQueries.resultSize|De resultaatgrootte van twee query's|Bytes|Average|De gemiddelde, min, en max van het resultaat grootte van alle succesvolle dubbele query's.|Geen|
|jobs.createTwinUpdateJob.success|Succesvolle creaties van twin update jobs|Count|Totaal|De telling van alle succesvolle creatie van twin update banen.|Geen|
|jobs.createTwinUpdateJob.failure|Mislukte creaties van twin update-taken|Count|Totaal|De telling van alle mislukte creatie van dubbele update taken.|Geen|
|jobs.createDirectMethodJob.success|Succesvolle creaties van methode aanroepbanen|Count|Totaal|De telling van alle succesvolle creatie van directe methode aanroepbanen.|Geen|
|jobs.createDirectMethodJob.failure|Mislukte creaties van aanroeptaken voor methoden|Count|Totaal|De telling van alle mislukte creatie van directe methode aanroeptaken.|Geen|
|jobs.listJobs.success|Succesvolle oproepen om vacatures op te noemen|Count|Totaal|De telling van alle succesvolle oproepen om taken op te sommen.|Geen|
|jobs.listJobs.failure|Mislukte oproepen om taken weer te geven|Count|Totaal|De telling van alle mislukte oproepen om taken op te sommen.|Geen|
|jobs.cancelJob.success|Succesvolle annuleringen van vacatures|Count|Totaal|De telling van alle succesvolle oproepen om een taak te annuleren.|Geen|
|jobs.cancelJob.failure|Mislukte annuleringen van werk|Count|Totaal|De telling van alle mislukte oproepen om een taak te annuleren.|Geen|
|jobs.queryJobs.success|Succesvolle zoekopdrachten|Count|Totaal|De telling van alle geslaagde oproepen naar querytaken.|Geen|
|jobs.queryJobs.failure|Mislukte query's|Count|Totaal|Het aantal mislukte oproepen naar querytaken.|Geen|
|vacatures.voltooid|Voltooide taken|Count|Totaal|De telling van alle voltooide taken.|Geen|
|jobs.failed|Mislukte taken|Count|Totaal|De telling van alle mislukte taken.|Geen|
|d2c.telemetry.ingress.sendThrottle|Aantal beperkingsfouten|Count|Totaal|Aantal beperkingsfouten als gevolg van gashendels voor de doorvoer van het apparaat|Geen|
|dailyMessageQuotaGebruikt|Totaal aantal gebruikte berichten|Count|Average|Aantal totale berichten dat vandaag wordt gebruikt. Dit is een cumulatieve waarde die elke dag om 00:00 UTC wordt gereset naar nul.|Geen|
|deviceDataUsage deviceDataUsage|Totaal gebruik van apparaatgegevens|Bytes|Totaal|Bytes die worden overgedragen van en naar alle apparaten die zijn aangesloten op IotHub|Geen|
|deviceDataUsageV2|Totaal apparaatgegevensgebruik (voorbeeld)|Bytes|Totaal|Bytes die worden overgedragen van en naar alle apparaten die zijn aangesloten op IotHub|Geen|
|totalDeviceCount totalDeviceCount|Totaal aantal apparaten (voorbeeld)|Count|Average|Aantal apparaten dat is geregistreerd op uw IoT-hub|Geen|
|connectedDeviceCount|Verbonden apparaten (voorbeeld)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|Configuraties|Configuratiestatistieken|Count|Totaal|Statistieken voor configuratiebewerkingen|Geen|

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Hub-statistieken hebt gezien, volgt u deze koppeling voor meer informatie over het beheer van Azure IoT Hub:

* [Diagnostische logboeken instellen](iot-hub-monitor-resource-health.md)

Zie:

* [Handleiding voor IoT Hub-ontwikkelaars](iot-hub-devguide.md)

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
