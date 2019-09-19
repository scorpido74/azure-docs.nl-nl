---
title: Metrische gegevens gebruiken voor het bewaken van Azure IoT Hub | Microsoft Docs
description: Hoe u Azure IoT Hub metrische gegevens kunt gebruiken om de algehele status van uw IoT-hubs te beoordelen en te controleren.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: f0bcf12a43a4732b371dd2d64c0b174a0087bea9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098944"
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

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Description|Dimensies|
|---|---|---|---|---|---|
|d2c<br>. telemetrie<br>ingangs.<br>allProtocol|Verzend pogingen voor telemetrie-berichten|Count|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. inkomend<br>.success|Verzonden telemetriegegevens|Count|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Er zijn geen dimensies|
|c2d<br>. opdrachten<br>. uitgangen<br>. complete<br>.success|Opdrachten voltooid|Count|Totaal|Aantal Cloud-naar-apparaat-opdrachten die zijn voltooid door het apparaat|Er zijn geen dimensies|
|c2d<br>. opdrachten<br>. uitgangen<br>.abandon<br>.success|Opdrachten afgebroken|Count|Totaal|Aantal Cloud-naar-apparaat-opdrachten die zijn afgebroken door het apparaat|Er zijn geen dimensies|
|c2d<br>. opdrachten<br>. uitgangen<br>.reject<br>.success|Geweigerde opdrachten|Count|Totaal|Aantal Cloud-naar-apparaat-opdrachten dat door het apparaat is afgewezen|Er zijn geen dimensies|
|apparaten<br>.totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Er zijn geen dimensies|
|apparaten<br>.connectedDevices<br>.allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. uitgangen<br>.success|Route ring: telemetrie-berichten|Count|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. uitgangen<br>. neergezet|Route ring: telemetrie-berichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. uitgangen<br>. zwevend|Route ring: telemetriegegevens van zwevende berichten |Count|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. uitgangen<br>. ongeldige|Route ring: telemetrie-berichten incompatibel|Count|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. uitgangen<br>. terugval|Route ring: berichten worden bezorgd bij terugval|Count|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.eventHubs|Route ring: berichten worden bezorgd bij Event hub|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. latentie<br>.eventHubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. latentie<br>.serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. latentie<br>.serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.builtIn<br>. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen). Deze metrische gegevens worden alleen gestart als route ring is https://aka.ms/iotrouting) ingeschakeld (voor de IOT-hub.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. latentie<br>. builtIn. Events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen). Deze metrische gegevens worden alleen gestart als route ring is https://aka.ms/iotrouting) ingeschakeld (voor de IOT-hub.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.storage|Route ring: berichten worden bezorgd bij de opslag|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. latentie<br>.storage|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.storage<br>.bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Er zijn geen dimensies|
|d2c<br>. eind punten<br>. uitgangen<br>.storage<br>.blobs|Route ring: blobs die aan de opslag worden geleverd|Count|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Er zijn geen dimensies|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Count|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort https://aka.ms/ioteventgrid) gebeurtenis weer (. Gebruik de dimensie type gebeurtenis om te zien van welke van de aanvragen afkomstig zijn.|Resultaat, type gebeurtenis|
|EventGridLatency|Event Grid latentie (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|EventType|
|d2c<br>. dubbele<br>. Lees<br>.success|Geslaagde dubbele Lees bewerkingen van apparaten|Count|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Er zijn geen dimensies|
|d2c<br>. dubbele<br>. Lees<br>. fout|Mislukte dubbele Lees bewerkingen van apparaten|Count|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Er zijn geen dimensies|
|d2c<br>. dubbele<br>. Lees<br>. grootte|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|d2c<br>. dubbele<br>.update<br>.success|Geslaagde dubbele updates van apparaten|Count|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Er zijn geen dimensies|
|d2c<br>. dubbele<br>.update<br>. fout|Mislukte dubbele updates van apparaten|Count|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Er zijn geen dimensies|
|d2c<br>. dubbele<br>.update<br>. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Er zijn geen dimensies|
|c2d<br>. methoden<br>.success|Geslaagde directe aanroepen van de methode|Count|Totaal|Het aantal voltooide direct-methode aanroepen.|Er zijn geen dimensies|
|c2d<br>. methoden<br>. fout|Mislukte directe aanroepen van methode|Count|Totaal|Het aantal mislukte direct-methode aanroepen.|Er zijn geen dimensies|
|c2d<br>. methoden<br>.requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|Er zijn geen dimensies|
|c2d<br>. methoden<br>.responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|Er zijn geen dimensies|
|c2d<br>. dubbele<br>. Lees<br>.success|Geslaagde dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen. Dit aantal bevat geen dubbele Lees bewerkingen die worden geïnitieerd vanuit dubbele query's.|Er zijn geen dimensies|
|c2d<br>. dubbele<br>. Lees<br>. fout|Mislukte dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|c2d<br>. dubbele<br>. Lees<br>. grootte|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|c2d<br>. dubbele<br>.update<br>.success|Geslaagde dubbele updates van back-end|Count|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Er zijn geen dimensies|
|c2d<br>. dubbele<br>.update<br>. fout|Mislukte dubbele updates van back-end|Count|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Er zijn geen dimensies|
|c2d<br>. dubbele<br>.update<br>. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|Er zijn geen dimensies|
|twinQueries<br>.success|Geslaagde dubbele query's|Count|Totaal|Het aantal geslaagde dubbele query's.|Er zijn geen dimensies|
|twinQueries<br>. fout|Mislukte dubbele query's|Count|Totaal|Het aantal mislukte dubbele query's.|Er zijn geen dimensies|
|twinQueries<br>.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|Er zijn geen dimensies|
|taken<br>.createTwinUpdateJob<br>.success|Geslaagde creatie van dubbele update taken|Count|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Er zijn geen dimensies|
|taken<br>.createTwinUpdateJob<br>. fout|Kan geen dubbele update taken uitvoeren|Count|Totaal|Het aantal mislukte het maken van dubbele update taken.|Er zijn geen dimensies|
|taken<br>.createDirectMethodJob<br>.success|Geslaagde creatie van methode aanroep taken|Count|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Er zijn geen dimensies|
|taken<br>.createDirectMethodJob<br>. fout|Kan geen aanroepen van methode aanroep taken uitvoeren|Count|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Er zijn geen dimensies|
|taken<br>.listJobs<br>.success|Geslaagde aanroepen naar lijst taken|Count|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Er zijn geen dimensies|
|taken<br>.listJobs<br>. fout|Mislukte aanroepen naar lijst taken|Count|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Er zijn geen dimensies|
|taken<br>.cancelJob<br>.success|Voltooide taak annuleringen|Count|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Er zijn geen dimensies|
|taken<br>.cancelJob<br>. fout|Mislukte taak annuleringen|Count|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Er zijn geen dimensies|
|taken<br>.queryJobs<br>.success|Geslaagde taak query's|Count|Totaal|Het aantal geslaagde aanroepen naar query taken.|Er zijn geen dimensies|
|taken<br>.queryJobs<br>. fout|Mislukte taak query's|Count|Totaal|Het aantal mislukte aanroepen naar query taken.|Er zijn geen dimensies|
|taken<br>. voltooid|Voltooide taken|Count|Totaal|Het aantal voltooide taken.|Er zijn geen dimensies|
|taken<br>. mislukt|Mislukte taken|Count|Totaal|Het aantal mislukte taken.|Er zijn geen dimensies|
|d2c<br>. telemetrie<br>. inkomend<br>.sendThrottle|Aantal beperkings fouten|Count|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Er zijn geen dimensies|
|dailyMessage<br>QuotaUsed|Totaal aantal gebruikte berichten|Count|Average|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|Er zijn geen dimensies|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Er zijn geen dimensies|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Count|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Er zijn geen dimensies|
|Connect<br>DeviceCount|Verbonden apparaten (preview-versie)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Er zijn geen dimensies|
|configuraties|Metrische configuratie gegevens|Count|Totaal|Metrische gegevens voor configuratie bewerkingen|Er zijn geen dimensies|

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van IoT Hub metrische gegevens hebt weer gegeven, volgt u deze koppeling voor meer informatie over het beheren van Azure IoT Hub:

* [Controle van bewerkingen](iot-hub-operations-monitoring.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)

* [AI implementeren op edge-apparaten met Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
