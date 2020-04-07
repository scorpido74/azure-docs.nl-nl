---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757003"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Voer de volgende stappen uit om een beleid voor gedeelde toegang te maken dat **serviceconnect-** en **registerschrijfmachtigingen** verleent en om een verbindingstekenreeks voor dit beleid te krijgen:

1. Selecteer **Resourcegroepen**in de [Azure-portal](https://portal.azure.com). Selecteer de resourcegroep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met bronnen.

1. Selecteer in het linkerdeelvenster van uw hub de optie **Beleid voor gedeelde toegang**.

1. Selecteer **Toevoegen**in het bovenste menu boven de lijst met beleidsregels .

1. Voer **onder Een beleid voor gedeelde toegang toevoegen**een beschrijvende naam in voor uw beleid, zoals *serviceEnRegisterReadWrite*. Selecteer **onder Machtigingen** **registerschrijven** en **Serviceverbinding**en selecteer **Vervolgens Maken**. (De **registerleestoestemming** wordt automatisch opgenomen wanneer u **Registerschrijven selecteert**.)

    ![Een nieuw beleid voor gedeelde toegang toevoegen](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selecteer uw nieuwe beleid in de lijst met beleidsregels.

1. Selecteer **onder Gedeelde toegangstoetsen**het kopieerpictogram voor de **verbindingstekenreeks : primaire sleutel** en sla de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Zie [Toegangsbeheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het gedeelde toegangsbeleid en machtigingen voor IoT Hub.
