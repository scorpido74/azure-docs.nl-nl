---
title: Informatie over hoe de beveiligingsinstellingen van apparaten, software - Azure IoT Edge beschermt | Microsoft Docs
description: Beheert de koers beveiliging van IoT Edge-apparaat en de integriteit van beveiligingsservices.
services: iot-edge
keywords: beveiliging, veilige element, enclave, t, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f137070cb8a62f2c11f9e2688b5c7db47c1b866f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208211"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge-beveiligingsbeheer

De Azure IoT Edge security manager is de kern van een goed begrensde beveiliging voor het beveiligen van de IoT Edge-apparaat en alle bijbehorende onderdelen door om de hardware van de beveiligde silicon te abstraheren. Het is het brand punt voor beveiligings beveiliging en biedt technologie integratie punt voor Original Equipment Manufacturers (OEM).

![Azure IoT Edge-beveiligingsbeheer](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge-beveiligingsbeheer is erop gericht op het verdedigen van de integriteit van de IoT Edge-apparaat en alle verbonden software-bewerkingen. De Security Manager overgangen vertrouwt van de onderliggende hardware-basis van Trusted hardware (indien beschikbaar) om de IoT Edge runtime te Boots trapn en actieve bewerkingen te controleren.  De IoT Edge Security Manager is software die samen met Secure Silicon hardware (indien beschikbaar) werkt om de hoogst mogelijke beveiligings garanties te leveren.  

De verantwoordelijkheden van de IoT Edge Security Manager omvatten, maar zijn niet beperkt tot:

* Beveiligd en gemeten opstarten van de Azure IoT Edge-apparaat.
* Apparaatinrichting voor identiteit en de overgang van de vertrouwensrelatie indien van toepassing.
* Host en beveiligen van Apparaatonderdelen van cloud-services zoals Device Provisioning Service.
* Richt IoT Edge-modules veilig in met unieke identiteiten.
* Gatekeeper apparaat hardware vertrouwensbasis via notaris services.
* De integriteit van IoT Edge-bewerkingen tijdens runtime bewaken.

IoT Edge security manager bevat drie onderdelen:

* IoT Edge security daemon.
* Hardware security module platform abstraction Layer (PAL HSM).
* Optioneel maar ten zeerste aanbevolen hardware silicon hoofdmap van de vertrouwensrelatie of HSM.

## <a name="the-iot-edge-security-daemon"></a>De daemon van de IoT Edge-beveiliging

De IoT Edge Security daemon is verantwoordelijk voor de logische bewerkingen van IoT Edge Security Manager. Dit vertegenwoordigt een aanzienlijk deel van de vertrouwde computing basis van het IoT Edge-apparaat. 

### <a name="design-principles"></a>Ontwerpprincipes

De IoT Edge Security daemon volgt twee kern principes: maximaliseert de operationele integriteit en minimaliseert de grootten en het verloop.

#### <a name="maximize-operational-integrity"></a>Operationele integriteit maximaliseren

De IoT Edge Security daemon werkt met de hoogst mogelijke integriteit in de verdedigings functie van een bepaalde hoofdmap van Trust-hardware. Met de juiste integratie, de hoofdmap van de vertrouwensrelatie hardware meet en en gecontroleerd de daemon security statisch tijdens runtime bestand tegen knoeien.

Fysieke toegang is altijd een bedreiging voor IoT-apparaten. De hoofdmap van de vertrouwens relatie van de hardware speelt een belang rijke rol bij het beschermen van de integriteit van de IoT Edge Security daemon.  De hoofdmap van de vertrouwens relatie komt in twee varianten voor:

* beveiligde-elementen voor de beveiliging van gevoelige informatie, zoals geheimen en cryptografische sleutels.
* beveiligde enclaves voor de beveiliging van geheimen zoals sleutels en gevoelige werkbelastingen, zoals meten en facturering.

Er zijn twee soorten uitvoerings omgevingen voor het gebruik van de hoofdmap van vertrouwen:

* De standaard of uitgebreide uitvoerings omgeving (REE) die afhankelijk is van het gebruik van beveiligde elementen om gevoelige informatie te beveiligen.
* De Trusted Execution Environment (TEE) die afhankelijk is van het gebruik van een beveiligde enclave-technologie om gevoelige informatie te beschermen en bescherming te bieden voor de uitvoering van software.

Voor apparaten die beveiligde enclaves gebruiken als de hoofdmap van de vertrouwens relatie, moet gevoelige logica binnen IoT Edge beveiligings-daemon zich in de enclave bevindt.  Niet-gevoelige delen van de beveiligings-daemon kunnen zich buiten het TEE bevinden.  In elk geval moeten Original Design Manufacturers (ODM) en Original Equipment Manufacturers (OEM) vertrouwen uitbreiden van de HSM om de integriteit van de IoT Edge Security daemon bij het opstarten en runtime te meten en te verdedigen.

#### <a name="minimize-bloat-and-churn"></a>Gegevensophoping minimaliseren en verloop

Een ander kern principe voor de IoT Edge Security daemon is om het verloop te minimaliseren.  Voor het hoogste vertrouwens niveau kan de IoT Edge-beveiligings-daemon nauw keurig zijn gekoppeld aan de apparaatstatus van de hardware van vertrouwen en als systeem eigen code worden gebruikt.  Het is gebruikelijk voor deze typen realisaties om de daemon-software bij te werken via de beveiligde update paden van de hoofdmap van de vertrouwens relatie (in plaats van de door het besturings systeem meegeleverde update mechanismen), wat in sommige scenario's lastig kan zijn.  Hoewel het vernieuwen van de beveiliging wordt aanbevolen voor IoT-apparaten, kunnen er grote update vereisten of nettoladingen met veel updates worden uitgebreid op veel manieren.  Voorbeelden van updates voor een maximale beschikbaarheid operationele overslaan of hoofdmap van de hardware van de vertrouwensrelatie te beperkt tot het verwerken van grote update nettoladingen.  Daarom is het ontwerp van IoT Edge Security daemon beknopt om de footprint en het vertrouwen van het vertrouwde computer gebruik te houden en om de update vereisten te minimaliseren.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architectuur van IoT Edge security daemon

![Azure IoT Edge security daemon](media/edge-security-manager/iot-edge-security-daemon.png)

De IoT Edge Security daemon maakt gebruik van de beschik bare hardware-root van vertrouwens technologie voor beveiligings beveiliging.  Het biedt ook een gesplitste wereld wijde werking tussen een standaard/krachtige uitvoerings omgeving (REE) en een Trusted Execution Environment (t) wanneer de hardware-technologieën vertrouwde uitvoerings omgevingen bieden. Met roldefinities kunnen de belangrijkste onderdelen van IoT Edge de integriteit van het IoT Edge apparaat en de bijbehorende bewerkingen garanderen.

#### <a name="cloud-interface"></a>Cloudinterface

Met de Cloud interface kan de IoT Edge Security daemon toegang krijgen tot Cloud Services, zoals Cloud complimenten, zoals beveiligings vernieuwing.  Zo gebruikt de IoT Edge Security daemon momenteel deze interface om toegang te krijgen tot de Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) voor levenscyclus beheer van de apparaat-id.  

