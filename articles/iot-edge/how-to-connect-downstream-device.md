---
title: 'Verbinding maken met downstream-apparaten: Azure IoT Edge | Microsoft Docs'
description: Stroomafwaartse of Leaf-apparaten configureren om verbinding te maken met Azure IoT Edge gateway apparaten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457119"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Een downstream-apparaat verbinden met Azure IoT Edge-gateway

Dit artikel bevat instructies voor het tot stand brengen van een vertrouwde verbinding tussen downstream-apparaten en IoT Edge transparante gateways. In een transparant Gateway scenario kunnen een of meer apparaten hun berichten door geven via één gateway apparaat waarmee de verbinding met IoT Hub wordt onderhouden. Een downstream-apparaat kan een toepassing of platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) -Cloud service. In veel gevallen gebruiken deze toepassingen de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md). Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway-apparaat zelf. 

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de derde stap behandeld:

1. Het gateway apparaat moet veilig verbinding maken met downstream-apparaten, communicaties ontvangen van downstream-apparaten en berichten naar de juiste bestemming routeren. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor meer informatie.
2. Het downstream-apparaat moet een apparaat-id hebben om te kunnen verifiëren met IoT Hub en te communiceren via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. **Het downstream-apparaat moet veilig verbinding kunnen maken met het gateway apparaat.**

In dit artikel identificeert bekende problemen met downstream apparaatverbindingen en helpt u bij het instellen van uw downstream-apparaten door: 

* Uitleg over transport layer security (TLS) en grondbeginselen van het certificaat. 
* Uitleg over de werking van TLS-bibliotheken voor verschillende besturingssystemen en hoe elk besturingssysteem omgaat met certificaten.
* Azure IoT hek voorbeelden in verschillende talen om u te helpen aan de slag. 

In dit artikel verwijzen de voor waarden *Gateway* en *IOT Edge gateway* naar een IOT edge apparaat geconfigureerd als een transparante gateway. 

## <a name="prerequisites"></a>Vereisten 

Laat het certificaat bestand **Azure-IOT-test-only. root. ca. cert. pem** dat is gegenereerd in [een IOT edge apparaat configureren om te fungeren als een transparante gateway die](how-to-create-transparent-gateway.md) beschikbaar is op uw downstream-apparaat. Het downstream-apparaat gebruikt dit certificaat om de identiteit van het gateway-apparaat te valideren. 

## <a name="prepare-a-downstream-device"></a>Een downstream apparaat voorbereiden

