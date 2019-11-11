---
title: 'Snelstartgids: een gesimuleerd X. 509-apparaat inrichten in azure IoT Hub met behulp van python'
description: 'Azure-quickstart: een gesimuleerd X.509-apparaat met de SDK voor Python maken en inrichten voor IoT Hub Device Provisioning Service. In deze snelstart wordt gebruikgemaakt van afzonderlijke registraties.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: d6cb5740da53f9132613c2c03a1c9b88c2ce923b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904813"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Quick Start: een gesimuleerd X. 509-apparaat maken en inrichten met behulp van python Device SDK voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze stappen wordt getoond hoe u een gesimuleerd X.509-apparaat maakt op een ontwikkelcomputer met Windows OS en het Python-codevoorbeeld gebruikt om dit gesimuleerde apparaat te verbinden met de Device Provisioning Service en uw IoT-hub. 

Als u niet bekend bent met het proces van automatisch inrichten, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen registraties:
- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke registraties gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

> [!NOTE]
> Deze hand leiding is alleen van toepassing op de nu afgeschafte v1 python SDK. Gesimuleerde X. 509-apparaten zijn nog niet ondersteund in v2. Het team is momenteel in staat om v2 te gebruiken voor de functie pariteit.

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat u [Visual studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger hebt geïnstalleerd met de optie ' Desktop Development with C++' voor de installatie van Visual Studio.

2. Download en installeer het [CMake-bouwsysteem](https://cmake.org/download/).

3. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

4. Open een opdrachtprompt of Git Bash. Kloon het codevoorbeeld voor apparaatsimulatie uit de GitHub-opslagplaats.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

5. Maak een map in de lokale kopie van deze GitHub-opslagplaats voor het CMake-bouwproces. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

6. Voer de volgende opdracht uit om de Visual Studio-oplossing te maken voor de client die de inrichting verzorgt.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Een zelfondertekend X.509-certificaat voor apparaten en een vermelding voor afzonderlijke registratie maken

In deze sectie gebruikt u een zelfondertekend X.509-certificaat. Het is belangrijk rekening te houden met de volgende punten:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de Azure IoT C-SDK gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.

1. Open de in de map *cmake* gemaakt oplossing met de naam `azure_iot_sdks.sln` en bouw deze in Visual Studio.

2. Klik met de rechtermuisknop op het project **dice\_device\_enrollment** onder de map **Provision\_Tools** en selecteer **Set as Startup Project**. Voer de oplossing uit. 

3. Voer in het uitvoervenster `i` in voor individuele inschrijving wanneer u hierom wordt gevraagd. In het uitvoervenster wordt een lokaal gegenereerd X.509-certificaat weergegeven voor uw gesimuleerde apparaat. 
    
    Kopieer het eerste certificaat naar het Klembord. Begin met het eerste exemplaar van:
    
        -----BEGIN CERTIFICATE----- 
        
    Beëindig het kopiëren na het eerste exemplaar van:
    
        -----END CERTIFICATE-----
        
    Zorg ervoor dat beide regels ook zijn opgenomen. 

    ![Toepassing voor opdelen van apparaat](./media/python-quick-create-simulated-device-x509/dice-device-enrollment.png)
 
4. Maak een bestand met de naam **_X509testcertificate.pem_** op uw Windows-computer, open het in een editor naar keuze en kopieer de inhoud van het Klembord naar dit bestand. Sla het bestand op. 

5. Meld u aan bij Azure Portal, klik in het linkermenu op de knop **Alle resources** en open uw Provisioning-service.

6. Selecteer **Manage enrollments** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Afzonderlijke registraties** en klik vervolgens op de knop **Afzonderlijke inschrijving toevoegen** bovenaan. 

7. Voer onder het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Klik onder het *PEM- of CER-bestand van het primaire certificaat* op een *Select a file* om het certificaatbestand **X509testcertificate.pem** te selecteren dat in de vorige stappen is gemaakt.
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Save** als u klaar bent. 

     [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Als het apparaat is ingeschreven, wordt uw X.509-apparaat weergegeven als **riot-device-cert** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 

## <a name="simulate-the-device"></a>Het apparaat simuleren

1. Selecteer **Overzicht** in de overzichtsblade van de Device Provisioning Service. Noteer uw _id-bereik_ en _globaal service-eindpunt_.

    ![Service-informatie](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. [Download en installeer Python 2.x of 3.x](https://www.python.org/downloads/). Zorg ervoor dat u de 32-bits of 64-bits installatie gebruikt, zoals vereist door uw configuratie. Zorg ervoor dat u Python toevoegt aan uw platformspecifieke omgevingsvariabelen als u hierom wordt gevraagd tijdens de installatie. Als u Python 2.x gebruikt, moet u mogelijk [pip *installeren of upgraden*, het Python-pakketbeheersysteem](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Als u van Windows gebruikmaakt, installeert u ook [Visual C++ Redistributable voor Visual Studio 2015](https://www.microsoft.com/download/confirmation.aspx?id=48145). Voor de pip-pakketten is het herdistribueerbare pakket vereist om de C-DLL's te laden en uit te voeren.

3. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) voor het bouwen van de Python-pakketten.

   > [!NOTE]
   > Als u `pip` gebruikt, zorg dan ook dat u het pakket `azure-iot-provisioning-device-client` installeert.

4. Navigeer naar de map met voorbeelden.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

5. Gebruik Python IDE om het Python-script met de naam **provisioning\_device\_client\_sample.py** te bewerken. Wijzig de variabelen _GLOBAL\_PROV\_URI_ en _ID\_SCOPE_ in de waarden die u eerder hebt genoteerd.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.X509
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

6. Voet het voorbeeld uit. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

7. De toepassing zorgt dat het apparaat verbinding heeft en wordt geregistreerd. Vervolgens wordt een bericht weergegeven dat de registratie is gelukt.

    ![geslaagde registratie](./media/python-quick-create-simulated-device-x509/enrollment-success.png)

8. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw Provisioning-service en open de blade **Device Explorer**. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is geslaagd, wordt de apparaat-ID weergegeven op de blade **Device Explorer** met de *STATUS* **ingeschakeld**. U moet mogelijk klikken op de knop **Vernieuwen** bovenaan als u de blade vóór het uitvoeren van de voorbeeldapparaattoepassing al hebt geopend. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.
>

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze Snelstartgids zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources die via deze Snelstartgids zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer uw Device Provisioning Service. Open de Blade **inschrijvingen beheren** voor uw service en klik vervolgens op het tabblad **afzonderlijke inschrijvingen** . Selecteer de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik bovenaan op de knop **verwijderen** . 
3. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, selecteer de *apparaat-id* van het apparaat dat u hebt geregistreerd in deze quickstart en klik vervolgens bovenaan op de knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat op uw Windows-computer gemaakt en het ingericht voor uw IoT-hub met de Azure IoT Hub Device Provisioning Service in de portal. Als u wilt weten hoe u uw X.509-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een X.509-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: X.509-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)
