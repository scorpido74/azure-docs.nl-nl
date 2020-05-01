---
title: Gesimuleerd X. 509-apparaat naar Azure IoT Hub inrichten met behulp van Java
description: 'Azure Quick Start: een gesimuleerd X. 509-apparaat maken en inrichten met behulp van de SDK voor Java-apparaten voor IoT Hub Device Provisioning Service (DPS). In deze Quick Start worden afzonderlijke registraties gebruikt.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 5122726cbda2145d190e3dbeb10a3c5b4ae65947
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82588441"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Quick Start: een gesimuleerd X. 509-apparaat maken en inrichten met behulp van de SDK voor Java-apparaten voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze Quick Start maakt u een gesimuleerd X. 509-apparaat op een Windows-computer. U gebruikt een voor beeld van een Java-code om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving met de Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Vereisten

- Beoordeling van [concepten voor automatische inrichting](concepts-auto-provisioning.md).
- [Het instellen van IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md)is voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](https://aka.ms/azure-jdks).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat [Java SE Development Kit 8](https://aka.ms/azure-jdks) is geïnstalleerd op de computer.

2. Download en installeer [Maven](https://maven.apache.org/install.html).

3. Zorg ervoor dat Git op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

4. Open een opdrachtprompt. Kloon het codevoorbeeld voor de GitHub-opslagplaats voor apparaatsimulatie:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Navigeer naar de hoofdmap van `azure-iot-sdk-`-java en bouw het project om alle benodigde pakketten te downloaden.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navigeer naar het certificaatgeneratorproject en bouw het project. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Een zelfondertekend X.509-certificaat voor apparaten en een vermelding voor afzonderlijke registratie maken

In deze sectie gebruikt u een zelf-ondertekend X.509-certificaat. Het is hierbij belangrijk dat u rekening houdt met het volgende:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de [Azure IoT C-SDK voor Java](https://github.com/Azure/azure-iot-sdk-java.git) gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om één apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

1. Ga met de opdracht prompt van de vorige stappen naar de `target` map en voer het jar-bestand uit dat u in de vorige stap hebt gemaakt.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Voer **N** in bij _Wilt u een algemene naam invoeren?_ Kopieer de uitvoer van `Client Cert` naar het klembord, vanaf *-----BEGIN CERTIFICATE-----* tot en met *-----END CERTIFICATE-----*.

   ![Generator voor afzonderlijke certificaten](./media/java-quick-create-simulated-device-x509/individual.png)

3. Maak een bestand met de naam **_X509individual.pem_** op de Windows-computer, open het in een editor naar keuze en kopieer de inhoud van het klembord naar dit bestand. Sla het bestand op en sluit de editor.

4. Voer in de opdracht prompt **N** in bij wilt _u de verificatie code_ invoeren en laat de programma-uitvoer open voor referentie verderop in de Snelstartgids. Later kopieert u de waarden `Client Cert` en `Client Cert Private Key` voor gebruik in de volgende sectie.

5. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer de knop **alle resources** in het menu aan de linkerkant en open uw Device Provisioning service-exemplaar.

6. Selecteer in het menu Device Provisioning Service de optie **inschrijvingen beheren**. Selecteer het tabblad **afzonderlijke** inschrijvingen en selecteer bovenaan de knop **afzonderlijke registratie toevoegen** . 

7. Voer in het deel venster **registratie toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *primaire certificaat. pem-of. cer-bestand*de optie *Selecteer een bestand* om het certificaat bestand **X509individual. pem** te selecteren dat in de vorige stappen is gemaakt.  
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
     - Als u klaar bent, drukt u op de knop **Opslaan** . 

     [![Afzonderlijke inschrijving voor X. 509-Attestation toevoegen in de portal](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Als het apparaat is ingeschreven, wordt het X.509-apparaat weergegeven als **microsoftriotcore** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 



## <a name="simulate-the-device"></a>Het apparaat simuleren

1. Selecteer **overzicht** in het menu Device Provisioning Service en noteer uw _id-bereik_ en het _Global-eind punt_voor het inrichten van de service.

    ![Service-informatie](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Open een opdrachtprompt. Ga naar de map met het voorbeeldproject van de Java-SDK-opslagplaats.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Voer de gegevens voor de inrichtingsservice en de X.509-identiteit in de code in. Deze worden voorafgaand aan de registratie van het apparaat tijdens het automatisch inrichten gebruikt voor attestation van het gesimuleerde apparaat:

   - Bewerk het bestand `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`om uw _id-bereik_ en het _globale eind punt_ voor het inrichten van de service op te maken, zoals eerder is aangegeven. Neem ook de waarden voor _Clientcertificaat_ en _Persoonlijke sleutel van clientcertificaat_ op die u in de vorige sectie hebt genoteerd.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Gebruik de volgende indeling bij het kopiëren/plakken van uw certificaat en privésleutel:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Bouw het voorbeeld. Ga naar de `target` map en voer het gemaakte jar-bestand uit.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Navigeer in Azure Portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en open de blade **Device Explorer**. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is voltooid, wordt de apparaat-id weergegeven op de blade **Device Explorer** met de *STATUS***ingeschakeld**.  Mogelijk moet u bovenaan op de knop **vernieuwen** klikken als u de Blade al hebt geopend voordat u de voorbeeld toepassing uitvoert. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie voor meer informatie [apparaat Apparaatdubbels begrijpen en gebruiken in IOT hub](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met het voor beeld van de apparaatclient, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Open de Blade **inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik boven aan het deel venster op de knop **verwijderen** . 
3. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Open de Blade **IOT-apparaten** voor uw hub, schakel het selectie vakje in naast de *apparaat-id* van het apparaat dat u in deze Quick Start hebt geregistreerd en druk op de knop **verwijderen** boven aan het deel venster.


## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een gesimuleerd X. 509-apparaat op uw Windows-computer gemaakt. U hebt de registratie ervan geconfigureerd in Azure IoT Hub Device Provisioning Service en vervolgens het apparaat automatisch ingericht voor IoT Hub. Als u wilt weten hoe u uw X. 509-apparaat programmatisch kunt registreren, gaat u verder met de Quick start voor programmatische registratie van X. 509-apparaten. 

> [!div class="nextstepaction"]
> [Azure Quick start-X. 509-apparaten inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
