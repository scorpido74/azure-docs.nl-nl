---
title: Downstream-apparaten verifiëren - Azure IoT Edge | Microsoft Documenten
description: Downstream-apparaten of leaf-apparaten verifiëren naar IoT Hub en hun verbinding routeren via Azure IoT Edge-gatewayapparaten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411502"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Een downstream-apparaat verifiëren voor Azure IoT Hub

In een transparant gatewayscenario hebben downstream-apparaten (ook wel bladapparaten of onderliggende apparaten genoemd) identiteiten in IoT Hub nodig, net als elk ander apparaat. In dit artikel worden de opties voor het verifiëren van een downstream-apparaat naar IoT Hub doorloopt en wordt uitgelegd hoe u de gatewayverbinding declareren.

Er zijn drie algemene stappen om een succesvolle transparante gatewayverbinding tot doel te stellen. Dit artikel behandelt de tweede stap:

1. Het gateway-apparaat moet veilig verbinding kunnen maken met downstream-apparaten, communicatie van downstream-apparaten kunnen ontvangen en berichten naar de juiste bestemming kunnen leiden. Zie [Een IoT Edge-apparaat configureren om als transparante gateway te fungeren](how-to-create-transparent-gateway.md)voor meer informatie.
2. **Het downstream-apparaat moet een apparaatidentiteit hebben om te kunnen verifiëren met IoT Hub en te weten dat het moet communiceren via het gateway-apparaat.**
3. Het downstream-apparaat moet veilig verbinding maken met het gateway-apparaat. Zie [Een downstream-apparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.

Downstream-apparaten kunnen verifiëren met IoT Hub met behulp van een van de drie methoden: symmetrische sleutels (soms aangeduid als gedeelde toegangssleutels), X.509 zelfondertekende certificaten of X.509-certificaatautoriteit (CA) ondertekende certificaten. De verificatiestappen zijn vergelijkbaar met de stappen die worden gebruikt om een niet-IoT-Edge-apparaat met IoT-hub in te stellen, met kleine verschillen om de gatewayrelatie te declareren.

De stappen in dit artikel tonen handmatige apparaatinrichting, niet automatische inrichting met de Azure IoT Hub Device Provisioning Service (DPS). Het inrichten van downstream-apparaten met DPS wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Een IoT Edge-apparaat configureren om als transparante gateway te fungeren.](how-to-create-transparent-gateway.md) Als u X.509-verificatie gebruikt voor uw downstream-apparaat, moet u hetzelfde certificaatgenererende script gebruiken dat u in het transparante gateway-artikel hebt ingesteld.

Dit artikel verwijst naar de *status van* gateway host op verschillende punten. De status van gatewayhost wordt gedeclareerd in de parameter **hostname** van het config.yaml-bestand op het IoT Edge-gateway-apparaat. Het wordt genoemd in de verbindingsreeks van het downstream-apparaat. De status van de gatewayhost moet oplosbaar zijn naar een IP-adres, met behulp van DNS of een hostbestandsvermelding.

## <a name="register-device-symmetric-key"></a>Apparaat registreren (symmetrische toets)

Symmetrische sleutelverificatie, of verificatie van gedeelde toegangssleutels, is de eenvoudigste manier om te verifiëren met IoT Hub. Met symmetrische sleutelverificatie is een base64-sleutel gekoppeld aan uw IoT-apparaat-id in IoT Hub. U neemt die sleutel op in uw IoT-toepassingen, zodat uw apparaat deze kan presenteren wanneer het verbinding maakt met IoT Hub.

### <a name="create-the-device-identity"></a>De apparaatidentiteit maken

Voeg een nieuw IoT-apparaat toe aan uw IoT-hub, met behulp van de Azure-portal, Azure CLI of de IoT-extensie voor Visual Studio Code. Houd er rekening mee dat downstream-apparaten in IoT Hub moeten worden geïdentificeerd als gewone IoT-apparaten, niet als IoT Edge-apparaten.

