---
title: Downstream-apparaten verifiëren-Azure IoT Edge | Microsoft Docs
description: Downstream-apparaten of-blad apparaten verifiëren voor IoT Hub en de verbinding via Azure IoT Edge gateway apparaten door sturen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1e184691ebbd34de0f69e93419d9c34ab18edbe6
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025941"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Een downstream-apparaat verifiëren voor Azure IoT Hub

In een transparant Gateway scenario moeten downstream-apparaten (ook wel Leaf-apparaten of onderliggende apparaten genoemd) identiteiten hebben in IoT Hub, zoals elk ander apparaat. In dit artikel worden de opties beschreven voor het verifiëren van een downstream-apparaat bij IoT Hub, en wordt vervolgens gedemonstreerd hoe de gateway verbinding moet worden gedeclareerd.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de tweede stap behandeld:

1. Het gateway apparaat moet in staat zijn om veilig verbinding te maken met downstream-apparaten, communicaties te ontvangen van downstream-apparaten en berichten te routeren naar de juiste bestemming. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor meer informatie.
2. **Het downstream-apparaat moet een apparaat-id hebben om te kunnen worden geverifieerd met IoT Hub en u moet communiceren via het gateway apparaat.**
3. Het downstream-apparaat moet veilig verbinding kunnen maken met het gateway apparaat. Zie [verbinding maken tussen een downstream-apparaat en een Azure IOT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.

Downstream-apparaten kunnen met IoT Hub worden geverifieerd met behulp van een van de volgende drie methoden: symmetrische sleutels (ook wel gedeelde toegangs sleutels genoemd), X. 509 zelfondertekende certificaten of X. 509 Certificate Authority (CA) ondertekende certificaten. De verificatie stappen zijn vergelijkbaar met de stappen die worden gebruikt om een niet-IoT-edge-apparaat in te stellen met IoT Hub, met kleine verschillen in het declareren van de gateway relatie.

Met de stappen in dit artikel wordt het hand matig inrichten van apparaten weer gegeven, niet automatisch inrichten met de Azure-IoT Hub Device Provisioning Service. 

## <a name="prerequisites"></a>Vereisten

Voer de stappen in [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md). Als u X. 509-verificatie gebruikt voor uw downstream-apparaat, moet u hetzelfde certificaat genereren dat u in het artikel transparante gateway hebt ingesteld. 

Dit artikel verwijst naar de *hostnaam* van de gateway op verschillende punten. De hostnaam van de gateway wordt gedeclareerd in de para meter **hostname** van het bestand config. yaml op de IOT Edge gateway-apparaat. Het wordt gebruikt om de certificaten in dit artikel te maken en wordt verwezen naar de connection string van de downstream-apparaten. De hostnaam van de gateway moet kunnen worden omgezet in een IP-adres, hetzij met behulp van DNS of een bestands vermelding in de host.

## <a name="symmetric-key-authentication"></a>Symmetrische-sleutel verificatie

Verificatie op basis van symmetrische sleutels, of verificatie van de gedeelde toegangs sleutel, is de eenvoudigste manier om te verifiëren met IoT Hub. Bij symmetrische sleutel verificatie wordt een base64-sleutel gekoppeld aan uw IoT-apparaat-ID in IoT Hub. U neemt die sleutel op in uw IoT-toepassingen, zodat uw apparaat deze kan presen teren wanneer er verbinding wordt gemaakt met IoT Hub. 

### <a name="create-the-device-identity"></a>De apparaat-id maken 

Voeg een nieuw IoT-apparaat toe aan uw IoT-hub met behulp van de Azure Portal, Azure CLI of de IoT-extensie voor Visual Studio code. Houd er rekening mee dat downstream-apparaten moeten worden geïdentificeerd in IoT Hub als een normaal IoT-apparaat, niet IoT Edge apparaten. 

Wanneer u de nieuwe apparaat-id maakt, geeft u de volgende informatie op: 

* Maak een ID voor uw apparaat.

* Selecteer **symmetrische sleutel** als het verificatie type. 

* U kunt desgewenst **een bovenliggend apparaat instellen** en het IOT Edge gateway apparaat selecteren dat door dit downstream-apparaat wordt verbonden. Deze stap is optioneel voor symmetrische sleutel verificatie, maar wordt aanbevolen omdat het instellen van een bovenliggend apparaat [offline mogelijkheden](offline-capabilities.md) voor het downstream-apparaat mogelijk maakt. U kunt de details van het apparaat altijd bijwerken om het bovenliggende item later toe te voegen of te wijzigen. 

   ![Apparaat-ID met symmetrische-sleutel verificatie maken in de portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

U kunt de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om dezelfde bewerking te volt ooien. In het volgende voor beeld wordt een nieuw IoT-apparaat met symmetrische sleutel verificatie gemaakt en wordt een bovenliggend apparaat toegewezen: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten en bovenliggend/onderliggend beheer raadpleegt u de referentie-inhoud voor [AZ IOT hub-apparaat-id-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) opdrachten.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Verbinding maken met IoT Hub via een gateway

Hetzelfde proces wordt gebruikt voor het verifiëren van regel matige IoT-apparaten voor het IoT Hub van symmetrische sleutels, ook van toepassing op downstream-apparaten. Het enige verschil is dat u een verwijzing naar het gateway apparaat moet toevoegen om de verbinding te routeren of, in offline scenario's, voor het afhandelen van de verificatie namens IoT Hub. 

Voor symmetrische-sleutel verificatie zijn er geen extra stappen die u moet uitvoeren op uw apparaat om te verifiëren met IoT Hub. U hebt de certificaten nog steeds nodig om ervoor te zorgen dat uw downstream-apparaat verbinding kan maken met het gateway apparaat, zoals beschreven in [een downstream-apparaat verbinden met een Azure IOT Edge gateway](how-to-connect-downstream-device.md).

Nadat u in de portal een IoT-apparaat-id hebt gemaakt, kunt u de primaire of secundaire sleutels ophalen. Een van deze sleutels moet worden opgenomen in de connection string die u opneemt in een toepassing die met IoT Hub communiceert. Voor de verificatie van symmetrische sleutels biedt IoT Hub de volledig opgemaakte connection string in de apparaatgegevens voor uw gemak. U moet extra informatie over het gateway apparaat toevoegen aan de connection string. 

De verbindings reeksen voor symmetrische sleutels voor downstream-apparaten hebben de volgende onderdelen nodig: 

* De IoT-hub waarmee het apparaat verbinding maakt: `Hostname={iothub name}.azure-devices.net`
* De apparaat-ID die is geregistreerd bij de hub: `DeviceID={device ID}`
* De primaire of secundaire sleutel: `SharedAccessKey={key}`
* Het gateway apparaat waarmee het apparaat verbinding maakt. Geef de waarde van de **hostnaam** op uit het bestand config. yaml van IOT Edge gateway-apparaat: `GatewayHostName={gateway hostname}`

Alle samen, een volledig connection string ziet er als volgt uit:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Als u een bovenliggende/onderliggende relatie hebt ingesteld voor dit downstream-apparaat, kunt u de connection string vereenvoudigen door de gateway rechtstreeks aan te roepen als de host van de verbinding. Bijvoorbeeld: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X. 509-verificatie 

Er zijn twee manieren om een IoT-apparaat te verifiëren met behulp van X. 509-certificaten. Op welke manier u ervoor kiest om te verifiëren, zijn de stappen om uw apparaat te verbinden met IoT Hub hetzelfde. Kies zelfondertekende of CA-ondertekende certificaten voor verificatie en ga vervolgens verder met meer informatie over het maken van verbinding met IoT Hub. 

Raadpleeg de volgende artikelen voor meer informatie over hoe IoT Hub X. 509-verificatie gebruikt: 
* [Verificatie van apparaten met behulp van X. 509 CA-certificaten](../iot-hub/iot-hub-x509ca-overview.md)
* [Conceptuele uitleg van X. 509 CA-certificaten in de IoT-industrie](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>De apparaat-id maken met X. 509 zelfondertekende certificaten

Voor X. 509 zelfondertekende verificatie, ook wel vingerafdruk verificatie genoemd, moet u nieuwe certificaten maken om op uw IoT-apparaat te plaatsen. Deze certificaten bevatten een vinger afdruk die u met IoT Hub voor verificatie kunt delen. 

De eenvoudigste manier om dit scenario te testen is het gebruik van dezelfde computer die u hebt gebruikt voor het maken van certificaten in [een IOT edge apparaat configureren dat als transparante gateway fungeert](how-to-create-transparent-gateway.md). Deze computer moet al zijn ingesteld met het juiste hulp programma, het basis-CA-certificaat en het tussenliggende CA-certificaat voor het maken van de IoT-apparaats certificaten. U kunt de definitieve certificaten en hun persoonlijke sleutels later naar uw downstream-apparaat kopiëren. Volg de stappen in het artikel van de gateway om openssl op uw computer in te stellen en vervolgens de IoT Edge opslag plaats te klonen om toegang te krijgen tot scripts voor het maken van certificaten. Vervolgens hebt u een werkmap gemaakt die wordt aangeroepen **\<WRKDIR >** om de certificaten te bewaren. De standaard certificaten zijn bedoeld voor ontwikkelen en testen, dus alleen de afgelopen 30 dagen. U moet een basis-CA-certificaat en een tussenliggend certificaat hebben gemaakt. 

1. Navigeer naar uw werkmap in een bash-of Power shell-venster. 

2. Maak twee certificaten (primair en secundair) voor het downstream-apparaat. Geef de naam van uw apparaat en vervolgens het primaire of secundaire label op. Deze informatie wordt gebruikt om de bestanden een naam te verlenen zodat u certificaten voor meerdere apparaten kunt bijhouden. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Haal de SHA1-vinger afdruk (een vinger afdruk in de IoT Hub Interface) op van elk certificaat, wat een 40 hexadecimale teken reeks is. Gebruik de volgende openssl-opdracht om het certificaat weer te geven en de vinger afdruk te zoeken:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Navigeer naar uw IoT-hub in het Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende waarden: 

   * Selecteer **X. 509 zelf ondertekend** als verificatie type.
   * Plak de hexadecimale teken reeksen die u hebt gekopieerd van het primaire en het secundaire certificaat van uw apparaat.
   * Selecteer **een bovenliggend apparaat instellen** en kies het IOT Edge gateway apparaat waarmee dit downstream-apparaat verbinding maakt. Er is een bovenliggend apparaat vereist voor X. 509-verificatie van een downstream-apparaat. 

   ![Apparaat-ID maken met X. 509 zelfondertekende auth in Portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Kopieer de volgende bestanden naar een map op uw downstream-apparaat:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   U verwijst naar deze bestanden in de Leaf-toepassingen die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.

U kunt de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om de bewerking voor het maken van een apparaat te volt ooien. In het volgende voor beeld wordt een nieuw IoT-apparaat gemaakt met X. 509 zelfondertekende authenticatie en wordt een bovenliggend apparaat toegewezen: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Zie voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten, het genereren van certificaten en het beheer van bovenliggende en onderliggende items de referentie-inhoud voor [AZ IOT hub-apparaat-id-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) opdrachten.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>De apparaat-id maken met de X. 509 certificerings instantie ondertekend certificaten

Voor X. 509 certificerings instantie (CA) ondertekende verificatie hebt u een basis-CA-certificaat geregistreerd in IoT Hub dat u gebruikt om certificaten voor uw IoT-apparaat te ondertekenen. Elk apparaat dat een certificaat gebruikt dat problemen heeft met het basis-CA-certificaat of een van de tussenliggende certificaten, mag worden geverifieerd. 

Deze sectie is gebaseerd op de instructies in het artikel IoT Hub [X. 509-beveiliging in te stellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md). Volg de stappen in deze sectie om te zien welke waarden u moet gebruiken voor het instellen van een downstream-apparaat dat via een gateway verbinding maakt. 

De eenvoudigste manier om dit scenario te testen is het gebruik van dezelfde computer die u hebt gebruikt voor het maken van certificaten in [een IOT edge apparaat configureren dat als transparante gateway fungeert](how-to-create-transparent-gateway.md). Deze computer moet al zijn ingesteld met het juiste hulp programma, het basis-CA-certificaat en het tussenliggende CA-certificaat voor het maken van de IoT-apparaats certificaten. U kunt de definitieve certificaten en hun persoonlijke sleutels later naar uw downstream-apparaat kopiëren. Volg de stappen in het artikel van de gateway om openssl op uw computer in te stellen en vervolgens de IoT Edge opslag plaats te klonen om toegang te krijgen tot scripts voor het maken van certificaten. Vervolgens hebt u een werkmap gemaakt die wordt aangeroepen **\<WRKDIR >** om de certificaten te bewaren. De standaard certificaten zijn bedoeld voor ontwikkelen en testen, dus alleen de afgelopen 30 dagen. U moet een basis-CA-certificaat en een tussenliggend certificaat hebben gemaakt. 

1. Volg de instructies in de sectie [509 van CA-certificaten registreren bij uw IOT-hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) voor het instellen van *X. 509-beveiliging in uw Azure IOT hub*. In deze sectie voert u de volgende stappen uit: 

   1. Upload een basis-CA-certificaat. Als u de certificaten gebruikt die u in het artikel van de transparante gateway hebt gemaakt, uploadt u **\<WRKDIR >/certs/Azure-IOT-test-only.root.ca.cert.pem** als het basis certificaat bestand. 
   2. Controleer of u bent eigenaar van het basis-CA-certificaat. U kunt het bezit van de CERT-hulpprogram ma's in \<WRKDIR > controleren. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Volg de instructies in het gedeelte [een x. 509-apparaat voor uw IOT-hub maken van de](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *X. 509-beveiliging instellen in uw Azure IOT hub*. In deze sectie voert u de volgende stappen uit: 

   1. Een nieuw apparaat toevoegen. Geef een kleine naam op voor de **apparaat-id**en kies het verificatie type **X. 509 certificerings instantie ondertekend**. 
   2. Stel een bovenliggend apparaat in. Selecteer voor downstream-apparaten de optie **een bovenliggend apparaat instellen** en kies het IOT Edge gateway-apparaat dat de verbinding met IOT hub zal bieden. 

3. Maak een certificaat keten voor het downstream-apparaat. Gebruik hetzelfde basis-CA-certificaat dat u hebt geüpload naar IoT Hub om deze keten te maken. Gebruik dezelfde kleine apparaat-ID die u in de portal hebt opgegeven voor de identiteit van uw apparaat.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Kopieer de volgende bestanden naar een map op uw downstream-apparaat: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   U verwijst naar deze bestanden in de Leaf-toepassingen die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.

U kunt de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om de bewerking voor het maken van een apparaat te volt ooien. In het volgende voor beeld wordt een nieuw IoT-apparaat gemaakt met X. 509 certificerings instantie ondertekend verificatie en wijst een bovenliggend apparaat toe: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Voor meer informatie over Azure CLI-opdrachten voor het maken van apparaten en bovenliggend/onderliggend beheer raadpleegt u de referentie-inhoud voor [AZ IOT hub-apparaat-id-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) opdrachten.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Verbinding maken met IoT Hub via een gateway

Elke Azure IoT SDK verwerkt X. 509-verificatie iets anders. Hetzelfde proces wordt echter gebruikt voor het verifiëren van regel matige IoT-apparaten om te IoT Hub met X. 509-certificaten zijn ook van toepassing op downstream-apparaten. Het enige verschil is dat u een verwijzing naar het gateway apparaat moet toevoegen om de verbinding te routeren of, in offline scenario's, voor het afhandelen van de verificatie namens IoT Hub. Over het algemeen kunt u dezelfde X. 509-verificatie stappen volgen voor alle apparaten van IoT Hub en de waarde van **hostname** in de Connection String vervangen door de hostnaam van het gateway apparaat. 

De volgende secties bevatten enkele voor beelden voor verschillende SDK-talen. 

>[!IMPORTANT]
>De volgende voor beelden laten zien hoe de IoT Hub Sdk's certificaten gebruiken om apparaten te verifiëren. In een productie-implementatie moet u alle geheimen zoals persoonlijke of SAS-sleutels in een HSM (hardware Secure module) opslaan. 

#### <a name="net"></a>.NET

Zie X. 509- C# [beveiliging instellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)voor een voor beeld van een programma dat wordt geverifieerd om te IOT hub met X. 509-certificaten. Enkele van de belangrijkste regels van dit voor beeld zijn hier opgenomen om het verificatie proces te demonstreren.

Wanneer u de hostnaam voor uw DeviceClient-exemplaar declareert, gebruikt u de hostnaam van het IoT Edge-gateway apparaat. De hostnaam vindt u in het bestand config. yaml van het gateway apparaat. 

Als u de test certificaten gebruikt die zijn opgenomen in de IoT Edge Git-opslag plaats, is de sleutel tot de certificaten **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Zie het [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) -voor beeld van de c IOT SDK voor een voor beeld van een c-programma voor het verifiëren van IOT hub met X. 509-certificaten. Enkele van de belangrijkste regels van dit voor beeld zijn hier opgenomen om het verificatie proces te demonstreren.

Gebruik bij het definiëren van de connection string voor uw downstream-apparaat de hostnaam van het IoT Edge gateway apparaat voor de para meter **hostname** . De hostnaam vindt u in het bestand config. yaml van het gateway apparaat. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Zie het voor beeld van een node. js-programma voor het verifiëren van IoT Hub met X. 509-certificaten, de node. js IoT SDK [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) -voor beeld. Enkele van de belangrijkste regels van dit voor beeld zijn hier opgenomen om het verificatie proces te demonstreren.

Gebruik bij het definiëren van de connection string voor uw downstream-apparaat de hostnaam van het IoT Edge gateway apparaat voor de para meter **hostname** . De hostnaam vindt u in het bestand config. yaml van het gateway apparaat. 

Als u de test certificaten gebruikt die zijn opgenomen in de IoT Edge Git-opslag plaats, is de sleutel tot de certificaten **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

De python-SDK biedt momenteel alleen ondersteuning voor het gebruik van x509-certificaten en sleutels van bestanden, niet de gedefinieerde inline. In het volgende voor beeld worden relevante bestandspad opgeslagen in omgevings variabelen.

Wanneer u de hostnaam voor het downstream-apparaat definieert, gebruikt u de hostnaam van het IoT Edge gateway apparaat voor de para meter **hostname** . De hostnaam vindt u in het bestand config. yaml van het gateway apparaat. 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

Voor een voor beeld van een Java-programma dat wordt geverifieerd om te IoT Hub met X. 509-certificaten, raadpleegt u het voor beeld van de Java IoT SDK [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) . Enkele van de belangrijkste regels van dit voor beeld zijn hier opgenomen om het verificatie proces te demonstreren.

Gebruik bij het definiëren van de connection string voor uw downstream-apparaat de hostnaam van het IoT Edge gateway apparaat voor de para meter **hostname** . De hostnaam vindt u in het bestand config. yaml van het gateway apparaat. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
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

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Volgende stappen

Door dit artikel te volt ooien, moet u een IoT Edge apparaat gebruiken als transparante gateway en een downstream-apparaat dat is geregistreerd bij een IoT-hub. Vervolgens moet u uw downstream-apparaten configureren om het gateway apparaat te vertrouwen en er berichten naar verzenden. Zie [verbinding maken tussen een downstream-apparaat en een Azure IOT Edge-gateway](how-to-connect-downstream-device.md)voor meer informatie.
