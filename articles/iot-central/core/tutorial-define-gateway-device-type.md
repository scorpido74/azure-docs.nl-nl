---
title: Een nieuw gatewayapparaattype definiëren in Azure IoT Central | Microsoft Documenten
description: In deze zelfstudie ziet u als bouwer hoe u een nieuw IoT-gateway-apparaattype definieert in uw Azure IoT Central-toepassing.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7bb386d39f53331e77bee4d863c431c2e3655f5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79298832"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Een nieuw IoT-gatewayapparaattype definiëren in uw Azure IoT Central-toepassing

In deze zelfstudie ziet u als bouwer hoe u een sjabloon voor gateway-apparaten gebruikt om een gateway-apparaat in uw IoT Central-toepassing te definiëren. Vervolgens configureert u verschillende downstream-apparaten die verbinding maken met uw IoT Central-toepassing via het gateway-apparaat. 

In deze zelfstudie maakt u een sjabloon **voor het slimme gebouw-gatewayapparaat.** Een **Smart Building-gatewayapparaat** heeft relaties met andere downstream-apparaten.

![Diagram van de relatie tussen gatewayapparaat en downstream-apparaten](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Naast het inschakelen van downstream-apparaten om te communiceren met uw IoT Central-toepassing, kan een gateway-apparaat ook:

* Stuur zijn eigen telemetrie, zoals temperatuur.
* Reageren op op e-schrijfbare eigenschapsupdates van een operator. Een operator kan bijvoorbeeld het interval voor telemetrieverzenden gewijzigd.
* Reageer op opdrachten, zoals het opnieuw opstarten van het apparaat.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, moet u [een Azure IoT Central-toepassing maken.](./quick-deploy-iot-central.md)

## <a name="create-downstream-device-templates"></a>Downstreamapparaatsjablonen maken

Deze zelfstudie maakt gebruik van apparaatsjablonen voor een **S1-sensorapparaat** en een **RS40 Occupancy Sensor-apparaat** om gesimuleerde downstream-apparaten te genereren.

Ga als lid van het apparaat om een apparaatsjabloon voor een **S1-sensorapparaat** te maken:

1. Selecteer **apparaatsjablonen**in het linkerdeelvenster . Selecteer **+** vervolgens om de sjabloon toe te voegen.

1. Schuif naar beneden totdat u de tegel voor het **S1-sensorapparaat** zien. Selecteer de tegel en selecteer **Volgende: Aanpassen**.

1. Selecteer **op** de pagina Controleren de optie **Maken** om de apparaatsjabloon aan uw toepassing toe te voegen. 

Ga als lid van het apparaat om een apparaatsjabloon te maken voor een ***RS40-bezettingssensor:**

1. Selecteer **apparaatsjablonen**in het linkerdeelvenster . Selecteer **+** vervolgens om de sjabloon toe te voegen.

1. Scroll naar beneden totdat u de tegel voor het apparaat ***RS40 Occupancy Sensor** zien. Selecteer de tegel en selecteer **Volgende: Aanpassen**.

1. Selecteer **op** de pagina Controleren de optie **Maken** om de apparaatsjabloon aan uw toepassing toe te voegen. 

U hebt nu apparaatsjablonen voor de twee downstream-apparaattypen:

![Apparaatsjablonen voor downstream-apparaten](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Een gateway-apparaatsjabloon maken

In deze zelfstudie maakt u vanaf het begin een apparaatsjabloon voor een gatewayapparaat. U gebruikt deze sjabloon later om een gesimuleerd gateway-apparaat in uw toepassing te maken.

Ga als lid van het volgende over een nieuwe sjabloon voor het gatewayapparaat naar uw toepassing:

1. Selecteer **apparaatsjablonen**in het linkerdeelvenster . Selecteer **+** vervolgens om de sjabloon toe te voegen.

1. Selecteer op de pagina **Sjabloontype selecteren** de tegel **IoT-apparaat** en selecteer **Volgende: Aanpassen**.

1. Schakel **op** de pagina Apparaat aanpassen het selectievakje **Gateway-apparaat** in.

1. Selecteer **op** de pagina Controleren de optie **Maken**. 

1. Voer **het gatewayapparaat Voor slim bouwen** in als de naam van de sjabloon.

1. Selecteer op de pagina **Een optiemodel maken** de tegel **Aangepast.**

1. Selecteer **+** om een interface toe te voegen.  Kies de standaardinterface **apparaatgegevens.**

### <a name="add-relationships"></a>Relaties toevoegen

Vervolgens voegt u relaties toe aan de sjablonen voor de downstream-apparaatsjablonen:

1. Selecteer **Relaties**in de sjabloon **Voor het bewerken van gateway-apparaten** .

1. Selecteer **+ Relatie toevoegen**. Voer **de omgevingssensor** in als weergavenaam en selecteer **S1-sensor** als doel.

1. Selecteer + Relatie opnieuw **toevoegen.** Voer **de bezettingssensor** in als weergavenaam en selecteer **rs40-bezettingssensor** als doel.

1. Selecteer **Opslaan**.

![Sjabloon voor smartbuilding-gatewayapparaten, waarin relaties worden weergegeven](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Cloudeigenschappen toevoegen

Een sjabloon voor gateway-apparaten kan cloudeigenschappen bevatten. Cloudeigenschappen bestaan alleen in de IoT Central-toepassing en worden nooit verzonden naar of ontvangen van een apparaat.

Cloudeigenschappen toevoegen aan de sjabloon **Smart Building-gateway-apparaat.**

1. Selecteer **cloudeigenschappen**in de sjabloon **Voor het bewerken van gateway-apparaten** .

1.  Gebruik de informatie in de volgende tabel om twee cloudeigenschappen toe te voegen aan de sjabloon gateway-apparaat.

    | Weergavenaam      | Semantisch type | Schema |
    | ----------------- | ------------- | ------ |
    | Laatste servicedatum | Geen          | Date   |
    | Naam van de klant     | Geen          | Tekenreeks |

2. Selecteer **Opslaan**.

### <a name="create-views"></a>Weergaven maken

Als bouwer u de toepassing aanpassen om relevante informatie over het milieusensorapparaat aan een operator weer te geven. Met uw aanpassingen kan de operator de milieusensorapparaten beheren die op de toepassing zijn aangesloten. U twee typen weergaven maken die een operator kan gebruiken om met apparaten te communiceren:

* Formulieren om apparaat- en cloudeigenschappen weer te geven en te bewerken.
* Dashboards om apparaten te visualiseren.

Ga als u de standaardweergaven voor de sjabloon **Smart Building-gateway-apparaat** genereren:

1. Selecteer **Weergaven**in de sjabloon **Voor het gatewayapparaat voor slim bouwen** .

1. Selecteer **De tegel Standaardweergaven genereren** en controleer of alle opties zijn geselecteerd.

1. Selecteer **Standaarddashboardweergave(s) genereren**.

## <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat u een gesimuleerd gatewayapparaat maken of een echt gateway-apparaat aansluiten, moet u uw apparaatsjabloon publiceren.

Ga als een voorbeeld van het gatewayapparaat:

1. Selecteer de sjabloon **voor het slimme gebouw-gatewayapparaat** op de pagina **Apparaatsjablonen.**

2. Selecteer **Publiceren**.

3. Kies Publiceren in het dialoogvenster **Een apparaatsjabloon publiceren** de optie **Publiceren**.

Nadat een apparaatsjabloon is gepubliceerd, is deze zichtbaar op de pagina **Apparaten** en op de operator. In een gepubliceerde apparaatsjabloon u een apparaatcapaciteitsmodel niet bewerken zonder een nieuwe versie te maken. U echter updates maken voor cloudeigenschappen, aanpassingen en weergaven in een gepubliceerde apparaatsjabloon. Deze updates zorgen er niet voor dat er een nieuwe versie wordt gemaakt. Nadat u wijzigingen hebt aangebracht, selecteert u **Publiceren** om deze wijzigingen naar uw operator te pushen.

## <a name="create-the-simulated-devices"></a>De gesimuleerde apparaten maken

Deze zelfstudie maakt gebruik van gesimuleerde downstream-apparaten en een gesimuleerd gateway-apparaat.

Ga als lid van het nieuwe gatewayapparaat als lid van het gatewayapparaat:

1. Selecteer **op** de pagina Apparaten de optie **Smart Building-gatewayapparaat** in de lijst met apparaatsjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Houd de gegenereerde **apparaat-id** en **de naam van het apparaat**. Controleer of de **gesimuleerde** schakelaar is **ingeschakeld.** Selecteer **Maken**.

Ga als lid van het werk om een gesimuleerde downstream-apparaten te maken:

1. Selecteer **rs40-bezettingssensor** op de pagina **Apparaten** in de lijst met apparaatsjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Houd de gegenereerde **apparaat-id** en **de naam van het apparaat**. Controleer of de **gesimuleerde** schakelaar is **ingeschakeld.** Selecteer **Maken**.

1. Selecteer **op** de pagina Apparaten de optie **S1-sensor** in de lijst met apparaatsjablonen.

1. Selecteer **+** om een nieuw apparaat toe te voegen.

1. Houd de gegenereerde **apparaat-id** en **de naam van het apparaat**. Controleer of de **gesimuleerde** schakelaar is **ingeschakeld.** Selecteer **Maken**.

![Gesimuleerde apparaten in uw toepassing](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Downstream-apparaatrelaties toevoegen aan een gatewayapparaat

Nu u de gesimuleerde apparaten in uw toepassing hebt, u de relaties tussen de downstream-apparaten en het gateway-apparaat maken:

1. Selecteer **op** de pagina Apparaten **de s1-sensor** in de lijst met apparaatsjablonen en selecteer vervolgens het gesimuleerde **S1-sensorapparaat.**

1. Selecteer **Verbinding maken met gateway**.

1. Selecteer in het dialoogvenster **Verbinding maken met een gateway** de sjabloon voor het slim bouwen van het **gateway-apparaat** en selecteer vervolgens de gesimuleerde instantie die u eerder hebt gemaakt.

1. Selecteer **Deelnemen**.

1. Selecteer **op** de pagina Apparaten **rs40-bezettingssensor** in de lijst met apparaatsjablonen en selecteer vervolgens het gesimuleerde **RS40-apparaat voor bezettingssensor.**

1. Selecteer **Verbinding maken met gateway**.

1. Selecteer in het dialoogvenster **Verbinding maken met een gateway** de sjabloon voor het slim bouwen van het **gateway-apparaat** en selecteer vervolgens de gesimuleerde instantie die u eerder hebt gemaakt.

1. Selecteer **Deelnemen**.

Beide gesimuleerde downstream-apparaten zijn nu verbonden met uw gesimuleerde gateway-apparaat. Als u naar de weergave **Downstream-apparaten** voor uw gateway-apparaat navigeert, u de gerelateerde downstream-apparaten zien:

![Downstream-apparaten bekijken](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selecteer een sjabloon voor gatewayapparaten en een instantie voor gateway-apparaten en selecteer **Join**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

* Maak een nieuwe IoT-gateway als apparaatsjabloon.
* Cloudeigenschappen maken.
* Maak aanpassingen.
* Definieer een visualisatie voor de telemetrie van het apparaat.
* Relaties toevoegen.
* Publiceer uw apparaatsjabloon.

> [!NOTE]
> VS Code gebaseerde code generatie wordt momenteel niet ondersteund voor gateway-apparaten gemodelleerd in IoT Central.

Vervolgens u:

> [!div class="nextstepaction"]
> [Een apparaat verbinden](tutorial-connect-device.md)