#### <a name="management-api"></a>Beheer-API

IoT Edge Security daemon biedt een beheer-API, die wordt aangeroepen door de IoT Edge agent bij het maken/starten/stoppen/verwijderen van een IoT Edge module. De Security daemon slaat registraties op voor alle actieve modules. Deze registraties toewijzen van een module identiteit aan enkele eigenschappen van de module. Enkele voorbeelden van deze eigenschappen zijn de proces-id (pid) van het proces dat wordt uitgevoerd in de container of de hash van inhoud van de docker-container.

Deze eigenschappen worden gebruikt door de API voor workload (hieronder beschreven) om te controleren of de aanroeper gemachtigd is om een actie uit te voeren.

De beheer-API is een geautoriseerde API die alleen kan worden aangeroepen vanuit de IoT Edge-agent.  Aangezien de IoT Edge security-daemon bootstrapt en de IoT Edge-agent wordt gestart, kunt dit een impliciete registratie maken voor de IoT Edge-agent, nadat deze is aangetoond dat de IoT Edge-agent niet is geknoeid met. Hetzelfde Attestation-proces dat door de workload API wordt gebruikt, beperkt ook de toegang tot de beheer-API tot alleen de IoT Edge-agent.

#### <a name="container-api"></a>Container API

De container-API communiceert met het container systeem dat in gebruik is voor module beheer, zoals Moby of docker.

#### <a name="workload-api"></a>Workload API

