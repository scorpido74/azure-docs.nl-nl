---
title: Apparaatsets gebruiken in uw Azure IoT Central-toepassing | Microsoft Docs
description: Als operator kunt u de apparaatsets gebruiken in uw Azure IoT Central-toepassing.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 87bbab041405a085d405707ff419fbad55fdcd09
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952756"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Apparaatsets gebruiken in uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe, als operator, Apparaatsets moet gebruiken in uw Azure IoT Central-toepassing.

Een apparaatset is een lijst met apparaten die zijn gegroepeerd, omdat deze voldoen aan bepaalde criteria. Met apparaatsets kunt u apparaten op schaal beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U kunt bijvoorbeeld een apparaatset maken voor het weer geven van alle airconditioning apparaten in Seattle, zodat een technicus kan zoeken naar de apparaten waarvoor ze verantwoordelijk zijn. In dit artikel leest u hoe u Apparaatsets kunt maken en configureren.

## <a name="create-a-device-set"></a>Een apparaatset maken

Een apparaatset maken:

1. Kies **Apparaatsets** in het linkerdeel venster.

1. Selecteer **+ Nieuw**.

    ![Nieuwe apparaatset](media/howto-use-device-sets/image1.png)

1. Geef een naam op voor het apparaat dat uniek is voor de gehele toepassing. U kunt ook een beschrijving toevoegen. Een apparaatset kan alleen apparaten uit een sjabloon van één apparaat bevatten. Kies de apparaatprofiel die u voor deze set wilt gebruiken.

1. Maak de query om de apparaten te identificeren voor het apparaat dat is ingesteld door een eigenschap, een vergelijkings operator en een waarde te selecteren. U kunt meerdere query's en apparaten toevoegen die voldoen aan **alle** criteria in de apparaatset worden geplaatst. De apparaatset die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatset kan bekijken, wijzigen of verwijderen.

    ![Query voor het instellen van een apparaat](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > De set met apparaten is een dynamische query. Telkens wanneer u de lijst met apparaten bekijkt, zijn er mogelijk andere apparaten in de lijst. De lijst is afhankelijk van de apparaten die momenteel voldoen aan de criteria van de query.

1. Kies **Opslaan**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Het dash board voor uw apparaatset configureren

Nadat u de apparaatset hebt gemaakt, kunt u het **dash board**configureren. Het **dash board** is de introductie pagina waar u afbeeldingen en koppelingen plaatst. U kunt ook rasters toevoegen met een lijst met apparaten in de apparaatset.

1. Kies **Apparaatsets** in het linkerdeel venster.

1. Selecteer uw apparaatset.

1. Selecteer het tabblad **Dashboard**.

1. Selecteer **Bewerken**.

    ![Ontwerp modus op](media/howto-use-device-sets/image3.png)

1. Zie [installatie kopieën voorbereiden en uploaden naar uw Azure IOT Central-toepassing](howto-prepare-images.md)voor meer informatie over het toevoegen van een installatie kopie.

1. Een koppelings tegel toevoegen:
    1. Kies **koppeling** in het rechterdeel venster.
    1. Geef uw koppeling een **titel**.
    1. Kies een URL die moet worden geopend wanneer de koppeling wordt geselecteerd.
    1. Geef uw koppeling een beschrijving die onder de **titel**wordt weer gegeven.
    1. Kies **Opslaan**.

        ![Koppeling opslaan](media/howto-use-device-sets/image7.png)

    1. U kunt de koppelings tegel op het **dash board**verplaatsen en het formaat ervan wijzigen.

1. Voeg een raster toe. Een raster is een tabel met apparaten in de apparaatset met de kolommen die u kiest.
    1. Kies **raster** in het rechterdeel venster.
    1. Geef uw raster een **titel**.
    1. Selecteer de kolommen die moeten worden weer gegeven door **toevoegen/verwijderen**te kiezen. In het deel venster dat verschijnt, kiest u de kolom die u wilt weer geven en klikt u op de pijl-rechts om deze te selecteren.
    1. Kies **OK**.
    1. Kies **Opslaan**.

        ![Raster opslaan](media/howto-use-device-sets/image9.png)

    1. Sleep het raster en zet het neer om het op het **dash board**te plaatsen.

        > [!NOTE]
        > U kunt meerdere installatie kopieën, koppelingen en rasters toevoegen.
  
    1. Selecteer **Done**.

Zie [Dashboard tegels gebruiken](howto-use-tiles.md)voor meer informatie over het gebruik van tegels in azure IOT Central.

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Een locatie kaart configureren in het dash board sets van uw apparaat

U kunt een kaart toevoegen aan de visualiseren locatie van de apparaten in uw apparaatset.

Als u een kaart wilt toevoegen aan het dash board van uw apparaat sets, moet u een locatie meting of locatie-eigenschap in uw apparaatprofiel hebben geconfigureerd. Zie [een locatie meting maken](howto-set-up-template.md) of [een locatie-eigenschap maken](howto-set-up-template.md)voor meer informatie.

1. Selecteer in het **dash board**van uw apparaat instellen de optie **kaart** uit de bibliotheek.
2. Voeg een titel toe en kies de locatie meting of eigenschap die u eerder hebt geconfigureerd.
3. Selecteer **Opslaan** en de kaart tegel worden de laatste bekende locaties van de apparaten in de apparaatset weer gegeven.
4. Wanneer een operator het dash board Apparaatsets weergeeft, ziet de operator alle tegels die u hebt geconfigureerd, met inbegrip van de locatie kaart.

U kunt de grootte van de kaart tegel op het dash board wijzigen. Als u een pincode selecteert op de kaart, worden de apparaatgegevens, de naam en de locatie weer gegeven. Selecteer de pop-up om naar de eigenschappen pagina van het apparaat te gaan.

## <a name="configure-the-list-for-your-device-set"></a>De lijst voor de apparaatset configureren

Nadat u de apparaatset hebt gemaakt, kunt u de **lijst**configureren. De **lijst** bevat alle apparaten in de set apparaten in een tabel met de kolommen die u kiest.

1. Kies **Apparaatsets** in het linkerdeel venster.

1. Kies het tabblad **lijst** .

1. Kies **kolomopties**.

    ![Kolom opties](media/howto-use-device-sets/image11.png)

1. Selecteer de kolommen die moeten worden weer gegeven door de kolom te selecteren die u wilt weer geven en kies de pijl-rechts om deze te selecteren.

    ![Kolom kiezen](media/howto-use-device-sets/image12.png)

1. Kies **OK**.

## <a name="analytics"></a>Analyse

De analyses in apparaatsets zijn hetzelfde als het tabblad hoofd analyse in het linkerdeel venster. Meer informatie over analyses vindt u in het artikel over het [maken van analyses](howto-use-device-sets.md).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Apparaatsets kunt gebruiken in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrie-regels maken](howto-create-telemetry-rules.md)
