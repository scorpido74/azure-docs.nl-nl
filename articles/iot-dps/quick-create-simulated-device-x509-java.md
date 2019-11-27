---
title: Gesimuleerd X. 509-apparaat naar Azure IoT Hub inrichten met behulp van Java
description: 'Azure-snelstart: een gesimuleerd X.509-apparaat maken en inrichten voor IoT Hub Device Provisioning Service met de SDK voor Java. Voor deze snelstart worden afzonderlijke registraties gebruikt.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: ec4c276b951e5cda43a4b224f25aac547cd26bec
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229576"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Quick Start: een gesimuleerd X. 509-apparaat maken en inrichten met behulp van de SDK voor Java-apparaten voor IoT Hub Device Provisioning Service
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze stappen wordt getoond hoe u een gesimuleerd X.509-apparaat maakt op een ontwikkelcomputer met Windows OS en het codevoorbeeld gebruikt om dit gesimuleerde apparaat te verbinden met de Device Provisioning Service en uw IoT-hub. 

Als u niet bekend bent met het proces van automatisch inrichten, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen registraties:
- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke registraties gedemonstreerd.

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


1. Ga met behulp van de opdrachtprompt uit de vorige stappen naar de map `target` en voer het JAR-bestand uit dat in de vorige stap is gemaakt.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Voer **N** in bij _Wilt u een algemene naam invoeren?_ Kopieer de uitvoer van `Client Cert` naar het klembord, vanaf *-----BEGIN CERTIFICATE-----* tot en met *-----END CERTIFICATE-----* .

   ![Generator voor afzonderlijke certificaten](./media/java-quick-create-simulated-device-x509/individual.png)

3. Maak een bestand met de naam **_X509individual.pem_** op de Windows-computer, open het in een editor naar keuze en kopieer de inhoud van het klembord naar dit bestand. Sla het bestand op en sluit de editor.

4. Voer in de opdrachtprompt **N** in bij _Wilt u de verificatiecode invoeren?_ en houd de programma-uitvoer geopend zodat u deze later in deze snelstart kunt raadplegen. Later kopieert u de waarden `Client Cert` en `Client Cert Private Key` voor gebruik in de volgende sectie.

5. Meld u aan bij [Azure Portal](https://portal.azure.com), klik in het linkermenu op de knop **Alle resources** en open het Device Provisioning Service-exemplaar.

6. Selecteer **Manage enrollments** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Afzonderlijke registraties** en klik vervolgens op de knop **Afzonderlijke inschrijving toevoegen** bovenaan. 

7. Voer onder het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Klik onder het *PEM- of CER-bestand van het primaire certificaat* op een *Select a file* om het certificaatbestand **X509individual.pem** te selecteren dat in de vorige stappen is gemaakt.  
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
     - Klik op de knop **Save** als u klaar bent. 

     [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Als het apparaat is ingeschreven, wordt het X.509-apparaat weergegeven als **microsoftriotcore** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 



## <a name="simulate-the-device"></a>Het apparaat simuleren

1. Selecteer **Overzicht** in de overzichtsblade Device Provisioning Service en noteer de waarden voor _Id-bereik_ en _Globaal eindpunt voor inrichtingsservice_.

    ![Service-informatie](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Open een opdrachtprompt. Ga naar de map met het voorbeeldproject van de Java-SDK-opslagplaats.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Voer de gegevens voor de inrichtingsservice en de X.509-identiteit in de code in. Deze worden voorafgaand aan de registratie van het apparaat tijdens het automatisch inrichten gebruikt voor attestation van het gesimuleerde apparaat:

   - Bewerk bestand `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` om de eerder genoteerde waarden voor _Id-bereik_ en _Globaal eindpunt voor inrichtingsservice_ toe te voegen. Neem ook de waarden voor _Clientcertificaat_ en _Persoonlijke sleutel van clientcertificaat_ op die u in de vorige sectie hebt genoteerd.

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

4. Bouw het voorbeeld. Navigeer naar de map `target` en voer het gemaakte JAR-bestand uit.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Navigeer in Azure Portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en open de blade **Device Explorer**. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is voltooid, wordt de apparaat-id weergegeven op de blade **Device Explorer** met de *STATUS* **ingeschakeld**.  U moet mogelijk klikken op de knop **Vernieuwen** bovenaan als u de blade vóór het uitvoeren van de voorbeeldapparaattoepassing al hebt geopend. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.
>


## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze Snelstartgids zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources die via deze Snelstartgids zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer uw Device Provisioning Service. Open de Blade **inschrijvingen beheren** voor uw service en klik vervolgens op het tabblad **afzonderlijke inschrijvingen** . Selecteer de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik bovenaan op de knop **verwijderen** . 
3. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, selecteer de *apparaat-id* van het apparaat dat u hebt geregistreerd in deze quickstart en klik vervolgens bovenaan op de knop **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een gesimuleerd X.509-apparaat gemaakt op uw Windows-computer. U hebt de registratie ervan geconfigureerd in Azure IoT Hub Device Provisioning Service en vervolgens het apparaat automatisch ingericht voor IoT Hub. Als u wilt weten hoe u uw X.509-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een X.509-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: X.509-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)