De workload API is toegankelijk voor alle modules. Het biedt bewijs van identiteit, hetzij als een met HSM geroot ondertekend token of een x509-certificaat, en de bijbehorende vertrouwens bundel aan een module. De vertrouwens bundel bevat CA-certificaten voor alle andere servers die door de modules moeten worden vertrouwd.

De IoT Edge Security daemon maakt gebruik van een Attestation-proces om deze API te beveiligen. Wanneer een module deze API aanroept, probeert de beveiligings-daemon een registratie voor de identiteit te vinden. Als dit lukt, worden de eigenschappen van de registratie voor het meten van de module. Als het resultaat van het meting proces overeenkomt met de registratie, wordt er een nieuw identiteits bewijs gegenereerd. De bijbehorende CA-certificaten (vertrouwens bundel) worden geretourneerd naar de module.  De module gebruikt dit certificaat te verbinden met IoT Hub, andere modules, of een server te starten. Wanneer het ondertekende token of certificaat bijna is verlopen, is het de verantwoordelijkheid van de module om een nieuw certificaat aan te vragen. 

### <a name="integration-and-maintenance"></a>Integratie en onderhoud

Microsoft onderhoudt de belangrijkste code voor de [IoT Edge security-daemon op GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installatie en updates

Installatie en updates van de IoT Edge Security daemon worden beheerd via de pakketbeheersysteem van het besturings systeem. IoT Edge apparaten met de hoofdmap van het vertrouwens niveau van de hardware moeten extra beveiliging bieden voor de integriteit van de daemon door de levens cyclus te beheren via de beheer systemen beveiligd opstarten en bijwerken. De makers van apparaten moeten deze mogelijkheden verkennen op basis van hun respectieve apparaatfuncties.

#### <a name="versioning"></a>Versiebeheer

IoT Edge-runtime worden bijgehouden en de versie van de IoT Edge security-daemon-rapporten. De versie wordt gerapporteerd als *runtime. platform. version* -kenmerk van de gerapporteerde eigenschap van de IOT Edge Agent module.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardware security module platform abstraction layer (HSM PAL)

De HSM-PAL isoleert alle hoofdmap van de vertrouwensrelatie hardware voor het isoleren van de ontwikkelaar of gebruiker van IoT Edge van de complexiteit.  Het bevat een combi natie van Application Programming Interface (API) en transdomein communicatie procedures, bijvoorbeeld communicatie tussen een standaard uitvoerings omgeving en een beveiligde enclave.  De daadwerkelijke implementatie van de HSM-PAL, is afhankelijk van de specifieke beveiligde hardware wordt gebruikt. Het bestaan hiervan maakt het gebruik van vrijwel elk veilig siliconen mogelijk.

## <a name="secure-silicon-root-of-trust-hardware"></a>Beveiligde silicon hoofdmap van de vertrouwensrelatie hardware

Beveiligde silicon is nodig om anker vertrouwen in de hardware van de IoT Edge-apparaten.  Beveiligde silicon worden geleverd in verschillende om op te nemen Trusted Platform Module (TPM), ingesloten beveiligde Element (eSE), ARM TrustZone, Intel SGX en aangepaste beveiligde silicon technologieën.  Het gebruik van Secure Silicon root of Trust in apparaten wordt aanbevolen, omdat de bedreigingen zijn gekoppeld aan fysieke toegankelijkheid van IoT-apparaten.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integratie van IoT Edge security manager en onderhoud

Het IoT Edge Security Manager streeft ernaar de onderdelen te identificeren en isoleren die de beveiliging en integriteit van het Azure IoT Edge platform voor aangepaste beveiliging beschermen. Derden, zoals Device makers, moeten gebruikmaken van aangepaste beveiligings functies die beschikbaar zijn met de hardware van het apparaat.  Zie de sectie volgende stappen voor koppelingen die laten zien hoe u de beveiliging van Azure IoT Security Manager kunt beveiligen met de Trusted Platform Module (TPM) op Linux-en Windows-platforms. Deze voor beelden gebruiken software of virtuele-Tpm's, maar zijn rechtstreeks van toepassing op het gebruik van afzonderlijke TPM-apparaten.  

## <a name="next-steps"></a>Volgende stappen

Lees de blog op [beveiligen van de intelligente edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Een IoT Edge apparaat maken en inrichten [met een virtuele TPM op een virtuele Linux-machine](how-to-auto-provision-simulated-device-linux.md).

Een IoT Edge apparaat maken en inrichten [met een gesimuleerde TPM in Windows](how-to-auto-provision-simulated-device-windows.md).
