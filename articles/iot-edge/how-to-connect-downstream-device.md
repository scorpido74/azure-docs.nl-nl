---
title: Downstream-apparaten verbinden - Azure IoT Edge | Microsoft Documenten
description: Downstream- of leaf-apparaten configureren om verbinding te maken met Azure IoT Edge-gatewayapparaten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3113f01341d2a1ec6160cfea3eb9d12d18b8495c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687173"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Een downstreamapparaat verbinden met een Azure IoT Edge-gateway

In dit artikel vindt u instructies voor het tot stand brengen van een vertrouwde verbinding tussen downstream-apparaten en transparante Gateways van IoT Edge. In een transparant gatewayscenario kunnen een of meer apparaten hun berichten doorgeven via één gatewayapparaat dat de verbinding met IoT Hub onderhoudt. Een downstream-apparaat kan elke toepassing of platform zijn met een identiteit die is gemaakt met de [Azure IoT Hub-cloudservice.](https://docs.microsoft.com/azure/iot-hub) In veel gevallen maken deze toepassingen gebruik van de [Azure IoT-apparaat SDK.](../iot-hub/iot-hub-devguide-sdks.md) Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge-gateway-apparaat zelf.

Er zijn drie algemene stappen om een succesvolle transparante gatewayverbinding tot doel te stellen. Dit artikel behandelt de derde stap:

1. Het gateway-apparaat moet veilig verbinding maken met downstream-apparaten, communicatie ontvangen van downstream-apparaten en berichten naar de juiste bestemming leiden. Zie [Een IoT Edge-apparaat configureren om als transparante gateway te fungeren](how-to-create-transparent-gateway.md)voor meer informatie.
2. Het downstream-apparaat heeft een apparaatidentiteit nodig om te kunnen verifiëren met IoT Hub en te weten dat het moet communiceren via het gateway-apparaat. Zie [Een downstream-apparaat verifiëren naar Azure IoT Hub](how-to-authenticate-downstream-device.md)voor meer informatie.
3. **Het downstream-apparaat moet veilig verbinding maken met het gateway-apparaat.**

In dit artikel worden veelvoorkomende problemen met downstream-apparaatverbindingen geïdentificeerd en wordt u bij het instellen van uw downstream-apparaten geloodst door:

* Uitleg over tls (transportlayer security) en certificate fundamentals.
* Uitleg over hoe TLS-bibliotheken werken op verschillende besturingssystemen en hoe elk besturingssysteem omgaat met certificaten.
* U azure IoT-voorbeelden in verschillende talen doorlopen om u op weg te helpen.

In dit artikel verwijzen de termen *gateway* en *IoT Edge-gateway* naar een IoT Edge-apparaat dat is geconfigureerd als een transparante gateway.

## <a name="prerequisites"></a>Vereisten

* Het **azure-iot-test-only.root.ca.cert.pem-certificaatbestand** dat is gegenereerd in [Een IoT Edge-apparaat configureren om te fungeren als een transparante gateway](how-to-create-transparent-gateway.md) die beschikbaar is op uw downstream-apparaat. Uw downstream-apparaat gebruikt dit certificaat om de identiteit van het gateway-apparaat te valideren.
* De gewijzigde verbindingstekenreeks die naar het gatewayapparaat verwijst, zoals uitgelegd in [Authenticeren van een downstream-apparaat naar Azure IoT Hub.](how-to-authenticate-downstream-device.md)

## <a name="prepare-a-downstream-device"></a>Een downstream-apparaat voorbereiden

Een downstream-apparaat kan elke toepassing of platform zijn met een identiteit die is gemaakt met de [Azure IoT Hub-cloudservice.](https://docs.microsoft.com/azure/iot-hub) In veel gevallen maken deze toepassingen gebruik van de [Azure IoT-apparaat SDK.](../iot-hub/iot-hub-devguide-sdks.md) Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge-gateway-apparaat zelf. Een ander IoT Edge-apparaat kan echter niet stroomafwaarts van een IoT Edge-gateway zijn.

>[!NOTE]
>IoT-apparaten met identiteiten die zijn geregistreerd in IoT Hub, kunnen [moduletweelingen](../iot-hub/iot-hub-devguide-module-twins.md) gebruiken om verschillende processen, hardware of functies op één apparaat te isoleren. IoT Edge-gateways ondersteunen downstream-moduleverbindingen met symmetrische sleutelverificatie, maar niet x.509-certificaatverificatie.

Als u een downstream-apparaat wilt aansluiten op een IoT Edge-gateway, hebt u twee dingen nodig:

* Een apparaat of toepassing die is geconfigureerd met een IE-verbinding met een IoT Hub-apparaat, is toegevoegd aan informatie om het met de gateway te verbinden.

    Deze stap wordt uitgelegd in [Authenticeren van een downstream-apparaat naar Azure IoT Hub.](how-to-authenticate-downstream-device.md)

* Het apparaat of de toepassing moet het **hoofd-CA-certificaat** van de gateway vertrouwen om de TLS-verbindingen met het gateway-apparaat te valideren.

    Deze stap wordt in detail uitgelegd in de rest van dit artikel. Deze stap kan op twee manieren worden uitgevoerd: door het CA-certificaat te installeren in het certificaatarchief van het besturingssysteem of (voor bepaalde talen) door te verwijzen naar het certificaat binnen toepassingen met behulp van de Azure IoT-SDK's.

## <a name="tls-and-certificate-fundamentals"></a>TLS en certificaatfundamenten

De uitdaging van het veilig aansluiten van downstream-apparaten op IoT Edge is net als elke andere beveiligde client / server communicatie die plaatsvindt via het internet. Een client en een server communiceren veilig via internet via [Transport layer security (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS is gebouwd met behulp van standaard [Public key infrastructure (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) constructies genaamd certificaten. TLS is een redelijk betrokken specificatie en behandelt een breed scala aan onderwerpen met betrekking tot het beveiligen van twee eindpunten. In deze sectie worden de concepten samengevat die relevant zijn om apparaten veilig te verbinden met een IoT Edge-gateway.

Wanneer een client verbinding maakt met een server, presenteert de server een keten van certificaten, de *servercertificaatketen*genaamd. Een certificaatketen bestaat meestal uit een CA-certificaat (Root Certificate Authority), een of meer tussenliggende CA-certificaten en ten slotte het certificaat van de server zelf. Een client stelt vertrouwen in een server door de hele servercertificaatketen cryptografisch te verifiëren. Deze clientvalidatie van de servercertificaatketen wordt *serverketenvalidatie*genoemd. De client daagt de service cryptografisch uit om het bezit van de privésleutel in verband met het servercertificaat te bewijzen in een proces dat *het bewijs van bezit*wordt genoemd. De combinatie van serverketenvalidatie en bewijs van bezit wordt *serververificatie*genoemd. Om een servercertificaatketen te valideren, heeft een client een kopie nodig van het basis-CA-certificaat dat is gebruikt om het certificaat van de server te maken (of uit te geven). Normaal gesproken wordt een browser bij het maken van verbinding met websites vooraf geconfigureerd met veelgebruikte CA-certificaten, zodat de client een naadloos proces heeft.

Wanneer een apparaat verbinding maakt met Azure IoT Hub, is het apparaat de client en is de IoT Hub-cloudservice de server. De IoT Hub cloud service wordt ondersteund door een root CA certificaat genaamd **Baltimore CyberTrust Root**, die openbaar beschikbaar is en op grote schaal gebruikt. Aangezien het IoT Hub CA-certificaat al op de meeste apparaten is geïnstalleerd, worden veel TLS-implementaties (OpenSSL, Schannel, LibreSSL) automatisch gebruikt tijdens de validatie van servercertificaten. Een apparaat dat mogelijk verbinding maakt met IoT Hub, kan problemen hebben met het proberen verbinding te maken met een IoT Edge-gateway.

Wanneer een apparaat verbinding maakt met een IoT Edge-gateway, is het downstream-apparaat de client en is het gateway-apparaat de server. Azure IoT Edge stelt operators (of gebruikers) in staat om gatewaycertificaatketens te bouwen zoals ze dat nodig achten. De operator kan ervoor kiezen om een openbaar CA-certificaat te gebruiken, zoals Baltimore, of een zelfondertekend (of in-house) root CA-certificaat te gebruiken. Openbare CA-certificaten hebben vaak een kosten die eraan verbonden zijn, dus worden meestal gebruikt in productiescenario's. Zelfondertekende CA-certificaten hebben de voorkeur voor ontwikkeling en testen. De transparante gateway-installatieartikelen die in de inleiding worden vermeld, gebruiken zelfondertekende basis-CA-certificaten.

Wanneer u een zelfondertekend root CA-certificaat gebruikt voor een IoT Edge-gateway, moet het worden geïnstalleerd op of worden geleverd aan alle downstream-apparaten die verbinding proberen te maken met de gateway.

![Gatewaycertificaat-instelling](./media/how-to-create-transparent-gateway/gateway-setup.png)

Zie [De gebruiksgegevens van IoT Edge-certificaat voor](iot-edge-certs.md)meer informatie over IoT Edge-certificaten en enkele productieimplicaties.

## <a name="provide-the-root-ca-certificate"></a>Het basis-CA-certificaat opgeven

Om de certificaten van het gatewayapparaat te verifiëren, heeft het downstream-apparaat een eigen kopie van het basis-CA-certificaat nodig. Als u de scripts in de IoT Edge git-repository hebt gebruikt om testcertificaten te maken, wordt het root **CA-certificaat azure-iot-test-only.root.ca.cert.pem**genoemd. Als u dit certificaatbestand nog niet hebt gebruikt als onderdeel van de andere downstream-apparaatvoorbereidingsstappen, verplaatst u dit certificaatbestand naar een map op uw downstream-apparaat. U een service zoals [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) of een functie zoals Secure copy [protocol](https://www.ssh.com/ssh/scp/) gebruiken om het certificaatbestand te verplaatsen.

## <a name="install-certificates-in-the-os"></a>Certificaten installeren in het besturingssysteem

Als u het basis-CA-certificaat installeert in het certificaatarchief van het besturingssysteem, kunnen de meeste toepassingen over het algemeen het basis-CA-certificaat gebruiken. Er zijn enkele uitzonderingen, zoals NodeJS-toepassingen die het OS-certificaatarchief niet gebruiken, maar het interne certificaatarchief van Node runtime gebruiken. Als u het certificaat niet op het niveau van het besturingssysteem installeren, gaat u verder [met Certificaten gebruiken met Azure IoT SDKs.](#use-certificates-with-azure-iot-sdks)

### <a name="ubuntu"></a>Ubuntu

De volgende opdrachten zijn een voorbeeld van het installeren van een CA-certificaat op een Ubuntu-host. In dit voorbeeld wordt ervan uitgegaan dat u het **azure-iot-test-only.root.ca.cert.pem-certificaat** gebruikt van de artikelen met voorwaarden en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

U ziet een bericht met de tekst: "Certificaten bijwerken in /etc/ssl/certs... 1 toegevoegd, 0 verwijderd; gedaan."

### <a name="windows"></a>Windows

De volgende stappen zijn een voorbeeld van het installeren van een CA-certificaat op een Windows-host. In dit voorbeeld wordt ervan uitgegaan dat u het **azure-iot-test-only.root.ca.cert.pem-certificaat** gebruikt van de artikelen met voorwaarden en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

U certificaten installeren met het [importcertificaat van](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) PowerShell als beheerder:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

U certificaten ook installeren met behulp van het **certlm-hulpprogramma:**

1. Zoek in het menu Start naar en selecteer **Computercertificaten beheren**. Een hulpprogramma genaamd **certlm** opent.
2. Navigeer naar **certificaten - Lokale computer** > **vertrouwde rootcertificeringsinstanties**.
3. Klik met de rechtermuisknop op **Certificaten** en selecteer **Alle taken** > **importeren**. De wizard Certificaatimport moet worden gestart.
4. Volg de stappen zoals aangegeven `<path>/azure-iot-test-only.root.ca.cert.pem`en het certificaatbestand voor importeren . Wanneer u klaar bent, ziet u een bericht 'Met succes geïmporteerd'.

U certificaten ook programmatisch installeren met .NET API's, zoals later in dit artikel in het .NET-voorbeeld wordt weergegeven.

Meestal toepassingen gebruik maken van de Windows verstrekte TLS stack genaamd [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) om veilig verbinding te maken via TLS. Schannel *vereist* dat certificaten worden geïnstalleerd in het Windows-certificaatarchief voordat u een TLS-verbinding probeert op te zetten.

## <a name="use-certificates-with-azure-iot-sdks"></a>Certificaten gebruiken met Azure IoT SDKs

In deze sectie wordt beschreven hoe de Azure IoT SDK's verbinding maken met een IoT Edge-apparaat met behulp van eenvoudige voorbeeldtoepassingen. Het doel van alle voorbeelden is om de apparaatclient te verbinden en telemetrieberichten naar de gateway te verzenden en vervolgens de verbinding en afsluiting te sluiten.

Heb twee dingen klaar voordat u de monsters op toepassingsniveau gebruikt:

* De IoT Hub-verbindingstekenreeks van uw downstream-apparaat is aangepast om naar het gateway-apparaat te wijzen en alle certificaten die nodig zijn om uw downstream-apparaat te verifiëren naar IoT Hub. Zie [Een downstream-apparaat verifiëren naar Azure IoT Hub](how-to-authenticate-downstream-device.md)voor meer informatie.

* Het volledige pad naar het basis-CA-certificaat dat u ergens op uw downstream-apparaat hebt gekopieerd en opgeslagen.

    Bijvoorbeeld `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>Node.js

In deze sectie wordt een voorbeeldtoepassing opgenomen om een Azure IoT NodeJS-apparaatclient te verbinden met een IoT Edge-gateway. Voor NodeJS-toepassingen moet u het basis-CA-certificaat installeren op toepassingsniveau zoals hier wordt weergegeven. NodeJS-toepassingen maken geen gebruik van het certificaatarchief van het systeem.

1. Download het voorbeeld voor **edge_downstream_device.js** van de [Azure IoT-apparaat SDK voor Node.js-voorbeelden repo](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Zorg ervoor dat u over alle vereisten beschikt om het voorbeeld uit te voeren door het **readme.md-bestand** te bekijken.
3. Werk in het bestand edge_downstream_device.js de **variabelen connectionString** en **edge_ca_cert_path** bij.
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

Als u inzicht wilt krijgen in het voorbeeld dat u uitvoert, leest de client SDK het certificaatbestand en gebruikt u het om een beveiligde TLS-verbinding tot stand te brengen:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

In deze sectie wordt een voorbeeldtoepassing geïntroduceerd om een Azure IoT .NET-apparaatclient te verbinden met een IoT Edge-gateway. .NET-toepassingen kunnen echter automatisch geïnstalleerde certificaten gebruiken in de certificaatopslag van het systeem op zowel Linux- als Windows-hosts.

1. Download het voorbeeld voor **EdgeDownstreamDevice** in de [map IoT Edge .NET-voorbeelden](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Zorg ervoor dat u over alle vereisten beschikt om het voorbeeld uit te voeren door het **readme.md-bestand** te bekijken.
3. Werk in het bestand **Properties / launchSettings.json** de **DEVICE_CONNECTION_STRING** en **CA_CERTIFICATE_PATH** variabelen bij. Als u het certificaat wilt gebruiken dat is geïnstalleerd in het vertrouwde certificaatarchief op het hostsysteem, laat u deze variabele leeg.
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

Als u een vertrouwd certificaat programmatisch wilt installeren in het certificaatarchief via een .NET-toepassing, raadpleegt u de functie **InstallCACert()** in het bestand **EdgeDownstreamDevice / Program.cs.** Deze bewerking is idempotent, dus kan meerdere keren worden uitgevoerd met dezelfde waarden zonder extra effect.

### <a name="c"></a>C

In deze sectie wordt een voorbeeldtoepassing geïntroduceerd om een Azure IoT C-apparaatclient te verbinden met een IoT Edge-gateway. De C SDK kan werken met veel TLS-bibliotheken, waaronder OpenSSL, WolfSSL en Schannel. Zie de Azure [IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)voor meer informatie.

1. Download de **iotedge_downstream_device_sample** toepassing van de [SDK voor Azure IoT-apparaat voor C-voorbeelden.](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
2. Zorg ervoor dat u over alle vereisten beschikt om het voorbeeld uit te voeren door het **readme.md-bestand** te bekijken.
3. Werk in het iotedge_downstream_device_sample.c-bestand de **verbindingsreeks** en **edge_ca_cert_path** variabelen bij.
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

De Azure IoT-apparaat SDK voor C biedt een optie om een CA-certificaat te registreren bij het instellen van de client. Deze bewerking installeert het certificaat nergens, maar gebruikt een tekenreeksindeling van het certificaat in het geheugen. Het opgeslagen certificaat wordt geleverd aan de onderliggende TLS-stack bij het tot stand brengen van een verbinding.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Als u op Windows-hosts geen OpenSSL of een andere TLS-bibliotheek gebruikt, wordt de SDK standaard gebruikt voor het gebruik van Schannel. Als Schannel werkt, moet het IoT Edge root CA-certificaat worden geïnstalleerd `IoTHubDeviceClient_SetOption` in het Windows-certificaatarchief en niet worden ingesteld met de bewerking.

### <a name="java"></a>Java

In deze sectie wordt een voorbeeldtoepassing geïntroduceerd om een Azure IoT Java-apparaatclient te verbinden met een IoT Edge-gateway.

1. Download het voorbeeld voor **Send-event** van de [Azure IoT-apparaat SDK voor Java-voorbeelden.](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
2. Zorg ervoor dat u over alle vereisten beschikt om het voorbeeld uit te voeren door het **readme.md-bestand** te bekijken.
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

### <a name="python"></a>Python

In deze sectie wordt een voorbeeldtoepassing geïntroduceerd om een Azure IoT Python-apparaatclient te verbinden met een IoT Edge-gateway.

1. Download het voorbeeld voor **send_message** van de [Azure IoT-apparaat SDK voor Python-voorbeelden.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)
2. Zorg ervoor dat u in een IoT Edge-container of in `EdgeHubConnectionString` `EdgeModuleCACertificateFile` een foutopsporingsscenario de variabelen en omgevings hebt ingesteld.
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voorbeeld op uw apparaat.

## <a name="test-the-gateway-connection"></a>De gatewayverbinding testen

Gebruik deze voorbeeldopdracht om te testen of uw downstream-apparaat verbinding kan maken met het gatewayapparaat:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Deze opdracht test verbindingen via MQTTS (poort 8883). Als u een ander protocol gebruikt, past u de opdracht zo nodig aan voor AMQPS (5671) of HTTPS (433)

De uitvoer van deze opdracht kan lang zijn, inclusief informatie over alle certificaten in de keten. Als uw verbinding succesvol is, ziet `Verification: OK` u `Verify return code: 0 (ok)`een regel als of .

![Gatewayverbinding verifiëren](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Problemen met de gatewayverbinding oplossen

Als uw leaf-apparaat een onderbroken verbinding heeft met het gateway-apparaat, probeert u de volgende stappen voor de oplossing.

1. Is de statusnaam van de gateway in de verbindingstekenreeks hetzelfde als de hostnamewaarde in het IoT Edge-config.yaml-bestand op het gatewayapparaat?
2. Is de gatewayhostnaam reoplosbaar naar een IP-adres? U intermitterende verbindingen oplossen door DNS te gebruiken of door een hostbestandsvermelding toe te voegen op het bladapparaat.
3. Zijn communicatiepoorten open in uw firewall? Communicatie op basis van het gebruikte protocol (MQTTS:8883/AMQPS:5671/HTTPS:433) moet mogelijk zijn tussen het downstream-apparaat en de transparante IoT Edge.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe IoT Edge [offline-mogelijkheden](offline-capabilities.md) kan uitbreiden naar downstream-apparaten.
