---
title: Downstream-apparaten verifiëren-Azure IoT Edge | Microsoft Docs
description: Downstream-apparaten of-blad apparaten verifiëren voor IoT Hub en de verbinding via Azure IoT Edge gateway apparaten door sturen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ca066729a81ff51d87c5d8063c94be86366811c
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548761"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Een downstream-apparaat verifiëren voor Azure IoT Hub

In een transparant Gateway scenario moeten downstream-apparaten (ook wel Leaf-apparaten of onderliggende apparaten genoemd) identiteiten hebben in IoT Hub, zoals elk ander apparaat. In dit artikel worden de opties beschreven voor het verifiëren van een downstream-apparaat bij IoT Hub, en wordt vervolgens gedemonstreerd hoe de gateway verbinding moet worden gedeclareerd.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de tweede stap behandeld:

1. Het gateway apparaat moet in staat zijn om veilig verbinding te maken met downstream-apparaten, communicaties te ontvangen van downstream-apparaten en berichten te routeren naar de juiste bestemming. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor meer informatie.
2. **Het downstream-apparaat moet een apparaat-id hebben om te kunnen worden geverifieerd met IoT Hub en u moet communiceren via het gateway apparaat.**
3. Het downstream-apparaat moet een beveiligde verbinding maken met het gateway apparaat. Zie voor meer informatie, [een downstream-apparaat verbinden met Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

Downstream-apparaten kunnen met IoT Hub worden geverifieerd met behulp van een van de volgende drie methoden: symmetrische sleutels (ook wel gedeelde toegangs sleutels genoemd), X. 509 zelfondertekende certificaten of X. 509 Certificate Authority (CA) ondertekende certificaten. De verificatie stappen zijn vergelijkbaar met de stappen die worden gebruikt om een niet-IoT-edge-apparaat in te stellen met IoT Hub, met kleine verschillen in het declareren van de gateway relatie.

In de stappen in dit artikel wordt het hand matig inrichten van apparaten weer gegeven, niet automatisch inrichten met de Azure IoT Hub Device Provisioning Service (DPS). Het inrichten van downstream-apparaten met DPS wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md). Als u X. 509-verificatie gebruikt voor uw downstream-apparaat, moet u hetzelfde certificaat genereren dat u in het artikel transparante gateway hebt ingesteld.

Dit artikel verwijst naar de *hostnaam* van de gateway op verschillende punten. De hostnaam van de gateway wordt gedeclareerd in de para meter **hostname** van het bestand config. yaml op de IOT Edge gateway-apparaat. In de connection string van het downstream-apparaat waarnaar wordt verwezen. De hostnaam van de gateway moet kunnen worden omgezet in een IP-adres, hetzij met behulp van DNS of een bestands vermelding in de host.

## <a name="register-device-symmetric-key"></a>Apparaat registreren (symmetrische sleutel)

Verificatie op basis van symmetrische sleutels, of verificatie van de gedeelde toegangs sleutel, is de eenvoudigste manier om te verifiëren met IoT Hub. Bij symmetrische sleutel verificatie wordt een base64-sleutel gekoppeld aan uw IoT-apparaat-ID in IoT Hub. U neemt die sleutel op in uw IoT-toepassingen, zodat uw apparaat deze kan presen teren wanneer er verbinding wordt gemaakt met IoT Hub.

### <a name="create-the-device-identity"></a>De apparaat-id maken

Voeg een nieuw IoT-apparaat toe aan uw IoT-hub met behulp van de Azure Portal, Azure CLI of de IoT-extensie voor Visual Studio code. Houd er rekening mee dat downstream-apparaten moeten worden geïdentificeerd in IoT Hub als gewone IoT-apparaten, niet IoT Edge apparaten.

Wanneer u de nieuwe apparaat-id maakt, geeft u de volgende informatie op:

* Maak een ID voor uw apparaat.

* Selecteer **symmetrische sleutel** als het verificatie type.

