---
title: Een IoT-Plug en Play apparaat verbinden met IoT Central | Microsoft Docs
description: Als ontwikkel aars over het gebruik van Visual Studio code kunt u een IoT-Plug en Play apparaat maken en testen dat verbinding maakt met IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997232"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Visual Studio code gebruiken om een IoT Plug en Play-apparaat te maken dat verbinding maakt met IoT Central

In deze hand leiding wordt uitgelegd hoe u het volgende kunt doen:

* Als operator, om een echt apparaat toe te voegen en te configureren in uw Azure IoT Central-toepassing.

* Als ontwikkel aars voor het gebruik van Visual Studio code om een [IOT-Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) apparaat te maken dat verbinding maakt met uw IOT Central-toepassing.

U gebruikt een [mogelijkheidsprofiel](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) om het apparaat te definiëren dat verbinding maakt met IOT Central. In deze hand leiding gebruikt u een model dat een omgevings sensor apparaat definieert.

De ontwikkelaar van het apparaat gebruikt het model voor het genereren van de client code van een apparaat en de opbouw functie maakt gebruik van het model voor het [maken van een sjabloon voor een apparaat](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in IOT Central. Het model fungeert als een contract tussen uw apparaat en uw IoT Central-toepassing.

In de sectie in deze hand leiding wordt beschreven hoe u de gegenereerde code bouwt, wordt ervan uitgegaan dat u Windows gebruikt.

In deze handleiding leert u het volgende:

* Een mogelijkheidsprofiel importeren in een sjabloon voor een apparaat in IoT Central
* Een dash board toevoegen aan de sjabloon waarin telemetrie van apparaten wordt weer gegeven
* Een echt apparaat uit de sjabloon toevoegen
* Een mogelijkheidsprofiel importeren in Visual Studio code
* Een C Device-client toepassing vanuit het model genereren
* Bouw de client toepassing C device op en verbind deze met IoT Central

## <a name="prerequisites"></a>Vereisten

Als u uw apparaatcode in deze hand leiding wilt testen, moet u een IoT Central toepassing die is gemaakt op basis van de sjabloon **Preview** -toepassing. Als u nog geen IoT Central toepassing hebt om te gebruiken, voltooit u de Snelstartgids [een Azure IOT Central-toepassing maken (preview-functies)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

Als u in deze hand leiding wilt werken met het mogelijkheidsprofiel, hebt u het volgende nodig:

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio code is beschikbaar voor meerdere platforms
* Azure IoT Device Workbench-extensie voor Visual Studio code. Gebruik de volgende stappen om de Azure IoT Device Workbench-extensie in VS code te installeren:

    1. Selecteer in VS code het tabblad **extensies** .
    1. Zoek naar **Azure IOT Device Workbench**.
    1. Selecteer **Installeren**.

    > [!NOTE]
    > De huidige versie van de code generator in de uitbrei ding biedt geen ondersteuning voor de geopunt-en **Vector** schema typen of de semantische typen **versnelling**, **snelheid**en **locatie** . Deze schema's en semantische typen worden ondersteund door IoT Central.

    > [!NOTE]
    > Als u met IoT Central wilt werken, moeten in het mogelijkheidsprofiel alle interfaces in hetzelfde bestand zijn gedefinieerd.

Als u in deze hand leiding de gegenereerde C-code wilt maken in Windows, hebt u het volgende nodig:

* [Visual Studio (Community, Professional of ENTER prise)](https://visualstudio.microsoft.com/downloads/) : Zorg ervoor dat u het onderdeel **NuGet package manager** en de **Desktop ontwikkeling C++ met** werk belasting opneemt tijdens de installatie van Visual Studio.
* [Git](https://git-scm.com/download)
* [Cmake](https://cmake.org/download/) : als u **cmake**installeert, selecteert u de optie **cmake toevoegen aan het**systeempad.
* Een lokale kopie van de Azure IoT C-SDK:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Als u de instructies voor het eerste apparaat aan het eind van deze procedure wilt volgen, moet u ook het volgende installeren:

* [Node.js](https://nodejs.org)
* Het [hulp programma DPS-keygen](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Sjabloon voor het maken van een apparaat

Maak map met de naam **pnp_app** in de map **Azure-IOT-SDK-c** die de Azure IOT c-SDK bevat die u hebt gekloond. Down load het capaciteits model [EnvironmentalSensor. capabilitymodel. json](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) van github en sla het bestand op in de map **pnp_app** . Vervang de twee exemplaren van `<YOUR_COMPANY_NAME_HERE>` door uw naam. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepen. Noteer de volledige waarde van het `"@id"` veld dat het mogelijkheidsprofiel van uw apparaat uniek identificeert. u hebt dit later nodig. Dit model bevat twee interfaces:

* De gemeen schappelijke interface **DeviceManagement** .
* de aangepaste **EnvironmentalSensor** -interface.

Een apparaatprofiel maken voor het omgevings sensor apparaat in IoT Central:

1. Ga naar de pagina met **Apparaatinstellingen** en selecteer **+ Nieuw**. Kies vervolgens **aangepast**.

1. Voer een **omgevings sensor** in als de naam van de sjabloon.

1. Kies het **model voor import mogelijkheden**. Zoek vervolgens de **EnvironmentalSensor. capabilitymodel. json** die u hebt gemaakt en selecteer **openen**. De twee interfaces, **apparaatgegevens** en **omgevings sensor**, worden weer gegeven in het model voor de **omgevings sensor**.

1. Selecteer **weer gaven** en **Visualiseer vervolgens het apparaat**.

1. Selecteer in de lijst met velden die u aan het dash board kunt toevoegen de twee telemetrie-waarden, **vochtigheid** en **Tempe ratuur** en selecteer **combi neren**. Kies vervolgens **Opslaan**.

U hebt nu een sjabloon voor een apparaat dat gebruikmaakt van het **omgevings sensor** model en met een eenvoudig dash board om telemetrie van een apparaat weer te geven.

## <a name="publish-the-template-and-add-a-real-device"></a>De sjabloon publiceren en een echt apparaat toevoegen

Als u de sjabloon wilt publiceren en een echt apparaat wilt toevoegen, gaat u naar de pagina met **Apparaatinstellingen** en selecteert u de sjabloon **omgevings sensor** apparaat. Selecteer **publiceren**, Controleer de gegevens en selecteer **publiceren**.

Voordat u verbinding maakt met een client toepassing, moet u een apparaat toevoegen op de pagina **apparaten** en de verbindings gegevens ophalen:

1. Ga naar de pagina **apparaten** en selecteer **omgevings sensor**. Op de pagina **apparaten** kunt u de apparaten beheren die verbinding kunnen maken met uw toepassing.

1. Als u een echt apparaat wilt toevoegen, kiest u **+ Nieuw**, wijzigt u de apparaat-id in **sensor01**en selecteert u **maken**. Zorg ervoor dat gesimuleerd is **uitgeschakeld**.

1. Selecteer in de lijst met apparaten het apparaat **omgevings sensor** . Selecteer vervolgens **verbinding maken**.

1. Noteer de **bereik-id**, de **apparaat-id**en de **primaire sleutel**. Selecteer vervolgens **sluiten**.

## <a name="generate-a-device-client-application"></a>Een apparaatclient genereren

U gebruikt Visual Studio code voor het genereren van een skelet apparaat-client toepassing vanuit het mogelijkheidsprofiel:

1. Start Visual Studio code en open de map **pnp_app** .

1. Gebruik **CTRL + SHIFT + P** om het opdracht palet te openen.

1. Voer **Plug en Play** in en selecteer vervolgens **apparaatcode Souche genereren**.

1. Selecteer het **EnvironmentalSensor. capabilitymodel. json** -bestand met het capaciteits model.

1. Voer **sensor_app** in als de project naam.

1. Kies **ANSI C** als taal.

1. Kies **cmake project** als doel.

1. Kies **via DPS (Device Provisioning Service) symmetrische sleutel** als de verbindings methode.

Met Visual Studio code wordt in de map **sensor_app** een nieuw venster geopend met de gegenereerde C-code.

## <a name="add-connection-details-to-the-device-client"></a>Verbindings Details toevoegen aan de apparaatclient

Als u de verbindings gegevens wilt toevoegen aan de client van uw apparaat, opent u **Main. c** in de map die de gegenereerde code bevat:

1. Vervang `[DPS Id Scope]` door de waarde van de **scope-id** die u eerder hebt genoteerd.

1. Vervang `[DPS symmetric key]` door de waarde van de **primaire sleutel** die u eerder hebt genoteerd.

1. Vervang `[device registration Id]` door de waarde van de **apparaat-id** die u eerder hebt genoteerd.

1. Sla de wijzigingen op.

## <a name="build-and-run-the-client"></a>De client bouwen en uitvoeren

Als u de-client wilt bouwen en uitvoeren, moet u de build van de Azure IoT C-SDK aanpassen:

1. Open het bestand **CMakeLists. txt** in de hoofdmap van de map **Azure-IOT-SDK-c** .

1. Voeg aan het einde van dit bestand de volgende regel toe voor het toevoegen van de nieuwe client-app in de build:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Sla de wijzigingen op.

1. Open een opdracht prompt en ga naar de map **Azure-IOT-SDK-c** .

1. Voer de volgende opdrachten uit om de toepassing te bouwen:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Als u de toepassing wilt uitvoeren, voert u de volgende opdracht uit vanaf dezelfde opdracht prompt:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>De telemetrie weer geven

Na een paar minuten kunt u de telemetrie van het apparaat op het dash board van het apparaat in uw IoT Central-toepassing zien.

## <a name="connect-device-first"></a>Apparaat verbinden-eerst

U kunt een IoT-Plug en Play apparaat verbinden via een apparaat-eerste verbinding zoals wordt beschreven in [connectiviteit](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Het detectie proces volgt deze volg orde:

1. Hiermee wordt gekoppeld aan de sjabloon voor het apparaat als deze al in de IoT Central toepassing is gepubliceerd.

1. Haalt het capaciteits model op uit de [open bare opslag plaats](https://aka.ms/ACFI) van gepubliceerde en gecertificeerde bekwaamheids modellen.

Met Visual Studio code en de versleutelde stub-opdracht voor het **genereren** van de apparaatcode, kunt u de volgende stappen uitvoeren om het apparaat te verbinden, en automatisch het model voor het maken van uw gepubliceerde apparaat uit de open bare opslag plaats in IOT Central:

1. Gebruik een bestaand hulp model voor apparaten dat is gepubliceerd in de open bare opslag plaats. U hebt het functionaliteits model volledig apparaat nodig en noteer de URN van dit model.

1. Volg de bovenstaande stappen om [een apparaatclient te genereren](#generate-a-device-client-application) voor het gebruik van Visual Studio code en het genereren van de apparaatcode.

1. Ga vanuit uw IoT Central-toepassing naar het tabblad **beheer** en selecteer het gedeelte **apparaat-verbinding** . Noteer de **bereik-id** en de **primaire sleutel** waarden voor uw toepassing.

    > [!NOTE]
    > De **primaire sleutel** die op deze pagina wordt weer gegeven, is een gedeelde toegangs handtekening van een groep die u kunt gebruiken om meerdere Apparaatsets voor uw toepassing te genereren.

1. Gebruik het hulp programma [DPS keygen](https://www.npmjs.com/package/dps-keygen) om een apparaatcode te genereren op basis van de primaire sleutel die u eerder hebt genoteerd. Voer de volgende opdracht uit om de apparaatcode te genereren:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Volg de stappen in de vorige sectie [verbindings Details toevoegen aan de](#add-connection-details-to-the-device-client) apparaatclient om de **scope-id**, de **primaire sleutel**en de **apparaat-id**toe te voegen.

1. Volg de stappen in de vorige sectie om [de-client te bouwen en uit te voeren](#build-and-run-the-client).

1. Nu ziet u het apparaat verbinding maken met uw IoT Central-toepassing en wordt automatisch het mogelijkheidsprofiel van de open bare opslag plaats als een sjabloon voor het apparaat beschikbaar gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een echt apparaat kunt verbinden met IoT Central, is een voorgestelde volgende stap meer informatie over de sjablonen in [een sjabloon voor een apparaat instellen](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
