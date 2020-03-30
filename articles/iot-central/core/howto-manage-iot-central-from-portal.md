---
title: IoT Central beheren vanuit de Azure-portal | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassingen maken en beheren vanuit de Azure-portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157922"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT Central beheren vanuit de Azure-portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de azure [IoT Central-toepassingsbeheerwebsite,](https://aka.ms/iotcentral) u de [Azure-portal](https://portal.azure.com) gebruiken om uw toepassingen te beheren.

## <a name="create-iot-central-applications"></a>IoT Central-toepassingen maken

Als u een toepassing wilt maken, navigeert u naar de [Azure-portal](https://ms.portal.azure.com) en selecteert u **Een resource maken.**

Typ IoT Central in **De Marketplace-balk** *zoeken:*

![Beheerportal: zoeken](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecteer de tegel **IoT Central Application** in de zoekresultaten:

![Beheerportal: zoekresultaten](media/howto-manage-iot-central-from-portal/image0b1.png)

Selecteer nu **Maken:**

![Beheerportal: IoT Central-bron](media/howto-manage-iot-central-from-portal/image0c1.png)

Vul alle velden in het formulier in. Dit formulier is vergelijkbaar met het formulier dat u invult om toepassingen te maken op de website [van Azure IoT Central-toepassingsbeheer.](https://aka.ms/iotcentral) Zie snel aan de slag met [de toepassing Een IoT Central](quick-deploy-iot-central.md) maken voor meer informatie.

![IoT-centrale vorm maken](media/howto-manage-iot-central-from-portal/image6a.png)

**Locatie** is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw toepassing wilt maken. Meestal moet u de locatie kiezen die fysiek het dichtst bij uw apparaten ligt om optimale prestaties te krijgen. Azure IoT Central is momenteel beschikbaar in de regio's **Australië,** **Azië-Pacific,** **Europa,** **de Verenigde Staten,** **het Verenigd Koninkrijk**en **Japan.** Zodra u een locatie kiest, u uw toepassing later niet naar een andere locatie verplaatsen.

Nadat u alle velden hebt ingevuld, selecteert u **Maken**.

## <a name="manage-existing-iot-central-applications"></a>Bestaande IoT Central-toepassingen beheren

Als u al een Azure IoT Central-toepassing hebt, u deze verwijderen of verplaatsen naar een ander abonnement of brongroep in de Azure-portal.

> [!NOTE]
> U geen toepassingen zien die zijn gemaakt op het gratis prijsplan in de Azure-portal omdat ze niet aan uw abonnement zijn gekoppeld.

Selecteer **Alle bronnen** in de portal om aan de slag te gaan. Selecteer **Verborgen typen weergeven** en typ de naam van uw toepassing in **Filter op naam** om deze te vinden. Selecteer vervolgens de IoT Central-toepassing die u wilt beheren.

Als u naar de toepassing wilt navigeren, selecteert u de **URL van de Centrale toepassing van IoT:**

![Beheerportal: resourcebeheer](media/howto-manage-iot-central-from-portal/image3.png)

Als u de toepassing naar een andere resourcegroep wilt verplaatsen, selecteert u **Wijzigen** naast de resourcegroep. Kies op de pagina **Resources verplaatsen** de resourcegroep waarnaar u deze toepassing wilt verplaatsen:

![Beheerportal: resourcebeheer](media/howto-manage-iot-central-from-portal/image4a.png)

Als u de toepassing naar een ander abonnement wilt verplaatsen, selecteert u **Wijzigen** naast het abonnement. Kies op de pagina **Resources verplaatsen** het abonnement waarnaar u deze toepassing wilt verplaatsen:

![Beheerportal: resourcebeheer](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen vanaf de Azure-portal beheren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)