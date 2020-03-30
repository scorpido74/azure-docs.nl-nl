---
title: Snelstart - Een gesimuleerd TPM-apparaat inrichten op Azure IoT Hub met Java
description: Snelstart - Een gesimuleerd TPM-apparaat maken en inrichten met Java-apparaat SDK voor Azure IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: ce67b5e254a62def5f8b024e960cea7f8780e8b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605488"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-java-device-sdk-for-azure-iot-hub-device-provisioning-service"></a>Snelstart: een gesimuleerd TPM-apparaat maken en inrichten met Java-apparaat SDK voor Azure IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In deze quickstart maakt u een gesimuleerd IoT-apparaat op een Windows-computer. Het gesimuleerde apparaat bevat een TPM-simulator als Hardware Security Module (HSM). U gebruikt java-code voor het voorbeeld van het apparaat om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving bij de DPS (Device Provisioning Service).

## <a name="prerequisites"></a>Vereisten

- Herziening van [auto-inrichting concepten](concepts-auto-provisioning.md).
- Voltooiing van [de Inrichtingsservice voor IoT-hub-apparaten met de Azure-portal](./quick-setup-auto-provision.md).
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Development Kit 8](https://aka.ms/azure-jdks).
- [Maven.](https://maven.apache.org/install.html)
- [Git.](https://git-scm.com/download/)

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat [Java SE Development Kit 8](https://aka.ms/azure-jdks) is geïnstalleerd op de computer.

1. Download en installeer [Maven](https://maven.apache.org/install.html).

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt. Kloon het codevoorbeeld voor apparaatsimulatie uit de GitHub-opslagplaats.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Voer de [TPM-simulator](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) uit als de [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) voor het gesimuleerde apparaat. Klik op **Allow access** zodat de instellingen voor _Windows Firewall_ kunnen worden gewijzigd. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit venster niet; je moet deze simulator draaiende te houden tot het einde van deze quickstart gids. 

    ```cmd/sh
    .\azure-iot-sdk-java\provisioning\provisioning-tools\tpm-simulator\Simulator.exe
    ```

    ![TPM-simulator](./media/java-quick-create-simulated-device/simulator.png)

1. Ga via een aparte opdrachtprompt naar de hoofdmap en stel de voorbeeldafhankelijkheden samen.

    ```cmd/sh
    cd azure-iot-sdk-java
    mvn install -DskipTests=true
    ```

1. Navigeer naar de voorbeeldmap.

    ```cmd/sh
    cd provisioning/provisioning-samples/provisioning-tpm-sample
    ```

1. Meld u aan bij de Azure-portal, selecteer de knop **Alle bronnen** in het linkermenu en open de service Apparaatvoorziening. Let op uw _ID-scope_ en _het global endpoint van de inrichtingsservice_.

    ![Informatie over de Device Provisioning Service](./media/java-quick-create-simulated-device/extract-dps-endpoints.png)

1. Bewerk `src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningTpmSample.java` om uw _ID-scope_ en _provisioning service Global Endpoint_ op te nemen zoals eerder vermeld.  

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    ```
    Sla het bestand op.

1. Gebruik de volgende opdrachten om het project te bouwen, naar de doelmap te navigeren en het gemaakte .jar-bestand uit te voeren. Vervang `version` de tijdelijke aanduiding door uw versie van Java.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-tpm-sample-{version}-with-deps.jar
    ```

1. Het programma wordt uitgevoerd. Noteer de _goedkeuringssleutel_ en _registratie-id_ voor de volgende sectie en laat het programma draaien.

    ![Java TPM-apparaatprogramma](./media/java-quick-create-simulated-device/program.png)
    

## <a name="create-a-device-enrollment-entry"></a>Een vermelding voor apparaatinschrijving maken

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om één apparaat in te schrijven.

Dit artikel toont individuele inschrijvingen.

1. Meld u aan bij de Azure-portal, selecteer de knop **Alle bronnen** in het linkermenu en open de service Apparaatvoorziening.

1. Selecteer inschrijvingen beheren in het menu **Apparaatinrichtingsservice**. Selecteer het tabblad **Individuele inschrijvingen** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 

1. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor uw TPM-apparaat in op basis van de eerder aangenomen waarden.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id in*. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest er geen aan te geven, wordt de registratie-id gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Druk op de knop **Opslaan** nadat u bent voltooid. 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/java-quick-create-simulated-device/enterdevice-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *registratie-id* ervan weergegeven in de lijst onder het tabblad *Individuele inschrijvingen*. 


## <a name="simulate-the-device"></a>Het apparaat simuleren

1. Druk in het opdrachtvenster met de Java-voorbeeldcode op uw machine op *Enter* om de toepassing te blijven uitvoeren. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren.  

    ![Voltooien van Java TPM-apparaatprogramma](./media/java-quick-create-simulated-device/program-final.png)

1. Bij een succesvolle inrichting van uw gesimuleerde apparaat naar de IoT-hub die is gekoppeld aan uw inrichtingsservice, wordt de apparaat-id weergegeven op het **Iot-apparaatblad** van de hub.

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/java-quick-create-simulated-device/hubregistration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie.


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Open het **mes Inschrijvingen beheren** voor uw service en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* van het apparaat dat u in deze quickstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster. 
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Open het **IE-apparaatblad** voor uw hub, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een TPM-gesimuleerd apparaat op uw machine gemaakt en deze in gerichte tijd in uw IoT-hub met behulp van de IoT Hub Device Provisioning Service. Ga door met de snelle start voor programmatische inschrijving van een TPM-apparaat om uw TPM-apparaat programmatisch in te schrijven. 

> [!div class="nextstepaction"]
> [Azure quickstart - TPM-apparaat inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-java.md)