* U kunt desgewenst **een bovenliggend apparaat instellen** en het IOT Edge gateway apparaat selecteren dat door dit downstream-apparaat wordt verbonden. Deze stap is optioneel voor symmetrische sleutel verificatie, maar wordt aanbevolen omdat het instellen van een bovenliggend apparaat [offline mogelijkheden](offline-capabilities.md) voor het downstream-apparaat mogelijk maakt. U kunt de details van het apparaat altijd bijwerken om het bovenliggende item later toe te voegen of te wijzigen.

   ![Apparaat-ID met symmetrische-sleutel verificatie maken in de portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

U kunt de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om dezelfde bewerking te volt ooien. In het volgende voor beeld wordt een nieuw IoT-apparaat met symmetrische sleutel verificatie gemaakt en wordt een bovenliggend apparaat toegewezen:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten en bovenliggend/onderliggend beheer raadpleegt u de referentie-inhoud voor [AZ IOT hub-apparaat-id-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) opdrachten.


Vervolgens [haalt u de Connection String op en wijzigt](#retrieve-and-modify-connection-string) u zo dat uw apparaat verbinding kan maken via de gateway.

## <a name="register-device-x509-self-signed"></a>Apparaat registreren (X. 509 zelf-ondertekend)

Voor X. 509 zelfondertekende verificatie, ook wel vingerafdruk verificatie genoemd, moet u nieuwe certificaten maken om op uw IoT-apparaat te plaatsen. Deze certificaten bevatten een vinger afdruk die u met IoT Hub voor verificatie kunt delen.

Als u geen certificerings instantie hebt om X. 509-certificaten te maken, kunt u [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md). Bij het genereren van test certificaten voor uw downstream-apparaat gebruikt u hetzelfde basis-CA-certificaat dat de certificaten voor uw gateway apparaat heeft gegenereerd.

1. Maak met behulp van uw CA-certificaat twee apparaats certificaten (primair en secundair) voor het downstream-apparaat.

   Het certificaat van het apparaat moet de onderwerpnaam hebben die is ingesteld op de apparaat-ID die u gebruikt bij het registreren van het IoT-apparaat in de Azure IoT Hub. Deze instelling is vereist voor verificatie.

2. Haal de SHA1-vinger afdruk (een vinger afdruk in de IoT Hub Interface) op van elk certificaat, wat een 40 hexadecimale teken reeks is. Gebruik de volgende openssl-opdracht om het certificaat weer te geven en de vinger afdruk te zoeken:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Voer deze opdracht twee keer uit en eenmaal voor het primaire certificaat en eenmaal voor het secundaire certificaat. U geeft de vinger afdrukken voor beide certificaten op wanneer u een nieuw IoT-apparaat registreert met zelfondertekende X. 509-certificaten.

3. Navigeer naar uw IoT-hub in het Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende waarden:

   * Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van de certificaten van uw apparaat.
   * Selecteer **X. 509 zelf ondertekend** als verificatie type.
   * Plak de hexadecimale teken reeksen die u hebt gekopieerd van het primaire en het secundaire certificaat van uw apparaat.
   * Selecteer **een bovenliggend apparaat instellen** en kies het IOT Edge gateway apparaat waarmee dit downstream-apparaat verbinding maakt. Er is een bovenliggend apparaat vereist voor X. 509-verificatie van een downstream-apparaat.

   ![Apparaat-ID maken met X. 509 zelfondertekende auth in Portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopieer het certificaat en de sleutels van het apparaat naar een wille keurige locatie op het downstream-apparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat het certificaat van de gateway-apparaat en de downstream-apparaatstuurprogramma's heeft gegenereerd.

   U verwijst naar deze bestanden in de Leaf-toepassingen die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.

5. Bekijk, afhankelijk van de taal van uw voor keur, de voor beelden van hoe X. 509-certificaten kunnen worden verwezen in IoT-toepassingen:

   * C#: [X. 509-beveiliging instellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

U kunt de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om de bewerking voor het maken van een apparaat te volt ooien. In het volgende voor beeld wordt een nieuw IoT-apparaat gemaakt met X. 509 zelfondertekende authenticatie en wordt een bovenliggend apparaat toegewezen:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Zie voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten, het genereren van certificaten en het beheer van bovenliggende en onderliggende items de referentie-inhoud voor [AZ IOT hub-apparaat-id-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) opdrachten.

Vervolgens [haalt u de Connection String op en wijzigt](#retrieve-and-modify-connection-string) u zo dat uw apparaat verbinding kan maken via de gateway.

## <a name="register-device-x509-ca-signed"></a>Apparaat registreren (X. 509 CA-ondertekend)

Voor X. 509 certificerings instantie (CA) ondertekende verificatie hebt u een basis-CA-certificaat geregistreerd in IoT Hub dat u gebruikt om certificaten voor uw IoT-apparaat te ondertekenen. Elk apparaat dat een certificaat gebruikt dat problemen heeft met het basis-CA-certificaat of een van de tussenliggende certificaten, mag worden geverifieerd.

Deze sectie is gebaseerd op de instructies in het artikel IoT Hub [X. 509-beveiliging in te stellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md). Volg de stappen in deze sectie om te zien welke waarden u moet gebruiken voor het instellen van een downstream-apparaat dat via een gateway verbinding maakt.

Als u geen certificerings instantie hebt om X. 509-certificaten te maken, kunt u [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md). Bij het genereren van test certificaten voor uw downstream-apparaat gebruikt u hetzelfde basis-CA-certificaat dat de certificaten voor uw gateway apparaat heeft gegenereerd.

1. Volg de instructies in de sectie [509 van CA-certificaten registreren bij uw IOT-hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) voor het instellen van *X. 509-beveiliging in uw Azure IOT hub*. In deze sectie voert u de volgende stappen uit:

   1. Upload een basis-CA-certificaat. Als u de demo certificaten gebruikt, wordt de basis-CA **\<pad >/certs/Azure-IOT-test-only.root.ca.cert.pem**.

   2. Controleer of u bent eigenaar van het basis-CA-certificaat.

2. Volg de instructies in het gedeelte [een x. 509-apparaat voor uw IOT-hub maken van de](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *X. 509-beveiliging instellen in uw Azure IOT hub*. In deze sectie voert u de volgende stappen uit:

   1. Een nieuw apparaat toevoegen. Geef een kleine naam op voor de **apparaat-id**en kies het verificatie type **X. 509 certificerings instantie ondertekend**.
   2. Stel een bovenliggend apparaat in. Selecteer voor downstream-apparaten de optie **een bovenliggend apparaat instellen** en kies het IOT Edge gateway-apparaat dat de verbinding met IOT hub zal bieden.

3. Maak een certificaat keten voor het downstream-apparaat. Gebruik hetzelfde basis-CA-certificaat dat u hebt geüpload naar IoT Hub om deze keten te maken. Gebruik dezelfde kleine apparaat-ID die u in de portal hebt opgegeven voor de identiteit van uw apparaat.

4. Kopieer het certificaat en de sleutels van het apparaat naar een wille keurige locatie op het downstream-apparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat het certificaat van de gateway-apparaat en de downstream-apparaatstuurprogramma's heeft gegenereerd.

   U verwijst naar deze bestanden in de Leaf-toepassingen die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.

5. Bekijk, afhankelijk van de taal van uw voor keur, de voor beelden van hoe X. 509-certificaten kunnen worden verwezen in IoT-toepassingen:

   * C#: [X. 509-beveiliging instellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

U kunt de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om de bewerking voor het maken van een apparaat te volt ooien. In het volgende voor beeld wordt een nieuw IoT-apparaat gemaakt met X. 509 certificerings instantie ondertekend verificatie en wijst een bovenliggend apparaat toe:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Zie de Azure CLI-referentie-inhoud voor [AZ IOT hub-apparaat-id-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) opdrachten voor meer informatie.

Vervolgens [haalt u de Connection String op en wijzigt](#retrieve-and-modify-connection-string) u zo dat uw apparaat verbinding kan maken via de gateway.

## <a name="retrieve-and-modify-connection-string"></a>connection string ophalen en wijzigen

Nadat u in de portal een IoT-apparaat-id hebt gemaakt, kunt u de primaire of secundaire sleutels ophalen. Een van deze sleutels moet worden opgenomen in de connection string die toepassingen gebruiken om te communiceren met IoT Hub. Voor de verificatie van symmetrische sleutels biedt IoT Hub de volledig opgemaakte connection string in de apparaatgegevens voor uw gemak. U moet extra informatie over het gateway apparaat toevoegen aan de connection string.

Verbindings reeksen voor downstream-apparaten hebben de volgende onderdelen nodig:

* De IoT-hub waarmee het apparaat verbinding maakt: `Hostname={iothub name}.azure-devices.net`
* De apparaat-ID die is geregistreerd bij de hub: `DeviceID={device ID}`
* De primaire of secundaire sleutel: `SharedAccessKey={key}`
* Het gateway apparaat waarmee het apparaat verbinding maakt. Geef de waarde van de **hostnaam** op uit het bestand config. yaml van IOT Edge gateway-apparaat: `GatewayHostName={gateway hostname}`

Alle samen, een volledig connection string ziet er als volgt uit:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Als u een bovenliggende/onderliggende relatie hebt ingesteld voor dit downstream-apparaat, kunt u de connection string vereenvoudigen door de gateway rechtstreeks aan te roepen als de host van de verbinding. Bovenliggende/onderliggende relaties zijn vereist voor X. 509-verificatie, maar is optioneel voor symmetrische sleutel verificatie. Bijvoorbeeld:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

Op dit moment moet er een IoT Edge apparaat zijn geregistreerd en geconfigureerd als gateway. U hebt ook een downstream IoT-apparaat geregistreerd en naar het gateway apparaat verwezen. De laatste stap is het plaatsen van certificaten op het downstream-apparaat, zodat er een veilige verbinding kan worden gemaakt met de gateway.

Ga verder met het volgende artikel in de gateway reeks, [sluit een downstream-apparaat aan op een Azure IOT Edge gateway](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Volgende stappen

Door dit artikel te volt ooien, moet u een IoT Edge apparaat gebruiken als transparante gateway en een downstream-apparaat dat is geregistreerd bij een IoT-hub. Vervolgens moet u uw downstream-apparaten configureren om het gateway apparaat te vertrouwen en er veilig verbinding mee te maken. Zie voor meer informatie, [een downstream-apparaat verbinden met Azure IoT Edge-gateway](how-to-connect-downstream-device.md).