Een downstream-apparaat kan een toepassing of platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](https://docs.microsoft.com/azure/iot-hub) -Cloud service. In veel gevallen gebruiken deze toepassingen de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md). Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway-apparaat zelf. Een andere IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway. 

>[!NOTE]
>IoT-apparaten met geregistreerde identiteiten in IoT Hub kunnen [module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) gebruiken om verschillende processen, hardware of functies op één apparaat te isoleren. IoT Edge gateways ondersteunen downstream-module verbindingen met behulp van symmetrische sleutel verificatie, maar niet X. 509-certificaat verificatie. 

Voor een downstream-apparaat verbinding met een IoT Edge-gateway, moet u twee dingen:

* Een apparaat of een toepassing die geconfigureerd met een IoT Hub apparaat-verbindingsreeks toegevoegd met informatie om te verbinden met de gateway. 

    Deze stap wordt beschreven in [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md).

* Het apparaat of de toepassing moet het **basis-CA** -certificaat van de gateway vertrouwen om de TLS-verbindingen met het gateway apparaat te valideren. 

    Deze stap wordt gedetailleerd beschreven in de rest van dit artikel. Deze stap kan op twee manieren worden uitgevoerd: door het CA-certificaat in het certificaat archief van het besturings systeem te installeren of (voor bepaalde talen) door te verwijzen naar het certificaat in toepassingen met behulp van de Azure IoT Sdk's.

## <a name="tls-and-certificate-fundamentals"></a>Basisinformatie over TLS en certificaat

De uitdaging van downstream apparaten veilig verbinding te maken met IoT Edge is net als elke andere beveiligde client/server-communicatie die wordt uitgevoerd via internet. Een client en een server communiceren veilig via internet via [trans port Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS is gebouwd met behulp van standaard-constructies met een [open bare-sleutel infrastructuur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) met de naam certificaten. TLS is een redelijk gepaarde specificatie en behandelt een breed scala aan onderwerpen met betrekking tot het beveiligen van twee eind punten. In deze sectie vindt u een overzicht van de concepten die relevant zijn voor u om apparaten veilig te verbinden met een IoT Edge gateway.

Wanneer een client verbinding maakt met een server, presenteert de server een keten van certificaten, die de *server certificaat keten*wordt genoemd. Een certificaatketen bestaat gewoonlijk uit een basiscertificaat van de certificaat-certificeringsinstantie (CA), een of meer tussenliggende CA-certificaten en ten slotte certificaat van de server zelf. Een client brengt een vertrouwensrelatie met een server tot stand door cryptografisch te controleren of de certificaatketen voor de hele server. Deze client validatie van de server certificaat keten wordt de *validatie van de server keten*genoemd. De-client kan de service cryptografisch een uitdaging bieden om te bewijzen dat de persoonlijke sleutel die is gekoppeld aan het server certificaat in een proces met de naam *bewijs van bezit*is. De combi natie van validatie van de server keten en het bewijs van bezit wordt *Server verificatie*genoemd. Een client moet voor het valideren van de certificaatketen van een server, een kopie van het basis-CA-certificaat dat is gebruikt voor het certificaat van de server maken (of uitgeven). Normaal gesproken bij het verbinden met websites, een browser wordt al geconfigureerd geleverd met veelgebruikte CA-certificaten, zodat de client een naadloze proces heeft. 

Wanneer een apparaat verbinding met Azure IoT Hub maakt, het apparaat is de client en de IoT Hub-cloudservice is de server. De IoT Hub Cloud service wordt ondersteund door een basis-CA-certificaat met de naam **Baltimore Cyber Trust Root**, dat openbaar beschikbaar is en veel wordt gebruikt. Omdat de IoT Hub CA-certificaat is al geïnstalleerd op de meeste apparaten, wordt deze door veel implementaties van TLS (OpenSSL, Schannel, LibreSSL) automatisch gebruikt tijdens de validatie van het servercertificaat. Een apparaat dat is mogelijk verbinding met IoT Hub mogelijk problemen met het verbinden met IoT Edge-gateway.

Wanneer een apparaat verbinding met IoT Edge-gateway maakt, de downstream apparaat is de client en de gateway-apparaat is de server. Azure IoT Edge kunt operators (of gebruikers) gateway certificaatketens bouwen, maar ze naar eigen inzicht. De operator besluiten te gebruiken een openbare CA-certificaat, zoals Baltimore, of een zelf-ondertekend (of interne) basis-CA-certificaat gebruiken. Openbare CA-certificaten zijn kosten verbonden aan ze vaak, dus worden meestal gebruikt in productiescenario's. Zelf-ondertekende CA-certificaten worden verkozen voor ontwikkelen en testen. De transparante gateway-installatie artikelen die in de inleiding worden vermeld, maken gebruik van zelfondertekende basis-CA-certificaten. 

Wanneer u een zelfondertekend basiscertificaat CA-certificaat voor een IoT Edge-gateway gebruikt, moet worden geïnstalleerd op of die de downstream apparaten probeert verbinding maken met de gateway. 

![Certificaat-gatewayupgrade](./media/how-to-create-transparent-gateway/gateway-setup.png)

Zie [IOT Edge certificaat gebruik Details](iot-edge-certs.md)voor meer informatie over IOT Edge certificaten en enkele gevolgen voor de productie.

## <a name="provide-the-root-ca-certificate"></a>Het basis-CA-certificaat opgeven

Het downstream-apparaat heeft een eigen kopie van het basis-CA-certificaat nodig om de certificaten van het gateway-apparaat te controleren. Als u de scripts in de IoT Edge Git-opslag plaats hebt gebruikt om test certificaten te maken, wordt het basis-CA-certificaat de naam **Azure-IOT-test-only. root. ca. cert. pem**. Verplaats dit certificaat bestand naar een map op het downstream-apparaat als u dat nog niet hebt gedaan. U kunt een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om het certificaat bestand te verplaatsen.

## <a name="install-certificates-in-the-os"></a>Certificaten in het besturings systeem installeren

Als u het basis-CA-certificaat in het certificaat archief van het besturings systeem installeert, kunnen de meeste toepassingen het basis-CA-certificaat gebruiken. Er zijn enkele uitzonde ringen, zoals NodeJS-toepassingen die geen gebruikmaken van het certificaat archief van het besturings systeem, maar het interne certificaat archief van de node runtime gebruiken. Als u het certificaat niet op het niveau van het besturings systeem kunt installeren, gaat u verder met het [gebruik van certificaten met Azure IOT sdk's](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

De volgende opdrachten zijn een voorbeeld van een CA-certificaat installeren op een Ubuntu-host. In dit voor beeld wordt ervan uitgegaan dat u het certificaat **Azure-IOT-test-only. root. ca. cert. pem** gebruikt uit de artikelen van de vereisten en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Er wordt een bericht weer gegeven met de tekst ' certificaten bijwerken in/etc/ssl/certs... 1 toegevoegd, 0 verwijderd; gereed. "

### <a name="windows"></a>Windows

De volgende stappen zijn een voorbeeld van een CA-certificaat installeren op een Windows-host. In dit voor beeld wordt ervan uitgegaan dat u het certificaat **Azure-IOT-test-only. root. ca. cert. pem** gebruikt uit de artikelen van de vereisten en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

U kunt certificaten installeren met behulp van het [import-certificaat](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) van Power shell als beheerder:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

U kunt certificaten ook installeren met het hulp programma **certlm** : 

1. Zoek in het menu Start naar en selecteer **computer certificaten beheren**. Een hulp programma met de naam **certlm** wordt geopend.
2. Navigeer naar **certificaten-lokale Computer** > **vertrouwde basis certificerings instanties**.
3. Klik met de rechter muisknop op **certificaten** en selecteer **alle taken** > **importeren**. De wizard Certificaat importeren te starten. 
4. Volg de stappen als gericht en importeer certificaat bestand `<path>/azure-iot-test-only.root.ca.cert.pem`. Wanneer dit is voltooid, kunt u een "Geïmporteerd" bericht moet zien. 

U kunt ook certificaten programmatisch met behulp van .NET-API's, installeren, zoals weergegeven in de .NET-voorbeeld verderop in dit artikel. 

Doorgaans gebruiken toepassingen de door Windows meegeleverde TLS-stack [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) om veilig verbinding te maken via TLS. Voor *Schannel moeten* alle certificaten in het Windows-certificaat archief worden geïnstalleerd voordat er wordt geprobeerd een TLS-verbinding tot stand te brengen.

## <a name="use-certificates-with-azure-iot-sdks"></a>Certificaten gebruiken met Azure IoT SDK 's

Deze sectie wordt beschreven hoe de Azure IoT SDK's verbinding maken met een IoT Edge-apparaat met behulp van eenvoudige voorbeeldtoepassingen. Het doel van alle voorbeelden is verbinding maken met de apparaatclient en berichten over telemetrie verzenden naar de gateway en Verbreek de verbinding en afsluiten. 

Twee dingen die gereed is voordat u de voorbeelden op toepassingsniveau hebben:

* De IoT Hub van uw downstream-apparaat connection string zodanig gewijzigd dat het naar het gateway apparaat wijst, en eventuele certificaten die nodig zijn om uw downstream-apparaat te verifiëren voor IoT Hub. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.

* Het volledige pad naar het basis-CA-certificaat dat u hebt gekopieerd en opgeslagen ergens op uw downstream-apparaat.

    Bijvoorbeeld `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>Node.js

Deze sectie bevat een voorbeeld van toepassing op een client voor Azure IoT-NodeJS-apparaten verbinden met IoT Edge-gateway. Voor NodeJS-toepassingen moet u het basis-CA-certificaat installeren op het niveau van de toepassing, zoals hier wordt weer gegeven. NodeJS-toepassingen gebruiken het certificaat archief van het systeem niet. 

1. Haal het voor beeld voor **edge_downstream_device. js** op uit de [Azure IOT Device SDK voor voor beelden van node. js opslag plaats](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren. 
3. Werk de **Connections Tring** -en **edge_ca_cert_path** -variabelen bij in het bestand edge_downstream_device. js. 
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 

Voor inzicht in het voorbeeld dat u uitvoert, wordt het volgende codefragment weergegeven hoe de client-SDK leest het certificaatbestand en gebruikt voor het maken van een beveiligde TLS-verbinding: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

In deze sectie wordt een voorbeeld van toepassing op de client voor een Azure IoT .NET-apparaten verbinden met IoT Edge-gateway. .NET-toepassingen kunnen echter automatisch voor het gebruik van alle geïnstalleerde certificaten in het certificaatarchief van het systeem op zowel Windows als Linux-hosts.

1. Haal het voor beeld voor **EdgeDownstreamDevice** op uit de [map met IOT Edge .net](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)-voor beelden. 
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren. 
3. Werk in het bestand **Properties/launchSettings. json** de variabelen **DEVICE_CONNECTION_STRING** en **CA_CERTIFICATE_PATH** bij. Als u gebruikmaken van het certificaat geïnstalleerd in het vertrouwde certificaatarchief op het hostsysteem wilt, laat u deze variabele leeg. 
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 

Raadpleeg de functie **InstallCACert ()** in het **EdgeDownstreamDevice/Program.cs-** bestand om programmatisch een vertrouwd certificaat in het certificaat archief te installeren via een .NET-toepassing. Deze bewerking is idempotent zijn, zodat meerdere keren met dezelfde waarden zonder extra effect kunnen worden uitgevoerd. 

### <a name="c"></a>C

In deze sectie wordt een voorbeeld van toepassing op de client van een Azure IoT C-apparaten verbinden met IoT Edge-gateway. De C-SDK kunnen werken met veel TLS-bibliotheken, waaronder OpenSSL, WolfSSL en Schannel. Zie de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c)voor meer informatie. 

1. Haal de **iotedge_downstream_device_sample** toepassing op uit de [Azure IOT Device SDK voor C-voor beelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren. 
3. Werk in het bestand iotedge_downstream_device_sample. c de variabelen **Connections Tring** en **edge_ca_cert_path** bij. 
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 

De Azure IoT device-SDK voor C biedt een optie voor het registreren van een CA-certificaat bij het instellen van de client. Met deze bewerking niet overal in het certificaat wordt geïnstalleerd, maar in plaats daarvan maakt gebruik van een tekenreeksindeling van het certificaat in het geheugen. Het opgeslagen certificaat is verstrekt aan de onderliggende TLS-stack wanneer een verbinding tot stand brengen. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Op Windows-hosts, als u niet OpenSSL of een andere TLS-bibliotheek, de SDK standaard gebruikmaken van Schannel. Schannel werkt alleen als het basis-CA-certificaat van IoT Edge is geïnstalleerd in het Windows-certificaat archief en niet is ingesteld met behulp van de `IoTHubDeviceClient_SetOption` bewerking. 

### <a name="java"></a>Java

In deze sectie wordt een voorbeeld van toepassing op de client van een Azure IoT-Java-apparaten verbinden met IoT Edge-gateway. 

1. Het voor beeld voor **Send-Event** ophalen uit de [Azure IOT Device SDK voor Java-voor beelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren. 
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

### <a name="python"></a>Python

In deze sectie wordt een voorbeeld van toepassing op de client van een Azure IoT Python-apparaten verbinden met IoT Edge-gateway. 

1. Bekijk het voor beeld voor **send_message** van de [Azure IOT Device SDK voor python-voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Zorg ervoor dat u in een IoT Edge container of in een scenario voor fout opsporing werkt, of de variabelen `EdgeHubConnectionString` en `EdgeModuleCACertificateFile` omgeving zijn ingesteld.
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 


## <a name="test-the-gateway-connection"></a>De gatewayverbinding testen

Gebruik deze voorbeeld opdracht om te testen of uw downstream-apparaat verbinding kan maken met het gateway apparaat: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Met deze opdracht worden verbindingen getest via MQTTS (poort 8883). Als u een ander protocol gebruikt, past u de opdracht indien nodig aan voor AMQPS (5671) of HTTPS (433).

De uitvoer van deze opdracht kan lang zijn, inclusief informatie over alle certificaten in de keten. Als uw verbinding is geslaagd, ziet u een regel als `Verification: OK` of `Verify return code: 0 (ok)`.

![Gateway verbinding controleren](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Problemen met de gateway verbinding oplossen

Als uw blad apparaat een terugkerende verbinding met het gateway apparaat heeft, voert u de volgende stappen uit voor het oplossen van problemen. 

1. Is de hostnaam van de gateway in de connection string hetzelfde als de hostnaam-waarde in het bestand IoT Edge config. yaml op het gateway apparaat?
2. Is de hostnaam van de gateway oplosbaar voor een IP-adres? U kunt onregelmatige verbindingen oplossen door DNS te gebruiken of door een vermelding voor een hostbestand toe te voegen op het blad apparaat.
3. Zijn er communicatie poorten in de firewall geopend? Communicatie op basis van het gebruikte protocol (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) moet mogelijk zijn tussen het downstream-apparaat en de transparante IoT Edge.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe IoT Edge [offline mogelijkheden](offline-capabilities.md) kunt uitbreiden naar downstream-apparaten. 
