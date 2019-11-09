---
title: Een gesimuleerd apparaat toevoegen aan Azure IoT Central | Microsoft Docs
description: Een sjabloon voor een apparaat maken en een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 8b6720af6328242a1fe297e60c9d413d0b83f3d2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893821"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Snelstartgids: een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing (preview-functies)

*Dit artikel is van toepassing op operators, opbouwfuncties en beheerders.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Een sjabloon voor een apparaat definieert de mogelijkheden van een apparaat dat verbinding maakt met uw IoT Central-toepassing. Mogelijkheden zijn onder andere telemetrie die het apparaat verzendt, eigenschappen van het apparaat en de opdrachten waarop een apparaat reageert. Vanuit een apparaatprofiel kan een Builder of operator zowel echte als gesimuleerde apparaten toevoegen aan een toepassing. Gesimuleerde apparaten zijn handig voor het testen van het gedrag van uw IoT Central-toepassing voordat u echte apparaten verbindt.

In deze Quick Start maakt u een sjabloon voor **omgevings sensors** en voegt u een gesimuleerd apparaat toe. Een omgevings sensor apparaat:

* Verzendt telemetrie, zoals Tempe ratuur.
* Rapporteert apparaatspecifieke eigenschappen, zoals het helderheids niveau.
* Reageert op opdrachten zoals inschakelen en uitschakelen.
* Hiermee worden algemene apparaateigenschappen gerapporteerd, zoals de firmware versie en het serie nummer.

## <a name="prerequisites"></a>Vereisten

Voltooi de Snelstartgids [een Azure IOT Central-toepassing maken (preview-functies)](./quick-deploy-iot-central.md) om een IOT Central toepassing te maken met behulp van de sjabloon **aangepaste app > preview-toepassing** .

U hebt ook een lokale kopie van het **EnvironmentalSensorInline. capabilitymodel. json** -bestand nodig dat de [IOT Plug en Play](../../iot-pnp/overview-iot-plug-and-play.md) device capability model bevat. U kunt deze [hier](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)downloaden. Klik met de rechter muisknop op de pagina en selecteer **Opslaan als**.

Nadat u het bestand hebt gedownload, opent u het in een tekst editor en vervangt u de twee exemplaren van `<YOUR_COMPANY_NAME_HERE>` door uw naam. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen.

## <a name="create-a-template"></a>Een sjabloon maken

Als ontwerper kunt u sjablonen voor apparaten maken en bewerken in uw toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u een gesimuleerd apparaat genereren of echte apparaten verbinden die de sjabloon voor het apparaat implementeren. Met gesimuleerde apparaten kunt u het gedrag van uw toepassing testen voordat u een echt apparaat verbindt.

Als u een nieuwe apparaatprofiel wilt toevoegen aan uw toepassing, gaat u naar de pagina met **Apparaatinstellingen** . Hiertoe selecteert u het tabblad **device templates** in het linkerdeel venster.

