---
title: Een gesimuleerd X. 509-apparaat inrichten voor Azure IoT Hub met behulp van node. js
description: Een gesimuleerd X. 509-apparaat maken en inrichten met behulp van node. js apparaat SDK voor Azure IoT Hub Device Provisioning Service (DPS). In deze Quick Start worden afzonderlijke registraties gebruikt.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 5a713018cb616fd1b82c253554932f6589185e0b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976465"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstartgids: een gesimuleerd X. 509-apparaat maken en inrichten met de SDK voor node. js voor IoT Hub Device Provisioning Service
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Deze stappen laten zien hoe in u een vermelding voor apparaatinschrijving kunt maken in de Device Provisioning Service, hoe u een X.509-apparaat op uw ontwikkelcomputer kunt simuleren, het gesimuleerde apparaat kunt verbinden met de Device Provisioning Service en het apparaat kunt registreren op uw IoT-hub met behulp van de [apparaat-SDK voor Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node).

Als u niet bekend bent met het proces van automatische inrichting, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen registraties:
- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Voltooi de stappen in [Set up the IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (IoT Hub Device Provisioning Service instellen met Azure Portal) voordat u verdergaat.

2. Zorg ervoor dat [Node.js v4.0 of hoger](https://nodejs.org) is geïnstalleerd op de computer.

3. Zorg ervoor dat [Git](https://git-scm.com/download/) op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. 

4. Zorg ervoor dat [OpenSSL](https://www.openssl.org/) op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Deze bibliotheek kan ofwel worden gebouwd en geïnstalleerd vanuit de bron, of [van derden](https://wiki.openssl.org/index.php/Binaries) worden gedownload en geïnstalleerd zoals [deze](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Als u uw _basis-_ , _tussen-_ en/of _leaf_ X.509-certificaten al hebt gemaakt, kunt u deze stap en alle volgende stappen met betrekking tot het maken van certificaten overslaan.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Een zelfondertekend X.509-certificaat voor apparaten en een vermelding voor afzonderlijke registratie maken

In deze sectie gebruikt u een zelf-ondertekend X.509-certificaat. Het is hierbij belangrijk dat u rekening houdt met het volgende:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de [Azure IoT C-SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node.git) gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.


1. Open een opdrachtprompt. Kloon de GitHub-opslagplaats voor de codevoorbeelden:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Navigeer naar het certificaatgeneratorscript en bouw het project. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Maak een _leaf_ X.509-certificaat door het script uit te voeren met uw eigen _certificaatnaam_. De algemene naam van het leaf-certificaat wordt de [registratie-id](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id), dus zorg ervoor dat u alleen kleine letters en afbreekstreepjes gebruikt.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer de knop **alle resources** in het menu aan de linkerkant en open uw Device Provisioning service-exemplaar.

5. Selecteer in het menu Device Provisioning Service de optie **inschrijvingen beheren**. Selecteer het tabblad **afzonderlijke** inschrijvingen en selecteer bovenaan de knop **afzonderlijke registratie toevoegen** . 

6. Voer in het deel venster **registratie toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *primaire certificaat. pem-of. cer-bestand*de optie *Selecteer een bestand* om het certificaat bestand **{Certificate-name} _cert. pem** te selecteren dat in de vorige stappen is gemaakt.  
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
     - Als u klaar bent, drukt u op de knop **Opslaan** . 

     [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Bij een geslaagde inschrijving wordt uw X. 509-apparaat weer gegeven als **{certificaatpad}** onder de kolom *registratie-id* op het tabblad *afzonderlijke inschrijvingen* . Noteer deze waarde voor later.

## <a name="simulate-the-device"></a>Het apparaat simuleren

De [apparaat-SDK voor Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) biedt een eenvoudige manier om een apparaat te simuleren. Raadpleeg [Apparaatconcepten](https://docs.microsoft.com/azure/iot-dps/concepts-device) voor meer informatie.

1. Selecteer in de Azure Portal de Blade **overzicht** voor uw Device Provisioning Service en noteer de waarden van het **_globale apparaat-eind punt_** en de **_id-Scope_** .

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Kopieer uw _certificaat_ en _sleutel_ naar de voorbeeldmap.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Navigeer naar het testscript van het apparaat en bouw het project. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Bewerk het bestand **register\_x509.js**. Sla het bestand op nadat u de volgende wijzigingen hebt aangebracht.
    - Vervang `provisioning host` door het **_globaal apparaateindpunt_** dat u hebt genoteerd in **stap 1** hierboven.
    - Vervang `id scope` door het **_id-bereik_** dat u hebt genoteerd in **stap 1** hierboven. 
    - Vervang `registration id` door de **_registratie-id_** die u in de vorige sectie hebt genoteerd.
    - Vervang `cert filename` en `key filename` door de bestanden die u hebt gekopieerd in **stap 2** hierboven. 

5. Voer het script uit en controleer of het apparaat is ingericht.

    ```cmd/sh
    node register_x509.js
    ```   

6. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw inrichtings service en open de Blade **IOT-apparaten** . Wanneer het inrichten van het gesimuleerde X. 509-apparaat naar de hub is geslaagd, wordt de apparaat-ID weer gegeven op de Blade **IOT-apparaten** , met de *status* **ingeschakeld**. Mogelijk moet u bovenaan op de knop **vernieuwen** klikken als u de Blade al hebt geopend voordat u de voorbeeld toepassing uitvoert. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met het voor beeld van de apparaatclient, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Open de Blade **inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik boven aan het deel venster op de knop **verwijderen** . 
3. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Open de Blade **IOT-apparaten** voor uw hub, schakel het selectie vakje in naast de *apparaat-id* van het apparaat dat u in deze Quick Start hebt geregistreerd en druk op de knop **verwijderen** boven aan het deel venster.


## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een gesimuleerd X. 509-apparaat gemaakt en het ingericht voor uw IoT-hub met behulp van de Azure-IoT Hub Device Provisioning Service op de portal. Als u wilt weten hoe u uw X. 509-apparaat programmatisch kunt registreren, gaat u verder met de Quick start voor programmatische registratie van X. 509-apparaten. 

> [!div class="nextstepaction"]
> [Azure Quick start-X. 509-apparaten inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)
