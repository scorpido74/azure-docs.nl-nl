---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050416"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Voer de volgende stappen uit om een beleid voor gedeelde toegang te maken dat machtigingen voor **serviceverbinding** en **registerlees** verleent en een verbindingstekenreeks voor dit beleid wilt krijgen:

1. Selecteer **Resourcegroepen**in de [Azure-portal](https://portal.azure.com). Selecteer de resourcegroep waar uw hub zich bevindt en selecteer vervolgens uw hub in de lijst met bronnen.

1. Selecteer in het linkerdeelvenster van uw hub de optie **Beleid voor gedeelde toegang**.

1. Selecteer **Toevoegen**in het bovenste menu boven de lijst met beleidsregels .

1. Voer **onder Een beleid voor gedeelde toegang toevoegen**een beschrijvende naam in voor uw beleid, zoals *serviceEnRegisterLezen*. Selecteer **onder Machtigingen** **registerlees-** en **Serviceverbinding**en selecteer **Vervolgens Maken**.

    ![Een nieuw beleid voor gedeelde toegang toevoegen](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecteer uw nieuwe beleid in de lijst met beleidsregels.

1. Selecteer **onder Gedeelde toegangstoetsen**het kopieerpictogram voor de **verbindingstekenreeks : primaire sleutel** en sla de waarde op.

    ![Het ophalen van de verbindingsreeks](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Zie [Toegangsbeheer en machtigingen](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie over het gedeelde toegangsbeleid en machtigingen voor IoT Hub.
