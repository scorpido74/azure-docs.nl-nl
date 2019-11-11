---
title: 'Snelstartgids: een gesimuleerd TPM-apparaat inrichten voor Azure IoT Hub met behulp van node. js'
description: 'Azure-quickstart: een gesimuleerd TPM-apparaat met de SDK voor Node.js maken en inrichten voor Azure IoT Hub Device Provisioning Service In deze snelstart wordt gebruikgemaakt van afzonderlijke registraties.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1495b8b62f775fcc8ae68fe08d9736beb72d67c6
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903538"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstartgids: een gesimuleerd TPM-apparaat met de SDK voor node. js maken en inrichten voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In deze stappen wordt getoond hoe u een gesimuleerd apparaat maakt op een ontwikkelcomputer met Windows OS, de Windows TPM-simulator uitvoert als de [HSM (Hardware Security Module)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) van het apparaat en het codevoorbeeld gebruikt om dit gesimuleerde apparaat te verbinden met Device Provisioning Service en uw IoT-hub. 

Als u niet bekend bent met het proces van automatisch inrichten, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen registraties:
- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke registraties gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat [Node.js v4.0 of hoger](https://nodejs.org) is geïnstalleerd op de computer.

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 


## <a name="simulate-a-tpm-device"></a>TPM-apparaat simuleren

1. Open een opdrachtprompt of Git Bash. Kloon de GitHub-opslagplaats `azure-utpm-c`:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-utpm-c.git --recursive
    ```

1. Ga naar de hoofdmap van GitHub en voer de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-simulator uit. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdrachtvenster niet; de simulator moet actief blijven tot u deze Snelstartgids hebt voltooid: 

    ```cmd/sh
    .\azure-utpm-c\tools\tpm_simulator\Simulator.exe
    ```

1. Maak een nieuwe, lege map met de naam **registerdevice**. Maak in de map **registerdevice** een bestand met de naam package.json door achter de opdrachtprompt de volgende opdracht op te geven. Beantwoord alle vragen die door `npm` worden gesteld of accepteer de standaardwaarden als deze voor u geschikt zijn:
   
    ```cmd/sh
    npm init
    ```

1. Installeer de volgende precursorpakketten:

    ```cmd/sh
    npm install node-gyp -g
    npm install ffi -g
    ```

    > [!NOTE]
    > Het installeren van de bovengenoemde pakketten kan problemen met zich meebrengen. U kunt deze oplossen door `npm install --global --production windows-build-tools` uit te voeren via een opdrachtprompt in de modus **Uitvoeren als administrator**. Vervolgens voert u `SET VCTargetsPath=C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140` uit nadat u het pad hebt vervangen door dat van de geïnstalleerde versie. Voer ten slotte de bovenstaande installatieopdrachten opnieuw uit.
    >

1. Installeer de volgende pakketten met de onderdelen die worden gebruikt tijdens de registratie:

   - een beveiligingsclient die werkt met TPM: `azure-iot-security-tpm`
   - een transport voor het apparaat om verbinding te maken met de Device Provisioning Service: `azure-iot-provisioning-device-http` of `azure-iot-provisioning-device-amqp`
   - een client om het transport en de beveiligingsclient te kunnen gebruiken: `azure-iot-provisioning-device`

     Zodra het apparaat is geregistreerd, kunt u de normale IoT Hub Device Client-pakketten gebruiken om het apparaat te verbinden met behulp van de referenties die u tijdens de registratie hebt gekregen. U hebt het volgende nodig:

   - de apparaatclient: `azure-iot-device`
   - een transportmiddel: hetzij `azure-iot-device-amqp`, `azure-iot-device-mqtt` of `azure-iot-device-http`
   - de beveiligingsclient die u al hebt geïnstalleerd: `azure-iot-security-tpm`

     > [!NOTE]
     > De voorbeelden hieronder maken gebruik van de transporten `azure-iot-provisioning-device-http` en `azure-iot-device-mqtt`.
     > 

     U kunt al deze pakketten tegelijk installeren door de volgende opdracht via de opdrachtprompt in de map **registerdevice** uit te voeren:

       ```cmd/sh
       npm install --save azure-iot-device azure-iot-device-mqtt azure-iot-security-tpm azure-iot-provisioning-device-http azure-iot-provisioning-device
       ```

1. Maak met behulp van een teksteditor een nieuw bestand **ExtractDevice.js** in de map **registerdevice**.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **ExtractDevice.js**:
   
    ```
    'use strict';

    var tpmSecurity = require('azure-iot-security-tpm');
    var tssJs = require("tss.js");

    var myTpm = new tpmSecurity.TpmSecurityClient(undefined, new tssJs.Tpm(true));
    ```

1. Voeg de volgende functie toe om de methode te implementeren:
   
    ```
    myTpm.getEndorsementKey(function(err, endorsementKey) {
      if (err) {
        console.log('The error returned from get key is: ' + err);
      } else {
        console.log('the endorsement key is: ' + endorsementKey.toString('base64'));
        myTpm.getRegistrationId((getRegistrationIdError, registrationId) => {
          if (getRegistrationIdError) {
            console.log('The error returned from get registration id is: ' + getRegistrationIdError);
          } else {
            console.log('The Registration Id is: ' + registrationId);
            process.exit();
          }
        });
      }
    });
    ```

1. Sla het bestand **ExtractDevice.js** op en sluit het. Voer het voorbeeld uit:

    ```cmd/sh
    node ExtractDevice.js
    ```

1. In het uitvoervenster worden de voor apparaatinschrijving vereiste **_goedkeuringssleutel_** en **_registratie-id_** weergegeven. Noteer deze waarden. 


## <a name="create-a-device-entry"></a>Apparaatvermelding maken

1. Meld u aan bij Azure Portal, klik in het linkermenu op de knop **Alle bronnen** en open Device Provisioning Service.

1. Selecteer **Manage enrollments** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Afzonderlijke registraties** en klik vervolgens op de knop **Afzonderlijke inschrijving toevoegen** bovenaan. 

1. Voer onder **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor het TPM-apparaat in.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
       - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Save** als u klaar bent. 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *registratie-id* ervan weergegeven in de lijst onder het tabblad *Individuele inschrijvingen*. 


## <a name="register-the-device"></a>Apparaat registreren

1. In Azure Portal selecteert u de blade **Overview** voor uw Device Provisioning-service en noteert u de waarden **_Global Device Endpoint_** en **_ID Scope_** .

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

1. Maak met behulp van een teksteditor een nieuw bestand **RegisterDevice.js** in de map **registerdevice**.

1. Voeg de volgende `require`-instructies toe aan het begin van het bestand **RegisterDevice.js**:
   
    ```
    'use strict';

    var ProvisioningTransport = require('azure-iot-provisioning-device-http').Http;
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var tpmSecurity = require('azure-iot-security-tpm');
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

    > [!NOTE]
    > **Azure IoT SDK for Node.js** ondersteunt aanvullende protocollen, zoals _AMQP_, _AMQP WS_ en _MQTT WS_.  Zie [Device Provisioning Service SDK for Node.js samples](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples) (Device Provisioning Service-SDK voor Node.js-voorbeelden) voor meer voorbeelden.
    > 

1. Voeg de variabelen **globalDeviceEndpoint** en **idScope** toe en gebruik ze om een **ProvisioningDeviceClient**-exemplaar te maken. Vervang **{globalDeviceEndpoint}** en **{idScope}** door de waarden **_Global Device Endpoint_** en **_ID Scope_** uit **Stap 1**:
   
    ```
    var provisioningHost = '{globalDeviceEndpoint}';
    var idScope = '{idScope}';

    var tssJs = require("tss.js");
    var securityClient = new tpmSecurity.TpmSecurityClient('', new tssJs.Tpm(true));
    // if using non-simulated device, replace the above line with following:
    //var securityClient = new tpmSecurity.TpmSecurityClient();

    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), securityClient);
    ```

1. Voeg de volgende functie toe om de methode in het apparaat te implementeren:
   
    ```
    provisioningClient.register(function(err, result) {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.registrationState.assignedHub);
        console.log('deviceId=' + result.registrationState.deviceId);
        var tpmAuthenticationProvider = tpmSecurity.TpmAuthenticationProvider.fromTpmSecurityClient(result.registrationState.deviceId, result.registrationState.assignedHub, securityClient);
        var hubClient = Client.fromAuthenticationProvider(tpmAuthenticationProvider, iotHubTransport);

        var connectCallback = function (err) {
          if (err) {
            console.error('Could not connect: ' + err.message);
          } else {
            console.log('Client connected');
            var message = new Message('Hello world');
            hubClient.sendEvent(message, printResultFor('send'));
          }
        };

        hubClient.open(connectCallback);

        function printResultFor(op) {
          return function printResult(err, res) {
            if (err) console.log(op + ' error: ' + err.toString());
            if (res) console.log(op + ' status: ' + res.constructor.name);
            process.exit(1);
          };
        }
      }
    });
    ```

1. Sla het bestand **RegisterDevice.js** op en sluit het. Voer het voorbeeld uit:

    ```cmd/sh
    node RegisterDevice.js
    ```

1. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. Als het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld met de provisioning-service, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device/hub-registration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie


## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze Snelstartgids zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources die via deze Snelstartgids zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer uw Device Provisioning Service. Open de Blade **inschrijvingen beheren** voor uw service en klik vervolgens op het tabblad **afzonderlijke inschrijvingen** . Selecteer de *registratie-id* van het apparaat dat u in deze Quick Start hebt Inge schreven en klik bovenaan op de knop **verwijderen** . 
1. Klik in het linkermenu in de Azure Portal op **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, selecteer de *apparaat-id* van het apparaat dat u hebt geregistreerd in deze quickstart en klik vervolgens bovenaan op de knop **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd TPM-apparaat op de computer gemaakt en dit ingericht voor uw IoT-hub met IoT Hub Device Provisioning Service. Als u wilt weten hoe u uw TPM-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een TPM-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: TPM-apparaat registreren bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-node.md)
