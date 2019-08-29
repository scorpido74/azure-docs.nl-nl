---
title: Een nieuw apparaattype definiëren in Azure IoT Central | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u, als ontwerper, een nieuw apparaattype maakt in uw Azure IoT Central-toepassing. U definieert de telemetrie, de status, de eigenschappen en de opdrachten voor uw type.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 660b2ead146695657ae13444cb7936eff8224f3a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099519"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Zelfstudie: Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Deze zelfstudie laat zien hoe u als bouwer een apparaatsjabloon kunt gebruiken om een nieuw apparaattype te definiëren in uw Microsoft Azure IoT Central-toepassing. Met een apparaatprofiel worden de mogelijkheden van uw apparaat gedefinieerd. Mogelijkheden zijn onder andere telemetrie die het apparaat verzendt, eigenschappen van het apparaat en de opdrachten waarop een apparaat reageert.

In deze zelf studie maakt u een **omgevings sensor** -apparaat sjabloon. Een omgevings sensor apparaat:

* Verzendt telemetrie, zoals Tempe ratuur.
* Rapporteert apparaatspecifieke eigenschappen, zoals het helderheids niveau.
* Reageert op opdrachten zoals inschakelen en uitschakelen.
* Hiermee worden algemene apparaateigenschappen gerapporteerd, zoals de firmware versie en het serie nummer.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een nieuwe sjabloon voor het apparaat.
> * Importeer een mogelijkheidsprofiel.
> * Cloud eigenschappen maken.
> * Definieer een visualisatie voor de telemetrie van het apparaat.
> * De sjabloon van het apparaat publiceren.
> * Een gesimuleerd apparaat maken voor de sjabloon voor uw apparaat.
> * Bekijk uw gesimuleerde apparaat.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure IoT Central-toepassing nodig om deze zelfstudie te voltooien. Als u de snelstart [Een Azure IoT Central-toepassing maken](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) hebt voltooid, kunt u de toepassing die u in de snelstart hebt gemaakt, weer gebruiken. Voer anders de volgende stappen uit om een lege Azure IoT Central-toepassing te maken:

1. Ga naar de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) .

