---
title: Gesimuleerd X.509-apparaat inrichten op Azure IoT Hub met Python
description: Snelstart - Een gesimuleerd X.509-apparaat maken en inrichten met Python-apparaat SDK voor De Voorzieningsservice (IoT Hub Device Provisioning Service) In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 44f1a2cd3336eeae87878c333fb05d2e6b1f88e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605391"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstart: een gesimuleerd X.509-apparaat maken en inrichten met Python-apparaat SDK voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze quickstart maakt u een gesimuleerd X.509-apparaat op een Windows-computer. U gebruikt python-code voor apparaatmonsters om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving bij de DPS (Device Provisioning Service).

## <a name="prerequisites"></a>Vereisten

- Herziening van [auto-inrichting concepten](concepts-auto-provisioning.md).
- Voltooiing van [de Inrichtingsservice voor IoT-hub-apparaten met de Azure-portal](./quick-setup-auto-provision.md).
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/) met desktopontwikkeling met C++.
- [CMake-buildsysteem](https://cmake.org/download/).
- [Git.](https://git-scm.com/download/)

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de afgeschafte V1 Python SDK. Apparaat- en serviceclients voor de Iot Hub Device Provisioning Service zijn nog niet beschikbaar in V2. Het team is momenteel hard aan het werk om V2 te brengen om pariteit te kenmerken.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat u [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger hebt geïnstalleerd, met de werkbelasting 'Bureaubladontwikkeling met C++' ingeschakeld voor uw Visual Studio-installatie.

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

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om één apparaat in te schrijven.

Dit artikel toont individuele inschrijvingen.

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

5. Meld u aan bij de Azure-portal, selecteer de knop **Alle bronnen** in het linkermenu en open uw inrichtingsservice.

6. Selecteer inschrijvingen beheren in het menu **Apparaatinrichtingsservice**. Selecteer het tabblad **Individuele inschrijvingen** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 

7. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *bestand Primair certificaat .pem of .cer*de optie *Een bestand selecteren* om het certificaatbestand **X509testcertificate.pem** te selecteren dat in de vorige stappen is gemaakt.
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Druk op de knop **Opslaan** nadat u bent voltooid. 

     [![Individuele inschrijving voor X.509-attest toevoegen in de portal](./media/python-quick-create-simulated-device-x509/device-enrollment.png)](./media/python-quick-create-simulated-device-x509/device-enrollment.png#lightbox)

   Als het apparaat is ingeschreven, wordt uw X.509-apparaat weergegeven als **riot-device-cert** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 

## <a name="simulate-the-device"></a>Het apparaat simuleren

1. Selecteer **Overzicht**in het menu Apparaatvoorzieningsservice . Let op uw _ID-scope_ en _global service eindpunt_.

    ![Service-informatie](./media/python-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. [Download en installeer Python 2.x of 3.x](https://www.python.org/downloads/). Zorg ervoor dat u de 32-bits of 64-bits installatie gebruikt, zoals vereist door uw configuratie. Zorg ervoor dat u Python toevoegt aan uw platformspecifieke omgevingsvariabelen als u hierom wordt gevraagd tijdens de installatie. Als u Python 2.x gebruikt, moet u mogelijk [pip* installeren of upgraden*, het Python-pakketbeheersysteem](https://pip.pypa.io/en/stable/installing/).
    
    > [!NOTE] 
    > Als u van Windows gebruikmaakt, installeert u ook [Visual C++ Redistributable voor Visual Studio 2015](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads). Voor de pip-pakketten is het herdistribueerbare pakket vereist om de C-DLL's te laden en uit te voeren.

3. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) voor het bouwen van de Python-pakketten.

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

8. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw Provisioning-service en open de blade **Device Explorer**. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is geslaagd, wordt de apparaat-ID weergegeven op de blade **Device Explorer** met de *STATUS***ingeschakeld**. Mogelijk moet u bovenop op de knop **Vernieuwen** drukken als u het blad al hebt geopend voordat u de toepassing van het voorbeeldapparaat uitvoert. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/python-quick-create-simulated-device-x509/registration.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie.
>

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Open het **mes Inschrijvingen beheren** voor uw service en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* van het apparaat dat u in deze quickstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster. 
3. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Open het **IE-apparaatblad** voor uw hub, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat op uw Windows-machine gemaakt en deze op uw IoT-hub ingericht met behulp van de Azure IoT Hub Device Provisioning Service op de portal. Ga voor meer informatie over het programmatisch inschrijven van uw X.509-apparaat door naar de snelle start voor programmatische inschrijving van X.509-apparaten. 

> [!div class="nextstepaction"]
> [Azure quickstart - X.509-apparaten inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-python.md)
