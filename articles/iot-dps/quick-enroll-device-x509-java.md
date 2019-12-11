---
title: X. 509-apparaten registreren bij Azure Device Provisioning Service met behulp van Java
description: In deze quickstart wordt gebruikgemaakt van zowel groepsregistraties als afzonderlijke registraties. In deze Quick Start schrijft u X. 509-apparaten in bij Azure IoT Hub Device Provisioning Service (DPS) met behulp van Java.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 4be7dc3e93b61f036a4167347a45725eff185f04
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969707"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>Snelstart: X.509-apparaten registreren bij Device Provisioning Service met behulp van Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze snelstart wordt uitgelegd hoe u met Java programmatisch een groep gesimuleerde X.509-apparaten kunt registreren bij Azure IoT Hub Device Provisioning Service. Apparaten worden geregistreerd op een exemplaar van de inrichtingsservice door een [registratiegroep](concepts-service.md#enrollment-group) of een [afzonderlijke inschrijving](concepts-service.md#individual-enrollment) te maken. In deze snelstart wordt het maken van beide typen inschrijvingen uitgelegd. De inschrijvingen worden gemaakt met de [Java Service-SDK](https://azure.github.io/azure-iot-sdk-java/service/) met behulp van een Java-voorbeeldtoepassing. 

Voor deze snelstart wordt aangenomen dat u al een IoT-hub en Device Provisioning Service-exemplaar hebt gemaakt. Als u deze resources niet al hebt gemaakt, voert u de snelstart [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) uit voordat u verdergaat met dit artikel.