1. Als u een Azure-abonnement hebt, meldt u zich aan met de referenties die u gebruikt om het te openen. u kunt het ook ondertekenen met een Microsoft-account:

    ![Voer uw organisatieaccount in](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing**.

Een nieuwe Azure IoT Central-toepassing maken die gebruikmaakt van preview-functies, waaronder IoT-Plug en Play:

1. Kies **Proef**. U hebt geen Azure-abonnement nodig om een proef toepassing te maken.

    Zie de [snelstart over het maken van een toepassing](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)voor meer informatie over directory's en abonnementen.

1. Kies **Preview-toepassing**.

1. U kunt desgewenst een beschrijvende naam voor de toepassing kiezen, zoals **Contoso-airconditioners**. Azure IoT Central genereert een uniek URL-voorvoegsel voor u. U kunt dit URL-voorvoegsel wijzigen in iets dat gemakkelijker te onthouden is.

1. Als u een proef toepassing maakt, moet u uw contact gegevens opgeven.

1. Selecteer **Maken**.

    ![Pagina Toepassing maken van Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

U hebt ook een lokale kopie van het **EnvironmentalSensorInline. capabilitymodel. json** -bestand nodig dat de [IOT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) device capability model bevat. U kunt deze [hier](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)downloaden. Klik met de rechter muisknop op de pagina en selecteer **Opslaan als**.

Nadat u het bestand hebt gedownload, opent u het in een tekst editor en vervangt u de twee `<YOUR_COMPANY_NAME_HERE>` exemplaren van met uw naam. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen.

## <a name="create-a-template"></a>Een sjabloon maken

Als ontwerper kunt u sjablonen voor apparaten maken en bewerken in uw toepassing. Nadat u een apparaataccount hebt gepubliceerd, kunt u een gesimuleerd apparaat genereren of echte apparaten verbinden die de sjabloon voor het apparaat implementeren. Met gesimuleerde apparaten kunt u het gedrag van uw toepassing testen voordat u een echt apparaat verbindt.

Als u een nieuwe apparaatprofiel wilt toevoegen aan uw toepassing, gaat u naar de pagina met **Apparaatinstellingen** . Hiertoe selecteert u het tabblad **Apparaatinstellingen** in het navigatie menu aan de linkerkant.

![De pagina Apparaatsjablonen](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Een mogelijkheidsprofiel toevoegen

Er zijn verschillende opties voor het ontwerpen van een hulp model voor het maken van een apparaat in IoT Central. U kunt ervoor kiezen om zelf een aangepast model te maken, uit een bestand te importeren, te selecteren in de-catalogus of een IoT-Plug en Play apparaat te verbinden via een apparaat-eerste verbinding waarbij het functionaliteits model van het apparaat is gepubliceerd in de open bare opslag plaats. In deze zelf studie importeert u een mogelijkheidsprofiel vanuit een bestand.

De volgende stappen laten zien hoe u het capaciteits model voor een **omgevings sensor** kunt importeren. Met deze apparaten worden telemetrie, zoals de Tempe ratuur, naar uw toepassing verzonden:

1. Als u een nieuwe sjabloon voor het apparaat wilt toevoegen, selecteert u **+ Nieuw** op de pagina **Apparaatinstellingen** .

1. Kies **aangepast** in de lijst met sjablonen.

1. Voer een **omgevings sensor** in als de naam van de sjabloon voor het apparaat.

1. Kies **model importeren** om een nieuw mogelijkheidsprofiel te maken op basis van een JSON-bestand. Navigeer naar de map waar u het bestand **EnvironmentalSensorInline. capabilitymodel. json** hebt opgeslagen op de lokale computer. Selecteer het bestand **EnvironmentalSensorInline. capabilitymodel. json** en selecteer vervolgens **openen**. Het model voor de omgevings sensor bevat de interfaces voor omgevings sensors en **apparaatgegevens** :

    ![Functionaliteits model voor omgevings sensor apparaten](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Met deze interfaces worden de mogelijkheden van een **omgevings sensor** apparaat gedefinieerd. De mogelijkheden omvatten de telemetrie die een apparaat verzendt, de eigenschappen van een apparaat en de opdrachten waarop een apparaat reageert.

### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

1. Selecteer **Cloud eigenschappen** en vervolgens **+ eigenschap Cloud toevoegen**. Gebruik de informatie in de volgende tabel om een Cloud eigenschap toe te voegen aan de sjabloon van uw apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Klantnaam     | Geen          | Tekenreeks |

1. Selecteer **Opslaan** om uw wijzigingen op te slaan:

    ![Eigenschappen van Cloud](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Aanpassingen toevoegen

Gebruik aanpassingen wanneer u een interface moet wijzigen of IoT Central-specifieke functies wilt toevoegen aan een mogelijkheid waarvoor u geen versie hoeft te hebben van uw apparaat. U kunt velden aanpassen wanneer het capaciteits model zich in een concept of gepubliceerde status bevindt. U kunt alleen velden aanpassen die de interface compatibiliteit niet verstoren. U kunt bijvoorbeeld:

- Pas de weergave naam en de eenheden van een mogelijkheid aan.
- Een standaard kleur toevoegen die moet worden gebruikt wanneer de waarde in een grafiek wordt weer gegeven.
- Geef de initiële, minimale en maximale waarde voor een eigenschap op.

U kunt de naam of het type van de mogelijkheid niet aanpassen.


## <a name="create-views"></a>Weer gaven maken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

### <a name="generate-default-views"></a>Standaard weergaven genereren

Het genereren van standaard weergaven is een snelle manier om aan de slag te gaan met het visualiseren van uw belang rijke apparaatgegevens. U kunt Maxi maal drie standaard weergaven genereren voor de sjabloon voor uw apparaat:

* Met de weer gave **opdrachten** kan de operator opdrachten verzenden naar uw apparaat.
* In de overzichts weergave worden grafieken en metrische gegevens gebruikt om de telemetrie van apparaten weer te geven.
* In de weer gave **about** worden apparaateigenschappen weer gegeven.

Nadat u **Standaard weergaven genereren**hebt geselecteerd, worden ze automatisch toegevoegd onder het gedeelte **weer gaven** van de sjabloon voor uw apparaat.

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

1. Selecteer het **helderheids niveau** en de apparaateigenschappen. Selecteer vervolgens **sectie toevoegen**. Bewerk de titel van de sectie in de **sensor eigenschappen**. Selecteer **Toepassen**.

1. Selecteer het **apparaat model**, de **Software versie**, de **fabrikant**en de apparaateigenschappen van de **Processor fabrikant** . Selecteer vervolgens **sectie toevoegen**. Bewerk de titel van de sectie in eigenschappen van het **apparaat**. Selecteer **Toepassen**.

1. Selecteer **Opslaan** om de weer gave op te slaan.

## <a name="publish-device-template"></a>Device-sjabloon publiceren

Voordat u een gesimuleerde omgevings sensor kunt maken, of als u een echte omgevings sensor wilt verbinden, moet u de sjabloon voor het apparaat publiceren.

Een sjabloon voor een apparaat publiceren:

1. Ga op de pagina **Apparaatinstellingen** naar uw sjabloon.

1. Selecteer **Publiceren**.

1. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**:

    ![Gepubliceerd model](media/tutorial-define-device-type-pnp/publishedmodel.png)

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter wel updates maken voor de eigenschappen, aanpassingen en weer gaven van de cloud in een sjabloon voor gepubliceerde apparaten zonder versie beheer. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een nieuwe apparaatsjabloon maken
* Importeer een mogelijkheidsprofiel.
* Cloud eigenschappen maken.
* Aanpassingen maken.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* De sjabloon van het apparaat publiceren.

Nu u een sjabloon voor een apparaat in uw Azure IoT Central-toepassing hebt gemaakt, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Apparaat toevoegen](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
