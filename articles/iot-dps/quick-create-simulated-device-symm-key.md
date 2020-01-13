---
title: Quick Start-symmetrische sleutel gebruiken om gesimuleerd apparaat in te richten op Azure IoT Hub met C
description: In deze Snelstartgids gebruikt u de C SDK voor het maken van een gesimuleerd apparaat dat gebruikmaakt van symmetrische sleutel met de Azure-IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4e84a43f8984e2adeac916e467a8cac693ee453d
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912436"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels

In deze snelstartgids leert u hoe u een apparaatsimulator kunt maken en uitvoeren op een Windows-ontwikkelcomputer. U configureert dit gesimuleerde apparaat om een symmetrische sleutel te gebruiken voor een verificatie met een Device Provisioning Service-exemplaar en voor toewijzing aan een IoT-hub. Er wordt voorbeeldcode van de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt voor het simuleren van een opstartprocedure voor het apparaat dat de inrichting initieert. Het apparaat wordt herkend op basis van een afzonderlijke inschrijving met een inrichtingsservice-exemplaar en wordt toegewezen aan een IoT-hub.

Hoewel in dit artikel wordt gedemonstreerd met het inrichten met een individuele inschrijving, kunt u registratie groepen gebruiken. Er zijn enkele verschillen bij het gebruik van registratie groepen. U moet bijvoorbeeld een afgeleide-apparaatwachtwoord gebruiken met een unieke registratie-ID voor het apparaat. Hoewel inschrijvingsgroepen met symmetrische sleutel niet tot oudere apparaten beperkt zijn, biedt [Oudere apparaten inrichten door middel van attestation met een symmetrische sleutel](how-to-legacy-device-symm-key.md) een voorbeeld van een inschrijvingsgroep. Zie [Groepsinschrijvingen voor attestation met behulp van een symmetrische sleutel](concepts-symmetric-key-attestation.md#group-enrollments) voor meer informatie.

Als u niet bekend bent met het proces van automatische inrichting, bekijk dan de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). 

Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze snelstart. Voor deze snelstartgids wordt aangenomen dat u al een Device Provisioning Service-exemplaar hebt gemaakt.

Dit artikel is gericht op een Windows-gebaseerd werkstation. U kunt de procedures echter ook uitvoeren op Linux. Zie [Inrichten voor multitenancy](how-to-provision-multitenant.md) voor een Linux-voorbeeld.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

