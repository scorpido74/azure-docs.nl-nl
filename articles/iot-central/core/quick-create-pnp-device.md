---
title: 'Quick Start: een gesimuleerd apparaat toevoegen aan Azure IoT Central'
description: In deze Quick start ziet u hoe u een apparaatprofiel maakt en een gesimuleerd apparaat toevoegt aan uw IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169647"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Snelstartgids: een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Een sjabloon voor een apparaat definieert de mogelijkheden van een apparaat dat verbinding maakt met uw IoT Central-toepassing. Mogelijkheden zijn onder andere telemetrie die het apparaat verzendt, eigenschappen van het apparaat en de opdrachten waarop een apparaat reageert. Vanuit een apparaatprofiel kan een Builder of operator zowel echte als gesimuleerde apparaten toevoegen aan een toepassing. Gesimuleerde apparaten zijn handig voor het testen van het gedrag van uw IoT Central-toepassing voordat u echte apparaten verbindt.

In deze Quick Start voegt u een apparaataccount toe voor een *MXChip IOT DevKit* (DevKit)-bord en maakt u een gesimuleerd apparaat. Voor het volt ooien van deze Snelstartgids hebt u geen echt apparaat nodig, werkt u met een simulatie van het apparaat. Een DevKit-apparaat:

* Verzendt telemetrie, zoals Tempe ratuur.
* Rapporteert apparaatspecifieke eigenschappen, zoals het helderheids niveau.
* Reageert op opdrachten zoals inschakelen en uitschakelen.
* Hiermee worden algemene apparaateigenschappen gerapporteerd, zoals de firmware versie en het serie nummer.

## <a name="prerequisites"></a>Vereisten

Voltooi de Snelstartgids [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md) om een IOT Central toepassing te maken met behulp van de aangepaste **app > aangepaste toepassings** sjabloon.

## <a name="create-a-template"></a>Een sjabloon maken

Als ontwerper kunt u sjablonen voor apparaten maken en bewerken in uw IoT Central-toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u een gesimuleerd apparaat genereren of echte apparaten verbinden met behulp van de sjabloon. Met gesimuleerde apparaten kunt u het gedrag van uw toepassing testen voordat u een echt apparaat verbindt.

Als u een nieuwe apparaatprofiel wilt toevoegen aan uw toepassing, selecteert u het tabblad **device templates** in het linkerdeel venster.

![De pagina Apparaatsjablonen](./media/quick-create-pnp-device/devicedefinitions.png)

Een sjabloon voor een apparaat bevat een mogelijkheidsprofiel waarmee de telemetrie wordt gedefinieerd die het apparaat verzendt, de apparaateigenschappen en de opdrachten waarop het apparaat reageert.

### <a name="add-a-device-capability-model"></a>Een mogelijkheidsprofiel toevoegen

Er zijn verschillende opties voor het toevoegen van een functionaliteits model voor apparaten aan uw IoT Central-toepassing. U kunt een volledig nieuw model maken, een model importeren uit een bestand of een apparaat uit de catalogus van het apparaat selecteren. IoT Central biedt ook ondersteuning voor een *apparaat-eerste* benadering waarbij een model automatisch wordt geïmporteerd uit een opslag plaats wanneer een apparaat voor de eerste keer verbinding maakt. In deze Quick Start kiest u een apparaat uit de catalogus met apparaten om het functionaliteits model van het apparaat te importeren.

De volgende stappen laten zien hoe u de catalogus met apparaten kunt gebruiken om het mogelijkheidsprofiel te importeren voor een **MXChip IOT DevKit** -apparaat. Met deze apparaten worden telemetrie, zoals de Tempe ratuur, naar uw toepassing verzonden:

1. Als u een nieuwe sjabloon voor het apparaat wilt toevoegen, selecteert u **+** op de pagina met **Apparaatinstellingen** .

1. Schuif op de pagina **sjabloon type selecteren** omlaag totdat u de tegel **MXChip IOT DevKit** vindt.

1. Selecteer de tegel **MXChip IOT DevKit** en selecteer vervolgens **volgende: aanpassen**.

1. Op de pagina **controleren** selecteert u **maken**.

1. Na een paar seconden ziet u de nieuwe sjabloon voor het apparaat:

    ![MXChip IoT DevKit-Apparaatbeheer](./media/quick-create-pnp-device/devkit-template.png)

    Het MXChip IoT DevKit-functionaliteits model bevat interfaces zoals **mxchip_sensor**, **mxchip_settings**en **apparaatgegevens**. Interfaces definiëren de mogelijkheden van een MXChip IoT DevKit-apparaat. De mogelijkheden omvatten de telemetrie die een apparaat verzendt, de eigenschappen van een apparaat en de opdrachten waarop een apparaat reageert.

### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** en vervolgens **+ eigenschap Cloud toevoegen**. Gebruik de informatie in de volgende tabel om twee Cloud eigenschappen toe te voegen aan de sjabloon van het apparaat:

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | None          | Date   |
    | Klant naam     | None          | Tekenreeks |

