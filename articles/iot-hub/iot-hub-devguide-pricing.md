---
title: Prijzen van Azure IoT Hub | Microsoft Docs
description: Handleiding voor ontwikkelaars - informatie over hoe metingen en prijzen werkt met IoT Hub, met inbegrip van voorbeelden gewerkt.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 9b6db1b7171652ea5ace4db370b72dc22b6bdc90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271224"
---
# <a name="azure-iot-hub-pricing-information"></a>Azure IoT Hub-prijsinformatie

[Prijzen voor Azure IOT hub](https://azure.microsoft.com/pricing/details/iot-hub) biedt algemene informatie over de verschillende sku's en prijzen voor IOT hub. In dit artikel bevat meer informatie over hoe de verschillende functies van de IoT Hub als berichten worden gemeten door IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Kosten per bewerking

| Bewerking | Factureringsgegevens | 
| --------- | ------------------- |
| Registerbewerkingen voor identiteit <br/> (maken, ophalen, weergeven, bijwerken en verwijderen) | Niet in rekening gebracht. |
| Apparaat-naar-cloud-berichten | Verzonden berichten worden gefactureerd in chunks van 4 KB voor inkomend verkeer naar IoT Hub. Een bericht van 6-KB wordt bijvoorbeeld in rekening gebracht 2 berichten. |
| Cloud-naar-apparaat-berichten | Verzonden berichten worden gefactureerd in chunks van 4 KB, bijvoorbeeld een 6-KB-bericht 2 berichten in rekening wordt gebracht. |
| Uploaden van bestanden | Bestandsoverdracht naar Azure Storage wordt geen datalimiet door IoT Hub. Bestand overdracht initialisatie- en -voltooiing berichten worden in rekening gebracht zoals mailberichten gemeten in intervallen van 4 KB. Een voor beeld: het overdragen van een bestand van 10 MB wordt in rekening gebracht als twee berichten naast de Azure Storage kosten. |
| Directe methoden | Aanvragen voor een geslaagde methode worden in rekening gebracht in segmenten van 4 KB en de antwoorden worden in segmenten van 4 KB in rekening gebracht als extra berichten. Aanvragen voor niet-verbonden apparaten worden in rekening gebracht als berichten in chunks van 4 KB. Een methode met een hoofd tekst van 4 KB die resulteert in een reactie zonder hoofd code van het apparaat, wordt bijvoorbeeld in rekening gebracht als twee berichten. Een methode met een 6-KB-instantie die in een 1 KB-reactie van het apparaat resulteert wordt in rekening gebracht als twee berichten voor de aanvraag een ander bericht voor het antwoord. |
| Apparaat- en -module dubbele leesbewerkingen | Dubbele leesbewerkingen van het apparaat of de module en de oplossing back end worden in rekening gebracht als berichten in 512-byte-segmenten. Bijvoorbeeld, een dubbel 6-KB lezen wordt in rekening gebracht als 12-berichten. |
| Dubbel apparaat en de module-updates (tags en eigenschappen) | Apparaatdubbel werkt bij van het apparaat of de module en de back-end van de oplossing worden in rekening gebracht als berichten in 512-byte-segmenten. Bijvoorbeeld, een dubbel 6-KB lezen wordt in rekening gebracht als 12-berichten. |
| Apparaat- en -module apparaatdubbel-query 's | Query's worden in rekening gebracht als berichten, afhankelijk van de grootte van het resultaat in 512-byte-segmenten. |
| Taakbewerkingen <br/> (maken, bijwerken, weergeven, verwijderen) | Niet in rekening gebracht. |
| Taken per apparaat bewerkingen | Taakbewerkingen (zoals apparaatdubbel werkt, en methoden) worden in rekening gebracht als normale. Een taak die leidt tot 1000 methodeaanroepen met 1 KB aanvragen en antwoorden van de lege-instantie wordt bijvoorbeeld in rekening gebracht 1000 berichten. |
| Keepalive-berichten | Wanneer u AMQP of MQTT-protocol gebruikt, worden de berichten uitgewisseld verbinding te maken en berichten die in de onderhandeling wordt uitgewisseld worden niet gefactureerd. |

> [!NOTE]
> Alle grootten worden berekend overweegt de payload-grootte in bytes (protocol framing wordt genegeerd). Voor berichten die eigenschappen en de hoofdtekst hebben, wordt de grootte in een protocol platformonafhankelijk berekend. Zie [IOT hub-bericht indeling](iot-hub-devguide-messages-construct.md)voor meer informatie.

## <a name="example-1"></a># 1

Een apparaat verzendt een 1 KB apparaat-naar-cloud bericht per minuut naar IoT Hub, die vervolgens kan worden gelezen door Azure Stream Analytics. De back-end van de oplossing roept elke 10 minuten een methode (met een nettolading van 512 bytes) op het apparaat aan om een specifieke actie te activeren. Het apparaat reageert op de methode met een resultaat van 200 bytes.

Het apparaat is verbruikt:

* Een bericht * 60 minuten * 24 uur = 1440 berichten per dag voor de apparaat-naar-cloud-berichten.
* Twee aanvragen plus reactie * 6 keer per uur * 24 uur = 288 berichten voor de methoden.

Deze berekening biedt een totaal van 1728 berichten per dag.

## <a name="example-2"></a># 2

Een apparaat verzendt één 100 KB apparaat-naar-cloud bericht per uur. Werkt het ook de apparaatdubbel met 1 KB payloads elke vier uur. De oplossing terug beëindigen, één keer per dag, leest het dubbele apparaat 14-KB en wordt deze bijgewerkt met 512-byte-nettoladingen configuraties wijzigen.

Het apparaat is verbruikt:

* Berichten van 25 (100 KB/4 KB) * 24 uur voor apparaat-naar-cloud-berichten.
* Twee berichten (1 KB/0,5 KB) * zes keer per dag voor apparaatdubbel werkt.

Deze berekening biedt een totaal van 612 berichten per dag.

De back-end oplossing verbruikt 28 berichten (14 KB/0,5 KB) te lezen van het dubbele apparaat, plus een bericht bij te werken, voor een totaal van 29 berichten.

Verbruiken 641 berichten per dag in totaal, het apparaat en de back-end van de oplossing.
