---
title: Snelstartgids-symmetrische sleutel gebruiken om gesimuleerd apparaat in te richten op Azure IoT Hub met behulp van Java
description: In deze Snelstartgids gebruikt u de Java-apparaat-SDK om een gesimuleerd apparaat te maken dat gebruikmaakt van symmetrische sleutel met de Azure-IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76941400"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels

In deze snelstartgids leert u hoe u een apparaatsimulator kunt maken en uitvoeren op een Windows-ontwikkelcomputer. U configureert dit gesimuleerde apparaat voor het gebruik van een symmetrische sleutel voor verificatie met een DPS-exemplaar (Device Provisioning Service) en worden toegewezen aan een IoT-hub. Voorbeeld code van de [Microsoft Azure IOT sdk's voor Java](https://github.com/Azure/azure-iot-sdk-java) wordt gebruikt voor het simuleren van een opstart procedure voor het apparaat dat het inrichten initieert. Het apparaat wordt herkend op basis van een afzonderlijke inschrijving met een DPS service-exemplaar en toegewezen aan een IoT-hub.

Hoewel in dit artikel wordt gedemonstreerd met het inrichten met een individuele inschrijving, kunt u registratie groepen gebruiken. Er zijn enkele verschillen bij het gebruik van registratie groepen. U moet bijvoorbeeld een afgeleide-apparaatwachtwoord gebruiken met een unieke registratie-ID voor het apparaat. Hoewel inschrijvingsgroepen met symmetrische sleutel niet tot oudere apparaten beperkt zijn, biedt [Oudere apparaten inrichten door middel van attestation met een symmetrische sleutel](how-to-legacy-device-symm-key.md) een voorbeeld van een inschrijvingsgroep. Zie [Groepsinschrijvingen voor attestation met behulp van een symmetrische sleutel](concepts-symmetric-key-attestation.md#group-enrollments) voor meer informatie.

Als u niet bekend bent met het proces van automatische inrichting, bekijk dan de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). 

Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze snelstart. Voor deze snelstartgids wordt aangenomen dat u al een Device Provisioning Service-exemplaar hebt gemaakt.

Dit artikel is gericht op een Windows-gebaseerd werkstation. U kunt de procedures echter ook uitvoeren op Linux. Zie [Inrichten voor multitenancy](how-to-provision-multitenant.md) voor een Linux-voorbeeld.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Controleer of [Java SE Development Kit 8](https://aka.ms/azure-jdks) of hoger op uw computer is geïnstalleerd.

* Download en installeer [Maven](https://maven.apache.org/install.html).

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>De Java SDK-omgeving voorbereiden 

1. Zorg ervoor dat Git op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

2. Open een opdrachtprompt. Kloon het codevoorbeeld voor de GitHub-opslagplaats voor apparaatsimulatie:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Ga naar de hoofdmap `azure-iot-sdk-java` Directory en bouw het project om alle benodigde pakketten te downloaden.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Een apparaatregistratie maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer de knop **alle resources** in het menu aan de linkerkant en open de instantie van uw Device Provisioning Service (DPS).

2. Selecteer het tabblad **inschrijvingen beheren** en selecteer vervolgens de knop **afzonderlijke registratie toevoegen** bovenaan. 

3. Voer in het deel venster **registratie toevoegen** de volgende informatie in en klik op de knop **Opslaan** .

   - **Mechanisme:** selecteer **Symmetrische sleutel** als *mechanisme* voor identiteitscontrole.

   - **Sleutels automatisch genereren**: Schakel dit selectie vakje in.

   - **Registratie-ID**: voer een registratie-ID voor het identificeren van de inschrijving. Gebruik alleen kleine alfanumerieke tekens en streepjes ('-'). Bijvoorbeeld **symm-Key-Java-Device-007**.

   - **IoT Hub apparaat-ID:** voer een apparaat-ID in. Bijvoorbeeld **Java-apparaat-007**.

     ![Afzonderlijke inschrijving toevoegen voor attestation met symmetrische sleutel in de portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Zodra u uw inschrijving hebt opgeslagen, worden de **primaire sleutel** en **secundaire sleutel** gegenereerd en toegevoegd aan de inschrijvings vermelding. De registratie van uw symmetrische-sleutel apparaat wordt weer gegeven als **symm-Key-Java-apparaat-007** onder de kolom *registratie-id* op het tabblad *afzonderlijke inschrijvingen* . 

    Open de inschrijving en kopieer de waarde van uw gegenereerde **primaire sleutel**. U gebruikt deze sleutel waarde en de **registratie-id** later wanneer u de Java-code voor het apparaat bijwerkt.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Opstart volgorde van apparaat simuleren

In deze sectie gaat u de voorbeeld code van het apparaat bijwerken om de opstart procedure van het apparaat naar uw DPS-exemplaar te verzenden. Als gevolg van deze opstart procedure wordt het apparaat herkend, geverifieerd en toegewezen aan een IoT-hub die is gekoppeld aan het DPS-exemplaar.

1. Selecteer **overzicht** in het menu Device Provisioning Service en noteer uw _id-bereik_ en het _Global-eind punt_voor het inrichten van de service.

    ![Service-informatie](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Open de voorbeeld code van het Java-apparaat om het te bewerken. Het volledige pad naar de voorbeeld code van het apparaat is:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Voeg het _id-bereik_ en het _Provisioning Service Global-eind punt_ van uw DPS-exemplaar toe. Neem ook de primaire symmetrische sleutel en de registratie-ID op die u voor de afzonderlijke inschrijving hebt gekozen. Sla uw wijzigingen op. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Open een opdracht prompt om te maken. Navigeer naar de map voor beeld van een project voor het inrichten van de Java-SDK-opslag plaats.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Bouw het voor beeld en ga naar de map `target` om het gemaakte jar-bestand uit te voeren.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. De verwachte uitvoer moet er ongeveer als volgt uitzien:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. Navigeer in Azure Portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en open de blade **Device Explorer**. Nadat het gesimuleerde symmetrische-sleutel apparaat is ingericht op de hub, wordt de apparaat-ID weer gegeven op de Blade **device Explorer** , met de *status* **ingeschakeld**.  Mogelijk moet u bovenaan op de knop **vernieuwen** klikken als u de Blade al hebt geopend voordat u de voorbeeld toepassing uitvoert. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.
>


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met het voor beeld van de apparaatclient, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Open **inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik boven aan het deel venster op de knop **verwijderen** . 
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Open **IOT-apparaten** voor uw hub, schakel het selectie vakje in naast de *apparaat-id* van het apparaat dat u in deze Quick Start hebt geregistreerd en druk op de knop **verwijderen** boven aan het deel venster.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een gesimuleerd apparaat op uw Windows-computer gemaakt en dit ingericht voor uw IoT-hub met behulp van de symmetrische sleutel met de Azure-IoT Hub Device Provisioning Service op de portal. Als u wilt weten hoe u uw apparaat programmatisch kunt registreren, gaat u verder met de Quick start voor programmatische inschrijving van X. 509-apparaten. 

> [!div class="nextstepaction"]
> [Azure Quick start-X. 509-apparaten inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
