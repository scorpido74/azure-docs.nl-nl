---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558495"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Voer de volgende stappen uit om de verbindingstekenreeks van IoT Hub voor het **servicebeleid** te gebruiken:

1. Selecteer **Resourcegroepen**in de [Azure-portal](https://portal.azure.com). Selecteer de resourcegroep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met bronnen.

1. Selecteer in het linkerdeelvenster van uw IoT-hub de optie **Beleid voor gedeelde toegang**.

1. Selecteer in de lijst met beleidsregels het **servicebeleid.**

1. Selecteer **onder Gedeelde toegangstoetsen**het kopieerpictogram voor de **verbindingstekenreeks : primaire sleutel** en sla de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Zie [Toegangsbeheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het gedeelde toegangsbeleid en machtigingen voor IoT Hub.
