---
title: Gesimuleerd X.509-apparaat inrichten op Azure IoT Hub met Node.js
description: Maak en informeer een gesimuleerd X.509-apparaat met Node.js device SDK voor Azure IoT Hub Device Provisioning Service (DPS). Deze quickstart maakt gebruik van individuele inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 746f4adbf616f95c21874d7c1c48881f88c38d34
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605402"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstart: een X.509 gesimuleerd apparaat maken en inrichten met Behulp van Node.js device SDK voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze quickstart maakt u een gesimuleerd X.509-apparaat op een Windows-computer. U gebruikt de node.js-code van het apparaat om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving bij de DPS (Device Provisioning Service).

## <a name="prerequisites"></a>Vereisten

- Herziening van [auto-inrichting concepten](concepts-auto-provisioning.md).
- Voltooiing van [de Inrichtingsservice voor IoT-hub-apparaten met de Azure-portal](./quick-setup-auto-provision.md).
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org).
- [Git.](https://git-scm.com/download/)
- [OpenSSL](https://www.openssl.org/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Voltooi de stappen in [Set up the IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (IoT Hub Device Provisioning Service instellen met Azure Portal) voordat u verdergaat.

2. Zorg ervoor dat [Node.js v4.0 of hoger](https://nodejs.org) is geïnstalleerd op de computer.

3. Zorg ervoor dat [Git](https://git-scm.com/download/) op uw machine is geïnstalleerd en wordt toegevoegd aan de omgevingsvariabelen die toegankelijk zijn voor het opdrachtvenster. 

4. Zorg ervoor dat [OpenSSL](https://www.openssl.org/) op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Deze bibliotheek kan ofwel worden gebouwd en geïnstalleerd vanuit de bron, of [van derden](https://wiki.openssl.org/index.php/Binaries) worden gedownload en geïnstalleerd zoals [deze](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Als u uw _basis-_, _tussen-_ en/of _leaf_ X.509-certificaten al hebt gemaakt, kunt u deze stap en alle volgende stappen met betrekking tot het maken van certificaten overslaan.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Een zelfondertekend X.509-certificaat voor apparaten en een vermelding voor afzonderlijke registratie maken

In deze sectie gebruikt u een zelf-ondertekend X.509-certificaat. Het is hierbij belangrijk dat u rekening houdt met het volgende:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de [Azure IoT C-SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node.git) gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om één apparaat in te schrijven.

Dit artikel toont individuele inschrijvingen.

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

4. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer de knop **Alle bronnen** in het linkermenu en open de instantie Apparaatinrichting.

5. Selecteer inschrijvingen beheren in het menu **Apparaatinrichtingsservice**. Selecteer het tabblad **Individuele inschrijvingen** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 

6. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *bestand Primair certificaat .pem of .cer*de optie *Een bestand selecteren* om het certificaatbestand **{certificate-name}_cert.pem** te selecteren dat in de vorige stappen is gemaakt.  
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
     - Druk op de knop **Opslaan** nadat u bent voltooid. 

     [![Individuele inschrijving voor X.509-attest toevoegen in de portal](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Bij succesvolle inschrijving wordt uw X.509-apparaat weergegeven als **{certificaatnaam}** onder de kolom *Registratie-id* op het tabblad *Individuele inschrijvingen.*

## <a name="simulate-the-device"></a>Het apparaat simuleren

De [apparaat-SDK voor Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) biedt een eenvoudige manier om een apparaat te simuleren. Raadpleeg [Apparaatconcepten](https://docs.microsoft.com/azure/iot-dps/concepts-device) voor meer informatie.

1. Selecteer in de Azure-portal het **overzichtsblad** voor uw service voor apparaatinrichting en noteer de eindpunt- en **_id-scopewaarden_** **_voor GLobal-apparaten._**

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
    - Vervang `id scope` de **_id-scope_** die in **stap 1** hierboven is vermeld. 
    - Vervang `registration id` door de **_registratie-id_** die in de vorige sectie is vermeld.
    - Vervang `cert filename` en `key filename` door de bestanden die u hebt gekopieerd in **stap 2** hierboven. 

5. Voer het script uit en controleer of het apparaat is ingericht.

    ```cmd/sh
    node register_x509.js
    ```   

6. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en open het **IE-apparatenblad.** Bij een succesvolle inrichting van het gesimuleerde X.509-apparaat naar de hub wordt de apparaat-id weergegeven op het **iE-apparaatblad,** met *STATUS* zoals **ingeschakeld**. Mogelijk moet u bovenop op de knop **Vernieuwen** drukken als u het blad al hebt geopend voordat u de toepassing van het voorbeeldapparaat uitvoert. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie.


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Open het **mes Inschrijvingen beheren** voor uw service en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* van het apparaat dat u in deze quickstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster. 
3. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Open het **IE-apparaatblad** voor uw hub, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk op de knop **Verwijderen** boven aan het deelvenster.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat gemaakt en deze op uw IoT-hub ingericht met behulp van de Azure IoT Hub Device Provisioning Service op de portal. Ga voor meer informatie over het programmatisch inschrijven van uw X.509-apparaat door naar de snelle start voor programmatische inschrijving van X.509-apparaten. 

> [!div class="nextstepaction"]
> [Azure quickstart - X.509-apparaten inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-node.md)
