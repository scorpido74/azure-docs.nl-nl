---
title: Gesimuleerd X.509-apparaat inrichten op Azure IoT Hub met C
description: In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen. In deze quickstart maakt en innmeert u een gesimuleerd X.509-apparaat met C-apparaat SDK voor Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f0c95e495e222cc72f0a6fc432404fcbaa47df65
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241162"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Snelstart: Een gesimuleerd X.509-apparaat inrichten met de Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze snelstart leert u hoe u een X.509-apparaatsimulator op een Windows-ontwikkelcomputer kunt maken en uitvoeren. U configureert dit gesimuleerde apparaat voor toewijzing aan een IoT-hub met behulp van een inschrijving bij een Device Provisioning Service-exemplaar. Er wordt voorbeeldcode van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt voor het simuleren van een opstartvolgorde voor het apparaat. Het apparaat wordt herkend op basis van de inschrijving bij de inschrijvingsservice en wordt toegewezen aan de IoT-hub.

Als u niet bekend bent met het proces van autoprovisioning, bekijkt u [concepten voor automatisch inrichten](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze snelstart. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

* [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
* [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende voorwaarden zijn voor een Windows-ontwikkelomgeving. Zie voor Linux of macOS de juiste sectie in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de ['Desktop development with C++'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) workload enabled. Ook Visual Studio 2015 en Visual Studio 2017 worden ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)te bouwen, waaronder de voorbeeldcode voor de X.509-opstartreeks.

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

5. De voorbeeldcode gebruikt een X.509-certificaat voor attestation via x.509-verificatie. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw ontwikkelingsplatform, inclusief de apparaatinrichtingsclient. Een Visual Studio-oplossing voor het gesimuleerde apparaat wordt gegenereerd in de `cmake` map.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Zodra de build slaagt, lijken de laatste paar uitvoerregels op de volgende uitvoer:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Een zelfondertekend X.509-apparaatcertificaat maken

In deze sectie gebruikt u een zelf-ondertekend X.509-certificaat. Het is hierbij belangrijk dat u rekening houdt met de volgende punten:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de Azure IoT C-SDK gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.

1. Open Visual Studio en open het nieuwe oplossingbestand met de naam `azure_iot_sdks.sln`. Dit oplossingsbestand bevindt zich in de map `cmake` die u eerder hebt gemaakt in de hoofdmap van de azure-iot-sdk-c git-opslagplaats.

2. Selecteer**build-oplossing** **bouwen** > om alle projecten in de oplossing te bouwen in het menu Visual Studio.

3. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Tools**. Klik met de rechtermuisknop op het**dice\_device\_enrollment**-project en selecteer **Set as Startup Project**.

4. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. Voer in het uitvoervenster **i** in voor individuele registratie wanneer hierom wordt gevraagd.

    In het uitvoervenster wordt een lokaal gegenereerd zelfondertekend X.509-certificaat weergegeven voor uw gesimuleerde apparaat. Kopieer de uitvoer naar Klembord vanaf **-----BEGIN CERTIFICATE-----** tot en met de eerste **-----END CERTIFICATE-----**, en zorg ervoor dat deze beide regels ook zijn opgenomen. U hebt alleen het eerste certificaat uit het uitvoervenster nodig.

5. Sla het certificaat met behulp van een teksteditor op naar een nieuw bestand met de naam **_X509testcert.pem_**.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Een vermelding voor apparaatregistratie maken in de portal

1. Meld u aan bij de Azure-portal, selecteer de knop **Alle bronnen** in het linkermenu en open de service Apparaatvoorziening.

2. Selecteer het tabblad **Inschrijvingen beheren** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.**

3. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in en druk op de knop **Opslaan.**

    * **Mechanisme:** selecteer **X.509** als *mechanisme* voor identiteitscontrole.
    * **Primair certificaat .pem- of .cer-bestand:** Kies **Selecteer een bestand** selecteren om het certificaatbestand X509testcert.pem te selecteren dat u eerder hebt gemaakt.
    * **IoT Hub-apparaat-id:** voer **test-docs-cert-device** in als id voor het apparaat.

      [![Individuele inschrijving voor X.509-attest toevoegen in de portal](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Als het apparaat is ingeschreven, wordt uw X.509-apparaat weergegeven als **riot-device-cert** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Eerste opstartvolgorde voor het apparaat simuleren

In deze sectie werkt u de voorbeeldcode voor het verzenden van de opstartvolgorde van het apparaat naar uw Device Provisioning Service-exemplaar bij. Deze opstartvolgorde zorgt ervoor dat het apparaat kan worden herkend en toegewezen aan een IoT-hub die is gekoppeld aan het Device Provisioning Service-exemplaar.

1. Selecteer in de Azure-portal het tabblad **Overzicht** voor uw service Voor het inrichten van apparaten en noteer de waarde **_ID-bereik._**

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Samples**. Vouw het voorbeeldproject met de naam **prov\_dev\_client\_sample** uit. Vouw **Source Files** uit en open **prov\_dev\_client\_sample.c**.

3. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_X509` in plaats van `SECURE_DEVICE_TYPE_TPM`, zoals hieronder wordt weergegeven.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**.

6. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **starten zonder foutopsporing** om de oplossing uit te voeren. Selecteer **Ja** om het project opnieuw op te bouwen voordat u het project wilt herbouwen voordat u het project wilt herbouwen.

    De volgende uitvoer is een voorbeeld van de voorbeeldcode van de Provisioning Device-client die met succes opstart en verbinding maakt met het Provisioning Service-exemplaar om IoT hub-informatie op te halen en zich te registreren:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en selecteer het tabblad **IoT-apparaten.** Bij een succesvolle inrichting van het gesimuleerde X.509-apparaat naar de hub wordt de apparaat-id weergegeven op het **iE-apparaatblad,** met *STATUS* zoals **ingeschakeld**. Mogelijk moet u bovenaan op de knop **Vernieuwen** drukken. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device/hub-registration.png) 

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Open **Inschrijvingen voor** uw service beheren en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* van het apparaat dat u in deze quickstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster. 
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Open **IoT-apparaten** voor uw hub, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat op uw Windows-machine gemaakt en deze op uw IoT-hub ingericht met behulp van de Azure IoT Hub Device Provisioning Service op de portal. Ga voor meer informatie over het programmatisch inschrijven van uw X.509-apparaat door naar de snelle start voor programmatische inschrijving van X.509-apparaten. 

> [!div class="nextstepaction"]
> [Azure quickstart - X.509-apparaten inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
