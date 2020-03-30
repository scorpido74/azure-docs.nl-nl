---
title: Snelstart - Een gesimuleerd apparaat toevoegen aan Azure IoT Central
description: Met deze quickstart ziet u hoe u een apparaatsjabloon maakt en een gesimuleerd apparaat toevoegt aan uw IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169647"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Snelstart: een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

Een apparaatsjabloon definieert de mogelijkheden van een apparaat dat verbinding maakt met uw IoT Central-toepassing. Mogelijkheden omvatten telemetrie die het apparaat verzendt, apparaateigenschappen en de opdrachten waarop een apparaat reageert. Vanuit een apparaatsjabloon kan een bouwer of operator zowel echte als gesimuleerde apparaten aan een toepassing toevoegen. Gesimuleerde apparaten zijn handig voor het testen van het gedrag van uw IoT Central-toepassing voordat u echte apparaten aansluit.

In deze quickstart voegt u een apparaatsjabloon toe voor een *MXChip IoT DevKit-bord* en maakt u een gesimuleerd apparaat. Om deze quickstart te voltooien heb je geen echt apparaat nodig, je werkt met een simulatie van het apparaat. Een DevKit-apparaat:

* Hiermee verzendt u telemetrie zoals temperatuur.
* Rapporteert apparaatspecifieke eigenschappen, zoals helderheidsniveau.
* Reageert op opdrachten zoals in- en uitschakelen.
* Rapporteert algemene apparaateigenschappen zoals firmwareversie en serienummer.

## <a name="prerequisites"></a>Vereisten

Voltooi de [snel start--toepassing Een Azure IoT Central](./quick-deploy-iot-central.md) maken om een IoT Central-toepassing te maken met de **aangepaste app > aangepaste toepassingssjabloon.**

## <a name="create-a-template"></a>Een sjabloon maken

Als bouwer u apparaatsjablonen maken en bewerken in uw IoT Central-toepassing. Nadat u een apparaatsjabloon hebt gepubliceerd, u gesimuleerdapparaat genereren of echte apparaten verbinden met de apparaatsjabloon. Met gesimuleerde apparaten u het gedrag van uw toepassing testen voordat u een echt apparaat aansluit.

Als u een nieuwe apparaatsjabloon aan uw toepassing wilt toevoegen, selecteert u het tabblad **Apparaatsjablonen** in het linkerdeelvenster.

![De pagina Apparaatsjablonen](./media/quick-create-pnp-device/devicedefinitions.png)

Een apparaatsjabloon bevat een apparaatcapaciteitsmodel dat de telemetrie definieert die het apparaat verzendt, apparaateigenschappen en de opdrachten waarop het apparaat reageert.

### <a name="add-a-device-capability-model"></a>Een apparaatcapaciteitsmodel toevoegen

Er zijn verschillende opties voor het toevoegen van een apparaatcapaciteitsmodel aan uw IoT Central-toepassing. U een model helemaal opnieuw maken, een model importeren uit een bestand of een apparaat selecteren in de apparaatcatalogus. IoT Central ondersteunt ook een *apparaat-first* benadering waarbij het automatisch een model importeert uit een opslagplaats wanneer een apparaat voor de eerste keer verbinding maakt. In deze quickstart kiest u een apparaat uit de apparaatcatalogus om het apparaatcapaciteitsmodel te importeren.

In de volgende stappen ziet u hoe u de apparaatcatalogus gebruiken om het capaciteitsmodel voor een **MXChip IoT DevKit-apparaat** te importeren. Deze apparaten sturen telemetrie, zoals temperatuur, naar uw toepassing:

1. Als u een nieuwe **+** apparaatsjabloon wilt toevoegen, selecteert u op de pagina **Apparaatsjablonen.**

1. Schuif op de pagina **Sjabloontype selecteren** naar beneden totdat u de **MXChip IoT DevKit-tegel** vindt.

1. Selecteer de **MXChip IoT DevKit-tegel** en selecteer **Volgende: Aanpassen**.