Hoewel de Java service-SDK werkt op Windows- en Linux-computers, gebruiken we in dit artikel een Windows-ontwikkelcomputer om stapsgewijs het inschrijvingsproces te doorlopen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Installeer de [Java SE Development Kit 8](https://aka.ms/azure-jdks).
* Installeer [Maven 3](https://maven.apache.org/download.cgi). U kunt uw huidige versie van Maven controleren door het volgende uit te voeren:

    ```cmd/sh
    mvn --version
    ```

* Installeer [Git](https://git-scm.com/download/).


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>De Java-voorbeeldcode downloaden en wijzigen

In dit gedeelte wordt een zelfondertekend X.509-certificaat gebruikt. Het is belangrijk dat u rekening houdt met de volgende punten:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

In de volgende stappen wordt gedemonstreerd hoe u de inrichtingsgegevens van het X.509-apparaat toevoegt aan de voorbeeldcode. 

1. Open een opdrachtprompt. Kloon de GitHub-opslagplaats voor het codevoorbeeld van de apparaatinschrijving met behulp van de Java service-SDK:
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Navigeer in de gedownloade broncode naar de voorbeeldmap **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** . Open het bestand **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** in een editor naar keuze, en voeg de volgende gegevens toe:

    1. Voeg de `[Provisioning Connection String]` voor de inrichtingsservice als volgt toe vanuit de portal:
        1. Navigeer naar de inrichtingsservice in [Azure Portal](https://portal.azure.com). 
        2. Open **Gedeeld toegangsbeleid** en selecteer een beleid met de machtiging *EnrollmentWrite*.
        3. Kopieer de **Verbindingsreeks van de primaire sleutel**. 

            ![De verbindingsreeks voor de inrichting ophalen uit de portal](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. Vervang `[Provisioning Connection String]` in het voorbeeldcodebestand **_ServiceEnrollmentGroupSample.java_** door de **Verbindingsreeks van de primaire sleutel**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Voeg het basiscertificaat voor de groep apparaten toe. Als u een voorbeeldbasiscertificaat nodig hebt, gebruikt u het hulpprogramma _X.509-certificaatgenerator_ als volgt:
        1. Navigeer in een opdrachtvenster naar de map **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_** .
        2. Bouw het hulpprogramma met behulp van de volgende opdracht:

                ```cmd\sh
                mvn clean install
                ```

        4. Voer het hulpprogramma uit met behulp van de volgende opdrachten:

                ```cmd\sh
                cd target
                java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
                ```

        5. Wanneer u hierom wordt gevraagd, kunt u ook een _algemene naam_ invoeren voor de certificaten.
        6. Het hulpprogramma genereert lokaal een **Clientcertificaat**, de **Persoonlijke sleutel van clientcertificaat** en het **Basiscertificaat**.
        7. Kopieer het **Basiscertificaat**, inclusief de regels **_-----BEGIN CERTIFICATE-----_** and **_-----END CERTIFICATE-----_** . 
        8. Wijs de waarde van het **Basiscertificaat** toe aan de parameter **PUBLIC_KEY_CERTIFICATE_STRING** zoals hieronder wordt weergegeven:

                ```Java
                private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                        "-----BEGIN CERTIFICATE-----\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                        "-----END CERTIFICATE-----\n";
                ```

        9. Sluit het opdrachtvenster of voer **n** in wanneer u wordt gevraagd om een *Verificatiecode*. 
 
    3. Desgewenst kunt u de inrichtingsservice configureren via de voorbeeldcode:
        - Volg deze stappen om deze configuratie toe te voegen aan het voorbeeld:
            1. Navigeer naar de IoT-hub die is gekoppeld aan de inrichtingsservice in [Azure Portal](https://portal.azure.com). Open het tabblad **Overzicht** voor de hub en kopieer de **Hostnaam**. Wijs deze **Hostnaam** toe aan de parameter *IOTHUB_HOST_NAME*.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Wijs een beschrijvende naam toe aan de parameter *DEVICE_ID* en houd *PROVISIONING_STATUS* aan als standaardwaarde *ENABLED*. 

        - OF, als u de inrichtingsservice niet wilt configureren, zorgt u ervoor dat u de volgende instructies in het bestand _ServiceEnrollmentGroupSample.java_ als commentaar markeert of verwijdert:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Bestudeer de voorbeeldcode. Hiermee wordt een groepsregistratie voor X.509-apparaten gemaakt, bijgewerkt, en verwijderd, en wordt voor deze registratie een query uitgevoerd. Markeer de volgende coderegels aan het einde van het bestand _ServiceEnrollmentGroupSample.java_ tijdelijk als commentaar voor een geslaagde inschrijving via de portal:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Sla het bestand _ServiceEnrollmentGroupSample.java_ op. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Een voorbeeldgroepsinschrijving bouwen en uitvoeren

1. Open een opdrachtvenster en navigeer naar de map **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_** .

2. Bouw de voorbeeldcode met behulp van deze opdracht:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Met deze opdracht wordt het Maven-pakket [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) gedownload naar de computer. Dit pakket bevat de binaire bestanden voor de Java service-SDK, die de voorbeeldcode nodig heeft voor het bouwen. Als u het hulpprogramma _X.509-certificaatgenerator_ hebt uitgevoerd in de vorige sectie, is dit pakket al gedownload op de computer. 

3. Voer het voorbeeld uit met behulp van deze opdrachten in het opdrachtvenster:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Houd het uitvoervenster in de gaten voor een geslaagde inschrijving.

5. Navigeer naar de inrichtingsservice in Azure Portal. Klik op **Inschrijvingen beheren**. U ziet dat de groep X.509-apparaten wordt weergegeven op het tabblad **Registratiegroepen** met een automatisch gegenereerde *GROEPSNAAM*. 

    ![De X.509-inschrijving controleren in de portal](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Wijzigingen voor het inschrijven van een enkel X.509-apparaat

Als u een enkel X.509-apparaat wilt inschrijven, wijzigt u als volgt de voorbeeldcode voor *afzonderlijke inschrijving* die wordt gebruikt in [TPM-apparaat inschrijven bij IoT Hub Device Provisioning Service met behulp van de Java service-SDK](quick-enroll-device-tpm-java.md#javasample):

1. Kopieer de *Algemene naam* van het X.509-clientcertificaat naar het klembord. Als u het hulpprogramma _X.509-certificaatgenerator_ wilt gebruiken zoals wordt weergegeven in de [vorige voorbeeldcodesectie](#javasample), voert u een _Algemene naam_ in voor uw certificaat, of gebruikt u de standaardwaarde **microsoftriotcore**. Gebruik deze **Algemene naam** als de waarde voor de variabele *REGISTRATION_ID*. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Wijzig de naam van de variabele *TPM_ENDORSEMENT_KEY* in *PUBLIC_KEY_CERTIFICATE_STRING*. Kopieer **Clientcertificaat** uit de uitvoer van het hulpprogramma _X.509-certificaatgenerator_ als de waarde voor de variabele *PUBLIC_KEY_CERTIFICATE_STRING*. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```
3. Vervang in de **hoofdfunctie** de regel `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` door het volgende om het X.509-clientcertificaat te gebruiken:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Sla het voorbeeldbestand voor de *afzonderlijke registratie* op, bouw het bestand en voer het uit. Gebruik hiervoor de stappen in het gedeelte [Build and run the sample code for individual enrollment](quick-enroll-device-tpm-java.md#runjavasample) (De voorbeeldcode voor afzonderlijke registraties bouwen en uitvoeren).


## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent het Java service-voor beeld te verkennen, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het Java-voorbeeld op de computer.
1. Sluit het venster voor de _X.509-certificaatgenerator_ op de computer.
1. Navigeer naar uw Device Provisioning Service in de Azure Portal, selecteer **inschrijvingen beheren**en selecteer vervolgens het tabblad **registratie groepen** . Schakel het selectie vakje in naast de *groeps naam* voor de X. 509-apparaten die u hebt Inge schreven met behulp van deze Quick Start. Klik vervolgens op de knop **verwijderen** boven aan het deel venster.  

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u een gesimuleerde groep X. 509-apparaten Inge schreven bij uw Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
