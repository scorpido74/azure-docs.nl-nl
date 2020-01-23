---
title: Certificaten voor apparaatbeveiliging - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge maakt gebruik van certificaten om te valideren van apparaten, modules en leaf-apparaten en beveiligde verbindingen tussen beide tot stand brengen.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a222f72e705184c5a7ba6701cfda41073c7eba57
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548744"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Meer informatie over het gebruik van Azure IoT Edge certificaten

IoT Edge certificaten worden gebruikt door de modules en downstream IoT-apparaten om de identiteit en rechtmatigheid van de module [IOT Edge hub](iot-edge-runtime.md#iot-edge-hub) runtime te controleren. Deze verificatie inschakelen voor een beveiligde verbinding van TLS (transport layer security) tussen de runtime, de modules en de IoT-apparaten. Zoals IoT-Hub zelf, IoT Edge vereist een veilige en versleutelde verbinding van IoT downstream (bladeren) apparaten en IoT Edge-modules. Om een beveiligde TLS-verbinding tot stand te brengen, presenteert de IoT Edge hub-module een server certificaat keten om clients te verbinden, zodat ze hun identiteit kunnen verifiëren.

In dit artikel wordt uitgelegd hoe IoT Edge certificaten kunnen werken in productie-, ontwikkelings-en test scenario's. De scripts zijn verschillende (Powershell versus bash), zijn de concepten hetzelfde tussen Linux en Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certificaten

Meestal zijn fabrikanten niet de eind gebruikers van een IoT Edge apparaat. Soms is de enige relatie tussen de twee, wanneer de eind gebruiker of de operator, een algemeen apparaat koopt dat door de fabrikant is gemaakt. Andere keren werkt de fabrikant onder contract om een aangepast apparaat voor de operator te bouwen. Het ontwerp van de IoT Edge-certificaat wil rekening gehouden met beide scenario's.

De volgende afbeelding ziet u IoT-Edge-gebruik van certificaten. Er kunnen zich nul, één of veel tussenliggende handtekening certificaten bevinden tussen het basis-CA-certificaat en het CA-certificaat van het apparaat, afhankelijk van het aantal betrokken entiteiten. Hier zien we één geval.

![Diagram van typische certificaat relaties](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certificeringsinstantie

De certificeringsinstantie of 'CA' genoemd, is een entiteit die digitale certificaten uitgeeft. Een certificeringsinstantie fungeert als een vertrouwde derde partij tussen de eigenaar en de ontvanger van het certificaat. Een digitaal certificaat verklaart de eigendom van een openbare sleutel door de ontvanger van het certificaat. De certificaatketen van de vertrouwensrelatie werkt met behulp van een basiscertificaat, de basis voor vertrouwen in alle certificaten die door de instantie vormt in eerste instantie. De eigenaar van de kunt daarna het basiscertificaat gebruiken om aanvullende ('leaf-certificaten) in de tussenliggende certificaten te verlenen.

### <a name="root-ca-certificate"></a>Basis-CA-certificaat

Een basis-CA-certificaat is de hoofdmap van de vertrouwensrelatie van het gehele proces. In productie scenario's wordt dit CA-certificaat meestal gekocht van een vertrouwde commerciële certificerings instantie, zoals Baltimore, VeriSign of DigiCert. Als u volledige controle hebt over de apparaten die verbinding maken met uw IoT Edge apparaten, is het mogelijk om een certificerings instantie op bedrijfs niveau te gebruiken. In beide gevallen wordt de volledige certificaat keten van de IoT Edge-hub omhoog Gerolt, zodat de Blade IoT-apparaten het basis certificaat moeten vertrouwen. U kunt het basis-CA-certificaat opslaan in het archief van de vertrouwde basis certificerings instantie of de certificaat gegevens opgeven in de code van uw toepassing.

### <a name="intermediate-certificates"></a>Tussenliggende certificaten

In een typische fabricageproces voor het maken van beveiligde apparaten, worden zelden rechtstreeks, basis-CA-certificaten gebruikt voornamelijk vanwege het risico van gegevenslekken en blootstelling. Met het basis-CA-certificaat worden een of meer tussenliggende CA-certificaten gemaakt en ondertekend. Er kan alleen worden een of er mogelijk een keten van deze tussenliggende certificaten. Scenario's waarin een keten van tussenliggende certificaten zijn onder andere:

* Een hiërarchie van afdelingen binnen een fabrikant.

* Meerdere bedrijven die betrokken zijn bij de productie van een apparaat serie.

* Een klant aanschaf van een basis-CA en die is afgeleid van een handtekeningcertificaat van de fabrikant voor het ondertekenen van de apparaten die ze maken namens de klant.

De fabrikant gebruikt in elk geval een tussenliggende CA-certificaat aan het einde van deze keten ondertekenen van het CA-certificaat van het apparaat op het apparaat end geplaatst. Deze tussenliggende certificaten zijn over het algemeen goed beveiligde in de fabriek. Ze ondergaan strikt omschreven processen, zowel fysieke als elektronische voor hun gebruik.

### <a name="device-ca-certificate"></a>Device CA-certificaat

Het CA-certificaat van het apparaat is gegenereerd op basis van en ondertekend door het laatste tussenliggende CA-certificaat in het proces. Dit certificaat is geïnstalleerd op het IoT Edge apparaat zelf, bij voor keur in beveiligde opslag, zoals een Hardware Security module (HSM). Bovendien identificatie een certificaat van een apparaat unieke van een IoT Edge-apparaat. Het CA-certificaat van het apparaat kan andere certificaten ondertekenen.

### <a name="iot-edge-workload-ca"></a>IoT Edge-werkbelasting CA

De [IOT Edge Security Manager](iot-edge-security-manager.md) GENEREERT het CA-certificaat van de werk belasting, de eerste op de ' operator '-kant van het proces, wanneer IOT Edge voor het eerst wordt gestart. Dit certificaat wordt gegenereerd op basis van en ondertekend door het "device CA-certificaat". Dit certificaat, dat gewoon een tussenliggende handtekeningcertificaat is, wordt gebruikt om te genereren en meld u aan alle andere certificaten die worden gebruikt door de IoT Edge-runtime. En dat is het hoofd zakelijk het IoT Edge hub-server certificaat dat in de volgende sectie wordt besproken, maar in de toekomst kunnen ook andere certificaten voor het verifiëren van IoT Edge-onderdelen bevatten.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub-server certificaat

Het IoT Edge hub-server certificaat is het daad werkelijke certificaat dat wordt aangeboden aan Leaf-apparaten en-modules voor identiteits verificatie tijdens het instellen van de TLS-verbinding die is vereist voor IoT Edge. Dit certificaat geeft de volledige keten van het ondertekenen van certificaten gebruikt voor het genereren ervan tot aan de basis-CA-certificaat de leaf-IoT-apparaat moet vertrouwen. Wanneer het is gegenereerd door IoT Edge Security Manager, wordt de algemene naam (CN) van dit IoT Edge hub-certificaat ingesteld op de eigenschap hostname in het bestand config. yaml na conversie naar kleine letters. Deze configuratie is een veelvoorkomende bron van Verwar ring met IoT Edge.

## <a name="production-implications"></a>Gevolgen voor productie

Een redelijke vraag mogelijk "Waarom IoT Edge hoeft de 'werkbelasting CA' extra certificaat? Kan het CA-certificaat van het apparaat niet gebruiken om het IoT Edge hub-server certificaat direct te genereren? '. In technisch opzicht kunnen kan deze. Het doel van deze "werkbelasting" tussenliggende certificaat is echter voor het scheiden van opmerkingen tussen de fabrikant van het apparaat en de operator apparaat. Stel een scenario waarbij een IoT Edge-apparaat wordt verkocht of naar een andere wordt overgenomen van een klant. U wilt waarschijnlijk het CA-certificaat van het apparaat geleverd door de fabrikant zijn onveranderd. De 'werkbelasting' certificaten die specifiek zijn voor de werking van het apparaat moeten echter worden gewist en opnieuw gemaakt voor de nieuwe implementatie.

Omdat productie-en bewerkings processen worden gescheiden, moet u rekening houden met de volgende implicaties bij het voorbereiden van productie-apparaten:

* Met een proces op basis van certificaten, moeten het basis-CA-certificaat en alle tussenliggende CA-certificaten worden beveiligd en gecontroleerd tijdens het hele proces van het implementeren van een IoT Edge-apparaat. De fabrikant van het IoT Edge-apparaat hebt strenge procedures zijn voor de juiste opslag en het gebruik van de tussenliggende certificaten. Bovendien moet de CA-certificaat van het apparaat worden opgeslagen in als beveiligde opslag mogelijk op het apparaat zelf, bij voorkeur een hardware security module.

* Het IoT Edge hub-server certificaat wordt door IoT Edge hub gepresenteerd aan de client apparaten en-modules die verbinding maken. De algemene naam (CN) van het CA-certificaat van het apparaat **mag niet hetzelfde zijn** als de hostnaam die wordt gebruikt in config. yaml op het IOT edge-apparaat. De naam die door clients wordt gebruikt om verbinding te maken met IoT Edge (bijvoorbeeld via de para meter GatewayHostName van de connection string of de opdracht CONNECT in MQTT), **mag niet hetzelfde zijn** als de algemene naam die wordt gebruikt in het certificaat van de certificerings instantie. Deze beperking is omdat de IoT Edge hub de volledige certificaat keten geeft voor verificatie door clients. Als het IoT Edge hub-server certificaat en het CA-certificaat van het apparaat dezelfde CN hebben, krijgt u een verificatie-lus en wordt het certificaat ongeldig.

* Omdat het certificaat van de certificerings instantie van het apparaat wordt gebruikt door de IoT Edge Security daemon voor het genereren van de definitieve IoT Edge certificaten, moet het een handtekening certificaat zijn, wat betekent dat het een certificaat heeft voor de ondertekening van certificaten. Als ' v3 Basic-beperkingen CA: True ' wordt toegepast op het apparaat-CA-certificaat, worden de vereiste eigenschappen voor sleutel gebruik automatisch ingesteld.

>[!Tip]
> Als u hebt al doorlopen de installatie van IoT Edge als een transparante gateway in een scenario voor ontwikkelen en testen met behulp van onze "scripts voor uw gemak bedoeld;" (Zie volgende sectie) en dezelfde hostnaam gebruikt bij het maken van het CA-apparaatcertificaat als u dit hebt gedaan voor de hostnaam in config.yaml , u mogelijk vraagt zich misschien af waarom het is gegaan. In een poging voor het vereenvoudigen van de ervaring voor ontwikkelaars, voegt de scripts gemak ".ca' aan het einde van de naam die u in het script doorgeeft. Dus, bijvoorbeeld, als u "mygateway" voor zowel de apparaatnaam in de scripts en hostname in config.yaml gebruikt, de voormalige wordt omgezet in mygateway.ca voordat het wordt gebruikt als de algemene naam voor het apparaat CA-certificaat.

## <a name="devtest-implications"></a>Gevolgen voor ontwikkelen en testen

Voor het vereenvoudigen van de ontwikkeling en testen van scenario's, biedt Microsoft een set [gemak scripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) voor het genereren van niet-productie-certificaten die geschikt zijn voor IoT Edge in het scenario voor transparante gateway. Zie [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md)voor voor beelden van de werking van de scripts.

>[!Tip]
> Als u wilt verbinding maken met uw apparaat IoT "leaf" apparaten en toepassingen die gebruikmaken van onze IoT-device-SDK via IoT Edge, moet u de optionele parameter GatewayHostName u aan bij het einde van de verbindingsreeks van het apparaat toevoegen. Wanneer het certificaat van de Edge Hub wordt gegenereerd, is gebaseerd op een indeling met een lagere versie van de hostnaam van config.yaml, daarom de namen van de overeenkomst en de verificatie van TLS-certificaat te voltooien, moet u de parameter GatewayHostName in kleine letters.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Voorbeeld van een certificaathiërarchie IoT Edge

Ter illustratie van een voorbeeld van dit pad naar het certificaat, is de volgende schermafbeelding van een werkende IoT Edge-apparaat instellen als een transparante gateway. OpenSSL wordt gebruikt om verbinding te maken met de IoT Edge hub, de certificaten te valideren en te dumpen.

![Schermafbeelding van de certificaathiërarchie op elk niveau](./media/iot-edge-certs/iotedge-cert-chain.png)

Hier ziet u de hiërarchie van certificaat diepte weergegeven in de schermafbeelding:

| Basis-CA-certificaat         | Azure IoT Hub-CA-certificaat Test alleen                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Tussenliggende CA-certificaat | Azure IoT Hub tussenliggende certificaat alleen testen                                                                 |
| Device CA-certificaat       | iotgateway.CA ("iotgateway" is doorgegeven als de naam < gateway > aan de scripts voor uw gemak bedoeld;)   |
| Workload CA-certificaat     | iotedge werkbelasting ca                                                                                       |
| IoT Edge hub-server certificaat | iotedgegw.local (overeenkomt met de 'hostnaam' van config.yaml)                                            |

## <a name="next-steps"></a>Volgende stappen

[Informatie over Azure IoT Edge-modules](iot-edge-modules.md)

[Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
