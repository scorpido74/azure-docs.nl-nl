---
title: Regels en acties configureren in Azure IoT Central | Microsoft Docs
description: In dit artikel ziet u als bouwer hoe u op telemetrie gebaseerde regels en acties configureert in uw Azure IoT Central-toepassing.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158447"
---
# <a name="configure-rules"></a>Regels configureren



*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Regels in IoT Central dienen als een aanpasbaar responshulpmiddel dat activeert op actief bewaakte gebeurtenissen vanaf verbonden apparaten. In de volgende secties wordt beschreven hoe regels worden geëvalueerd.

## <a name="select-target-devices"></a>Doelapparaten selecteren

Gebruik de sectie doelapparaten om te selecteren op welk type apparaat deze regel wordt toegepast. Met filters u verder verfijnen welke apparaten moeten worden opgenomen. De filters gebruiken eigenschappen op de apparaatsjabloon om de set apparaten te filteren. Filters zelf leiden niet tot een actie. In de volgende schermafbeelding zijn de apparaten waarop wordt getarget **apparaatsjabloontype Koelkast**. Het filter stelt dat de regel alleen koelkasten moet bevatten waar de **fabricerende staat** eigendom is gelijk aan **Washington**. **Refrigerators**

![Voorwaarden](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Meerdere voorwaarden gebruiken

Voorwaarden zijn waar regels op leiden. Op dit moment, wanneer u meerdere voorwaarden toevoegt aan een regel, zijn ze logisch en samen. Met andere woorden, aan alle voorwaarden moet worden voldaan om de regel als waar te laten beoordelen.  

In de volgende schermafbeelding controleren de omstandigheden wanneer&deg; de temperatuur hoger is dan 70 F en de luchtvochtigheid lager is dan 10. Wanneer beide instructies waar zijn, wordt de regel geëvalueerd en wordt een actie geactiveerd.

![Voorwaarden](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Geaggregeerde venstering gebruiken

Regels evalueren geaggregeerde tijdvensters als tuimelende vensters. In de screenshot hieronder, het tijdvenster is vijf minuten. Elke vijf minuten evalueert de regel op de laatste vijf minuten van de gegevens. De gegevens worden slechts eenmaal geëvalueerd in het venster waarnaar zij overeenkomen.

![Tumbling Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Regels gebruiken met IoT Edge-modules

Er geldt een beperking voor regels die worden toegepast op IoT Edge-modules. Regels voor telemetrie van verschillende modules worden niet beoordeeld als geldige regels. Neem het volgende als voorbeeld. De eerste voorwaarde van de regel is op een temperatuur telemetrie van Module A. De tweede voorwaarde van de regel is op een vochtigheidstelemetrie op Module B. Aangezien de twee voorwaarden zijn van verschillende modules, dit is een ongeldige set van voorwaarden. De regel is niet geldig en zal een fout op het proberen om de regel op te slaan gooien.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een regel in uw Azure IoT Central-toepassing configureren, u het als:

> [!div class="nextstepaction"]
> [Analyseer uw gegevens on the fly](howto-create-analytics.md)
