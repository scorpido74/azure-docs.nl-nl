---
title: Snelstart - Symmetrische sleutel gebruiken om gesimuleerd apparaat in te richten op Azure IoT Hub met Java
description: In deze quickstart gebruikt u de SDK van het Java-apparaat om een gesimuleerd apparaat te maken dat symmetrische sleutel gebruikt met de DpS (Azure IoT Hub Device Provisioning Service)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76941400"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels

In deze snelstartgids leert u hoe u een apparaatsimulator kunt maken en uitvoeren op een Windows-ontwikkelcomputer. U configureert dit gesimuleerde apparaat om een symmetrische sleutel te gebruiken om te verifiëren met een DPS-exemplaar (Device Provisioning Service) en wordt toegewezen aan een IoT-hub. Voorbeeldcode van de [Microsoft Azure IoT SDK's voor Java](https://github.com/Azure/azure-iot-sdk-java) wordt gebruikt om een opstartreeks te simuleren voor het apparaat dat de inrichting initieert. Het apparaat wordt herkend op basis van een individuele inschrijving met een DPS-serviceexemplaar en toegewezen aan een IoT-hub.

Hoewel in dit artikel wordt aangetoond dat u een individuele inschrijving hebt, u inschrijvingsgroepen gebruiken. Er zijn enkele verschillen bij het gebruik van inschrijvingsgroepen. U moet bijvoorbeeld een afgeleide apparaatsleutel gebruiken met een unieke registratie-id voor het apparaat. Hoewel inschrijvingsgroepen met symmetrische sleutel niet tot oudere apparaten beperkt zijn, biedt [Oudere apparaten inrichten door middel van attestation met een symmetrische sleutel](how-to-legacy-device-symm-key.md) een voorbeeld van een inschrijvingsgroep. Zie [Groepsinschrijvingen voor attestation met behulp van een symmetrische sleutel](concepts-symmetric-key-attestation.md#group-enrollments) voor meer informatie.

Als u niet bekend bent met het proces van automatische inrichting, bekijk dan de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). 

Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze snelstart. Voor deze snelstartgids wordt aangenomen dat u al een Device Provisioning Service-exemplaar hebt gemaakt.

Dit artikel is gericht op een Windows-gebaseerd werkstation. U kunt de procedures echter ook uitvoeren op Linux. Zie [Inrichten voor multitenancy](how-to-provision-multitenant.md) voor een Linux-voorbeeld.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

* Zorg ervoor dat u [Java SE Development Kit 8](https://aka.ms/azure-jdks) of hoger op uw machine hebt geïnstalleerd.

* Download en installeer [Maven](https://maven.apache.org/install.html).

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>De Java SDK-omgeving voorbereiden 

1. Zorg ervoor dat Git op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

2. Open een opdrachtprompt. Kloon het codevoorbeeld voor de GitHub-opslagplaats voor apparaatsimulatie:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Navigeer naar `azure-iot-sdk-java` de hoofdmap en bouw het project om alle benodigde pakketten te downloaden.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Een apparaatinschrijving maken

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer de knop **Alle bronnen** in het linkermenu en open het dps-exemplaar (Device Provisioning service).

2. Selecteer het tabblad **Inschrijvingen beheren** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 

3. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in en druk op de knop **Opslaan.**

   - **Mechanisme:** selecteer **Symmetrische sleutel** als *mechanisme* voor identiteitscontrole.

   - **Sleutels automatisch genereren:** Schakel dit selectievakje in.

   - **Registratie-ID**: voer een registratie-ID voor het identificeren van de inschrijving. Gebruik alleen kleine alfanumerieke tekens en streepjes ('-'). Bijvoorbeeld **symm-key-java-device-007**.

   - **IoT Hub apparaat-ID:** voer een apparaat-ID in. Bijvoorbeeld **java-apparaat-007**.

     ![Afzonderlijke inschrijving toevoegen voor attestation met symmetrische sleutel in de portal](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Zodra u uw inschrijving hebt opgeslagen, worden de **primaire sleutel** en **secundaire sleutel** gegenereerd en toegevoegd aan het inschrijvingsitem. De inschrijving van uw symmetrische sleutelapparaat wordt weergegeven als **symm-key-java-device-007** onder de kolom *Registratie-id* op het tabblad *Individuele inschrijvingen.* 

    Open de inschrijving en kopieer de waarde van uw gegenereerde **primaire sleutel**. U gebruikt deze sleutelwaarde en de **registratie-id** later wanneer u de Java-code voor het apparaat bijwerkt.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Opstartvolgorde van apparaten simuleren

In deze sectie werkt u de voorbeeldcode van het apparaat bij om de opstartvolgorde van het apparaat naar uw DPS-exemplaar te verzenden. Deze opstartreeks zorgt ervoor dat het apparaat wordt herkend, geverifieerd en toegewezen aan een IoT-hub die is gekoppeld aan het DPS-exemplaar.

1. Selecteer in het menu Apparaatinrichtingsservice de optie **Overzicht** en noteer uw _ID-bereik_ en _het globale eindpunt van de inrichtingsservice_.

    ![Service-informatie](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Open de voorbeeldcode van het Java-apparaat voor bewerking. Het volledige pad naar de voorbeeldcode van het apparaat is:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Voeg het globale eindpunt _van id-bereik_ en _inrichtingsservice_ toe van uw DPS-exemplaar. Voeg ook de primaire symmetrische sleutel en de registratie-ID toe die u voor uw individuele inschrijving hebt gekozen. Sla uw wijzigingen op. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Open een opdrachtprompt voor het bouwen. Navigeer naar de voorbeeldmap van het voorbeeldvoorbeeld van de Java SDK-opslagplaats.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Bouw het voorbeeld en `target` navigeer naar de map om het gemaakte .jar-bestand uit te voeren.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. De verwachte output moet op het volgende lijken:

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

6. Navigeer in Azure Portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en open de blade **Device Explorer**. Nadat het gesimuleerde symmetrische sleutelapparaat met succes naar de hub is **ingericht,** wordt de apparaat-id weergegeven op het **apparaat-explorer-blad,** met *STATUS* als ingeschakeld .  Mogelijk moet u bovenop op de knop **Vernieuwen** drukken als u het blad al hebt geopend voordat u de toepassing van het voorbeeldapparaat uitvoert. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie.
>


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Open **Inschrijvingen voor** uw service beheren en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* van het apparaat dat u in deze quickstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster. 
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Open **IoT-apparaten** voor uw hub, schakel het selectievakje in naast de *apparaat-id* van het apparaat dat u in deze quickstart hebt geregistreerd en druk op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd apparaat op uw Windows-machine gemaakt en deze op uw IoT-hub ingericht met behulp van symmetrische sleutel met de Azure IoT Hub Device Provisioning Service op de portal. Ga voor meer informatie over het programmatisch inschrijven van uw apparaat door naar de snelle start voor programmatische inschrijving van X.509-apparaten. 

> [!div class="nextstepaction"]
> [Azure quickstart - X.509-apparaten inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
