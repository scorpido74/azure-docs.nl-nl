---
title: Regels en acties configureren in Azure IoT Central | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u als een bouwer op telemetrie regels en acties in uw Azure IoT Central-toepassing kunt configureren.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f528135445824a17a8d8c95913adf6934cc87eb0
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260110"
---
# <a name="configure-rules-preview-features"></a>Regels configureren (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Regels in IoT Central fungeren als een hulp programma voor aanpas bare reactie dat actief bewaakte gebeurtenissen van verbonden apparaten activeren. In de volgende secties wordt beschreven hoe regels worden geëvalueerd.

## <a name="select-target-devices"></a>Doel apparaten selecteren

Gebruik de sectie doel apparaten om te selecteren op welk soort apparaten deze regel wordt toegepast. Met filters kunt u verder verfijnen welke apparaten moeten worden opgenomen. De filters gebruiken eigenschappen op het apparaatprofiel om de set apparaten te filteren. Filters zelf activeren geen actie. In de volgende scherm afbeelding zijn de apparaten waarop de doel **machine**wordt toegepast, het type apparaat sjabloon. Met het filter wordt aangegeven dat de regel alleen **koel kasten** moet bevatten waarvan de eigenschap **gefabriceerde status** gelijk is aan **Washington**.

![Voorwaarden](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Meerdere voor waarden gebruiken

Voor waarden worden de regels geactiveerd. Wanneer u op dit moment meerdere voor waarden aan een regel toevoegt, zijn ze logisch en samen. Met andere woorden: aan alle voor waarden moet worden voldaan voordat de regel als waar kan worden geëvalueerd.  

In de volgende scherm afbeelding worden de voor waarden gecontroleerd wanneer de Tempe ratuur groter is dan 70&deg; F en de vochtigheid kleiner is dan 10. Wanneer beide instructies waar zijn, evalueert de regel naar waar en wordt een actie geactiveerd.

![Voorwaarden](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Aggregatie venster gebruiken

Regels evalueren cumulatieve tijd Vensters als tumblingvenstertriggers Windows. In de onderstaande scherm afbeelding is het tijd venster vijf minuten. De regel wordt elke vijf minuten geëvalueerd op de laatste vijf minuten van de gegevens. De gegevens worden slechts eenmaal geëvalueerd in het venster waarin deze overeenkomt.

![Tumblingvenstertriggers Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Regels gebruiken met IoT Edge modules

Een beperking geldt voor regels die worden toegepast op IoT Edge modules. Regels op telemetrie van verschillende modules worden niet als geldige regels geëvalueerd. Doe het volgende als voor beeld. De eerste voor waarde van de regel bevindt zich op een temperatuur telemetrie van module A. De tweede voor waarde van de regel bevindt zich op een vochtigheids-telemetrie in module B. Aangezien de twee voor waarden afkomstig zijn uit verschillende modules, is dit een ongeldige set voor waarden. De regel is ongeldig en er treedt een fout op bij het opslaan van de regel.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een regel in uw Azure IoT Central-toepassing configureert, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [Uw gegevens onderweg analyseren](howto-create-analytics.md)
