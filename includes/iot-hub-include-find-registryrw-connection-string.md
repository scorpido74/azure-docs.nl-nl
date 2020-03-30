---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883767"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Voer de volgende stappen uit om de verbindingstekenreeks van IoT Hub voor het **registerReadWrite-beleid** te gebruiken:

1. Selecteer **Resourcegroepen**in de [Azure-portal](https://portal.azure.com). Selecteer de resourcegroep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met bronnen.

2. Selecteer in het linkerdeelvenster van uw hub de optie **Beleid voor gedeelde toegang**.

3. Selecteer in de lijst met beleidsregels het **beleid registerReadWrite.**

4. Selecteer **onder Gedeelde toegangstoetsen**het kopieerpictogram voor de **verbindingstekenreeks : primaire sleutel** en sla de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Zie [Toegangsbeheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het gedeelde toegangsbeleid en machtigingen voor IoT Hub.
