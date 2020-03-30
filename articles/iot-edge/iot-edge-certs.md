---
title: Certificaten voor apparaatbeveiliging - Azure IoT Edge | Microsoft Documenten
description: Azure IoT Edge gebruikt certificaat om apparaten, modules en bladapparaten te valideren en veilige verbindingen tussen deze apparaten tot stand te brengen.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58294c7afdf31ddd29611351d6442db1c4966157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269037"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Begrijpen hoe Azure IoT Edge certificaten gebruikt

IoT Edge-certificaten worden gebruikt door de modules en downstream IoT-apparaten om de identiteit en legitimiteit van de [IoT Edge-hubruntime-module](iot-edge-runtime.md#iot-edge-hub) te verifiëren. Met deze verificaties u een veilige TLS-verbinding (transportlaagbeveiliging) tussen de runtime, de modules en de IoT-apparaten maken. Net als IoT Hub zelf vereist IoT Edge een veilige en versleutelde verbinding van IoT downstream (of leaf) apparaten en IoT Edge-modules. Om een veilige TLS-verbinding tot stand te brengen, presenteert de IoT Edge-hubmodule een servercertificaatketen aan het verbinden van clients, zodat ze de identiteit kunnen verifiëren.

In dit artikel wordt uitgelegd hoe IoT Edge-certificaten kunnen werken in productie-, ontwikkelings- en testscenario's. Terwijl de scripts zijn verschillend (Powershell vs bash), de concepten zijn hetzelfde tussen Linux en Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certificaten

Meestal zijn fabrikanten niet de eindgebruikers van een IoT Edge-apparaat. Soms is de enige relatie tussen de twee is wanneer de eindgebruiker, of exploitant, koopt een generiek apparaat gemaakt door de fabrikant. Andere keren werkt de fabrikant onder contract om een aangepast apparaat voor de operator te bouwen. Het ontwerp van het IoT Edge-certificaat probeert rekening te houden met beide scenario's.

De volgende afbeelding illustreert het gebruik van certificaten door IoT Edge. Er kunnen nul-, één- of veel tussentijdse ondertekeningscertificaten zijn tussen het basis-CA-certificaat en het CA-certificaat van het apparaat, afhankelijk van het aantal betrokken entiteiten. Hier laten we één zaak zien.

![Diagram met typische certificaatrelaties](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certificeringsinstantie

De certificeringsinstantie, kortweg 'CA', is een entiteit die digitale certificaten uitgeeft. Een certificaatautoriteit treedt op als een vertrouwde derde partij tussen de eigenaar en de ontvanger van het certificaat. Een digitaal certificaat certificeert het eigendom van een openbare sleutel door de ontvanger van het certificaat. De certificaatketen van vertrouwen werkt door in eerste instantie een rootcertificaat uit te geven, dat de basis vormt voor vertrouwen in alle certificaten die door de autoriteit zijn afgegeven. Daarna kan de eigenaar het rootcertificaat gebruiken om extra tussencertificaten ('leaf'-certificaten) uit te geven.

### <a name="root-ca-certificate"></a>Root CA-certificaat

Een root CA-certificaat is de basis van het vertrouwen van het hele proces. In productiescenario's wordt dit CA-certificaat meestal gekocht bij een vertrouwde commerciële certificaatinstantie zoals Baltimore, Verisign of DigiCert. Als u volledige controle hebt over de apparaten die verbinding maken met uw IoT Edge-apparaten, is het mogelijk om een certificaatautoriteit op bedrijfsniveau te gebruiken. In beide gevallen rolt de hele certificaatketen van de IoT Edge-hub omhoog, zodat de loden IoT-apparaten op het basiscertificaat moeten vertrouwen. U het basis-CA-certificaat opslaan in het vertrouwde certificaatautoriteitarchief of de certificaatgegevens in uw toepassingscode opgeven.

### <a name="intermediate-certificates"></a>Tussencertificaten

In een typisch productieproces voor het maken van beveiligde apparaten worden root CA-certificaten zelden direct gebruikt, voornamelijk vanwege het risico op lekkage of blootstelling. Het basis-CA-certificaat maakt en ondertekent digitaal een of meer tussenliggende CA-certificaten. Er kan slechts één zijn, of er kan een keten van deze tussencertificaten zijn. Scenario's waarvoor een keten van tussentijdse certificaten vereist zijn:

* Een hiërarchie van afdelingen binnen een fabrikant.

* Meerdere bedrijven betrokken serieel in de productie van een apparaat.

* Een klant die een root-CA koopt en een ondertekeningscertificaat afgeeft voor de fabrikant om de apparaten te ondertekenen die hij namens die klant maakt.

In ieder geval gebruikt de fabrikant een tussenliggend CA-certificaat aan het einde van deze keten om het CA-certificaat van het apparaat te ondertekenen dat op het eindapparaat is geplaatst. Over het algemeen worden deze tussentijdse certificaten streng bewaakt in de fabriek. Ze ondergaan strikte processen, zowel fysiek als elektronisch voor hun gebruik.

### <a name="device-ca-certificate"></a>Apparaat CA-certificaat

Het CA-certificaat van het apparaat wordt gegenereerd uit en ondertekend door het uiteindelijke tussenliggende CA-certificaat in het proces. Dit certificaat is geïnstalleerd op het IoT Edge-apparaat zelf, bij voorkeur in beveiligde opslag, zoals een hardwarebeveiligingsmodule (HSM). Bovendien identificeert een CA-certificaat van een apparaat op unieke wijze een IoT Edge-apparaat. Het CA-certificaat van het apparaat kan andere certificaten ondertekenen.

### <a name="iot-edge-workload-ca"></a>CA-workload IoT Edge

De [IoT Edge Security Manager](iot-edge-security-manager.md) genereert het CA-workloadcertificaat, het eerste aan de operatorzijde van het proces, wanneer IoT Edge voor het eerst wordt gestart. Dit certificaat wordt gegenereerd uit en ondertekend door het "device CA-certificaat". Dit certificaat, dat slechts een tussenliggend ondertekeningscertificaat is, wordt gebruikt voor het genereren en ondertekenen van andere certificaten die worden gebruikt door de Runtime van IoT Edge. Vandaag de dag is dat in de eerste plaats het IoT Edge-hubservercertificaat dat in de volgende sectie wordt besproken, maar in de toekomst kunnen andere certificaten bevatten voor het verifiëren van IoT Edge-componenten.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hubservercertificaat

Het IoT Edge hub-servercertificaat is het eigenlijke certificaat dat wordt gepresenteerd aan bladapparaten en modules voor identiteitsverificatie tijdens het opzetten van de TLS-verbinding die vereist is door IoT Edge. Dit certificaat bevat de volledige keten van ondertekeningscertificaten die worden gebruikt om deze te genereren tot het basis-CA-certificaat, waarop het blad IoT-apparaat moet vertrouwen. Wanneer dit IoT Edge Security Manager wordt gegenereerd door De IoT Edge Security Manager, wordt de algemene naam (CN) van dit IoT Edge-hubcertificaat ingesteld op de eigenschap 'hostname' in het bestand config.yaml na conversie naar kleine letters. Deze configuratie is een veel voorkomende bron van verwarring met IoT Edge.

## <a name="production-implications"></a>Gevolgen voor de productie

Een redelijke vraag zou kunnen zijn "waarom heeft IoT Edge het extra certificaat 'workload CA' nodig? Kan het niet het CA-certificaat van het apparaat gebruiken om het IoT Edge-hubservercertificaat rechtstreeks te genereren?". Technisch gezien zou het kunnen. Het doel van dit tussentijdse certificaat "workload" is echter om de zorgen tussen de fabrikant van het apparaat en de apparaatoperator te scheiden. Stelt u zich een scenario voor waarin een IoT Edge-apparaat wordt verkocht of overgedragen van de ene klant naar de andere. U wilt waarschijnlijk dat het CA-certificaat van het apparaat dat door de fabrikant wordt verstrekt, onveranderlijk is. De 'workload'-certificaten die specifiek zijn voor de werking van het apparaat moeten echter worden gewist en opnieuw worden gemaakt voor de nieuwe implementatie.

Houd rekening met de volgende implicaties bij het voorbereiden van productieapparatuur omdat productie- en bewerkingsprocessen gescheiden zijn:

* Bij elk certificaatgebaseerd proces moeten het basis-CA-certificaat en alle tussentijdse CA-certificaten worden beveiligd en gecontroleerd tijdens het hele proces van het uitrollen van een IoT Edge-apparaat. De iot edge-apparaatfabrikant moet sterke processen hebben voor een goede opslag en het gebruik van hun tussenliggende certificaten. Bovendien moet het CA-certificaat van het apparaat zo veilig mogelijk op het apparaat zelf worden bewaard, bij voorkeur een hardwarebeveiligingsmodule.

* Het IoT Edge hub-servercertificaat wordt door de IoT Edge-hub gepresenteerd aan de verbindingsclientapparaten en -modules. De algemene naam (CN) van het CA-certificaat van het apparaat **mag niet** dezelfde zijn als de hostnaam die wordt gebruikt in config.yaml op het IoT Edge-apparaat. De naam die door clients wordt gebruikt om verbinding te maken met IoT Edge (bijvoorbeeld via de parameter GatewayHostName van de verbindingstekenreeks of de opdracht CONNECT in MQTT) **kan niet** dezelfde zijn als de algemene naam die wordt gebruikt in het CA-certificaat van het apparaat. Deze beperking is omdat de IoT Edge-hub de volledige certificaatketen presenteert voor verificatie door clients. Als het IoT Edge-hubservercertificaat en het CA-certificaat van het apparaat beide dezelfde CN hebben, krijgt u een verificatielus en wordt het certificaat ongeldig verklaard.

* Omdat het CA-certificaat van het apparaat wordt gebruikt door de IoT Edge-beveiligingsdaemon om de uiteindelijke IoT Edge-certificaten te genereren, moet het zelf een ondertekeningscertificaat zijn, wat betekent dat het certificaatondertekeningsmogelijkheden heeft. Als u 'V3 Basic-beperkingen CA:True' toepast op het CA-certificaat van het apparaat, worden automatisch de vereiste eigenschappen voor belangrijkgebruik ingesteld.

>[!Tip]
> Als u al de installatie van IoT Edge hebt doorlopen als een transparante gateway in een dev/testscenario met behulp van onze "gemaksscripts" (zie volgende sectie) en dezelfde hostnaam hebt gebruikt bij het maken van het CA-certificaat van het apparaat als voor de hostnaam in config.yaml, vraagt u zich misschien af waarom het werkte. In een poging om de ontwikkelaarservaring te vereenvoudigen, voegt de gemaksscripts een ".ca" toe aan het einde van de naam die u in het script doorgeeft. Dus, bijvoorbeeld, als je gebruikt "mygateway" voor zowel uw apparaat naam in de scripts en hostname in config.yaml, de eerste zal worden omgezet in mygateway.ca voordat ze worden gebruikt als de CN voor het apparaat CA cert.

## <a name="devtest-implications"></a>Dev/Test implicaties

Om de ontwikkeling en testscenario's te vergemakkelijken, biedt Microsoft een reeks [handige scripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) voor het genereren van niet-productiecertificaten die geschikt zijn voor IoT Edge in het transparante gatewayscenario. Zie Democertificaten maken om de functies van [IoT Edge-apparaatfuncties te testen](how-to-create-test-certificates.md)voor voorbeelden van hoe de scripts werken.

>[!Tip]
> Als u uw apparaat IoT-"leaf"-apparaten en -toepassingen wilt aansluiten die onze SDK van het IoT-apparaat gebruiken via IoT Edge, moet u de optionele parameter GatewayHostName toevoegen aan het einde van de verbindingstekenreeks van het apparaat. Wanneer het Edge Hub Server-certificaat wordt gegenereerd, is het gebaseerd op een lagere letters versie van de hostname van config.yaml, daarom moet u de parameter GatewayHostName in kleine letters invoeren om de namen te matchen en de TLS-certificaatverificatie te laten slagen.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Voorbeeld van de hiërarchie van iotedge-certificaten

Om een voorbeeld van dit certificaatpad te illustreren, is de volgende schermafbeelding van een werkend IoT Edge-apparaat dat is ingesteld als een transparante gateway. OpenSSL wordt gebruikt om verbinding te maken met de IoT Edge-hub, de certificaten te valideren en te dumpen.

![Schermafbeelding van de certificaathiërarchie op elk niveau](./media/iot-edge-certs/iotedge-cert-chain.png)

U de hiërarchie van certificaatdiepte zien die in de schermafbeelding wordt weergegeven:

| Basis-CA-certificaat         | Alleen Azure IoT Hub CA-cert-test                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Intermediair CA-certificaat | Alleen Azure IoT Hub Intermediate Cert-test                                                                 |
| Apparaat CA-certificaat       | iotgateway.ca ("iotgateway" werd doorgegeven als de < gateway host naam > naar het gemak scripts)   |
| CA-certificaat voor werkbelasting     | iotedge workload ca                                                                                       |
| IoT Edge Hub Server-certificaat | iotedgegw.local (komt overeen met de 'hostname' van config.yaml)                                            |

## <a name="next-steps"></a>Volgende stappen

[Informatie over Azure IoT Edge-modules](iot-edge-modules.md)

[Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
