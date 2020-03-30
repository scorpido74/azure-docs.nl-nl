---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050389"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Apparaatdubbels zijn JSON-documenten waarin statusinformatie van een apparaat zijn opgeslagen, zoals metagegevens, configuraties en voorwaarden. IoT Hub blijft bestaan voor elk apparaat dat er verbinding mee maakt.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Gebruik apparaattweelingen om:

* Sla apparaatmetagegevens op via de back-end van uw oplossing.

* Rapporteer actuele statusgegevens, zoals beschikbare mogelijkheden en voorwaarden, bijvoorbeeld de gebruikte verbindingsmethode, vanuit uw apparaat-app.

* Synchroniseer de status van langlopende werkstromen, zoals firmware- en configuratie-updates, tussen een apparaat-app en een back-end-app.

* Query uw apparaat metagegevens, configuratie of status.

Apparaattweelingen zijn ontworpen voor synchronisatie en voor het opvragen van apparaatconfiguraties en -omstandigheden. Meer informatie over wanneer apparaattweelingen moeten worden gebruikt, vindt u [in Begrijpen apparaattweelingen](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Apparaattweelingen worden opgeslagen in een IoT-hub en bevatten de volgende elementen:

* **Tags**. Apparaatmetagegevens alleen toegankelijk via de back-end van de oplossing.

* **Gewenste eigenschappen.** JSON-objecten die door de back-end van de oplossing kunnen worden gewijzigd en door de apparaat-app kunnen worden waargenomen.

* **Gerapporteerde eigenschappen**. JSON-objecten die door de apparaat-app kunnen worden gewijzigd en leesbaar zijn door de back-end van de oplossing.

Tags en eigenschappen kunnen geen arrays bevatten, maar objecten kunnen worden genest.

In de volgende afbeelding ziet u de dubbele organisatie van het apparaat:

![Dubbele afbeelding van het apparaat met functionaliteit](./media/iot-hub-selector-twin-get-started/twin.png)

Bovendien kan de back-end van de oplossing apparaattweelingen opvragen op basis van alle bovenstaande gegevens.
Zie [Apparaattweelingen begrijpen](../articles/iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie over apparaattweelingen. Zie [IoT Hub-querytaal](../articles/iot-hub/iot-hub-devguide-query-language.md)voor meer informatie over query's.


In deze handleiding ontdekt u hoe u:

* Maak een back-end-app die tags toevoegt aan een apparaattweeling en een gesimuleerde apparaat-app die het verbindingskanaal rapporteert als een gerapporteerde eigenschap op de apparaattweeling.

* Queryapparaten uit uw back-end-app met behulp van filters op de eerder gemaakte tags en eigenschappen.
