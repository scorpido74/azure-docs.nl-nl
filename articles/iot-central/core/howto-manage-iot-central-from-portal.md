---
title: IoT Central beheren via de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassingen kunt maken en beheren via de Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1e206d9a1b14fc8ff1b92e4eb7217824d81212cf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990115"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>IoT Central beheren via de Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , kunt u de [Azure Portal](https://portal.azure.com) gebruiken om uw toepassingen te beheren.

## <a name="create-iot-central-applications"></a>IoT Central-toepassingen maken

Als u een toepassing wilt maken, gaat u naar de [Azure Portal](https://ms.portal.azure.com) en selecteert u **een resource maken** in het hoofd venster aan de linkerkant.

![Beheer Portal: navigatie menu](media/howto-manage-iot-central-from-portal/image0.png)

Typ **IOT Central**in de zoek balk.

![Beheer Portal: zoeken](media/howto-manage-iot-central-from-portal/image0a1.png)

Selecteer het regel item **IOT Central toepassing** in de zoek resultaten.

![Beheerportal: Zoek resultaten](media/howto-manage-iot-central-from-portal/image0b1.png)

Selecteer nu **Maken**.

![Beheer Portal: IoT Central resource](media/howto-manage-iot-central-from-portal/image0c1.png)

Vul alle velden in het formulier in. Dit formulier is vergelijkbaar met het formulier dat u invult om toepassingen te maken op de [Azure IOT Central Application Manager](https://aka.ms/iotcentral) -website. Zie de Snelstartgids [een IOT Central-toepassing maken](quick-deploy-iot-central.md) voor meer informatie.

U kunt IoT Central toepassing maken met algemeen beschik bare functies door een **verouderde toepassing** te selecteren als toepassings sjabloon, alle andere toepassings sjablonen gebruiken open bare preview-functies.

![IoT Central formulier maken](media/howto-manage-iot-central-from-portal/image6a.png)

**Locatie** is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Normaal gesp roken kiest u de locatie die zich het dichtst in de buurt van uw apparaten bevindt om optimaal te pres teren. Azure IoT Central is momenteel beschikbaar in de **Verenigde Staten**, **Australië**, **Azië en Stille Oceaan**of in **Europa**.  Wanneer u een locatie hebt gekozen, kunt u de toepassing later niet meer naar een andere locatie verplaatsen.

> [!NOTE]
> De preview-toepassings sjablonen zijn momenteel alleen beschikbaar in de locaties **Europa** en **Verenigde Staten** .


Nadat u alle velden hebt ingevuld, selecteert u **maken**.

## <a name="manage-existing-iot-central-applications"></a>Bestaande IoT Central-toepassingen beheren

Als u al een Azure IoT Central-toepassing hebt, kunt u deze verwijderen of verplaatsen naar een ander abonnement of een andere resource groep in de Azure Portal.

> [!NOTE]
> U kunt geen toepassingen zien die zijn gemaakt in het prijs plan gratis in de Azure Portal omdat deze niet zijn gekoppeld aan uw abonnement.

Als u aan de slag wilt gaan, selecteert u **alle resources** in het hoofd venster aan de linkerkant. Gebruik het zoekvak om de naam van uw toepassing te typen om deze te zoeken in de lijst met resources. Selecteer vervolgens de IoT Central toepassing die u wilt beheren.

![Beheer Portal: resource beheer](media/howto-manage-iot-central-from-portal/image2a.png)

Als u naar de toepassing wilt navigeren, selecteert u de IoT Central toepassings-URL.

![Beheer Portal: resource beheer](media/howto-manage-iot-central-from-portal/image3.png)

Als u de toepassing wilt verplaatsen naar een andere resource groep, selecteert u **wijzigen** naast de resource groep. Kies op de pagina **resources verplaatsen** de resource groep waar u deze toepassing naartoe wilt migreren.

![Beheer Portal: resource beheer](media/howto-manage-iot-central-from-portal/image4a.png)

Als u de toepassing naar een ander abonnement wilt verplaatsen, selecteert u de **wijzigings** koppeling naast het abonnement. Kies het abonnement waarnaar u deze toepassing wilt migreren in het dialoog venster dat wordt weer gegeven.

![Beheer Portal: resource beheer](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen kunt beheren vanuit de Azure Portal, volgt u de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)