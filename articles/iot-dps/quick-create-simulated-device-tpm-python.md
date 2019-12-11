---
title: 'Snelstartgids: gesimuleerd TPM-apparaat met behulp van python inrichten voor Azure IoT Hub'
description: 'Quick Start: een gesimuleerd TPM-apparaat maken en inrichten met behulp van de SDK voor Java-apparaten voor IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 157754c00f9d2b940a3c71401f4e033e914b511a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976533"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Quick Start: een gesimuleerd TPM-apparaat maken en inrichten met behulp van python Device SDK voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In deze stappen wordt getoond hoe u een gesimuleerd apparaat maakt op een ontwikkelcomputer met Windows OS, de Windows TPM-simulator uitvoert als de [HSM (Hardware Security Module)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) van het apparaat en het Python-codevoorbeeld gebruikt om dit gesimuleerde apparaat te verbinden met Device Provisioning Service en uw IoT-hub. 

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de afgeschafte v1 python SDK. Apparaat-en service-clients voor de IOT hub Device Provisioning Service zijn nog niet beschikbaar in v2. Het team is momenteel hard werk om v2 te voorzien van functie pariteit.

Als u niet bekend bent met het proces van automatische inrichting, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen registraties:
- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat u [Visual studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger hebt geïnstalleerd met de optie ' Desktop Development with C++' voor de installatie van Visual Studio.

1. Download en installeer het [CMake-bouwsysteem](https://cmake.org/download/).

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt of Git Bash. Kloon het codevoorbeeld voor de GitHub-opslagplaats voor apparaatsimulatie:
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Maak een map in de lokale kopie van deze GitHub-opslagplaats voor het CMake-bouwproces. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. In het codevoorbeeld wordt een Windows TPM-simulator gebruikt. Voer de volgende opdracht uit om SAS-tokenverificatie in te schakelen. Er wordt ook een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. Ga door middel van een aparte opdrachtprompt naar de map van de TPM-simulator en voer de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-simulator uit. Klik op **Toegang toestaan**. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdracht venster niet. u moet deze simulator blijven uitvoeren tot aan het einde van deze Snelstartgids. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![TPM-simulator](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Een vermelding voor apparaatinschrijving maken

1. Open de in de map *cmake* gemaakt oplossing met de naam `azure_iot_sdks.sln` en bouw deze in Visual Studio.

1. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**. Voer de oplossing uit. In het uitvoer venster worden de **_goedkeurings sleutel_** en de **_registratie-id_** weer gegeven die nodig zijn voor de registratie van het apparaat. Noteer deze waarden. 

    ![TPM-installatie](./media/python-quick-create-simulated-device/tpm-setup.png)

1. Meld u aan bij de Azure Portal, selecteer de knop **alle resources** in het linkermenu en open uw Device Provisioning-Service.

1. Selecteer in het menu Device Provisioning Service de optie **inschrijvingen beheren**. Selecteer het tabblad **afzonderlijke** inschrijvingen en selecteer bovenaan de knop **afzonderlijke registratie toevoegen** . 

1. Voer in het deel venster **registratie toevoegen** de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeurings sleutel* voor het TPM-apparaat in van de waarden die u eerder hebt genoteerd.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id*in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest om er geen op te geven, wordt de registratie-ID gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Als u klaar bent, drukt u op de knop **Opslaan** . 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *Registration ID* ervan weergegeven in de lijst onder het tabblad *Individual Enrollments*. 


## <a name="simulate-the-device"></a>Het apparaat simuleren

1. [Download en installeer Python 2.x of 3.x](https://www.python.org/downloads/). Zorg ervoor dat u de 32-bits of 64-bits installatie gebruikt, zoals vereist door uw configuratie. Zorg ervoor dat u Python toevoegt aan uw platformspecifieke omgevingsvariabelen als u hierom wordt gevraagd tijdens de installatie.
    - Als u een Windows-besturingssysteem hebt, gebruikt u vervolgens het [herdistribueerbare pakket van Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) om het gebruik van systeemeigen DLL's van Python mogelijk te maken.

1. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) voor het bouwen van de Python-pakketten.

   > [!NOTE]
   > Als u `build_client.cmd` uitvoert, moet u ervoor zorgen dat u de vlag `--use-tpm-simulator` gebruikt.
   > 
   > [!NOTE]
   > Als u `pip` gebruikt, zorg dan ook dat u het pakket `azure-iot-provisioning-device-client` installeert. Houd er rekening mee dat de uitgebrachte PIP-pakketten van de echte TPM gebruikmaken, niet van de simulator. Als u de simulator wilt gebruiken, moet u vanuit de bron compileren met behulp van de vlag `--use-tpm-simulator`.

1. Navigeer naar de map met voorbeelden.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. Gebruik Python IDE om het Python-script met de naam **provisioning\_device\_client\_sample.py** te bewerken. Wijzig de variabelen *GLOBAL\_PROV\_URI* en *ID\_SCOPE* in de waarden die u eerder hebt genoteerd. Zorg er ook voor dat *SECURITY\_DEVICE\_TYPE* zijn ingesteld op `ProvisioningSecurityDeviceType.TPM`.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Service-informatie](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Voet het voorbeeld uit. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. 

    ![Succesvolle registratie](./media/python-quick-create-simulated-device/registration-success.png)

1. Wanneer het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld aan uw inrichtings service, wordt de apparaat-ID weer gegeven op de Blade **IOT-apparaten** van de hub.

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/python-quick-create-simulated-device/hubregistration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met het voor beeld van de apparaatclient, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Open de Blade **inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik boven aan het deel venster op de knop **verwijderen** . 
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Open de Blade **IOT-apparaten** voor uw hub, schakel het selectie vakje in naast de *apparaat-id* van het apparaat dat u in deze Quick Start hebt geregistreerd en druk op de knop **verwijderen** boven aan het deel venster.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een door de TPM gesimuleerd apparaat op de computer gemaakt en dit ingericht voor uw IoT-hub met behulp van de IoT Hub Device Provisioning Service. Als u wilt weten hoe u uw TPM-apparaat programmatisch kunt registreren, gaat u verder met de Quick start voor programmatische inschrijving van een TPM-apparaat. 

> [!div class="nextstepaction"]
> [Azure Quick Start-TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-python.md)
