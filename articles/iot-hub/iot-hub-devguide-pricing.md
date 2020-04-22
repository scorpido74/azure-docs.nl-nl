---
title: Inzicht in azure IoT Hub-prijzen | Microsoft Documenten
description: Ontwikkelaarshandleiding - informatie over hoe meting en prijzen werken met IoT Hub, inclusief uitgewerkte voorbeelden.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729149"
---
# <a name="azure-iot-hub-pricing-information"></a>Prijsinformatie over Azure IoT Hub

[Azure IoT Hub-prijzen](https://azure.microsoft.com/pricing/details/iot-hub) bieden algemene informatie over verschillende SKU's en prijzen voor IoT Hub. Dit artikel bevat aanvullende details over hoe de verschillende IoT Hub-functionaliteiten worden gemeten als berichten door IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Kosten per bewerking

| Bewerking | Factureringsgegevens | 
| --------- | ------------------- |
| Registerbewerkingen voor identiteit <br/> (maken, ophalen, aanbieden, bijwerken, verwijderen) | Niet in rekening gebracht. |
| Apparaat-naar-cloud-berichten | Verzonden berichten worden in 4-KB-segmenten opgeladen bij binnendringen in IoT-hub. Er wordt bijvoorbeeld een 6-KB-bericht in rekening gebracht met 2 berichten. |
| Cloud-to-device berichten | Met succes verzonden berichten worden in 4-KB-segmenten in rekening gebracht, bijvoorbeeld een 6-KB-bericht wordt 2 berichten in rekening gebracht. |
| Bestandsuploads | Bestandsoverdracht naar Azure Storage wordt niet gemeten door IoT Hub. Bestandsoverdracht initiatie en voltooiing berichten worden in rekening gebracht als messaged gemeten in 4-KB stappen. Het overbrengen van een bestand van 10 MB wordt bijvoorbeeld in rekening gebracht als twee berichten naast de Azure Storage-kosten. |
| Directe methoden | Succesvolle methodeaanvragen worden in 4-KB-segmenten in rekening gebracht en antwoorden worden in stukken van 4 KB in rekening gebracht als extra berichten. Aanvragen voor losgekoppelde apparaten worden in rekening gebracht als berichten in stukken van 4 KB. Een methode met een 4-KB-behuizing die resulteert in een reactie zonder lichaam van het apparaat, wordt bijvoorbeeld als twee berichten in rekening gebracht. Een methode met een 6-KB-behuizing die resulteert in een reactie van 1 KB van het apparaat, wordt in rekening gebracht als twee berichten voor het verzoek plus een ander bericht voor het antwoord. |
| Apparaat en module twin leest | Twin leest van het apparaat of module en van de oplossing back-end worden opgeladen als berichten in 512-byte brokken. Het lezen van een 6-KB twin wordt bijvoorbeeld in rekening gebracht als 12 berichten. |
| Dubbele updates van apparaten en modules (tags en eigenschappen) | Dubbele updates van het apparaat of de module en van de back-end van de oplossing worden opgeladen als berichten in stukjes met 512 byte. Het lezen van een 6-KB twin wordt bijvoorbeeld in rekening gebracht als 12 berichten. |
| Dubbele query's voor apparaten en modules | Query's worden in rekening gebracht als berichten, afhankelijk van de resultaatgrootte in segmenten van 512 byte. |
| Taakbewerkingen <br/> (maken, bijwerken, weergeven, verwijderen) | Niet in rekening gebracht. |
| Bewerkingen per apparaat | Taken (zoals dubbele updates en methoden) worden als normaal in rekening gebracht. Een taak die resulteert in 1000 methodeaanroepen met 1-KB-aanvragen en antwoorden met lege tekst en wordt bijvoorbeeld 1000 berichten in rekening gebracht. |
| In leven houdende berichten | Bij het gebruik van AMQP- of MQTT-protocollen worden berichten die worden uitgewisseld om de verbinding en berichten die in de onderhandeling worden uitgewisseld, niet in rekening gebracht. |

> [!NOTE]
> Alle formaten worden berekend gezien de laadgrootte in bytes (protocolframing wordt genegeerd). Voor berichten, die eigenschappen en lichaam hebben, wordt de grootte berekend op een protocol-agnostische manier. Zie [De berichtindeling van IoT Hub](iot-hub-devguide-messages-construct.md)voor meer informatie .

## <a name="example-1"></a>Voorbeeld #1

Een apparaat stuurt één apparaat van 1 KB naar de cloud per minuut naar IoT Hub, dat vervolgens wordt gelezen door Azure Stream Analytics. De oplossing back-end roept een methode (met een 512-byte payload) op het apparaat om de 10 minuten om een specifieke actie te activeren. Het apparaat reageert op de methode met een resultaat van 200 bytes.

Het apparaat verbruikt:

* Eén bericht * 60 minuten * 24 uur = 1440 berichten per dag voor de device-to-cloud berichten.
* Twee aanvraag plus reactie * 6 keer per uur * 24 uur = 288 berichten voor de methoden.

Deze berekening geeft in totaal 1728 berichten per dag.

## <a name="example-2"></a>Voorbeeld #2

Een apparaat stuurt elk uur één 100-KB-apparaat-naar-cloud-bericht. Het werkt ook zijn apparaat tweeling met 1-KB payloads om de vier uur. De oplossing back-end, eenmaal per dag, leest de 14-KB apparaat twin en werkt het met 512-byte payloads om configuraties te veranderen.

Het apparaat verbruikt:

* 25 (100 KB / 4 KB) berichten * 24 uur voor device-to-cloud berichten.
* Twee berichten (1 KB / 0,5 KB) * zes keer per dag voor apparaat twin updates.

Deze berekening geeft in totaal 612 berichten per dag.

De back-end van de oplossing verbruikt 28 berichten (14 KB / 0,5 KB) om de apparaattweeling te lezen, plus één bericht om het bij te werken, voor een totaal van 29 berichten.

In totaal verbruiken het apparaat en de back-end van de oplossing 641 berichten per dag.
