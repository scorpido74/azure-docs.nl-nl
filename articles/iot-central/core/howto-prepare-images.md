---
title: Afbeeldingen uploaden naar uw Azure IoT Central-toepassing | Microsoft Docs
description: Als ontwerper leert u hoe u installatie kopieën voorbereidt en uploadt naar uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d8a863ee971fa154740f3726034fc4604b02fd34
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949922"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Installatie kopieën voorbereiden en uploaden naar uw Azure IoT Central-toepassing

In dit artikel wordt beschreven hoe u als een opbouw functie uw Azure IoT Central-toepassing kunt aanpassen door aangepaste installatie kopieën te uploaden. U kunt bijvoorbeeld een dash board van een apparaat aanpassen met een afbeelding van het apparaat.

## <a name="before-you-begin"></a>Voordat u begint

U hebt het volgende nodig om de stappen in dit artikel uit te voeren:

1. Een Azure IoT Central-toepassing. Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
1. Een hulp programma voor het schalen en aanpassen van de grootte van afbeeldings bestanden.

## <a name="choose-where-to-use-custom-images"></a>Kies waar u aangepaste installatie kopieën wilt gebruiken

U kunt aangepaste installatie kopieën toevoegen aan de volgende locaties in een Azure IoT Central-toepassing:

* De pagina **mijn toepassingen**

    ![Afbeelding op de pagina Toepassings beheer](media/howto-prepare-images/applicationmanager.png)

* Het toepassings dashboard

    ![Afbeelding op het toepassings dashboard](media/howto-prepare-images/homepage.png)

* Een sjabloon voor een apparaat

    ![Afbeelding in de sjabloon voor het apparaat](media/howto-prepare-images/devicetemplate.png)

* Een tegel op een dash board van een apparaat

    ![Afbeelding op de tegel apparaat](media/howto-prepare-images/devicetile.png)

* Een tegel op een apparaat instellen dash board

    ![Afbeelding op de tegel apparaat instellen](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>De installatie kopieën voorbereiden

Op alle vier de locaties kunt u PNG-, GIF-of JPEG-afbeeldingen gebruiken.

De volgende tabel bevat een overzicht van de afbeeldings grootten die u kunt gebruiken:

| Locatie | Grootten |
| -------- | ------ |
| Toepassings beheer | 268x160 PX |
| Apparaatprofiel | 64x64 PX |
| Dashboard tegels | De tegel kleinste grootte is 200x200 px, grotere tegels kunnen vier Kante of rechthoekige veelvouden van kleine tegels zijn. Bijvoorbeeld 200x400 px, 400x200 px of 400x400 PX |

Voor de beste weer gave in de toepassing moet u installatie kopieën maken die overeenkomen met de in de vorige tabel weer gegeven dimensies.

## <a name="upload-the-images"></a>De installatie kopieën uploaden

In de volgende secties wordt beschreven hoe u de installatie kopieën op de verschillende locaties uploadt:

### <a name="application-manager"></a>Toepassings beheer

Als u een afbeelding wilt uploaden voor gebruik op de pagina **mijn toepassingen** , gaat u naar de pagina **Toepassings instellingen** in de sectie **beheer** . U moet een beheerder zijn om deze taak te volt ooien:

![Toepassings installatie kopie uploaden](media/howto-prepare-images/uploadapplicationmanager.png)

Selecteer de tegel **toepassings installatie kopie** om een installatie kopie (268x160 px) te uploaden van uw lokale computer.

### <a name="application-dashboard"></a>Toepassingsdashboard

Als u een installatie kopie op het toepassings dashboard wilt uploaden, gaat u naar de pagina **dash board** van uw toepassing en selecteert u **bewerken**. U moet een Builder zijn om deze taak te volt ooien:

![Dashboard afbeelding uploaden](media/howto-prepare-images/uploadhomepage.png)

Selecteer onder **installatie kopie configureren**de tegel **installatie kopie** om een installatie kopie te uploaden vanaf uw lokale computer. De tegel kleinste grootte is 200x200 px, grotere tegels kunnen vier Kante of rechthoekige veelvouden van kleine tegels zijn. Bijvoorbeeld 200x400 px, 400x200 px of 400x400 px.

**Sla** de geüploade afbeelding op. U kunt de grootte wijzigen in de bewerkings modus. Selecteer **gereed** wanneer u klaar bent.

### <a name="device-template"></a>Apparaatprofiel

Als u een installatie kopie op een apparaatprofiel wilt uploaden, gaat u naar **apparaatprofielen** en kiest u de sjabloon voor het apparaat. U moet een Builder zijn om deze taak te volt ooien:

![Afbeelding van sjabloon voor uploaden van apparaat](media/howto-prepare-images/uploaddevicetemplate.png)

Selecteer de tegel installatie kopie om een afbeelding (64x64 px) te uploaden van uw lokale computer.

### <a name="device-dashboard"></a>Apparaatdashboard

Als u een installatie kopie op een dash board van een apparaat wilt uploaden, gaat u naar **apparaatprofielen** en kiest u de sjabloon voor het apparaat. Klik vervolgens op het tabblad **dash board** . U moet een Builder zijn om deze taak te volt ooien:

![Dashboard afbeelding van het dash board uploaden](media/howto-prepare-images/uploaddevicedashboard.png)

Selecteer onder **installatie kopie configureren**de tegel **installatie kopie** en kies vervolgens het bestand dat u wilt uploaden vanaf uw lokale computer. De tegel kleinste grootte is 200x200 px, grotere tegels kunnen vier Kante of rechthoekige veelvouden van kleine tegels zijn. Bijvoorbeeld 200x400 px, 400x200 px of 400x400 px.

**Sla** de geüploade afbeelding op. U kunt de grootte van het formaat wijzigen en deze verplaatsen in de bewerkings modus. Selecteer **gereed** wanneer u klaar bent.

### <a name="device-set-dashboard"></a>Dash board voor apparaat instellen

Als u een installatie kopie op een apparaat voor het instellen van apparaten wilt uploaden, gaat u naar **Apparaatsets** en kiest u de apparaatset en vervolgens een apparaat. Kies vervolgens de pagina **dash board** en selecteer **bewerken**:

![Dashboard afbeelding van de apparaatset uploaden](media/howto-prepare-images/uploaddevicesetdashboard.png)

Selecteer onder **installatie kopie configureren**de tegel **installatie kopie** om een installatie kopie te uploaden vanaf uw lokale computer. De tegel kleinste grootte is 200x200 px, grotere tegels kunnen vier Kante of rechthoekige veelvouden van kleine tegels zijn. Bijvoorbeeld 200x400 px, 400x200 px of 400x400 px.

**Sla** de geüploade afbeelding op. U kunt de grootte van het formaat wijzigen en deze verplaatsen in de bewerkings modus. Selecteer **gereed** wanneer u klaar bent.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u installatie kopieën voorbereidt en uploadt naar uw Azure IoT Central-toepassing, zijn dit de voorgestelde volgende stappen:

* [De Azure IoT Central-gebruikers interface aanpassen](./howto-customize-ui.md)
* [Tegels toevoegen aan uw dash board](./howto-add-tiles-to-your-dashboard.md)
* [Apparaten beheren in uw Azure IoT Central-toepassing](howto-manage-devices.md)