![De pagina Apparaatsjablonen](./media/quick-create-pnp-device/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Een mogelijkheidsprofiel toevoegen

Er zijn verschillende opties voor het ontwerpen van een hulp model voor het maken van een apparaat in IoT Central. U kunt ervoor kiezen om zelf een aangepast model te maken, uit een bestand te importeren, te selecteren in de-catalogus of een IoT-Plug en Play apparaat te verbinden via een apparaat-eerste verbinding waarbij het functionaliteits model van het apparaat is gepubliceerd in de open bare opslag plaats. In deze zelf studie importeert u een mogelijkheidsprofiel vanuit een bestand.

De volgende stappen laten zien hoe u het capaciteits model voor een **omgevings sensor** kunt importeren. Met deze apparaten worden telemetrie, zoals de Tempe ratuur, naar uw toepassing verzonden:

1. Als u een nieuwe sjabloon voor het apparaat wilt toevoegen, selecteert u **+** op de pagina met **Apparaatinstellingen** .

1. Kies **IOT-apparaat** in de lijst met aangepaste sjablonen, selecteer **volgende: aanpassen**, selecteer **volgende: controleren**en selecteer vervolgens **maken**.

1. Voer een **omgevings sensor** in als de naam van de sjabloon voor het apparaat.

1. Kies **model importeren** om een nieuw mogelijkheidsprofiel te maken op basis van een JSON-bestand. Navigeer naar de map waar u het bestand **EnvironmentalSensorInline. capabilitymodel. json** hebt opgeslagen op de lokale computer. Selecteer het bestand **EnvironmentalSensorInline. capabilitymodel. json** en selecteer vervolgens **openen**. Het model voor de omgevings sensor bevat de interfaces voor **omgevings sensors** en **apparaatgegevens** :

    ![Functionaliteits model voor omgevings sensor apparaten](./media/quick-create-pnp-device/newdevicecapabilitymodel.png)

    Met deze interfaces worden de mogelijkheden van een **omgevings sensor** apparaat gedefinieerd. De mogelijkheden omvatten de telemetrie die een apparaat verzendt, de eigenschappen van een apparaat en de opdrachten waarop een apparaat reageert.

### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** en vervolgens **+ eigenschap Cloud toevoegen**. Gebruik de informatie in de volgende tabel om een Cloud eigenschap toe te voegen aan de sjabloon van uw apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Klant naam     | Geen          | Tekenreeks |

1. Selecteer **Opslaan** om uw wijzigingen op te slaan:

    ![Eigenschappen van Cloud](media/quick-create-pnp-device/cloudproperties.png)

## <a name="create-views"></a>Weer gaven maken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

### <a name="generate-default-views"></a>Standaard weergaven genereren

Het genereren van standaard weergaven is een snelle manier om aan de slag te gaan met het visualiseren van uw belang rijke apparaatgegevens. U kunt Maxi maal drie standaard weergaven genereren voor de sjabloon voor uw apparaat:

* Met de weer gave **opdrachten** kan de operator opdrachten verzenden naar uw apparaat.
* In de **overzichts** weergave worden grafieken en metrische gegevens gebruikt om de telemetrie van apparaten weer te geven.
* In de weer gave **about** worden apparaateigenschappen weer gegeven.

Selecteer **weer gaven** en **Genereer vervolgens standaard weergaven**.

### <a name="configure-a-view-to-visualize-devices"></a>Een weer gave configureren om apparaten te visualiseren

Met een dash board van een apparaat kan een operator een apparaat visualiseren met behulp van grafieken en metrische gegevens. Als systeembouwer kunt u bepalen welke informatie in een apparaatdashboard wordt weergegeven. U kunt meerdere Dash boards voor apparaten definiëren. Als u een dash board wilt maken om de telemetrie van de omgevings sensor te visualiseren, selecteert u **weer gaven** en vervolgens **het apparaat visualiseren**:

1. Alle eigenschappen van het apparaat, eigenschappen van de Cloud, telemetrie en statische opties worden weer gegeven onder **Eigenschappen**. U kunt deze items slepen en neerzetten in de weer gave. Sleep de eigenschap **helderheids niveau** naar de weer gave. U kunt de tegel configureren met het tandwiel pictogram.

1. Als u een grafiek wilt toevoegen waarin telemetrie wordt getekend, selecteert u **vochtigheid** en **Tempe ratuur**en selecteert u vervolgens **combi neren**. Als u dit diagram wilt weer geven in een andere indeling, zoals een cirkel diagram of staaf diagram, selecteert u de knop **visualisatie wijzigen** boven aan de tegel.

1. Selecteer **Opslaan** om uw weer gave op te slaan:

U kunt meer tegels toevoegen waarin andere eigenschappen of telemetrie-waarden worden weer gegeven. U kunt ook statische tekst, koppelingen en afbeeldingen toevoegen. Als u een tegel op het dash board wilt verplaatsen of verg Roten of verkleinen, verplaatst u de muis aanwijzer over de tegel en sleept u de tegel naar een nieuwe locatie of wijzigt u het formaat ervan.

### <a name="add-a-device-form"></a>Een formulier van een apparaat toevoegen

Met een formulier van een apparaat kan een operator Beschrijf bare apparaateigenschappen en eigenschappen van Clouds bewerken. Als opbouw functie kunt u meerdere formulieren definiëren en kiezen welk apparaat en welke Cloud eigenschappen op elk formulier worden weer gegeven. U kunt ook eigenschappen voor alleen-lezen apparaten weer geven in een formulier.

Een formulier maken voor het weer geven en bewerken van eigenschappen van de omgevings sensor:

1. Navigeer naar **weer gaven** in de sjabloon voor de **omgevings sensor** . Selecteer de tegel **apparaat en Cloud gegevens bewerken** om een nieuwe weer gave toe te voegen.

1. Voer de naam van de **omgevings sensor**van het formulier in.

1. Sleep de **naam** van de klant en de **laatste service datum** -Cloud naar de bestaande sectie op het formulier.

1. Selecteer het **helderheids niveau** **en de apparaateigenschappen.** Selecteer vervolgens **sectie toevoegen**. Bewerk de titel van de sectie in de **sensor eigenschappen**. Selecteer **Toepassen**.

1. Selecteer het **apparaat model**, de **Software versie**, de **fabrikant**en de apparaateigenschappen van de **Processor fabrikant** . Selecteer vervolgens **sectie toevoegen**. Bewerk de titel van de sectie in eigenschappen van het **apparaat**. Selecteer **Toepassen**.

1. Selecteer **Opslaan** om de weer gave op te slaan.

## <a name="publish-device-template"></a>Device-sjabloon publiceren

Voordat u een gesimuleerde omgevings sensor kunt maken, of als u een echte omgevings sensor wilt verbinden, moet u de sjabloon voor het apparaat publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

1. Selecteer **Publiceren**.

1. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**:

    ![Gepubliceerd model](media/quick-create-pnp-device/publishedmodel.png)

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter wel updates maken voor de eigenschappen, aanpassingen en weer gaven van de cloud in een sjabloon voor gepubliceerde apparaten zonder versie beheer. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="add-a-simulated-device"></a>Een gesimuleerd apparaat toevoegen

Als u een gesimuleerd apparaat wilt toevoegen aan uw toepassing, gebruikt u de sjabloon voor de **omgevings sensor** die u hebt gemaakt.

1. Als u een nieuw apparaat wilt toevoegen als een operator, kiest u **apparaten** in het linkerdeel venster. Op het tabblad **apparaten** worden **alle apparaten** en de sjabloon voor de **omgevings sensor** van het apparaat weer gegeven. Selecteer **omgevings sensor**.

1. Selecteer **+ Nieuw**om een gesimuleerd omgevings sensor apparaat toe te voegen. Gebruik de voorgestelde **apparaat-id** of geef uw eigen kleine **apparaat-id**op. U kunt ook een naam voor het nieuwe apparaat opgeven. Schakel de **gesimuleerde** wissel knop in **op aan** en selecteer vervolgens **maken**.

    ![Gesimuleerd apparaat](./media/quick-create-pnp-device/simulated-device.png)

Nu kunt u communiceren met de weer gaven die zijn gemaakt door de opbouw functie voor de apparaatprofiel met gesimuleerde gegevens.

## <a name="use-a-simulated-device-to-improve-views"></a>Een gesimuleerd apparaat gebruiken om weer gaven te verbeteren

Nadat u een nieuw gesimuleerd apparaat hebt gemaakt, kan de Builder dit apparaat gebruiken om de weer gaven voor de apparaatprofiel te verbeteren en te bouwen.

1. Kies **Apparaatbeheer** in het linkerdeel venster en selecteer de sjabloon **omgevings sensor** .

1. Selecteer een van de weer gaven die u wilt bewerken of maak een nieuwe weer gave. Klik op **Preview-apparaat configureren**en **Selecteer vervolgens een apparaat dat wordt uitgevoerd**. Hier kunt u kiezen tussen het gebruik van geen preview-apparaat en een echt apparaat dat u kunt configureren voor het testen of van een bestaand apparaat dat u hebt toegevoegd aan IoT Central.

1. Kies uw gesimuleerde apparaat in de lijst. Selecteer vervolgens **Toep assen**. U ziet nu hetzelfde gesimuleerde apparaat in de sjabloon weergaven voor het maken van een apparaat. Deze weer gave is handig voor grafieken en andere visualisaties.

    ![Preview-apparaat configureren](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een **omgevings** sjabloon voor een computer sensor maakt en een gesimuleerd apparaat toevoegt aan uw toepassing.

Ga naar de Snelstartgids voor meer informatie over het bewaken van apparaten die zijn verbonden met uw toepassing:

> [!div class="nextstepaction"]
> [Regels en acties configureren](./quick-configure-rules.md)