1. Selecteer **Opslaan** om uw wijzigingen op te slaan:

    ![Eigenschappen van Cloud](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Weergaven

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het apparaat weer te geven in een operator. Met uw aanpassingen kunt u de operator gebruiken om de apparaten te beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren met inbegrip van de telemetrie die ze verzenden.

### <a name="default-views"></a>Standaard weergaven

Standaard weergaven zijn een snelle manier om aan de slag te gaan met het visualiseren van uw belang rijke apparaatgegevens. U kunt Maxi maal drie standaard weergaven genereren voor de sjabloon voor uw apparaat:

* Met de weer gave **opdrachten** kan de operator opdrachten verzenden naar uw apparaat.
* In de **overzichts** weergave worden grafieken en metrische gegevens gebruikt om de telemetrie van apparaten weer te geven.
* In de weer gave **about** worden apparaateigenschappen weer gegeven.

Selecteer het knoop punt **weer gaven** in de sjabloon voor het apparaat. U kunt zien dat IoT Central een **overzicht** heeft gegenereerd en **hoe** u er een weer gave van hebt gemaakt wanneer u de sjabloon hebt toegevoegd.

Een nieuw formulier voor het beheren van een **apparaat** toevoegen dat een operator kan gebruiken om het apparaat te beheren:

1. Selecteer het knoop punt **weer gaven** en selecteer vervolgens de tegel **apparaat en Cloud gegevens bewerken** om een nieuwe weer gave toe te voegen.

1. Wijzig de naam van het formulier om het **apparaat te beheren**.

1. Selecteer de **naam** van de klant en de **laatste service datum** Cloud eigenschappen en de eigenschap voor de **ventilator snelheid** . Selecteer vervolgens **sectie toevoegen**:

    ![Nieuw formulier maken](media/quick-create-pnp-device/new-form.png)

1. Selecteer **Opslaan** om het nieuwe formulier op te slaan.

## <a name="publish-device-template"></a>Device-sjabloon publiceren

Voordat u een gesimuleerd apparaat kunt maken of een echt apparaat verbindt, moet u de sjabloon voor het apparaat publiceren. Hoewel IoT Central de sjabloon heeft gepubliceerd toen u deze voor het eerst maakte, moet u de bijgewerkte versie publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

1. Selecteer **publiceren**:

    ![Gepubliceerd model](media/quick-create-pnp-device/publishedmodel.png)

1. Selecteer **publiceren**in het dialoog venster **sjabloon voor dit apparaat publiceren naar de toepassing** . 

Nadat u een sjabloon voor het apparaat hebt gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** . U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter wel updates maken voor de eigenschappen, aanpassingen en weer gaven van de cloud in een sjabloon voor gepubliceerde apparaten zonder versie beheer. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="add-a-simulated-device"></a>Een gesimuleerd apparaat toevoegen

Als u een gesimuleerd apparaat wilt toevoegen aan uw toepassing, gebruikt u de **MXChip IOT DevKit** -apparaat sjabloon die u hebt gemaakt.

1. Als u een nieuw apparaat wilt toevoegen als een operator, kiest u **apparaten** in het linkerdeel venster. Op het tabblad **apparaten** worden **alle apparaten** en de **MXChip IOT DevKit** -Apparaatbeheer weer gegeven. Selecteer **MXChip IOT DevKit**.

1. Selecteer **+** om een gesimuleerd DevKit-apparaat toe te voegen. Gebruik de voorgestelde **apparaat-id** of geef uw eigen kleine **apparaat-id**op. U kunt ook een naam voor het nieuwe apparaat opgeven. Zorg ervoor dat de **gesimuleerde** wissel knop is **ingeschakeld** en selecteer **maken**.

    ![Gesimuleerd apparaat](./media/quick-create-pnp-device/simulated-device.png)

U kunt nu met gesimuleerde gegevens werken met de weer gaven die zijn gemaakt door de opbouw functie voor de apparaatprofiel:

1. Selecteer uw gesimuleerde apparaat op de pagina **apparaten** .

1. In de **overzichts** weergave ziet u een grafiek van de gesimuleerde telemetrie:

    ![Overzichts weergave](./media/quick-create-pnp-device/simulated-telemetry.png)

1. De weer gave about bevat eigenschaps waarden, met inbegrip **van** de Cloud eigenschappen die u hebt toegevoegd aan de weer gave.

1. Met de weer gave **opdrachten** kunt u opdrachten uitvoeren, zoals het **knip peren** op het apparaat.

1. De weer gave **apparaten beheren** is het formulier dat u voor de operator hebt gemaakt om het apparaat te beheren.

## <a name="use-a-simulated-device-to-improve-views"></a>Een gesimuleerd apparaat gebruiken om weer gaven te verbeteren

Nadat u een nieuw gesimuleerd apparaat hebt gemaakt, kan de Builder dit apparaat gebruiken om de weer gaven voor de apparaatprofiel te verbeteren en te bouwen.

1. Kies **Apparaatbeheer** in het linkerdeel venster en selecteer de sjabloon **MXChip IOT DevKit** .

1. Selecteer een van de weer gaven die u wilt bewerken of maak een nieuwe weer gave. Selecteer **Preview-apparaat configureren**en **Selecteer vervolgens een apparaat dat wordt uitgevoerd**. Hier kunt u ervoor kiezen om geen preview-apparaat, een echt apparaat dat is geconfigureerd voor testen of een bestaand apparaat dat u hebt toegevoegd aan IoT Central.

1. Kies uw gesimuleerde apparaat in de lijst. Selecteer vervolgens **Toep assen**. U ziet nu hetzelfde gesimuleerde apparaat in de sjabloon weergaven voor het maken van een apparaat. Deze weer gave is handig voor grafieken en andere visualisaties.

    ![Preview-apparaat configureren](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een **MXChip IOT DevKit** -apparaatprofiel maakt en een gesimuleerd apparaat toevoegt aan uw toepassing.

Ga naar de Snelstartgids voor meer informatie over het bewaken van apparaten die zijn verbonden met uw toepassing:

> [!div class="nextstepaction"]
> [Regels en acties configureren](./quick-configure-rules.md)
