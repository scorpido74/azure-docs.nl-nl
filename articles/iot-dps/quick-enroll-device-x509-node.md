---
title: Enroll X.509 devices to Azure Device Provisioning Service using Node.js
description: In deze quickstart wordt gebruikgemaakt van groepsregistraties. In deze snelstart registreert u X.509-apparaten bij Azure IoT Hub Device Provisioning Service met behulp van de Node.js service-SDK
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 6056859cf1742fc4bda6056ad7c1c78059a8407a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423228"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Snelstart: X.509-apparaten registreren bij Device Provisioning Service met behulp van Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze snelstart wordt uitgelegd hoe u met Node.js programmatisch een [registratiegroep](concepts-service.md#enrollment-group) kunt maken die gebruikmaakt van tussenliggende of hoofd-CA X.509-certificaten. De registratiegroep is gemaakt met behulp van de [IoT-SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) en een Node.js-voorbeeldtoepassing. Een registratiegroep beheert de toegang tot de inrichtingsservice voor apparaten die een gemeenschappelijk handtekeningcertificaat in hun certificaatketen delen. Zie [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie. Zie [Overzicht van beveiliging op basis van X.509-CA-certificaten](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview) voor meer informatie over het gebruik vanop X.509-certificaten gebaseerde Public Key Infrastructure (PKI) met Azure IoT Hub en Device Provisioning Service. 

Voor deze snelstart wordt aangenomen dat u al een IoT-hub en Device Provisioning Service-exemplaar hebt gemaakt. Als u deze resources niet al hebt gemaakt, voert u de snelstart [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) uit voordat u verdergaat met dit artikel.

Hoewel de stappen in dit artikel zowel op Windows- als op Linux-computers werken, is dit artikel ontwikkeld voor een Windows-ontwikkelcomputer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Vereisten

- Installeer [Node.js v4.0 of hoger](https://nodejs.org).
- Installeer [Git](https://git-scm.com/download/).


## <a name="prepare-test-certificates"></a>Testcertificaten voorbereiden

Voor deze snelstart hebt u een .pem- of een .cer-bestand met het openbare gedeelte van een tussenliggend of hoofd-CA x.509-certificaat nodig. Dit certificaat moet worden geüpload naar uw inrichtingsservice en door de service worden geverifieerd. 

De [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) bevat testhulpmiddelen waarmee u een X.509-certificaatketen kunt maken, een basis- of tussencertificaat kunt uploaden vanuit die keten en een bewijs van eigendom kunt uitvoeren met de service om het certificaat te verifiëren. Certificaten die zijn gemaakt met de SDK-hulpmiddelen, zijn alleen ontworpen voor **ontwikkeltesten**. Deze certificaten **mogen niet in productie worden gebruikt**. Ze bevatten in code vastgelegde wachtwoorden ('1234') die na 30 dagen verlopen. Zie [Een x.509-CA-certificaat ophalen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) in de documentatie van Azure IoT Hub voor meer informatie over het verkrijgen van certificaten die geschikt zijn voor productiegebruik.

Voer de volgende stappen uit om deze testhulpmiddelen te gebruiken om certificaten te genereren: 
 
1. Open een opdrachtprompt of Git Bash-shell en wijzig deze in een werkmap op uw computer. Voer de volgende opdracht uit voor het klonen van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-opslagplaats:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Deze bewerking kan enkele minuten in beslag nemen.

   De testhulpmiddelen bevinden zich in de *azure-iot-sdk-c/tools/CACertificates* van de opslagplaats die u hebt gekloond.    

2. Volg de stappen in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (CA-testcertificaten voor voorbeelden en zelfstudies beheren). 



## <a name="create-the-enrollment-group-sample"></a>Een voorbeeld van een registratiegroep maken 

 
1. Voer vanuit een opdrachtvenster in de werkmap het volgende uit:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Maak met behulp van een teksteditor het bestand **create_enrollment_group.js** in de werkmap. Voeg de volgende code toe aan het bestand en sla het op:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ```

## <a name="run-the-enrollment-group-sample"></a>Het voorbeeld van de registratiegroep uitvoeren
 
1. Als u het voorbeeld wilt uitvoeren, hebt u de verbindingsreeks voor de inrichtingsservice nodig. 
    1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service. 
    2. Click **Shared access policies**, then select the access policy you want to use to open its properties. Kopieer of noteer de verbindingsreeks van de primaire sleutel uit het venster **Toegangsbeleid**. 

       ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Zoals vermeld in [Testcertificaten voorbereiden](quick-enroll-device-x509-node.md#prepare-test-certificates) hebt u ook een .pem-bestand nodig dat een tussen- of basis-X.509 CA-certificaat bevat dat eerder is geüpload naar en is geverifieerd met uw inrichtingsservice. To check that your certificate has been uploaded and verified, on the Device Provisioning Service summary page in the Azure portal, select **Certificates**. Zoek het certificaat dat u wilt gebruiken voor de groepsregistratie en zorg ervoor dat de statuswaarde *geverifieerd* is.

    ![Geverifieerd certificaat in de portal](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Maak een registratiegroep voor uw certificaat door de volgende opdracht uit te voeren (inclusief de aanhalingstekens rond de opdrachtargumenten):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Als de inschrijving is gemaakt, worden in het opdrachtvenster de eigenschappen van de nieuwe registratiegroep weergegeven.

    ![Eigenschappen van de inschrijving in de opdrachtuitvoer](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Controleer of de registratiegroep is gemaakt. Selecteer in Azure Portal **Inschrijvingen beheren** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Registratiegroepen** en controleer of de nieuwe inschrijving (*eerste*) aanwezig is.

    ![Eigenschappen van de inschrijving in de portal](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Resources opschonen
If you plan to explore the Node.js service samples, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all Azure resources created by this quickstart.
 
1. Sluit het uitvoervenster van het Node.js-voorbeeld op de computer.
2. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select the **Enrollment Groups** tab. Select the check box next to the *GROUP NAME* for the X.509 devices you enrolled using this quickstart, and press the **Delete** button at the top of the pane.    
3. From your Device Provisioning service in the Azure portal, select **Certificates**, select the certificate you uploaded for this quickstart, and press the **Delete** button at the top of the **Certificate Details** window.  
 
## <a name="next-steps"></a>Volgende stappen
In this quickstart, you created a group enrollment for an X.509 intermediate or root CA certificate using the Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 
 
> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)