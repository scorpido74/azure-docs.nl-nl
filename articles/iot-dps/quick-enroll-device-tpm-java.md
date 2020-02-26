---
title: TPM-apparaat inschrijven bij Azure Device Provisioning Service met behulp van Java
description: 'Quick Start: TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service (DPS) met behulp van de Java Service-SDK. In deze quickstart wordt gebruikgemaakt van afzonderlijke registraties.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: c199d5be4c103c80a6fcc126af70f48367909f64
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605367"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Quick Start: TPM-apparaat inschrijven voor IoT Hub Device Provisioning Service met behulp van de Java Service-SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In deze Quick Start maakt u programmatisch een afzonderlijke inschrijving voor een gesimuleerd TPM-apparaat in de Azure IoT Hub Device Provisioning Service met behulp van de Java Service-SDK met behulp van een Java-voorbeeld toepassing.

## <a name="prerequisites"></a>Vereisten

- Volt ooien van [het instellen van de IOT hub Device Provisioning Service met de Azure Portal](./quick-setup-auto-provision.md).
- Het [lezen van cryptografische sleutels van het TPM-apparaat](quick-create-simulated-device.md#simulatetpm)is voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](https://aka.ms/azure-jdks). In deze Snelstartgids wordt de onderstaande SDK voor de [Java-service](https://azure.github.io/azure-iot-sdk-java/service/) geïnstalleerd. Het werkt op Windows en Linux. Deze Snelstartgids maakt gebruik van Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden 

1. Zorg ervoor dat [Java SE Development Kit 8](https://aka.ms/azure-jdks) is geïnstalleerd op de computer. 

2. Stel omgevingsvariabelen in voor de Java-installatie. De `PATH`-variabele moet het volledige pad naar de map *jdk1.8.x\bin* bevatten. Als dit de eerste Java-installatie op de computer is, maakt u een nieuwe omgevingsvariabele met de naam `JAVA_HOME`, en laat u deze naar het volledige pad naar de map *jdk1.8.x* verwijzen. Op Windows-computers vindt u deze map in de map *C:\\Program Files\\Java\\* . U kunt omgevingsvariabelen maken of bewerken door te zoeken naar **De omgevingsvariabelen van het systeem bewerken** in het **Configuratiescherm** van de Windows-computer. 

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

1. Open een opdrachtprompt. Kloon de GitHub-opslag plaats voor de registratie code van het apparaat met behulp van de SDK van de [Java-service](https://azure.github.io/azure-iot-sdk-java/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Navigeer in de gedownloade broncode naar de voorbeeldmap **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** . Open het bestand **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** in een editor naar keuze, en voeg de volgende gegevens toe:

   1. Voeg de `[Provisioning Connection String]` voor de inrichtingsservice als volgt toe vanuit de portal:
       1. Navigeer naar de inrichtingsservice in [Azure Portal](https://portal.azure.com). 
       2. Open **Gedeeld toegangsbeleid** en selecteer een beleid met de machtiging *EnrollmentWrite*.
       3. Kopieer de **Verbindingsreeks van de primaire sleutel**. 

           ![De verbindingsreeks voor de inrichting ophalen uit de portal](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. Vervang  **in het voorbeeldcodebestandServiceEnrollmentSample.java**`[Provisioning Connection String]` door de **Verbindingsreeks van de primaire sleutel**.
    
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

1. Open een opdrachtvenster en navigeer naar de map **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_** .

2. Bouw de voorbeeldcode met behulp van deze opdracht:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Met deze opdracht wordt het Maven-pakket [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) gedownload naar de computer. Dit pakket bevat de binaire bestanden voor de [Java Service-SDK](https://azure.github.io/azure-iot-sdk-java/service/), die de voorbeeld code moet bouwen. 

3. Voer het voorbeeld uit met behulp van deze opdrachten in het opdrachtvenster:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Houd het uitvoervenster in de gaten voor een geslaagde inschrijving. 

5. Navigeer naar de inrichtingsservice in Azure Portal. Selecteer **inschrijvingen beheren**en selecteer het tabblad **afzonderlijke inschrijvingen** . u ziet dat de *registratie-id* van uw gesimuleerde TPM-apparaat nu wordt weer gegeven. 

    ![De TPM-inschrijving controleren in de portal](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent het Java service-voor beeld te verkennen, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het Java-voorbeeld op de computer.
1. Sluit het venster van de TPM-simulator dat u wellicht hebt gemaakt om uw TPM-apparaat te simuleren.
1. Navigeer naar uw Device Provisioning Service in de Azure Portal, selecteer **inschrijvingen beheren**en selecteer vervolgens het tabblad **afzonderlijke inschrijvingen** . Schakel het selectie vakje in naast de *registratie-id* voor de inschrijvings vermelding die u hebt gemaakt met behulp van deze Quick Start. Klik vervolgens op de knop **verwijderen** boven aan het deel venster.

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u een gesimuleerd TPM-apparaat Inge schreven bij de Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
