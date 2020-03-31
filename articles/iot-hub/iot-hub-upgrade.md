---
title: Azure IoT-hub bijwerken | Microsoft Documenten
description: Wijzig de prijs- en schaallaag voor IoT Hub om meer mogelijkheden voor berichten- en apparaatbeheer te krijgen.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61440206"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Een upgrade uitvoeren voor uw IoT-hub

Naarmate uw IoT-oplossing groeit, staat Azure IoT Hub klaar om u te helpen bij het opschalen. Azure IoT Hub biedt twee lagen, basic (B) en standaard (S), voor klanten die verschillende functies willen gebruiken. Binnen elke laag zijn drie maten (1, 2 en 3) die het aantal berichten bepalen dat elke dag kan worden verzonden.

Als u meer apparaten hebt en meer mogelijkheden nodig hebt, zijn er drie manieren om uw IoT-hub aan te passen aan uw behoeften:

* Eenheden toevoegen binnen de IoT-hub. Elke extra eenheid in een B1 IoT-hub zorgt bijvoorbeeld voor 400.000 extra berichten per dag.

* Wijzig de grootte van de IoT-hub. Migreer bijvoorbeeld van de B1-laag naar de B2-laag om het aantal berichten te verhogen dat elke eenheid per dag kan ondersteunen.

* Upgrade naar een hogere laag. Upgrade bijvoorbeeld van de B1-laag naar de S1-laag voor toegang tot geavanceerde functies met dezelfde berichtencapaciteit.

Deze wijzigingen kunnen allemaal optreden zonder bestaande bewerkingen te onderbreken.

Als u uw IoT-hub wilt downgraden, u eenheden verwijderen en de grootte van de IoT-hub verkleinen, maar u niet downgraden naar een lagere laag. U bijvoorbeeld van de S2-laag naar de S1-laag gaan, maar niet van de S2-laag naar de B1-laag. Per IoT Hub kan slechts één type [Iot Hub-editie](https://azure.microsoft.com/pricing/details/iot-hub/) binnen een laag worden gekozen. U bijvoorbeeld een IoT-hub maken met meerdere eenheden S1, maar niet met een mix van eenheden uit verschillende edities, zoals S1 en B3, of S1 en S2.

Deze voorbeelden zijn bedoeld om u te helpen begrijpen hoe u uw IoT-hub aanpassen wanneer uw oplossing verandert. Voor specifieke informatie over de mogelijkheden van elke laag, moet u altijd verwijzen naar [azure IoT Hub-prijzen.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="upgrade-your-existing-iot-hub"></a>Uw bestaande IoT-hub upgraden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en navigeer naar uw IoT-hub.

2. Selecteer **Prijzen en schaal**.

   ![Prijzen en schaal](./media/iot-hub-upgrade/pricing-scale.png)

3. Als u de laag voor uw hub wilt wijzigen, selecteert u **Prijzen en schaallaag**. Kies de nieuwe laag en klik op **Selecteren**.

   ![Prijs- en schaalniveau](./media/iot-hub-upgrade/select-tier.png)

4. Als u het aantal eenheden in uw hub wilt wijzigen, voert u een nieuwe waarde in onder **IoT Hub-eenheden**.

5. Selecteer **Opslaan** om uw wijzigingen op te slaan.

Uw IoT-hub is nu aangepast en uw configuraties zijn ongewijzigd.

De maximale partitielimiet voor iot-hub met basisniveau en iot-hub met standaardniveau is 32. De meeste IoT-hubs hebben slechts 4 partities nodig. De partitielimiet wordt gekozen wanneer IoT Hub wordt gemaakt en koppelt de device-to-cloudberichten aan het aantal gelijktijdige lezers van deze berichten. Deze waarde blijft ongewijzigd wanneer u migreert van basislaag naar standaardlaag.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het kiezen van de juiste IoT Hub-laag.](iot-hub-scaling.md)