De volgende vereisten gelden voor een Windows-ontwikkel omgeving. Voor Linux of macOS raadpleegt u de desbetreffende sectie in [uw ontwikkel omgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de [' Desktop Development C++](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) '-werk belasting ingeschakeld. Visual Studio 2015 en Visual Studio 2017 worden ook ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Een ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te bouwen. 

De SDK bevat de voorbeeldcode voor een gesimuleerd apparaat. Dit gesimuleerde apparaat probeert de inrichting uit te voeren tijdens de opstartprocedure van het apparaat.

1. Down load het [cmake build-systeem](https://cmake.org/download/).

    Het is belangrijk dat de vereisten voor Visual Studio met (Visual Studio en de workload Desktopontwikkeling met C++) op uw computer zijn geïnstalleerd **voordat** de `CMake`-installatie wordt gestart. Zodra aan de vereisten is voldaan en de download is geverifieerd, installeert u het CMake-bouwsysteem.

    Oudere versies van het CMake build-systeem genereren het oplossings bestand dat in dit artikel wordt gebruikt, niet. Zorg ervoor dat u een nieuwere versie van CMake gebruikt.

2. Klik op **Tags** en zoek de naam van de tag voor de meest recente release op de [pagina release van de Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

3. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IOT C SDK](https://github.com/Azure/azure-iot-sdk-c) github-opslag plaats te klonen. Gebruik het label dat u in de vorige stap hebt gevonden als waarde voor de para meter `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

4. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. Voer de volgende opdrachten uit in de map `azure-iot-sdk-c`:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Voer de volgende opdracht uit om een versie van de SDK te bouwen die specifiek is voor uw clientplatform voor ontwikkeling. Er wordt een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd in de map `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Zodra het bouwen is voltooid, zijn de laatste paar uitvoerregels vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Een vermelding voor apparaatregistratie maken in de portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer de knop **alle resources** in het linkermenu en open uw Device Provisioning-Service.

2. Selecteer het tabblad **inschrijvingen beheren** en selecteer vervolgens de knop **afzonderlijke registratie toevoegen** bovenaan. 

3. Voer in het deel venster **registratie toevoegen** de volgende informatie in en klik op de knop **Opslaan** .

   - **Mechanisme:** selecteer **Symmetrische sleutel** als *mechanisme* voor identiteitscontrole.

   - **Sleutels automatisch genereren**: Schakel dit selectie vakje in.

   - **Registratie-ID**: voer een registratie-ID voor het identificeren van de inschrijving. Gebruik alleen kleine alfanumerieke tekens en streepjes ('-'). Bijvoorbeeld **symm-Key-Device-007**.

   - **IoT Hub apparaat-ID:** voer een apparaat-ID in. Bijvoorbeeld, **apparaat-007**.

     ![Afzonderlijke inschrijving toevoegen voor attestation met symmetrische sleutel in de portal](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Zodra u uw inschrijving hebt opgeslagen, worden de **primaire sleutel** en **secundaire sleutel** gegenereerd en toegevoegd aan de inschrijvings vermelding. De inschrijving met symmetrische sleutel van uw apparaat wordt weergegeven als **symm-key-device-007** in de kolom *Registratie-ID* op het tabblad *Afzonderlijke registraties*. 

    Open de inschrijving en kopieer de waarde van uw gegenereerde **primaire sleutel**.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Eerste opstartvolgorde voor het apparaat simuleren

In deze sectie werkt u de voorbeeldcode voor het verzenden van de opstartvolgorde van het apparaat naar uw Device Provisioning Service-exemplaar bij. Deze opstartvolgorde zorgt ervoor dat het apparaat kan worden herkend en toegewezen aan een IoT-hub die is gekoppeld aan het Device Provisioning Service-exemplaar.



1. Selecteer in de Azure Portal het tabblad **overzicht** voor de Device Provisioning Service en noteer de waarde van het **_id-bereik_** .

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Open in Visual Studio het oplossingsbestand **azure_iot_sdks.sln** dat is gegenereerd door CMake uit te voeren. Het oplossingsbestand bevindt zich als het goed is op de volgende locatie:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

    Als het bestand niet is gegenereerd in de cmake-Directory, zorg er dan voor dat u een recente versie van het CMake-build-systeem hebt gebruikt.

3. Navigeer in het deelvenster *Solution Explorer* van Visual Studio naar de map **Provision\_Samples**. Vouw het voorbeeldproject met de naam **prov\_dev\_client\_sample** uit. Vouw **Source Files** uit en open **prov\_dev\_client\_sample.c**.

4. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat de variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, zoals hieronder wordt weergegeven:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Zoek de aanroep naar `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**, waarbij een opmerking is geplaatst.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Verwijder de opmerkingen bij de functieaanroep en vervang de tijdelijke-aanduidingswaarden (inclusief de punthaken) bij uw registratie-id en primaire sleutel.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Sla het bestand op.

7. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**. 

8. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. In de prompt om het project opnieuw op te bouwen, selecteert u **Ja**om het project opnieuw te bouwen voordat u het uitvoert.

    De volgende output is een voorbeeld waarbij het gesimuleerde apparaat met succes opstart en verbinding maakt met het inrichtingsservice-exemplaar voor toewijzing aan een IoT-hub:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. Navigeer in de portal naar de IoT-hub waaraan uw gesimuleerde apparaat is toegewezen en selecteer het tabblad **IOT-apparaten** . Wanneer het inrichten van het gesimuleerde naar de hub is geslaagd, wordt de apparaat-ID weer gegeven op de Blade **IOT-apparaten** , met de *status* **ingeschakeld**. Mogelijk moet u bovenaan op de knop **vernieuwen** klikken. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met het voor beeld van de apparaatclient, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Open **inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik boven aan het deel venster op de knop **verwijderen** . 
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Open **IOT-apparaten** voor uw hub, schakel het selectie vakje in naast de *apparaat-id* van het apparaat dat u in deze Quick Start hebt geregistreerd en druk op de knop **verwijderen** boven aan het deel venster.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een gesimuleerd apparaat op uw Windows-computer gemaakt en dit ingericht voor uw IoT-hub met behulp van de symmetrische sleutel met de Azure-IoT Hub Device Provisioning Service op de portal. Als u wilt weten hoe u uw apparaat programmatisch kunt registreren, gaat u verder met de Quick start voor programmatische inschrijving van X. 509-apparaten. 

> [!div class="nextstepaction"]
> [Azure Quick start-X. 509-apparaten inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
