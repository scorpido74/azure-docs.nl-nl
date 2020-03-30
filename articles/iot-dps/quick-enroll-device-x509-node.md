---
title: X.509-apparaten inschrijven voor Azure Device Provisioning Service met Node.js
description: In deze snelstart wordt gebruikgemaakt van groepsregistraties. In deze quickstart schrijft u X.509-apparaten in voor de Azure IoT Hub Device Provisioning Service (DPS) met behulp van de Node.js-service SDK
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 35f5cc4914689fd171cc3fa8ec7d809924127f28
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605548"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-nodejs"></a>Snelstart: X.509-apparaten registreren bij Device Provisioning Service met behulp van Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze quickstart gebruikt u Node.js om programmatisch een inschrijvingsgroep te maken die tussentijdse of hoofd-CA X.509-certificaten gebruikt. De registratiegroep is gemaakt met behulp van de IoT-SDK voor Node.js en een Node.js-voorbeeldtoepassing.

## <a name="prerequisites"></a>Vereisten

- Voltooiing van het instellen van [de IoT Hub Device Provisioning Service met de Azure-portal.](./quick-setup-auto-provision.md)
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Node.js v4.0+](https://nodejs.org). Deze quickstart installeert hieronder de [IoT SDK voor Node.js.](https://github.com/Azure/azure-iot-sdk-node)
- [Git.](https://git-scm.com/download/)
- [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

## <a name="prepare-test-certificates"></a>Testcertificaten voorbereiden

Voor deze snelstart hebt u een .pem- of een .cer-bestand met het openbare gedeelte van een tussenliggend of hoofd-CA x.509-certificaat nodig. Dit certificaat moet worden geüpload naar uw inrichtingsservice en door de service worden geverifieerd.

Zie [Overzicht van beveiliging op basis van X.509-CA-certificaten](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview) voor meer informatie over het gebruik vanop X.509-certificaten gebaseerde Public Key Infrastructure (PKI) met Azure IoT Hub en Device Provisioning Service.

De [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) bevat testhulpmiddelen waarmee u een X.509-certificaatketen kunt maken, een basis- of tussencertificaat kunt uploaden vanuit die keten en een bewijs van eigendom kunt uitvoeren met de service om het certificaat te verifiëren. Certificaten die zijn gemaakt met de SDK-hulpmiddelen zijn alleen ontworpen voor **ontwikkeltesten**. Deze certificaten **mogen niet in productie worden gebruikt**. Ze bevatten in code vastgelegde wachtwoorden ('1234') die na 30 dagen verlopen. Zie [Een x.509-CA-certificaat ophalen](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) in de documentatie van Azure IoT Hub voor meer informatie over het verkrijgen van certificaten die geschikt zijn voor productiegebruik.

Voer de volgende stappen uit om deze testhulpmiddelen te gebruiken om certificaten te genereren:
 
1. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de Azure IoT C SDK.

2. Open een opdrachtprompt of Git Bash-shell en wijzig deze in een werkmap op uw computer. Voer de volgende opdrachten uit om de nieuwste versie van de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-repository te klonen. Gebruik de tag die u in de `-b` vorige stap hebt gevonden als de waarde voor de parameter:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

   De testhulpmiddelen bevinden zich in de *azure-iot-sdk-c/tools/CACertificates* van de opslagplaats die u hebt gekloond.

3. Volg de stappen in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (CA-testcertificaten voor voorbeelden en zelfstudies beheren). 



## <a name="create-the-enrollment-group-sample"></a>Een voorbeeld van een registratiegroep maken 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om één apparaat in te schrijven.

Een registratiegroep beheert de toegang tot de inrichtingsservice voor apparaten die een gemeenschappelijk handtekeningcertificaat in hun certificaatketen delen. Zie [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie.
 
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
    1. Meld u aan bij de Azure-portal, selecteer de knop **Alle bronnen** in het linkermenu en open de service Apparaatvoorziening. 
    2. Klik **op Beleid voor gedeelde toegang**en selecteer vervolgens het toegangsbeleid dat u wilt gebruiken om de eigenschappen ervan te openen. Kopieer of noteer de verbindingsreeks van de primaire sleutel uit het venster **Toegangsbeleid**. 

       ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Zoals vermeld in [Testcertificaten voorbereiden](quick-enroll-device-x509-node.md#prepare-test-certificates) hebt u ook een .pem-bestand nodig dat een tussen- of basis-X.509 CA-certificaat bevat dat eerder is geüpload naar en is geverifieerd met uw inrichtingsservice. Als u wilt controleren of uw certificaat is geüpload en geverifieerd, selecteert u op de overzichtspagina apparaatinrichtingsservice in de Azure-portal **certificaten**. Zoek het certificaat dat u wilt gebruiken voor de groepsregistratie en zorg ervoor dat de statuswaarde *geverifieerd* is.

    ![Geverifieerd certificaat in de portal](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Als u een [inschrijvingsgroep](concepts-service.md#enrollment-group) voor uw certificaat wilt maken, voert u de volgende opdracht uit (de aanhalingstekens rond de opdrachtargumenten):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Als de inschrijving is gemaakt, worden in het opdrachtvenster de eigenschappen van de nieuwe registratiegroep weergegeven.

    ![Eigenschappen van de inschrijving in de opdrachtuitvoer](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Controleer of de registratiegroep is gemaakt. Selecteer in Azure Portal **Inschrijvingen beheren** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Registratiegroepen** en controleer of de nieuwe inschrijving (*eerste*) aanwezig is.

    ![Eigenschappen van de inschrijving in de portal](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent de node.js-servicevoorbeelden te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle Azure-resources te verwijderen die door deze quickstart zijn gemaakt.
 
1. Sluit het uitvoervenster van het Node.js-voorbeeld op de computer.
2. Navigeer naar de service Apparaatinrichting in de Azure-portal, selecteer **Inschrijvingen beheren**en schakel het tabblad **Inschrijvingsgroepen** in. Schakel het selectievakje in naast de *groepsnaam* voor de X.509-apparaten die u met deze snelstart hebt ingeschreven en druk op de knop **Verwijderen** boven in het deelvenster.    
3. Selecteer **certificaten,** selecteer het certificaat dat u voor deze quickstart hebt geüpload en druk op de knop **Verwijderen** boven aan het venster **Certificaatgegevens.**  
 
## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een groepsinschrijving gemaakt voor een R.509 intermediate of root CA-certificaat met behulp van de Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 

Zie ook het voorbeeld voor het inrichten van het [apparaat node.js.](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples)
 
> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
