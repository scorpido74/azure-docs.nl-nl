---
title: TPM-apparaat inschrijven voor Azure Device Provisioning Service met Java
description: Snelstart - TPM-apparaat inschrijven voor Azure IoT Hub Device Provisioning Service (DPS) met Java-service SDK. In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: c199d5be4c103c80a6fcc126af70f48367909f64
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241694"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Snelstart: TPM-apparaat inschrijven voor IoT Hub-apparaatinrichtingsservice met Java Service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In deze quickstart maakt u programmatisch een individuele inschrijving voor een gesimuleerd TPM-apparaat in de Azure IoT Hub Device Provisioning Service met behulp van de Java Service SDK met behulp van een voorbeeld java-toepassing.

## <a name="prerequisites"></a>Vereisten

- Voltooiing van het instellen van [de IoT Hub Device Provisioning Service met de Azure-portal.](./quick-setup-auto-provision.md)
- Voltooiing van [Lees cryptografische sleutels van het TPM-apparaat](quick-create-simulated-device.md#simulatetpm).
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Java SE Development Kit 8](https://aka.ms/azure-jdks). Deze quickstart installeert hieronder de [Java Service SDK.](https://azure.github.io/azure-iot-sdk-java/service/) Het werkt op zowel Windows als Linux. Deze quickstart maakt gebruik van Windows.
- [Maven 3.](https://maven.apache.org/download.cgi)
- [Git.](https://git-scm.com/download/)

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden 

1. Zorg ervoor dat [Java SE Development Kit 8](https://aka.ms/azure-jdks) is geïnstalleerd op de computer. 

2. Stel omgevingsvariabelen in voor de Java-installatie. De `PATH`-variabele moet het volledige pad naar de map *jdk1.8.x\bin* bevatten. Als dit de eerste Java-installatie op de computer is, maakt u een nieuwe omgevingsvariabele met de naam `JAVA_HOME`, en laat u deze naar het volledige pad naar de map *jdk1.8.x* verwijzen. Op Windows-computers vindt u deze map in de map *C:\\Program Files\\Java\\*. U kunt omgevingsvariabelen maken of bewerken door te zoeken naar **De omgevingsvariabelen van het systeem bewerken** in het **Configuratiescherm** van de Windows-computer. 

   U kunt controleren of Java goed is ingesteld op de computer door de volgende opdracht uit te voeren in het opdrachtvenster:

    ```cmd\sh
    java -version
    ```

3. [Maven 3](https://maven.apache.org/download.cgi) downloaden en uitpakken op de computer. 

4. Bewerk omgevingsvariabele `PATH` zodat deze naar de map *apache-maven-3.x.x\\bin* verwijst binnen de map waarin Maven is uitgepakt. U kunt bevestigen dat Maven juist is geïnstalleerd door deze opdracht uit te voeren in het opdrachtvenster:

    ```cmd\sh
    mvn --version
    ```

5. Zorg ervoor dat [git](https://git-scm.com/download/) is geïnstalleerd op de computer en is toegevoegd aan de omgevingsvariabele `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>De Java-voorbeeldcode downloaden en wijzigen

In deze sectie ziet u hoe u de inrichtingsgegevens van het TPM-apparaat toevoegt aan de voorbeeldcode. 

1. Open een opdrachtprompt. Kloon de GitHub repo voor het voorbeeld van apparaatinschrijvingscode met behulp van de [Java Service SDK:](https://azure.github.io/azure-iot-sdk-java/service/)
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Navigeer in de gedownloade broncode naar de voorbeeldmap **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Open het bestand **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** in een editor naar keuze, en voeg de volgende gegevens toe:

   1. Voeg de `[Provisioning Connection String]` voor de inrichtingsservice als volgt toe vanuit de portal:
       1. Navigeer naar uw inrichtingsservice in de [Azure-portal.](https://portal.azure.com) 
       2. Open **Gedeeld toegangsbeleid** en selecteer een beleid met de machtiging *EnrollmentWrite*.
       3. Kopieer de **Verbindingsreeks van de primaire sleutel**. 

           ![De verbindingsreeks voor de inrichting ophalen uit de portal](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. Vervang `[Provisioning Connection String]` in het voorbeeldcodebestand **_ServiceEnrollmentSample.java_** door de **Verbindingsreeks van de primaire sleutel**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Voeg de TPM-apparaatgegevens toe:
       1. Haal de *Registratie-id* en de *TPM-goedkeuringssleutel* op voor een TPM-apparaatsimulatie door de stappen te volgen die leiden naar de sectie [TPM-apparaat simuleren](quick-create-simulated-device.md#simulatetpm).
       2. Gebruik de **_Registratie-id_** en de **_Goedkeuringssleutel_** uit de uitvoer van de vorige stap om `[RegistrationId]` en `[TPM Endorsement Key]` in het voorbeeldcodebestand **_ServiceEnrollmentSample.java_** te vervangen:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Desgewenst kunt u de inrichtingsservice configureren via de voorbeeldcode:
      - Volg deze stappen om deze configuratie toe te voegen aan het voorbeeld:
        1. Navigeer naar de IoT-hub die is gekoppeld aan de inrichtingsservice in [Azure Portal](https://portal.azure.com). Open het tabblad **Overzicht** voor de hub en kopieer de **Hostnaam**. Wijs deze **Hostnaam** toe aan de parameter *IOTHUB_HOST_NAME*.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Wijs een beschrijvende naam toe aan de parameter *DEVICE_ID* en houd *PROVISIONING_STATUS* aan als standaardwaarde *ENABLED*. 
    
      - OF, als u de inrichtingsservice niet wilt configureren, zorgt u ervoor dat u de volgende instructies in het bestand _ServiceEnrollmentSample.java_ als commentaar markeert of verwijdert:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Bestudeer de voorbeeldcode. Hiermee wordt een afzonderlijke TPM-apparaatinschrijving gemaakt, bijgewerkt, en verwijderd, en wordt voor deze inschrijving een query uitgevoerd. Markeer de volgende coderegels aan het einde van het bestand _ServiceEnrollmentSample.java_ tijdelijk als commentaar voor een geslaagde inschrijving via de portal:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Sla het bestand _ServiceEnrollmentSample.java_ op.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>De Java-voorbeeldcode bouwen en uitvoeren

1. Open een opdrachtvenster en navigeer naar de map **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Bouw de voorbeeldcode met behulp van deze opdracht:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Deze opdracht downloadt [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) het Maven-pakket naar uw machine. Dit pakket bevat de binaire bestanden voor de [Java Service SDK](https://azure.github.io/azure-iot-sdk-java/service/), die de voorbeeldcode moet bouwen. 

3. Voer het voorbeeld uit met behulp van deze opdrachten in het opdrachtvenster:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Houd het uitvoervenster in de gaten voor een geslaagde inschrijving. 

5. Navigeer naar de inrichtingsservice in Azure Portal. Selecteer **Inschrijvingen beheren**en selecteer het tabblad Individuele *Registration ID* **inschrijvingen.** 

    ![De TPM-inschrijving controleren in de portal](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent het voorbeeld van de Java-service te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het Java-voorbeeld op de computer.
1. Sluit het venster van de TPM-simulator dat u wellicht hebt gemaakt om uw TPM-apparaat te simuleren.
1. Navigeer naar de service Apparaatinrichting in de Azure-portal, selecteer Inschrijvingen beheren en schakel het tabblad **Individuele inschrijvingen** in. Schakel het selectievakje in naast de *registratie-id* voor het **inschrijvingsitem**dat u met deze quickstart hebt gemaakt en druk op de knop **Verwijderen** boven aan het deelvenster.

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een gesimuleerd TPM-apparaat ingeschreven bij uw service voor apparaatinrichting. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
