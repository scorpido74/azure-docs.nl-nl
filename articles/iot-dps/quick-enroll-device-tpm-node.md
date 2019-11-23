---
title: Enroll TPM device to Azure Device Provisioning Service using Node.js
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Node.js service SDK. In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 890ad28d99bfc53fa8a3fb40caf0469b31aeee61
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422964"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Node.js service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


In deze stappen ziet u hoe u programmatisch een afzonderlijke inschrijving voor een TPM-apparaat in Azure IoT Hub Device Provisioning Service kunt maken met behulp van de [Node.js service-SDK](https://github.com/Azure/azure-iot-sdk-node) en een Node.js-voorbeeldtoepassing. U kunt desgewenst ook een gesimuleerd TPM-apparaat bij de inrichtingsservice inschrijven met behulp van deze vermelding voor een afzonderlijke inschrijving. Hoewel deze stappen werken op Windows- en Linux-computers, gebruiken we in dit artikel een Windows-ontwikkelcomputer.

## <a name="prerequisites"></a>Vereisten

- Voltooi de stappen in [Set up the IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (IoT Hub Device Provisioning Service instellen met Azure Portal) voordat u verdergaat. 
-  Zorg ervoor dat [Node.js v4.0 of hoger](https://nodejs.org) is geïnstalleerd op de computer.
- If you want to enroll a simulated device at the end of this quickstart, follow the steps in [Create and provision a simulated device](quick-create-simulated-device.md) up until the step where you get an endorsement key for the device. Note down the endorsement key, you will use it later in this quickstart. **Volg niet de stappen voor het maken van een afzonderlijke inschrijving via Azure Portal.**
 
## <a name="create-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving maken 

 
1. Voer vanuit een opdrachtvenster in de werkmap het volgende uit:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Maak met behulp van een teksteditor een bestand **create_individual_enrollment.js** in de werkmap. Voeg de volgende code toe aan het bestand en sla het op:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving uitvoeren
  
1. Als u het voorbeeld wilt uitvoeren, hebt u de verbindingsreeks voor de inrichtingsservice nodig. 
    1. Sign in to the Azure portal, select the **All resources** button on the left-hand menu and open your Device Provisioning service. 
    2. Select **Shared access policies**, then select the access policy you want to use to open its properties. Kopieer of noteer de verbindingsreeks van de primaire sleutel uit het venster **Toegangsbeleid**. 

       ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. U hebt de goedkeuringssleutel ook nodig voor uw apparaat. Als u de stappen in de quickstart [Create and provision a simulated device](quick-create-simulated-device.md) (Een gesimuleerd apparaat maken en inrichten) voor het maken van een gesimuleerd TPM-apparaat hebt gevolgd, gebruikt u de sleutel voor dit apparaat. U kunt anders de volgende goedkeuringssleutel gebruiken die is opgegeven met de SDK, om een voorbeeld te maken van een afzonderlijke inschrijving:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Voer de volgende opdracht uit om een afzonderlijke inschrijving te maken voor uw TPM-apparaat (inclusief de aanhalingstekens rond het opdrachtargumenten):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Als de inschrijving is gemaakt, worden in het opdrachtvenster de eigenschappen weergegeven van de nieuwe afzonderlijke inschrijving.

    ![Eigenschappen van de inschrijving in de opdrachtuitvoer](./media/quick-enroll-device-tpm-node/output.png) 

4. Controleer of er een afzonderlijke inschrijving is gemaakt. Selecteer in Azure Portal **Inschrijvingen beheren** in de overzichtsblade van Device Provisioning Service. Select the **Individual Enrollments** tab and select the new enrollment entry (*first*) to verify the endorsement key and other properties for the entry.

    ![Eigenschappen van de inschrijving in de portal](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Nu u een afzonderlijke inschrijving hebt gemaakt voor een TPM-apparaat, kunt u doorgaan met de resterende stappen in [Create and provision a simulated device](quick-create-simulated-device.md) (Een gesimuleerd apparaat maken en inrichten) als u een gesimuleerd apparaat wilt inschrijven. Be sure to skip the steps to create an individual enrollment using the Azure portal in that quickstart.

## <a name="clean-up-resources"></a>Resources opschonen
If you plan to explore the Node.js service samples, do not clean up the resources created in this quickstart. If you do not plan to continue, use the following steps to delete all resources created by this quickstart.

1. Sluit het uitvoervenster van het Node.js-voorbeeld op de computer.
1. Als u een gesimuleerd TPM-apparaat hebt gemaakt, sluit u het venster van de TPM-simulator.
2. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the check box next to the *Registration ID* for the enrollment entry you created using this quickstart, and press the **Delete** button at the top of the pane. 
 
## <a name="next-steps"></a>Volgende stappen
In this quickstart, you’ve programmatically created an individual enrollment entry for a TPM device, and, optionally, created a TPM simulated device on your machine and provisioned it to your IoT hub using the Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 
 
> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)