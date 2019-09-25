---
title: 'Verbinding maken met downstream-apparaten: Azure IoT Edge | Microsoft Docs'
description: Stroomafwaartse of Leaf-apparaten configureren om verbinding te maken met Azure IoT Edge gateway apparaten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 822e58d1d35cfb9b62565ca78ea2277b8d194bc0
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266109"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Een downstream-apparaat verbinden met Azure IoT Edge-gateway

Dit artikel bevat instructies voor het tot stand brengen van een vertrouwde verbinding tussen downstream-apparaten en IoT Edge transparante gateways. In een transparant Gateway scenario kunnen een of meer apparaten hun berichten door geven via één gateway apparaat waarmee de verbinding met IoT Hub wordt onderhouden. Een downstream-apparaat mag een toepassing of het platform waarvoor een identiteit die is gemaakt met de [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudservice. In veel gevallen gebruikt u deze toepassingen de [Azure IoT device-SDK](../iot-hub/iot-hub-devguide-sdks.md). Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway-apparaat zelf. 

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de derde stap behandeld:

1. Het gateway apparaat moet veilig verbinding maken met downstream-apparaten, communicaties ontvangen van downstream-apparaten en berichten naar de juiste bestemming routeren. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor meer informatie.
2. Het downstream-apparaat moet een apparaat-id hebben om te kunnen verifiëren met IoT Hub en te communiceren via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. **Het downstream-apparaat moet veilig verbinding kunnen maken met het gateway apparaat.**

In dit artikel identificeert bekende problemen met downstream apparaatverbindingen en helpt u bij het instellen van uw downstream-apparaten door: 

* Uitleg over transport layer security (TLS) en grondbeginselen van het certificaat. 
* Uitleg over de werking van TLS-bibliotheken voor verschillende besturingssystemen en hoe elk besturingssysteem omgaat met certificaten.
* Azure IoT hek voorbeelden in verschillende talen om u te helpen aan de slag. 

In dit artikel, de voorwaarden *gateway* en *IoT Edge-gateway* verwijzen naar een IoT Edge-apparaat dat is geconfigureerd als een transparante gateway. 

## <a name="prerequisites"></a>Vereisten 

Laat het certificaat bestand **Azure-IOT-test-only. root. ca. cert. pem** dat is gegenereerd in [een IOT edge apparaat configureren om te fungeren als een transparante gateway die](how-to-create-transparent-gateway.md) beschikbaar is op uw downstream-apparaat. Het downstream-apparaat gebruikt dit certificaat om de identiteit van het gateway-apparaat te valideren. 

## <a name="prepare-a-downstream-device"></a>Een downstream apparaat voorbereiden

Een downstream-apparaat mag een toepassing of het platform waarvoor een identiteit die is gemaakt met de [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudservice. In veel gevallen gebruikt u deze toepassingen de [Azure IoT device-SDK](../iot-hub/iot-hub-devguide-sdks.md). Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway-apparaat zelf. Een andere IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway. 

>[!NOTE]
>IoT-apparaten met geregistreerde identiteiten in IoT Hub kunnen [module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) gebruiken om verschillende processen, hardware of functies op één apparaat te isoleren. IoT Edge gateways ondersteunen downstream-module verbindingen met behulp van symmetrische sleutel verificatie, maar niet X. 509-certificaat verificatie. 

Voor een downstream-apparaat verbinding met een IoT Edge-gateway, moet u twee dingen:

* Een apparaat of een toepassing die geconfigureerd met een IoT Hub apparaat-verbindingsreeks toegevoegd met informatie om te verbinden met de gateway. 

    Deze stap wordt beschreven in [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md).

* Het apparaat of de toepassing moet het **basis-CA** -certificaat van de gateway vertrouwen om de TLS-verbindingen met het gateway apparaat te valideren. 

    Deze stap wordt gedetailleerd beschreven in de rest van dit artikel. Deze stap kan op twee manieren worden uitgevoerd: door het CA-certificaat in het certificaat archief van het besturings systeem te installeren of (voor bepaalde talen) door te verwijzen naar het certificaat in toepassingen met behulp van de Azure IoT Sdk's.

## <a name="tls-and-certificate-fundamentals"></a>Basisinformatie over TLS en certificaat

De uitdaging van downstream apparaten veilig verbinding te maken met IoT Edge is net als elke andere beveiligde client/server-communicatie die wordt uitgevoerd via internet. Een client en server veilig communiceren via internet met [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS is gebouwd met behulp van standaard [openbare-sleutelinfrastructuur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) constructies certificaten genoemd. TLS is een redelijk gepaarde specificatie en behandelt een breed scala aan onderwerpen met betrekking tot het beveiligen van twee eind punten. In deze sectie vindt u een overzicht van de concepten die relevant zijn voor u om apparaten veilig te verbinden met een IoT Edge gateway.

Wanneer een client verbinding met een server maakt, de server een keten van certificaten, met de naam geeft de *server certificaatketen*. Een certificaatketen bestaat gewoonlijk uit een basiscertificaat van de certificaat-certificeringsinstantie (CA), een of meer tussenliggende CA-certificaten en ten slotte certificaat van de server zelf. Een client brengt een vertrouwensrelatie met een server tot stand door cryptografisch te controleren of de certificaatketen voor de hele server. Deze client validatie van de server certificaat keten wordt de *validatie van de server keten*genoemd. De-client kan de service cryptografisch een uitdaging bieden om te bewijzen dat de persoonlijke sleutel die is gekoppeld aan het server certificaat in een proces met de naam *bewijs van bezit*is. De combi natie van validatie van de server keten en het bewijs van bezit wordt *Server verificatie*genoemd. Een client moet voor het valideren van de certificaatketen van een server, een kopie van het basis-CA-certificaat dat is gebruikt voor het certificaat van de server maken (of uitgeven). Normaal gesproken bij het verbinden met websites, een browser wordt al geconfigureerd geleverd met veelgebruikte CA-certificaten, zodat de client een naadloze proces heeft. 

Wanneer een apparaat verbinding met Azure IoT Hub maakt, het apparaat is de client en de IoT Hub-cloudservice is de server. De IoT Hub-cloudservice wordt ondersteund door een basis-CA-certificaat met de naam **Baltimore CyberTrust Root**, is openbaar beschikbare en gebruikte. Omdat de IoT Hub CA-certificaat is al geïnstalleerd op de meeste apparaten, wordt deze door veel implementaties van TLS (OpenSSL, Schannel, LibreSSL) automatisch gebruikt tijdens de validatie van het servercertificaat. Een apparaat dat is mogelijk verbinding met IoT Hub mogelijk problemen met het verbinden met IoT Edge-gateway.

Wanneer een apparaat verbinding met IoT Edge-gateway maakt, de downstream apparaat is de client en de gateway-apparaat is de server. Azure IoT Edge kunt operators (of gebruikers) gateway certificaatketens bouwen, maar ze naar eigen inzicht. De operator besluiten te gebruiken een openbare CA-certificaat, zoals Baltimore, of een zelf-ondertekend (of interne) basis-CA-certificaat gebruiken. Openbare CA-certificaten zijn kosten verbonden aan ze vaak, dus worden meestal gebruikt in productiescenario's. Zelf-ondertekende CA-certificaten worden verkozen voor ontwikkelen en testen. De transparante gateway-installatie artikelen die in de inleiding worden vermeld, maken gebruik van zelfondertekende basis-CA-certificaten. 

Wanneer u een zelfondertekend basiscertificaat CA-certificaat voor een IoT Edge-gateway gebruikt, moet worden geïnstalleerd op of die de downstream apparaten probeert verbinding maken met de gateway. 

![Certificaat-gatewayupgrade](./media/how-to-create-transparent-gateway/gateway-setup.png)

Zie voor meer informatie over IoT Edge-certificaten en de gevolgen van sommige productie [informatie over het IoT Edge certificaat](iot-edge-certs.md).

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

U ziet een bericht waarin wordt gemeld, "Updating certificaten in /etc/ssl/certs... 1 heeft toegevoegd, 0 verwijderd; gedaan."

### <a name="windows"></a>Windows

De volgende stappen zijn een voorbeeld van een CA-certificaat installeren op een Windows-host. In dit voor beeld wordt ervan uitgegaan dat u het certificaat **Azure-IOT-test-only. root. ca. cert. pem** gebruikt uit de artikelen van de vereisten en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

U kunt certificaten installeren met behulp van het [import-certificaat](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) van Power shell als beheerder:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

U kunt certificaten ook installeren met het hulp programma **certlm** : 

1. Zoek in het menu Start en selecteer **computercertificaten beheren**. Een hulpprogramma **certlm** wordt geopend.
2. Navigeer naar **certificaten - lokale Computer** > **Trusted Root Certification Authorities**.
3. Met de rechtermuisknop op **certificaten** en selecteer **alle taken** > **importeren**. De wizard Certificaat importeren te starten. 
4. Volg de stappen te gaan en certificaatbestand importeren `<path>/azure-iot-test-only.root.ca.cert.pem`. Wanneer dit is voltooid, kunt u een "Geïmporteerd" bericht moet zien. 

U kunt ook certificaten programmatisch met behulp van .NET-API's, installeren, zoals weergegeven in de .NET-voorbeeld verderop in dit artikel. 

Toepassingen gebruiken normaal gesproken op de Windows voorwaarde TLS-stack met de naam [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) veilig verbinding maken via TLS. Schannel *vereist* dat alle certificaten in het certificaatarchief van Windows worden geïnstalleerd voordat u probeert een TLS-verbinding tot stand brengen.

## <a name="use-certificates-with-azure-iot-sdks"></a>Certificaten gebruiken met Azure IoT SDK 's

Deze sectie wordt beschreven hoe de Azure IoT SDK's verbinding maken met een IoT Edge-apparaat met behulp van eenvoudige voorbeeldtoepassingen. Het doel van alle voorbeelden is verbinding maken met de apparaatclient en berichten over telemetrie verzenden naar de gateway en Verbreek de verbinding en afsluiten. 

Twee dingen die gereed is voordat u de voorbeelden op toepassingsniveau hebben:

* De IoT Hub van uw downstream-apparaat connection string zodanig gewijzigd dat het naar het gateway apparaat wijst, en eventuele certificaten die nodig zijn om uw downstream-apparaat te verifiëren voor IoT Hub. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.

* Het volledige pad naar het basis-CA-certificaat dat u hebt gekopieerd en opgeslagen ergens op uw downstream-apparaat.

    Bijvoorbeeld `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>Node.js

Deze sectie bevat een voorbeeld van toepassing op een client voor Azure IoT-NodeJS-apparaten verbinden met IoT Edge-gateway. Voor NodeJS-toepassingen moet u het basis-CA-certificaat installeren op het niveau van de toepassing, zoals hier wordt weer gegeven. NodeJS-toepassingen gebruiken het certificaat archief van het systeem niet. 

1. Ophalen van het voorbeeld voor **edge_downstream_device.js** uit de [Azure IoT device-SDK voor Node.js-voorbeelden opslagplaats](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Zorg ervoor dat u alle vereisten hebt voor het uitvoeren van het voorbeeld aan de hand van de **readme.md** bestand. 
3. In het bestand edge_downstream_device.js bijwerken de **connectionString** en **edge_ca_cert_path** variabelen. 
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

1. Ophalen van het voorbeeld voor **EdgeDownstreamDevice** uit de [IoT Edge .NET-voorbeelden map](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Zorg ervoor dat u alle vereisten hebt voor het uitvoeren van het voorbeeld aan de hand van de **readme.md** bestand. 
3. In de **eigenschappen / launchSettings.json** bestand de **DEVICE_CONNECTION_STRING** en **CA_CERTIFICATE_PATH** variabelen. Als u gebruikmaken van het certificaat geïnstalleerd in het vertrouwde certificaatarchief op het hostsysteem wilt, laat u deze variabele leeg. 
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 

Als u wilt installeren via een programma een vertrouwd certificaat in het certificaatarchief via een .NET-toepassing, verwijzen naar de **InstallCACert()** werken in de **EdgeDownstreamDevice / Program.cs** bestand. Deze bewerking is idempotent zijn, zodat meerdere keren met dezelfde waarden zonder extra effect kunnen worden uitgevoerd. 

### <a name="c"></a>C

In deze sectie wordt een voorbeeld van toepassing op de client van een Azure IoT C-apparaten verbinden met IoT Edge-gateway. De C-SDK kunnen werken met veel TLS-bibliotheken, waaronder OpenSSL, WolfSSL en Schannel. Zie voor meer informatie de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

1. Krijgen de **iotedge_downstream_device_sample** toepassing uit de [Azure IoT device-SDK voor C-voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Zorg ervoor dat u alle vereisten hebt voor het uitvoeren van het voorbeeld aan de hand van de **readme.md** bestand. 
3. In het bestand iotedge_downstream_device_sample.c bijwerken de **connectionString** en **edge_ca_cert_path** variabelen. 
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 

De Azure IoT device-SDK voor C biedt een optie voor het registreren van een CA-certificaat bij het instellen van de client. Met deze bewerking niet overal in het certificaat wordt geïnstalleerd, maar in plaats daarvan maakt gebruik van een tekenreeksindeling van het certificaat in het geheugen. Het opgeslagen certificaat is verstrekt aan de onderliggende TLS-stack wanneer een verbinding tot stand brengen. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Op Windows-hosts, als u niet OpenSSL of een andere TLS-bibliotheek, de SDK standaard gebruikmaken van Schannel. Voor Schannel om te werken, moet het IoT Edge-basis-CA-certificaat worden geïnstalleerd in het Windows-certificaatarchief, niet is ingesteld met behulp van de `IoTHubDeviceClient_SetOption` bewerking. 

### <a name="java"></a>Java

In deze sectie wordt een voorbeeld van toepassing op de client van een Azure IoT-Java-apparaten verbinden met IoT Edge-gateway. 

1. Ophalen van het voorbeeld voor **verzenden-gebeurtenis** uit de [Azure IoT device-SDK voor Java-voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Zorg ervoor dat u alle vereisten hebt voor het uitvoeren van het voorbeeld aan de hand van de **readme.md** bestand. 
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

### <a name="python"></a>Python

In deze sectie wordt een voorbeeld van toepassing op de client van een Azure IoT Python-apparaten verbinden met IoT Edge-gateway. 

1. Haal het voor beeld voor **send_message** op uit de [Azure IOT Device SDK voor python-voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Zorg ervoor dat u in een Edge-container of in een scenario voor fout opsporing een omgevings `EdgeHubConnectionString` variabele `EdgeModuleCACertificateFile` en hebt ingesteld.
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat. 


## <a name="test-the-gateway-connection"></a>De gatewayverbinding testen

Dit is een voor beeld van een opdracht waarmee wordt getest dat alles correct is ingesteld. U ziet een bericht 'OK geverifieerd'.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Problemen met de gateway verbinding oplossen

Als uw blad apparaat een terugkerende verbinding met het gateway apparaat heeft, voert u de volgende stappen uit voor het oplossen van problemen. 

1. Is de hostnaam van de gateway in de connection string hetzelfde als de hostnaam-waarde in het bestand IoT Edge config. yaml op het gateway apparaat?
2. Is de hostnaam van de gateway oplosbaar voor een IP-adres? U kunt onregelmatige verbindingen oplossen door DNS te gebruiken of door een vermelding voor een hostbestand toe te voegen op het blad apparaat.
3. Zijn er communicatie poorten in de firewall geopend? Communicatie op basis van het gebruikte protocol (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) moet mogelijk zijn tussen het downstream-apparaat en de transparante IoT Edge.

## <a name="next-steps"></a>Volgende stappen

Informatie over hoe IoT Edge kunt uitbreiden [offlinemogelijkheden](offline-capabilities.md) op downstream-apparaten. 