1. Selecteer **op** de pagina Controleren de optie **Maken**.

1. Na een paar seconden u de nieuwe apparaatsjabloon zien:

    ![MXChip IoT DevKit-apparaatsjabloon](./media/quick-create-pnp-device/devkit-template.png)

    Het MXChip IoT DevKit-mogelijkheidsmodel bevat interfaces zoals **mxchip_sensor,** **mxchip_settings**en **apparaatinformatie.** Interfaces definiëren de mogelijkheden van een MXChip IoT DevKit-apparaat. Mogelijkheden omvatten de telemetrie die een apparaat verzendt, de eigenschappen die een apparaat rapporteert en de opdrachten waarop een apparaat reageert.

### <a name="add-cloud-properties"></a>Cloudeigenschappen toevoegen

Een apparaatsjabloon kan cloudeigenschappen bevatten. Cloudeigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud-eigenschappen** en voeg **+ Eigenschap Cloud toe**. Gebruik de informatie in de volgende tabel om twee cloudeigenschappen toe te voegen aan uw apparaatsjabloon:

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Naam van de klant     | Geen          | Tekenreeks |

1. Selecteer **Opslaan** om uw wijzigingen op te slaan:

    ![Cloudeigenschappen](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Weergaven

Als bouwer u de toepassing aanpassen om relevante informatie over het apparaat weer te geven aan een operator. Met uw aanpassingen kan de operator de apparaten beheren die met de toepassing zijn verbonden. U twee typen weergaven maken die een operator kan gebruiken om met apparaten te communiceren:

* Formulieren om apparaat- en cloudeigenschappen weer te geven en te bewerken.
* Dashboards om apparaten te visualiseren, inclusief de telemetrie die ze verzenden.

### <a name="default-views"></a>Standaardweergaven

Standaardweergaven zijn een snelle manier om aan de slag te gaan met het visualiseren van uw belangrijke apparaatgegevens. U maximaal drie standaardweergaven genereren voor uw apparaatsjabloon:

* Met de weergave **Opdrachten** kunnen de operator opdrachten naar uw apparaat verzenden.
* In **de overzichtsweergave** worden grafieken en statistieken gebruikt om apparaattelemetrie weer te geven.
* In de **weergave Informatie** worden apparaateigenschappen weergegeven.

Selecteer het knooppunt **Weergaven** in de apparaatsjabloon. U zien dat IoT Central een **overzicht** en een **informatie-weergave** voor u heeft gegenereerd wanneer u de sjabloon hebt toegevoegd.

Ga als u een nieuw **apparaatformulier beheren** toe dat een operator kan gebruiken om het apparaat te beheren:

1. Selecteer het knooppunt **Weergaven** en selecteer vervolgens het bewerkingsapparaat en de **tegel cloudgegevens** om een nieuwe weergave toe te voegen.

1. Wijzig de formuliernaam in **Apparaat beheren**.

1. Selecteer de **cloudeigenschappen Klantnaam** en **Laatste servicedatum** en de eigenschap **Fansnelheid.** Selecteer vervolgens **Sectie Toevoegen:**

    ![Nieuw formulier maken](media/quick-create-pnp-device/new-form.png)

1. Selecteer **Opslaan** om uw nieuwe formulier op te slaan.

## <a name="publish-device-template"></a>Apparaatsjabloon publiceren

Voordat u een gesimuleerd apparaat maken of een echt apparaat aansluiten, moet u uw apparaatsjabloon publiceren. Hoewel IoT Central de sjabloon heeft gepubliceerd toen u de sjabloon voor het eerst maakte, moet u de bijgewerkte versie publiceren.

Ga als lid van het werk om een apparaatsjabloon te publiceren:

1. Ga naar de apparaatsjabloon op de pagina **Apparaatsjablonen.**

1. Selecteer **Publiceren:**

    ![Gepubliceerd model](media/quick-create-pnp-device/publishedmodel.png)

1. Selecteer **Publiceren** in de sjabloon Dit **Publish**apparaat publiceren in het dialoogvenster toepassing . 

Nadat u een apparaatsjabloon hebt gepubliceerd, is deze zichtbaar op de pagina **Apparaten.** In een gepubliceerde apparaatsjabloon u een apparaatcapaciteitsmodel niet bewerken zonder een nieuwe versie te maken. U echter updates maken voor cloudeigenschappen, aanpassingen en weergaven in een gepubliceerde apparaatsjabloon zonder versiebeheer. Nadat u wijzigingen hebt aangebracht, selecteert u **Publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="add-a-simulated-device"></a>Een gesimuleerd apparaat toevoegen

Als u een gesimuleerd apparaat aan uw toepassing wilt toevoegen, gebruikt u de **mxchip IoT DevKit-apparaatsjabloon** die u hebt gemaakt.

1. Als u een nieuw apparaat als operator wilt toevoegen, kiest u **Apparaten** in het linkerdeelvenster. Het tabblad **Apparaten** toont **Alle apparaten** en de **MXChip IoT DevKit-apparaatsjabloon.** Selecteer **MXChip IoT DevKit**.

1. Als u een gesimuleerd DevKit-apparaat wilt toevoegen, selecteert u **+**. Gebruik de voorgestelde **apparaat-id** of voer uw eigen kleine **apparaat-id in**. U ook een naam voor uw nieuwe apparaat invoeren. Controleer of de **geschakelde optie Gesimuleerd** is **ingeschakeld** en selecteer **Vervolgens Maken**.

    ![Gesimuleerd apparaat](./media/quick-create-pnp-device/simulated-device.png)

Nu u communiceren met de weergaven die door de bouwer voor de apparaatsjabloon zijn gemaakt met gesimuleerde gegevens:

1. Selecteer uw gesimuleerde apparaat op de pagina **Apparaten.**

1. De **overzichtsweergave** toont een plot van de gesimuleerde telemetrie:

    ![Overzichtsweergave](./media/quick-create-pnp-device/simulated-telemetry.png)

1. In de weergave **Informatie** worden eigenschapswaarden weergegeven, inclusief de cloudeigenschappen die u aan de weergave hebt toegevoegd.

1. **Met de** weergave Opdrachten u opdrachten uitvoeren, zoals **knipperen** op het apparaat.

1. De weergave **Apparaten beheren** is het formulier dat u hebt gemaakt voor de operator om het apparaat te beheren.

## <a name="use-a-simulated-device-to-improve-views"></a>Een gesimuleerd apparaat gebruiken om weergaven te verbeteren

Nadat u een nieuw gesimuleerd apparaat hebt gemaakt, kan de bouwer dit apparaat gebruiken om de weergaven voor de apparaatsjabloon te blijven verbeteren en voort te bouwen.

1. Kies **Apparaatsjablonen** in het linkerdeelvenster en selecteer de **MXChip IoT DevKit-sjabloon.**

1. Selecteer een van de weergaven die u wilt bewerken of maak een nieuwe weergave. Selecteer **Voorbeeldapparaat configureren**en **selecteer vervolgens Selecteren vanaf een lopend apparaat**. Hier u ervoor kiezen om geen preview-apparaat, een echt apparaat dat is geconfigureerd voor tests of een bestaand apparaat dat u in IoT Central hebt toegevoegd, te hebben.

1. Kies uw gesimuleerde apparaat in de lijst. Selecteer vervolgens **Toepassen**. Nu u hetzelfde gesimuleerde apparaat zien in de bouwervaring van uw apparaatsjabloon. Deze weergave is handig voor grafieken en andere visualisaties.

    ![Voorbeeldapparaat configureren](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een **MXChip IoT DevKit-apparaatsjabloon** maakt en een gesimuleerd apparaat aan je toepassing toevoegt.

Ga voor meer informatie over bewakingsapparaten die zijn aangesloten op uw toepassing door naar de werkbalk Snel aan:

> [!div class="nextstepaction"]
> [Regels en acties configureren](./quick-configure-rules.md)
