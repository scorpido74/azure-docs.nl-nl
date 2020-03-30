---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7b022f71e197c5695876f2049ee376c3616afc6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70048997"
---
<!-- put the ## header in the file that includes this file -->

In deze sectie maakt u een apparaatidentiteit in het identiteitsregister in uw IoT-hub. Een apparaat kan geen verbinding maken met een hub, tenzij het een vermelding in het identiteitsregister heeft. Zie de [iot-hub-ontwikkelaarshandleiding](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)voor meer informatie.

1. Open **IoT-apparaten**in het navigatiemenu van uw IoT-hub en selecteer **Nieuw** om een apparaat toe te voegen in uw IoT-hub.

    ![Apparaatidentiteit maken in portal](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. Geef **in Een apparaat maken**een naam op voor uw nieuwe apparaat, zoals **myDeviceId,** en selecteer **Opslaan**. Met deze actie wordt een apparaatidentiteit voor uw IoT-hub gemaakt.

   ![Een nieuw apparaat toevoegen](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Nadat het apparaat is gemaakt, opent u het apparaat in de lijst in het deelvenster **IoT-apparaten**. Kopieer de **tekenreeks primaire verbinding** om later te gebruiken.

    ![Tekenreeks apparaatverbinding](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> In het id-register van IoT Hub worden alleen apparaat-id's opgeslagen waarmee veilig toegang tot de IoT-hub kan worden verkregen. De apparaat-id’s en sleutels worden opgeslagen en gebruikt als beveiligingsreferenties. Met de vlag voor ingeschakeld/uitgeschakeld kunt u toegang tot een afzonderlijk apparaat uitschakelen. Als uw toepassing andere apparaatspecifieke metagegevens moet opslaan, moet deze een toepassingsspecifieke opslagmethode gebruiken. Zie [IoT Hub-ontwikkelaarshandleiding](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie.
