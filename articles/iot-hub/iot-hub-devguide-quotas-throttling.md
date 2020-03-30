---
title: Azure IoT Hub-quota en beperking begrijpen | Microsoft Documenten
description: Ontwikkelaarshandleiding - beschrijving van de quota die van toepassing zijn op IoT Hub en het verwachte beperkingsgedrag.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284692"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referentie - IoT Hub-quota en beperking

In dit artikel worden de quota's voor een IoT-hub uitgelegd en wordt informatie verstrekt om u te helpen begrijpen hoe beperking werkt.

## <a name="quotas-and-throttling"></a>Quota en beperkingen

Elk Azure-abonnement kan hoogstens 50 IoT-hubs hebben, en hoogstens 1 Gratis hub.

Elke IoT-hub is ingericht met een bepaald aantal eenheden in een bepaalde laag. De laag en het aantal eenheden bepalen het maximale dagelijkse quotum van berichten dat u verzenden. De berichtgrootte die wordt gebruikt om het dagelijkse quotum te berekenen, is 0,5 KB voor een gratis laaghub en 4KB voor alle andere lagen. Zie [Azure IoT Hub-hubprijzen](https://azure.microsoft.com/pricing/details/iot-hub/)voor meer informatie.

De laag bepaalt ook de beperkingslimieten die IoT Hub afdwingt voor alle bewerkingen.

### <a name="iot-plug-and-play"></a>IoT Plug en Play

Tijdens openbare preview sturen IoT Plug and Play-apparaten afzonderlijke berichten per interface, waardoor het aantal berichten dat wordt geteld voor uw berichtquotum kan toenemen.

## <a name="operation-throttles"></a>Gashendels voor bediening

Bedieningsthrottles zijn tariefbeperkingen die worden toegepast in minutenbereiken en bedoeld zijn om misbruik te voorkomen. Ze zijn ook onderhevig aan [verkeersvorming.](#traffic-shaping)

In de volgende tabel worden de afgedwongen gashendels weergegeven. Waarden verwijzen naar een afzonderlijke hub.

| Vertragen | Gratis, B1 en S1 | B2 en S2 | B3 en S3 | 
| -------- | ------- | ------- | ------- |
| [Identiteitsregisterbewerkingen](#identity-registry-operations-throttle) (maken, ophalen, aanbieden, bijwerken, verwijderen) | 1.67/sec/eenheid (100/min/eenheid) | 1.67/sec/eenheid (100/min/eenheid) | 83.33/sec/eenheid (5.000/min/eenheid) |
| [Nieuwe apparaatverbindingen](#device-connections-throttle) (deze limiet is van toepassing op de snelheid van _nieuwe verbindingen,_ niet op het totale aantal verbindingen) | Hoger van 100/sec of 12/sec/eenheid <br/> Twee S1-eenheden zijn\*bijvoorbeeld 2 12 = 24 nieuwe verbindingen per seconde, maar u hebt ten minste 100 nieuwe verbindingen per seconde in uw eenheden. Met negen S1-eenheden heb je 108\*nieuwe aansluitingen per seconde (9 12) in je eenheden. | 120 nieuwe aansluitingen/sec/unit | 6.000 nieuwe aansluitingen/sec/eenheid |
| Apparaat-naar-cloud verzendt | Hoger dan 100 verzendbewerkingen per seconde of 12 verzendbewerkingen per seconde/eenheid <br/> Twee S1-eenheden zijn\*bijvoorbeeld 2 12 = 24/sec, maar u hebt ten minste 100 verzendbewerkingen per seconde over uw eenheden. Met negen S1-eenheden hebt u 108\*verzendbewerkingen per seconde (9 12) over uw eenheden. | 120 verzendbewerkingen/sec/eenheid | 6.000 verzendbewerkingen per seconde/eenheid |
| Cloud-to-device stuurt<sup>1</sup> | 1.67 verzendenverrichtingen/sec/eenheid (100 berichten/min/eenheid) | 1.67 verzendbewerkingen/sec/eenheid (100 verzendbewerkingen/min/eenheid) | 83.33 verzendbewerkingen/sec/eenheid (5.000 verzendbewerkingen/min/eenheid) |
| Cloud-to-device ontvangt<sup>1</sup> <br/> (alleen wanneer apparaat HTTPS gebruikt)| 16.67 ontvangen verrichtingen/sec/eenheid (1.000 ontvangen verrichtingen/min/eenheid) | 16.67 ontvangen verrichtingen/sec/eenheid (1.000 ontvangen verrichtingen/min/eenheid) | 833.33 ontvangen verrichtingen/sec/eenheid (50.000 ontvangen verrichtingen/min/eenheid) |
| Bestand uploaden | 1.67 file upload initiations/sec/unit (100/min/unit) | 1.67 file upload initiations/sec/unit (100/min/unit) | 83.33 file upload initiaties/sec/unit (5.000/min/unit) |
| Directe methoden<sup>1</sup> | 160KB/sec/eenheid<sup>2</sup> | 480KB/sec/eenheid<sup>2</sup> | 24 MB/sec/eenheid<sup>2</sup> | 
| Query's | 20/min/eenheid | 20/min/eenheid | 1.000/min/eenheid |
| Twin (apparaat en module) leest<sup>1</sup> | 100/sec | Hoger van 100/sec of 10/sec/eenheid | 500/sec/eenheid |
| Dubbele updates (apparaat en module)<sup>1</sup> | 50/sec | Hoger van 50/sec of 5/sec/eenheid | 250/sec/eenheid |
| Taken<sup>1</sup> <br/> (maken, bijwerken, weergeven, verwijderen) | 1.67/sec/eenheid (100/min/eenheid) | 1.67/sec/eenheid (100/min/eenheid) | 83.33/sec/eenheid (5.000/min/eenheid) |
| Bewerkingen van het werkapparaat<sup>1</sup> <br/> (update twin, beroep directe methode) | 10/sec | Hoger van 10/sec of 1/sec/eenheid | 50/sec/eenheid |
| Configuraties en randimplementaties<sup>1</sup> <br/> (maken, bijwerken, weergeven, verwijderen) | 0,33/sec/eenheid (20/min/eenheid) | 0,33/sec/eenheid (20/min/eenheid) | 0,33/sec/eenheid (20/min/eenheid) |
| Apparaatstream initiatiesnelheid<sup>1</sup> | 5 nieuwe streams/sec | 5 nieuwe streams/sec | 5 nieuwe streams/sec |
| Maximaal aantal gelijktijdig verbonden apparaatstreams<sup>1</sup> | 50 | 50 | 50 |
| Maximale apparaatstroom gegevensoverdracht<sup>1</sup> (geaggregeerd volume per dag) | 300 MB | 300 MB | 300 MB |

<sup>1.</sup> Deze functie is niet beschikbaar in de basislaag van IoT Hub. Zie [Hoe u de juiste IoT-hub kiest](iot-hub-scaling.md)voor meer informatie. <br/><sup>2.</sup> Beperking meter grootte is 4 KB.

### <a name="throttling-details"></a>Details voor beperking

* De metergrootte bepaalt bij welke stappen uw beperkingslimiet wordt verbruikt. Als de payload van uw directe oproep tussen 0 en 4 KB ligt, wordt deze geteld als 4 KB. U tot 40 oproepen per seconde per eenheid maken voordat u de limiet van 160 KB/sec/eenheid bereikt.

   Evenzo, als uw payload is tussen 4 KB en 8 KB, elke oproep is goed voor 8 KB en u tot 20 oproepen per seconde per eenheid voor het raken van de maximale limiet.

   Tot slot, als uw laadvermogen grootte is tussen 156KB en 160 KB, zult u in staat zijn om slechts 1 oproep per seconde per eenheid in uw hub te maken voordat u de limiet van 160 KB / sec / eenheid.

*  Voor *bewerkingen van taken (update twin, directe methode aanroepen)* voor laag S2 is 50/sec/eenheid alleen van toepassing op wanneer u methoden aanroept met behulp van taken. Als u directe methoden rechtstreeks aanroept, geldt de oorspronkelijke beperkingslimiet van 24 MB/sec/eenheid (voor S2).

*  **Quotum** is het geaggregeerde aantal berichten dat u *per dag*in uw hub verzenden. U de quotumlimiet van uw hub vinden onder de kolom **Totaal aantal berichten /dag** op de prijspagina van [iot-hub.](https://azure.microsoft.com/pricing/details/iot-hub/)

*  Uw throttles tussen cloud en apparaat tot cloud bepalen de maximale *snelheid* waarmee u berichten verzenden - het aantal berichten, ongeacht 4 KB-brokken. Elk bericht kan maximaal 256 KB zijn, wat de [maximale berichtgrootte](iot-hub-devguide-quotas-throttling.md#other-limits)is.

*  Het is een goede gewoonte om uw gesprekken te beperken, zodat u de beperkingslimieten niet raakt/overschrijdt. Als u de limiet bereikt, reageert IoT Hub met foutcode 429 en moet de client zich terugtrekken en opnieuw proberen. Deze limieten zijn per hub (of in sommige gevallen per hub/eenheid). Zie voor meer informatie [connectiviteit beheren en betrouwbare berichten/opnieuw proberenpatronen](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Het vormen van het verkeer

Om het burst-verkeer mogelijk te maken, accepteert IoT Hub verzoeken boven het gaspedaal voor een beperkte tijd. De eerste paar van deze aanvragen worden onmiddellijk verwerkt. Als het aantal aanvragen echter blijft in strijd met het gaspedaal, begint IoT Hub de aanvragen in een wachtrij te plaatsen en verwerkt tegen het limiettarief. Dit effect wordt *verkeersvormgeving*genoemd. Bovendien is de grootte van deze wachtrij beperkt. Als de gashendelovertreding blijft doorgaan, wordt de wachtrij uiteindelijk `429 ThrottlingException`gevuld en begint IoT Hub aanvragen af te wijzen met .

U gebruikt bijvoorbeeld een gesimuleerd apparaat om 200 device-to-cloudberichten per seconde naar uw S1 IoT Hub te verzenden (met een limiet van 100/sec D2C-verzendingen). Voor de eerste minuut of twee worden de berichten onmiddellijk verwerkt. Omdat het apparaat echter steeds meer berichten verzendt dan de gaslimiet, begint IoT Hub slechts 100 berichten per seconde te verwerken en zet de rest in een wachtrij. Je begint te merken verhoogde latentie. Uiteindelijk begin je `429 ThrottlingException` als de wachtrij vol raakt te krijgen en het "aantal gasfoutfouten" in de statistieken van [IoT Hub](iot-hub-metrics.md) begint toe te nemen.

### <a name="identity-registry-operations-throttle"></a>Gaspedaal van identiteitsregisterbewerkingen

Apparaatidentiteitsregisterbewerkingen zijn bedoeld voor runtime-gebruik in apparaatbeheer- en inrichtingsscenario's. Het lezen of bijwerken van een groot aantal apparaatidentiteiten wordt ondersteund door [import- en exporttaken.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

### <a name="device-connections-throttle"></a>Apparaataansluitingen gasgevend

Het gaspedaal van de *apparaatverbindingen* regelt de snelheid waarmee nieuwe apparaatverbindingen kunnen worden gemaakt met een IoT-hub. Het gaspedaal van het *apparaat regelt* niet het maximum aantal gelijktijdig verbonden apparaten. De snelheid van *de apparaataansluitingen* is afhankelijk van het aantal eenheden dat is ingericht voor de IoT-hub.

Als u bijvoorbeeld één S1-eenheid koopt, krijgt u een gaspedaal van 100 verbindingen per seconde. Daarom duurt het minstens 100.000 seconden (ongeveer 16 minuten) om 100.000 apparaten aan te sluiten. U echter evenveel tegelijk verbonden apparaten hebben als apparaten die zijn geregistreerd in uw identiteitsregister.

## <a name="other-limits"></a>Andere limieten

IoT Hub handhaaft andere operationele limieten:

| Bewerking | Limiet |
| --------- | ----- |
| Apparaten | Het totale aantal apparaten plus modules dat kan worden geregistreerd op een iot-hub is beperkt tot 1.000.000. De enige manier om deze limiet te verhogen is contact op te nemen met [Microsoft Support.](https://azure.microsoft.com/support/options/)|
| Bestandsuploads | 10 gelijktijdige bestandsuploads per apparaat. |
| Vacatures<sup>1</sup> | Maximale gelijktijdige taken zijn 1 (gratis en S1), 5 (voor S2) en 10 (voor S3). De maximale [import/exporttaken van](iot-hub-bulk-identity-mgmt.md) gelijktijdige apparaten zijn echter 1 voor alle lagen. <br/>De functiegeschiedenis blijft tot 30 dagen behouden. |
| Extra eindpunten | Betaalde SKU-hubs hebben mogelijk 10 extra eindpunten. Gratis SKU-hubs kunnen één extra eindpunt hebben. |
| Query's voor berichtroutering | Betaalde SKU-hubs kunnen 100 routeringsquery's hebben. Gratis SKU-hubs kunnen vijf routeringsquery's hebben. |
| Berichtverrijkingen | Betaalde SKU-hubs kunnen maximaal 10 berichtverrijkingen hebben. Gratis SKU-hubs kunnen maximaal 2 berichtverrijkingen hebben.|
| Device-to-cloud berichten | Maximale berichtgrootte 256 KB |
| Cloud-to-device messaging<sup>1</sup> | Maximale berichtgrootte 64 KB. De maximale in behandeling zijnde berichten voor levering zijn 50 per apparaat. |
| Directe methode<sup>1</sup> | Maximale directe methode payload grootte is 128 KB. |
| Automatische apparaat- en moduleconfiguraties<sup>1</sup> | 100 configuraties per betaalde SKU-hub. 20 configuraties per gratis SKU-hub. |
| IoT Edge automatische implementaties<sup>1</sup> | 50 modules per implementatie. 100 implementaties (inclusief gelaagde implementaties) per betaalde SKU-hub. 10 implementaties per gratis SKU-hub. |
| Tweelingen<sup>1</sup> | Maximale grootte van de gewenste eigenschappen en gerapporteerde eigenschappen secties zijn 32 KB per stuk. Maximale grootte van tags sectie is 8 KB. |
| Gedeeld toegangsbeleid | Maximaal aantal beleid voor gedeelde toegang is 16 |

<sup>1.</sup> Deze functie is niet beschikbaar in de basislaag van IoT Hub. Zie [Hoe u de juiste IoT-hub kiest](iot-hub-scaling.md)voor meer informatie.

## <a name="increasing-the-quota-or-throttle-limit"></a>De quotum- of gaslimiet verhogen

Op een gegeven moment u quota' s of throttlelimieten verhogen door [het aantal ingerichte eenheden in een IoT-hub te verhogen.](iot-hub-upgrade.md)

## <a name="latency"></a>Latentie

IoT Hub streeft ernaar om lage latentie te bieden voor alle bewerkingen. Vanwege netwerkomstandigheden en andere onvoorspelbare factoren kan het echter geen zekere latentie garanderen. Bij het ontwerpen van uw oplossing moet u:

* Vermijd het maken van veronderstellingen over de maximale latentie van een IoT Hub-bewerking.
* Inrichten van uw IoT-hub in het Azure-gebied dat het dichtst bij uw apparaten staat.
* Overweeg Azure IoT Edge te gebruiken om latentiegevoelige bewerkingen uit te voeren op het apparaat of op een gateway dicht bij het apparaat.

Meerdere IoT Hub-eenheden zijn van invloed op beperking zoals eerder beschreven, maar bieden geen extra latentievoordelen of garanties.

Als u onverwachte toenames in de bewerkingslatentie ziet, neemt u contact op met [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Volgende stappen

Voor een diepgaande discussie over iot-hubbeperkingsgedrag raadpleegt u de blogpost [IoT Hub-beperking en u](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Andere referentieonderwerpen in deze IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)
