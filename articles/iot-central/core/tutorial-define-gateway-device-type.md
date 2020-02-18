---
title: Een nieuw type gateway apparaat definiëren in azure IoT Central | Microsoft Docs
description: In deze zelf studie ziet u als een Builder hoe u een nieuw type IoT-gateway apparaat kunt definiëren in uw Azure IoT Central-toepassing.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 8020abf3f8ab153d0143ff50d837ebcfbf5bdfba
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423697"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Een nieuw type IoT-gateway apparaat in uw Azure IoT Central-toepassing definiëren

In deze zelf studie wordt uitgelegd hoe u, als een Builder, een gateway apparaat sjabloon gebruikt om een gateway apparaat te definiëren in uw IoT Central-toepassing. Vervolgens configureert u verschillende downstream-apparaten die verbinding maken met uw IoT Central-toepassing via het gateway apparaat. 

In deze zelf studie maakt u een **Smart** -sjabloon voor het maken van een gateway apparaat. Een **Smart buil ding** gateway-apparaat heeft relaties met andere downstream-apparaten.

![Diagram van relatie tussen gateway apparaat en downstream-apparaten](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Als u downstream-apparaten wilt laten communiceren met uw IoT Central-toepassing, kan een gateway apparaat ook:

* Verzend een eigen telemetrie, zoals de Tempe ratuur.
* Reageren op updates van Beschrijf bare eigenschappen die zijn gemaakt door een operator. Een operator kan bijvoorbeeld het verzend interval van de telemetrie wijzigen.
* Reageren op opdrachten, zoals het opnieuw opstarten van het apparaat.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u [een Azure IOT Central-toepassing maken](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Downstream-apparaatklassen maken

In deze zelf studie wordt gebruikgemaakt van Apparaatbeheer voor een apparaat met een **S1-sensor** en een **RS40-sensor** apparaat om gesimuleerde downstream-apparaten te genereren.

Een sjabloon voor een apparaat maken voor een **S1-sensor** apparaat:

1. Selecteer **Apparaatbeheer**in het linkerdeel venster. Selecteer vervolgens **+** om de sjabloon toe te voegen.

1. Schuif omlaag totdat u de tegel voor het apparaat **S1-sensor** kunt zien. Selecteer de tegel en selecteer vervolgens **volgende: aanpassen**.

1. Selecteer op de pagina **controleren** de optie **maken** om de sjabloon voor het apparaat toe te voegen aan uw toepassing. 

Een sjabloon voor een apparaat maken voor een**RS40-sensor** apparaat:

1. Selecteer **Apparaatbeheer**in het linkerdeel venster. Selecteer vervolgens **+** om de sjabloon toe te voegen.

1. Schuif omlaag totdat u de tegel voor het**RS40-sensor** apparaat kunt zien. Selecteer de tegel en selecteer vervolgens **volgende: aanpassen**.

1. Selecteer op de pagina **controleren** de optie **maken** om de sjabloon voor het apparaat toe te voegen aan uw toepassing. 

U hebt nu Device-sjablonen voor de twee typen downstream-apparaten:

![Device-sjablonen voor downstream-apparaten](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Een gateway-apparaatsjabloon maken

In deze zelf studie maakt u een nieuwe sjabloon voor een gateway apparaat. U kunt deze sjabloon later gebruiken om een gesimuleerd gateway apparaat in uw toepassing te maken.

Een nieuwe sjabloon voor gateway apparaten toevoegen aan uw toepassing:

1. Selecteer **Apparaatbeheer**in het linkerdeel venster. Selecteer vervolgens **+** om de sjabloon toe te voegen.

1. Selecteer op de pagina **sjabloon type selecteren** de tegel **IOT-apparaten** en selecteer vervolgens **volgende: aanpassen**.

1. Selecteer op de pagina **apparaat aanpassen** het selectie vakje voor het **gateway apparaat** .

1. Op de pagina **controleren** selecteert u **maken**. 

1. Voer een **Smart-gateway apparaat** in als de naam van de sjabloon.

1. Selecteer op de pagina **een Mogelijkheidsprofiel maken** de **aangepaste** tegel.

1. Selecteer **+** om een interface toe te voegen.  Kies de **apparaatgegevens** standaard interface.

### <a name="add-relationships"></a>Relaties toevoegen

Vervolgens voegt u relaties toe aan de sjablonen voor de downstream-Apparaatinstellingen:

1. Selecteer in de sjabloon **Smart buil ding gateway-apparaat** de optie **relaties**.

1. Selecteer **+ relatie toevoegen**. Voer een **omgevings sensor** in als weergave naam en selecteer **S1-sensor** als doel.

1. Selecteer **+ relatie opnieuw toevoegen** . Voer een **bezettings sensor** in als weergave naam en selecteer **RS40 bezettings sensor** als doel.

1. Selecteer **Opslaan**.

![Smart buil ding gateway-apparaat sjabloon, relaties weer geven](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Cloud eigenschappen toevoegen

Een sjabloon voor een gateway apparaat kan Cloud eigenschappen bevatten. Cloud eigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

Om Cloud eigenschappen toe te voegen aan de sjabloon **Smart buil ding gateway-apparaat** .

1. Selecteer in de sjabloon **Smart buil ding gateway-apparaat** de optie **Cloud eigenschappen**.

1.  Gebruik de informatie in de volgende tabel om twee Cloud eigenschappen toe te voegen aan de sjabloon voor het gateway-apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | None          | Date   |
    | Klant naam     | None          | Tekenreeks |

2. Selecteer **Opslaan**.

### <a name="create-views"></a>Weergaven aanmaken

Als opbouw functie kunt u de toepassing aanpassen om relevante informatie over het omgevings sensor apparaat weer te geven voor een operator. Met uw aanpassingen kan de operator de omgevings sensor apparaten beheren die zijn verbonden met de toepassing. U kunt twee soorten weer gaven maken voor een operator die u kunt gebruiken om te communiceren met apparaten:

* Formulieren voor het weer geven en bewerken van apparaat-en Cloud eigenschappen.
* Dash boards om apparaten te visualiseren.

De standaard weergaven genereren voor de sjabloon **Smart buil ding gateway-apparaat** :

1. Selecteer in de sjabloon **Smart buil ding gateway-apparaat** de optie **weer gaven**.

1. Selecteer de tegel **Standaard weergaven genereren** en zorg ervoor dat alle opties zijn geselecteerd.

1. Selecteer **standaard dashboard weergave (s) genereren**.

## <a name="publish-the-device-template"></a>De sjabloon voor het apparaat publiceren

Voordat u een gesimuleerd gateway apparaat kunt maken of een echt gateway apparaat verbindt, moet u de sjabloon voor het apparaat publiceren.

De sjabloon van het gateway-apparaat publiceren:

1. Selecteer de sjabloon **Smart buil ding gateway-apparaat** op de pagina met **Apparaatinstellingen** .

2. Selecteer **Publiceren**.

3. Kies in het dialoog venster **een sjabloon voor het publiceren van een apparaat** de optie **publiceren**.

Nadat een apparaat sjabloon is gepubliceerd, wordt deze weer gegeven op de pagina **apparaten** en aan de operator. U kunt in een sjabloon voor een gepubliceerd apparaat geen mogelijkheidsprofiel bewerken zonder een nieuwe versie te maken. U kunt echter updates voor de Cloud eigenschappen, aanpassingen en weer gaven maken in een sjabloon voor een gepubliceerd apparaat. Deze updates zorgen er niet voor dat er een nieuwe versie wordt gemaakt. Nadat u wijzigingen hebt aangebracht, selecteert u **publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="create-the-simulated-devices"></a>De gesimuleerde apparaten maken

Deze zelf studie maakt gebruik van gesimuleerde downstream-apparaten en een gesimuleerd gateway apparaat.

Een gesimuleerd gateway apparaat maken:

1. Selecteer op de pagina **apparaten** de optie **Slim bouwen van gateway apparaat** in de lijst met apparaatprofielen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Behoud de gegenereerde **apparaat-id** en **apparaatnaam**. Zorg ervoor dat de **gesimuleerde** switch is **ingeschakeld**. Selecteer **Maken**.

Een gesimuleerde downstream-apparaten maken:

1. Selecteer op de pagina **apparaten** **RS40 bezetting sensor** in de lijst met apparaatprofielen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Behoud de gegenereerde **apparaat-id** en **apparaatnaam**. Zorg ervoor dat de **gesimuleerde** switch is **ingeschakeld**. Selecteer **Maken**.

1. Selecteer op de pagina **apparaten** **S1-sensor** in de lijst met apparaatprofielen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Behoud de gegenereerde **apparaat-id** en **apparaatnaam**. Zorg ervoor dat de **gesimuleerde** switch is **ingeschakeld**. Selecteer **Maken**.

![Gesimuleerde apparaten in uw toepassing](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Downstream-apparaats relaties toevoegen aan een gateway apparaat

Nu u de gesimuleerde apparaten in uw toepassing hebt, kunt u de relaties tussen de downstream-apparaten en het gateway-apparaat maken:

1. Selecteer op de pagina **apparaten** **S1-sensor** in de lijst met apparaatprofielen en selecteer vervolgens uw gesimuleerde **S1-sensor** apparaat.

1. Selecteer **verbinding maken met Gateway**.

1. Selecteer in het dialoog venster **verbinding maken met een gateway** de sjabloon **Slim bouwen van gateway-apparaat** en selecteer vervolgens het gesimuleerde exemplaar dat u eerder hebt gemaakt.

1. Selecteer **lid worden**.

1. Selecteer op de pagina **apparaten** de optie **RS40 bezetting sensor** in de lijst met apparaatprofielen en selecteer vervolgens het gesimuleerde **sensor** apparaat voor RS40.

1. Selecteer **verbinding maken met Gateway**.

1. Selecteer in het dialoog venster **verbinding maken met een gateway** de sjabloon **Slim bouwen van gateway-apparaat** en selecteer vervolgens het gesimuleerde exemplaar dat u eerder hebt gemaakt.

1. Selecteer **lid worden**.

Uw gesimuleerde downstream-apparaten zijn nu verbonden met uw gesimuleerde gateway apparaat. Als u naar de weer gave **downstream-apparaten** voor uw gateway apparaat navigeert, ziet u de verwante downstream-apparaten:

![Weer gave downstream-apparaten](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selecteer een sjabloon voor het gateway-apparaat en het exemplaar van het gateway apparaat en selecteer **lid worden**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Maak een nieuwe IoT-gateway als een sjabloon voor een apparaat.
* Cloud eigenschappen maken.
* Aanpassingen maken.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* Relaties toevoegen.
* De sjabloon van het apparaat publiceren.

U kunt nu het volgende doen:

> [!div class="nextstepaction"]
> [Een apparaat verbinden](tutorial-connect-pnp-device.md)
