---
title: 'Snelstart: een gesimuleerd TPM-apparaat inrichten op Azure IoT Hub met C'
description: In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen. In deze quickstart maakt en insdient u een gesimuleerd TPM-apparaat met C-apparaat SDK voor Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d41c4757f0b81312cefa580c3a3263f87bccffa9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241701"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Snelstart: Een gesimuleerd TPM-apparaat inrichten met de Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In deze snelstart leert u hoe u een TPM-apparaatsimulator (Trusted Platform Module) op een Windows-ontwikkelcomputer kunt maken en uitvoeren. U verbindt dit gesimuleerde apparaat met een IoT-hub met behulp van een Device Provisioning Service-exemplaar. Er wordt voorbeeldcode van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt om u te helpen bij het registreren van het apparaat bij een Device Provisioning Service-exemplaar en een opstartvolgorde voor het apparaat te simuleren.

Als u niet bekend bent met het proces van autoprovisioning, bekijkt u [concepten voor automatisch inrichten](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze snelstart. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:
- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende voorwaarden zijn voor een Windows-ontwikkelomgeving. Zie voor Linux of macOS de juiste sectie in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de ['Desktop development with C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) workload enabled. Ook Visual Studio 2015 en Visual Studio 2017 worden ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) en het voorbeeld van de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-apparaatsimulator te bouwen.

1. Download het [CMake-buildsysteem.](https://cmake.org/download/)

    Het is belangrijk dat de vereisten voor Visual Studio met (Visual Studio en de workload Desktopontwikkeling met C++) op uw computer zijn geïnstalleerd **voordat** de `CMake`-installatie wordt gestart. Zodra aan de vereisten is voldaan en de download is geverifieerd, installeert u het CMake-bouwsysteem.

2. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de SDK.

3. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository te klonen. Gebruik de tag die u in de `-b` vorige stap hebt gevonden als de waarde voor de parameter:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

4. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten `azure-iot-sdk-c` uit de map uit:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>De SDK bouwen en de TPM-apparaatsimulator uitvoeren

In deze sectie maakt u de Azure IoT C SDK, die de voorbeeldcode van de TPM-apparaatsimulator bevat. Dit voorbeeld biedt een TPM [attestation-mechanisme](concepts-security.md#attestation-mechanism) via SAS-tokenverificatie (Shared Access Signature).

1. Voer vanuit de submap `cmake` die u hebt gemaakt in de azure-iot-sdk-c git-opslagplaats de volgende opdracht uit om het voorbeeld te maken. Er wordt door deze buildopdracht ook een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Navigeer naar de hoofdmap van de git-opslagplaats die u hebt gekloond en voer de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-simulator uit via het pad dat hieronder wordt weergegeven. Deze simulator luistert via een socket op poorten 2321 en 2322. Sluit dit opdrachtvenster niet; de simulator moet actief blijven tot u deze snelstart hebt voltooid. 

   Als u de map *cmake* hebt geopend, voert u van daaruit de volgende opdrachten uit:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    U ziet niet alle uitvoer van de simulator. Laat deze verder gaan met het simuleren van een TPM-apparaat.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Cryptografische sleutels van het TPM-apparaat lezen

In deze sectie bouwt u een voorbeeld dat de goedkeuringssleutel en registratie-ID leest van de TPM-simulator die u actief hebt en luistert op de poorten 2321 en 2322 en voert u dit voorbeeld uit. Deze waarden worden gebruikt voor de inschrijving van apparaten met uw Device Provisioning Service-exemplaar.

1. Open Visual Studio en open het nieuwe oplossingbestand met de naam `azure_iot_sdks.sln`. Dit oplossingsbestand bevindt zich in de map `cmake` die u eerder hebt gemaakt in de hoofdmap van de azure-iot-sdk-c git-opslagplaats.

2. Selecteer**build-oplossing** **bouwen** > om alle projecten in de oplossing te bouwen in het menu Visual Studio.

3. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Tools**. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**. 

4. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. De app leest en toont een **_registratie-id_** en een **_goedkeuringssleutel_**. Noteer of kopieer deze waarden. Deze worden gebruikt in de volgende sectie voor apparaatinschrijving. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Een vermelding voor apparaatregistratie maken in de portal

1. Meld u aan bij de Azure-portal, selecteer de knop **Alle bronnen** in het linkermenu en open de service Apparaatvoorziening.

1. Selecteer het tabblad **Inschrijvingen beheren** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 

1. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor uw TPM-apparaat in op basis van de eerder aangenomen waarden.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id in* (u het voorgestelde **test-docs-apparaat** gebruiken of uw eigen apparaat verstrekken). Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest er geen aan te geven, wordt de registratie-id gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Druk op de knop **Opslaan** nadat u bent voltooid. 

      ![Gegevens van apparaatinschrijving invoeren in de portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Als het apparaat is ingeschreven, wordt de *Registration ID* ervan weergegeven in de lijst onder het tabblad *Individual Enrollments*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Eerste opstartvolgorde voor het apparaat simuleren

In deze sectie configureert u voorbeeldcode voor het gebruik van het [Advanced Message Queuing Protocol (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) om de opstartvolgorde van het apparaat naar uw Device Provisioning Service-exemplaar te verzenden. Deze opstartvolgorde zorgt ervoor dat het apparaat kan worden herkend en toegewezen aan een IoT-hub die is gekoppeld aan het Device Provisioning Service-exemplaar.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning Service en kopieer de waarde bij **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portal extraheren](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Samples**. Vouw het voorbeeldproject met de naam **prov\_dev\_client\_sample** uit. Vouw **Source Files** uit en open **prov\_dev\_client\_sample.c**.

3. Zoek boven aan het bestand de instructies `#define` op voor elk apparaatprotocol, zoals hieronder wordt weergegeven. Zorg ervoor dat alleen `SAMPLE_AMQP` geen commentaar bevat.

    Op dit moment wordt het [MQTT-protocol niet ondersteund voor een afzonderlijke TPM-inschrijving](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_TPM` in plaats van `SECURE_DEVICE_TYPE_X509`, zoals hieronder wordt weergegeven.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**. 

7. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. Selecteer **Ja**, om het project opnieuw op te bouwen voordat u het project uitvoert.

    De volgende uitvoer is een voorbeeld van de voorbeeldcode van de Provisioning Device-client die met succes opstart en verbinding maakt met het Device Provisioning Service-exemplaar om IoT hub-informatie op te halen en zich te registreren:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Zodra het gesimuleerde apparaat door uw inrichtende service is ingericht op de IoT-hub, wordt de apparaat-id weergegeven met de **IoT-apparaten**van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Sluit het TPM-simulatorvenster op de computer.
3. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Open **Inschrijvingen voor** uw service beheren en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* van het apparaat dat u in deze quickstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster. 
4. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Open **IoT-apparaten** voor uw hub, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een TPM-gesimuleerd apparaat op uw machine gemaakt en deze in gerichte tijd in uw IoT-hub met behulp van de IoT Hub Device Provisioning Service. Ga door met de snelle start voor programmatische inschrijving van een TPM-apparaat om uw TPM-apparaat programmatisch in te schrijven. 

> [!div class="nextstepaction"]
> [Azure quickstart - TPM-apparaat inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-java.md)
