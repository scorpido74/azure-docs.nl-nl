---
title: Quickstart - Azure Kinect DK installeren
description: Deze quickstart biedt instructies voor het installeren van Azure Kinect DK-hardware
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, installatie, hardware, quick, usb, energie, viewer, sensor, streamen, installeren, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211275"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Quickstart: Uw Azure Kinect DK installeren

Deze quickstart biedt hulp bij het installeren van Azure Kinect DK. We laten u zien hoe u de visualisatie van sensorstreams kunt testen en de [Azure Kinect-viewer](azure-kinect-viewer.md) kunt gebruiken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="system-requirements"></a>Systeemvereisten

Raadpleeg de [Systeemvereisten](system-requirements.md) om te controleren of de configuratie van uw host-pc voldoet aan alle minimale vereisten van Azure Kinect DK

## <a name="set-up-hardware"></a>Hardware instellen

> [!NOTE]
> Zorg ervoor dat u het beschermingsfolie van de camera verwijdert, voordat u het apparaat gebruikt.

1. Sluit de stroomkabel aan op de voedingsaansluiting aan de achterkant van het apparaat. Sluit de USB-stroomadapter aan op het andere uiteinde van de kabel, en steek de adapter in een stopcontact.
2. Sluit de USB-gegevenskabel aan op het apparaat, en vervolgens op een USB 3.0-poort op de pc.
   >[!NOTE]
   >Voor de beste resultaten verbindt u de kabel rechtstreeks van het apparaat naar de pc. Vermijd het gebruik van verlengsnoeren of extra adapters in deze verbinding.

3. Controleer of het LED-lampje voor de aan/uit-indicator (naast de USB-kabel) effen wit is.
  
   Het duurt enkele seconden voordat het apparaat stroom krijgt. Het apparaat is klaar voor gebruik wanneer de het LED-lampje voor de streamingindicator aan de voorkant is uitgeschakeld.  

   Raadpleeg [Wat wil het lichtje zeggen?](hardware-specification.md#what-does-the-light-mean) voor meer informatie over het LED-lampje voor de aan/uit-indicator.

    ![Volledige apparaatfuncties](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>De SDK downloaden

1. Selecteer de koppeling om [de SDK te downloaden](sensor-sdk-download.md).
2. Installeer de SDK op de pc.

## <a name="update-firmware"></a>Firmware bijwerken

De SDK heeft de nieuwste versie van de apparaatfirmware nodig om juist te kunnen functioneren. Volg de stappen in [Azure Kinect DK-firmware bijwerken](update-device-firmware.md) om uw firmwareversie te controleren en eventueel bij te werken.

## <a name="verify-that-the-device-streams-data"></a>Controleren of via het apparaat gegevens worden gestreamd

1. Start de [Azure Kinect-viewer](azure-kinect-viewer.md). U kunt dit hulpprogramma starten met een van deze methoden:
   - U kunt de viewer starten vanaf de opdrachtregel of door te dubbelklikken op het uitvoerbare bestand. Het bestand, `k4aviewer.exe`, bevindt zich in de map met SDK-hulpprogramma’s (bijvoorbeeld, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, waarbij `X.Y.Z` de geïnstalleerde versie van de SDK is).
   - U kunt de Azure Kinect-viewer starten vanuit het menu **Start** op het apparaat.
2. Selecteer in de Azure Kinect-viewer achtereenvolgens **Apparaat openen** > **Start**.

    ![Azure Kinect-viewer](./media/quickstarts/viewer.png)

3. Controleer of met het hulpprogramma elke sensorstream wordt gevisualiseerd:
   - Dieptecamera
   - Kleurencamera
   - Infraroodcamera
   - IMU
   - Microfoons

    ![Hulpprogramma voor visualisatie](./media/quickstarts/visualization-tool.png)

U bent klaar met de installatie van Azure Kinect DK. Nu kunt u beginnen met de ontwikkeling van uw toepassing of de integratie van services.

Ga naar [Problemen oplossen](troubleshooting.md) als u problemen ondervindt.

## <a name="see-also"></a>Zie ook

- [Azure Kinect DK-hardwaregegevens](hardware-specification.md)
- [Apparaatfirmware bijwerken](update-device-firmware.md)
- Meer informatie over de [Azure Kinect-viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Volgende stappen

Nadat Azure Kinect DK is geïnstalleerd en werkt, kunt u ook het volgende leren:
> [!div class="nextstepaction"]
> [Sensorstreams vastleggen in een bestand](record-sensor-streams-file.md)