Wanneer u de nieuwe apparaatidentiteit maakt, geeft u de volgende informatie op:

* Maak een ID voor uw apparaat.

* Selecteer **Symmetrische sleutel** als verificatietype.

* Kies optioneel om **een bovenliggend apparaat in** te stellen en selecteer het IoT Edge-gatewayapparaat waarmee dit downstream-apparaat verbinding maakt. Deze stap is optioneel voor symmetrische sleutelverificatie, maar wordt aanbevolen omdat het instellen van een bovenliggend apparaat [offlinemogelijkheden](offline-capabilities.md) voor uw downstream-apparaat mogelijk maakt. U de apparaatgegevens altijd bijwerken om de bovenliggende ouder later toe te voegen of te wijzigen.

   ![Apparaat-ID maken met symmetrische sleutelauth in portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

U de [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) gebruiken om dezelfde bewerking te voltooien. In het volgende voorbeeld wordt een nieuw IoT-apparaat gemaakt met symmetrische sleutelverificatie en wordt een bovenliggend apparaat toewijst:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Zie de referentie-inhoud voor de az [iot-hub-apparaatidentiteitsopdrachten](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten en het beheer van bovenliggende/onderliggende stoffen.


Haal [vervolgens de verbindingstekenreeks op en wijzig](#retrieve-and-modify-connection-string) deze, zodat uw apparaat weet verbinding te maken via de gateway.

## <a name="register-device-x509-self-signed"></a>Apparaat registreren (X.509 zelf ondertekend)

Voor X.509 zelfondertekende verificatie, ook wel thumbprint-verificatie genoemd, moet u nieuwe certificaten maken om op uw IoT-apparaat te plaatsen. Deze certificaten hebben een duimafdruk die u deelt met IoT Hub voor verificatie.

Als u geen certificaatautoriteit hebt om X.509-certificaten te maken, u [democertificaten maken om de functies van IoT Edge-apparaten te testen.](how-to-create-test-certificates.md) Wanneer u testcertificaten voor uw downstream-apparaat genereert, gebruikt u hetzelfde basis-CA-certificaat dat de certificaten voor uw gatewayapparaat heeft gegenereerd.

1. Maak met behulp van uw CA-certificaat twee apparaatcertificaten (primair en secundair) voor het downstream-apparaat.

   Het apparaatcertificaat moet de onderwerpnaam hebben ingesteld op de apparaat-id die u gebruikt bij het registreren van het IoT-apparaat in de Azure IoT Hub. Deze instelling is vereist voor verificatie.

2. Haal de SHA1-vingerafdruk (een duimafdruk in de IoT Hub-interface genoemd) op uit elk certificaat, een tekenreeks van 40 hexadecimale tekens. Gebruik de volgende opdracht openssl om het certificaat te bekijken en de vingerafdruk te vinden:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Voer deze opdracht twee keer uit, één keer voor het primaire certificaat en één keer voor het secundaire certificaat. U verstrekt vingerafdrukken voor beide certificaten wanneer u een nieuw IoT-apparaat registreert met behulp van zelfondertekende X.509-certificaten.

3. Navigeer naar uw IoT-hub in de Azure-portal en maak een nieuwe IoT-apparaatidentiteit met de volgende waarden:

   * Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van uw apparaatcertificaten.
   * Selecteer **X.509 Zelf ondertekend** als verificatietype.
   * Plak de hexadecimale tekenreeksen die u hebt gekopieerd van de primaire en secundaire certificaten van uw apparaat.
   * Selecteer **Een bovenliggend apparaat instellen** en kies het IoT Edge-gatewayapparaat waarmee dit downstream-apparaat verbinding maakt. Een bovenliggend apparaat is vereist voor X.509-verificatie van een downstream-apparaat.

   ![Apparaat-ID maken met X.509 zelfondertekende auth in portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopieer het apparaatcertificaat en de sleutels naar elke locatie op het downstream-apparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat zowel het gatewayapparaatcertificaat als de downstream-apparaatcertificaten genereerde.

   U verwijst naar deze bestanden in de toepassingen van het Leaf-apparaat die verbinding maken met IoT Hub. U een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals Secure copy [protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaatbestanden te verplaatsen.

5. Bekijk voorbeelden van hoe x.509-certificaten kunnen worden gebruikt in IoT-toepassingen, afhankelijk van uw voorkeurstaal:

   * C#: [X.509-beveiliging instellen in uw Azure IoT-hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c.](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

U de [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) gebruiken om dezelfde bewerking voor het maken van apparaten te voltooien. In het volgende voorbeeld wordt een nieuw IoT-apparaat gemaakt met X.509 zelfondertekende verificatie en wordt een bovenliggend apparaat toewijst:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Zie de referentie-inhoud voor de az [iot-hub-apparaatidentiteitsopdrachten](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten, het genereren van certificaten en het beheer van bovenliggende en onderliggende apparaten.

Haal [vervolgens de verbindingstekenreeks op en wijzig](#retrieve-and-modify-connection-string) deze, zodat uw apparaat weet verbinding te maken via de gateway.

## <a name="register-device-x509-ca-signed"></a>Registerapparaat (X.509 CA ondertekend)

Voor door X.509-certificaatautoriteit (CA) ondertekende verificatie hebt u een basis-CA-certificaat nodig dat is geregistreerd in IoT Hub en dat u gebruikt om certificaten voor uw IoT-apparaat te ondertekenen. Elk apparaat dat een certificaat gebruikt dat is afgegeven door het basis-CA-certificaat of een van de tussentijdse certificaten, mag zich verifiëren.

Deze sectie is gebaseerd op de instructies die zijn beschreven in het IoT [Hub-artikel X.509-beveiliging instellen in uw Azure IoT-hub.](../iot-hub/iot-hub-security-x509-get-started.md) Volg de stappen in deze sectie om te weten welke waarden u moet gebruiken om een downstream-apparaat in te stellen dat via een gateway verbinding maakt.

Als u geen certificaatautoriteit hebt om X.509-certificaten te maken, u [democertificaten maken om de functies van IoT Edge-apparaten te testen.](how-to-create-test-certificates.md) Wanneer u testcertificaten voor uw downstream-apparaat genereert, gebruikt u hetzelfde basis-CA-certificaat dat de certificaten voor uw gatewayapparaat heeft gegenereerd.

1. Volg de instructies in de [Register X.509 CA-certificaten voor uw IoT-hubsectie](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) van *X.509-beveiliging instellen in uw Azure IoT-hub.* In die sectie voert u de volgende stappen uit:

   1. Upload een basis-CA-certificaat. Als u de democertificaten gebruikt, is de hoofd-CA ** \<pad>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Controleer of u eigenaar bent van dat basis-CA-certificaat.

2. Volg de instructies in [het Gedeelte Een X.509-apparaat maken voor uw IoT-hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) van *X.509-beveiliging instellen in uw Azure IoT-hub.* In die sectie voert u de volgende stappen uit:

   1. Voeg een nieuw apparaat toe. Geef een kleine naam op voor **apparaat-id**en kies het verificatietype **X.509 CA Ondertekend**.
   2. Stel een bovenliggend apparaat in. Selecteer voor downstream-apparaten **Een bovenliggend apparaat instellen** en kies het IoT Edge-gatewayapparaat dat de verbinding met IoT Hub biedt.

3. Maak een certificaatketen voor uw downstream-apparaat. Gebruik hetzelfde basis-CA-certificaat dat u hebt geüpload naar IoT Hub om deze keten te maken. Gebruik dezelfde kleine apparaat-id die u aan de identiteit van uw apparaat hebt gegeven in de portal.

4. Kopieer het apparaatcertificaat en de sleutels naar elke locatie op het downstream-apparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat zowel het gatewayapparaatcertificaat als de downstream-apparaatcertificaten genereerde.

   U verwijst naar deze bestanden in de toepassingen van het Leaf-apparaat die verbinding maken met IoT Hub. U een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals Secure copy [protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaatbestanden te verplaatsen.

5. Bekijk voorbeelden van hoe x.509-certificaten kunnen worden gebruikt in IoT-toepassingen, afhankelijk van uw voorkeurstaal:

   * C#: [X.509-beveiliging instellen in uw Azure IoT-hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c.](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

U de [IoT-extensie voor Azure CLI](https://github.com/Azure/azure-iot-cli-extension) gebruiken om dezelfde bewerking voor het maken van apparaten te voltooien. In het volgende voorbeeld wordt een nieuw IoT-apparaat gemaakt met X.509 CA-ondertekende verificatie en wordt een bovenliggend apparaat toewijst:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Zie voor meer informatie de Azure CLI-referentie-inhoud voor de [aio-hub-apparaatidentiteitsopdrachten.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Haal [vervolgens de verbindingstekenreeks op en wijzig](#retrieve-and-modify-connection-string) deze, zodat uw apparaat weet verbinding te maken via de gateway.

## <a name="retrieve-and-modify-connection-string"></a>Verbindingstekenreeks ophalen en wijzigen

Nadat u een IoT-apparaatidentiteit in de portal hebt aanmaken, u de primaire of secundaire sleutels ophalen. Een van deze sleutels moet worden opgenomen in de verbindingstekenreeks die toepassingen gebruiken om te communiceren met IoT Hub. Voor symmetrische sleutelverificatie biedt IoT Hub de volledig gevormde verbindingstekenreeks in de apparaatdetails voor uw gemak. U moet extra informatie over het gatewayapparaat toevoegen aan de verbindingstekenreeks.

Verbindingstekenreeksen voor downstream-apparaten hebben de volgende componenten nodig:

* De IoT-hub waarmee het apparaat verbinding maakt:`Hostname={iothub name}.azure-devices.net`
* De apparaat-id die bij de hub is geregistreerd:`DeviceID={device ID}`
* De primaire of secundaire sleutel:`SharedAccessKey={key}`
* Het gateway-apparaat waarmee het apparaat verbinding maakt. Geef de **hostnaamwaarde** op uit het config.yaml-bestand van het IoT Edge-gateway-apparaat:`GatewayHostName={gateway hostname}`

Alles bij elkaar ziet een volledige verbindingstekenreeks eruit als volgt:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Als u een bovenliggende/onderliggende relatie hebt ingesteld voor dit downstream-apparaat, u de verbindingstekenreeks vereenvoudigen door de gateway rechtstreeks als verbindingshost aan te roepen. Bovenliggende/onderliggende relaties zijn vereist voor X.509-verificatie, maar optioneel voor symmetrische sleutelverificatie. Bijvoorbeeld:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Op dit punt moet u een IoT Edge-apparaat hebben dat is geregistreerd en geconfigureerd als gateway. U hebt ook een downstream IoT-apparaat geregistreerd en wijst naar het gateway-apparaat. De laatste stap is om certificaten op uw downstream-apparaat te plaatsen, zodat het veilig verbinding kan maken met de gateway.

Ga verder naar het volgende artikel in de gateway-serie en [verbind een downstream-apparaat met een Azure IoT Edge-gateway.](how-to-connect-downstream-device.md)

## <a name="next-steps"></a>Volgende stappen

Als u dit artikel indient te vullen, moet u een IoT Edge-apparaat hebben dat werkt als een transparante gateway en een downstream-apparaat dat is geregistreerd bij een IoT-hub. Vervolgens moet u uw downstream-apparaten configureren om het gateway-apparaat te vertrouwen en er veilig verbinding mee te maken. Zie [Een downstream-apparaat verbinden met een Azure IoT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.
