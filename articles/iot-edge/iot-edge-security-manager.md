---
title: Azure IoT Edge-beveiligingsbeheer - Azure IoT Edge
description: Beheert de beveiliging van iot edge-apparaten en de integriteit van beveiligingsservices.
services: iot-edge
keywords: beveiliging, veilig element, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548693"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge-beveiligingsbeheer

De Azure IoT Edge-beveiligingsmanager is een begrensde beveiligingskern voor het beschermen van het IoT Edge-apparaat en al zijn componenten door de beveiligde siliciumhardware te abstraheren. De security manager is het brandpunt voor security verharding en biedt technologie integratie punt aan de fabrikanten van originele apparatuur (OEM).

![Azure IoT Edge-beveiligingsbeheer](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge security manager is gericht op het verdedigen van de integriteit van het IoT Edge-apparaat en alle inherente softwarebewerkingen. De beveiligingsmanager schakelt het vertrouwen over van de onderliggende hardwareroot van vertrouwenshardware (indien beschikbaar) om de Runtime van IoT Edge op te start zetten en lopende bewerkingen te controleren.  De IoT Edge security manager is software die samenwerkt met veilige silicium hardware (indien beschikbaar) om te helpen de hoogst mogelijke beveiligingsgaranties te bieden.  

De verantwoordelijkheden van de IoT Edge security manager omvatten, maar zijn niet beperkt tot:

* Beveiligde en gemeten bootstrapping van het Azure IoT Edge-apparaat.
* Apparaatidentiteitsinrichting en overgang van vertrouwen indien van toepassing.
* Host en bescherm apparaatonderdelen van cloudservices zoals Device Provisioning Service.
* Inrichten van IoT Edge-modules met unieke identiteiten.
* Gatekeeper naar apparaat hardware root van vertrouwen via notarisdiensten.
* Controleer de integriteit van IoT Edge-bewerkingen tijdens runtime.

IoT Edge security manager bevat drie componenten:

* IoT Edge security daemon.
* Hardware security module platform abstractie Layer (HSM PAL).
* Optionele maar sterk aanbevolen hardware silicium wortel van vertrouwen of HSM.

## <a name="the-iot-edge-security-daemon"></a>De IoT Edge security daemon

De IoT Edge security daemon is verantwoordelijk voor de logische bewerkingen van IoT Edge security manager. Het vertegenwoordigt een aanzienlijk deel van de vertrouwde computerbasis van het IoT Edge-apparaat.

### <a name="design-principles"></a>Ontwerpprincipes

De IoT Edge security daemon volgt twee kernprincipes: maximaliseer de operationele integriteit en minimaliseer bloat en churn.

#### <a name="maximize-operational-integrity"></a>Maximaliseer de operationele integriteit

De IoT Edge security daemon werkt met de hoogst mogelijke integriteit binnen de verdedigingsmogelijkheden van een bepaalde root van vertrouwenshardware. Met de juiste integratie, de wortel van vertrouwen hardware maatregelen en controleert de beveiliging daemon statisch en bij runtime om te weerstaan knoeien.

Fysieke toegang is altijd een bedreiging voor IoT-apparaten. Hardware root van vertrouwen speelt een belangrijke rol bij het verdedigen van de integriteit van de IoT Edge security daemon.  Hardware wortel van vertrouwen komen in twee variëteiten:

* veilige elementen voor de bescherming van gevoelige informatie zoals geheimen en cryptografische sleutels.
* veilige enclaves voor de bescherming van geheimen zoals sleutels en gevoelige workloads zoals meting en facturering.

Er bestaan twee soorten uitvoeringsomgevingen om hardwareroot van vertrouwen te gebruiken:

* De standaard- of rijke uitvoeringsomgeving (REE) die afhankelijk is van het gebruik van veilige elementen om gevoelige informatie te beschermen.
* De vertrouwde uitvoeringsomgeving (TEE) die afhankelijk is van het gebruik van veilige enclavetechnologie om gevoelige informatie te beschermen en bescherming te bieden aan softwareuitvoering.

Voor apparaten die veilige enclaves gebruiken als hardwareroot van vertrouwen, moet gevoelige logica binnen IoT Edge security daemon zich in de enclave bevinden.  Niet-gevoelige delen van de beveiligingsdaemon kunnen zich buiten de TEE begeven.  In ieder geval moeten fabrikanten van originele ontwerpfabrikanten (ODM) en fabrikanten van originele apparatuur (OEM) het vertrouwen van hun HSM uitbreiden om de integriteit van de IoT Edge security daemon bij opstart en runtime te meten en te verdedigen.

#### <a name="minimize-bloat-and-churn"></a>Minimaliseer bloat en churn

Een ander kernprincipe voor de IoT Edge security daemon is het minimaliseren van churn.  Voor het hoogste niveau van vertrouwen kan de IoT Edge-beveiligingsdaemon nauw koppelen aan de hardwareroot van het apparaat en werken als native code.  Het is gebruikelijk dat dit soort realisaties de daemon-software bijwerken via de hardwareroot van de beveiligde updatepaden van Trust (in tegenstelling tot door OS geleverde updatemechanismen), wat in sommige scenario's een uitdaging kan zijn.  Hoewel beveiligingsvernieuwing wordt aanbevolen voor IoT-apparaten, kunnen overmatige updatevereisten of grote updatepayloads het dreigingsoppervlak op vele manieren uitbreiden.  Voorbeelden hiervan zijn het overslaan van updates om de operationele beschikbaarheid te maximaliseren of de root van vertrouwenshardware die te beperkt is om grote updatepayloads te verwerken.  Als zodanig is het ontwerp van IoT Edge security daemon beknopt om de footprint en vertrouwde computerbasis klein te houden en updatevereisten te minimaliseren.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architectuur van IoT Edge security daemon

![Azure IoT Edge-beveiligingsdaemon](media/edge-security-manager/iot-edge-security-daemon.png)

De IoT Edge security daemon maakt gebruik van alle beschikbare hardware root van vertrouwenstechnologie voor security verharding.  Het maakt ook split-world werking tussen een standaard / rijke uitvoeringsomgeving (REE) en een vertrouwde uitvoeringsomgeving (TEE) wanneer hardwaretechnologieën bieden vertrouwde uitvoeringsomgevingen. Rolspecifieke interfaces stellen de belangrijkste onderdelen van IoT Edge in staat om de integriteit van het IoT Edge-apparaat en de bijbehorende werking te garanderen.

#### <a name="cloud-interface"></a>Cloud-interface

Met de cloudinterface heeft de IoT Edge security daemon toegang tot cloudservices zoals cloudcomplimenten voor apparaatbeveiliging, zoals beveiligingsvernieuwing.  De IoT Edge security daemon gebruikt deze interface momenteel bijvoorbeeld om toegang te krijgen tot de Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) voor het beheer van de levenscyclus van de identiteit van het apparaat.  

#### <a name="management-api"></a>Beheer-API

IoT Edge security daemon biedt een beheer-API, die wordt aangeroepen door de IoT Edge-agent bij het maken/starten/stoppen/verwijderen/verwijderen van een IoT Edge-module. De security daemon slaat "registraties" op voor alle actieve modules. Deze registraties stellen de identiteit van een module in kaart aan sommige eigenschappen van de module. Deze module-eigenschappen omvatten bijvoorbeeld de proces-id (pid) van het proces dat in de container wordt uitgevoerd en de hash van de inhoud van de dockercontainer.

Deze eigenschappen worden gebruikt door de workload-API (hieronder beschreven) om te controleren of de beller is geautoriseerd voor een actie.

De beheer-API is een geprivilegieerde API, alleen kan worden aangesproken vanuit de IoT Edge-agent.  Aangezien de IoT Edge-beveiliging daemon bootstraps start en de IoT Edge-agent start, wordt gecontroleerd of er niet met de IoT Edge-agent is geknoeid, waarna een impliciete registratie voor de IoT Edge-agent kan worden gemaakt. Hetzelfde attestproces dat de workload-API gebruikt, beperkt ook de toegang tot de beheer-API tot alleen de IoT Edge-agent.

#### <a name="container-api"></a>Container-API

De container-API werkt samen met het containersysteem dat wordt gebruikt voor modulebeheer, zoals Moby of Docker.

#### <a name="workload-api"></a>Api voor werkbelasting

De workload API is toegankelijk voor alle modules. Het biedt een bewijs van identiteit, hetzij als een HSM-geworteld ondertekend token of een X509-certificaat, en de bijbehorende vertrouwensbundel naar een module. De vertrouwensbundel bevat CA-certificaten voor alle andere servers die de modules moeten vertrouwen.

De IoT Edge security daemon maakt gebruik van een attestation proces om deze API te bewaken. Wanneer een module deze API aanroept, probeert de beveiligingsdaemon een registratie voor de identiteit te vinden. Als dit lukt, gebruikt het de eigenschappen van de registratie om de module te meten. Als het resultaat van het meetproces overeenkomt met de registratie, wordt een nieuw identiteitsbewijs gegenereerd. De bijbehorende CA-certificaten (vertrouwensbundel) worden teruggestuurd naar de module.  De module gebruikt dit certificaat om verbinding te maken met IoT Hub, andere modules of om een server te starten. Wanneer het ondertekende token of certificaat bijna afloopt, is het de verantwoordelijkheid van de module om een nieuw certificaat aan te vragen.

### <a name="integration-and-maintenance"></a>Integratie en onderhoud

Microsoft onderhoudt de hoofdcodebasis voor de [IoT Edge security daemon op GitHub.](https://github.com/Azure/iotedge/tree/master/edgelet)

#### <a name="installation-and-updates"></a>Installatie en updates

De installatie en updates van de IoT Edge security daemon worden beheerd via het pakketbeheersysteem van het besturingssysteem. IoT Edge-apparaten met hardwareroot van vertrouwen moeten de integriteit van de daemon extra verharden door de levenscyclus ervan te beheren via de beveiligde opstart- en updatesbeheersystemen. Apparaatmakers moeten deze mogelijkheden verkennen op basis van hun respectieve apparaatmogelijkheden.

#### <a name="versioning"></a>Versiebeheer

De IoT Edge runtime volgt en rapporteert de versie van de IoT Edge security daemon. De versie wordt gerapporteerd als het kenmerk *runtime.platform.version* van de iot edge-agentmodule gerapporteerde eigenschap.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>De abstractielaag van hardwarebeveiligingsmoduleplatforms (HSM PAL)

De HSM PAL abstraheert alle basisvan vertrouwenshardware om de ontwikkelaar of gebruiker van IoT Edge te isoleren van hun complexiteit.  Het omvat een combinatie van application programming interface (API) en trans-domein communicatieprocedures, bijvoorbeeld communicatie tussen een standaard uitvoeringsomgeving en een veilige enclave.  De daadwerkelijke implementatie van de HSM PAL is afhankelijk van de specifieke beveiligde hardware in gebruik. Het bestaan ervan maakt het gebruik van vrijwel elke veilige silicium hardware mogelijk.

## <a name="secure-silicon-root-of-trust-hardware"></a>Veilige siliciumwortel van vertrouwenshardware

Veilig silicium is nodig om het vertrouwen in de IoT Edge-apparaathardware te verankeren.  Veilig silicium is er in verscheidenheid met Trusted Platform Module (TPM), embedded Secure Element (eSE), ARM TrustZone, Intel SGX en aangepaste veilige siliciumtechnologieën.  Het gebruik van veilige siliciumwortel van vertrouwen in apparaten wordt aanbevolen gezien de bedreigingen die gepaard gaan met de fysieke toegankelijkheid van IoT-apparaten.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integratie en onderhoud van IoT Edge-beveiligingsbeheer

De IoT Edge-beveiligingsmanager heeft als doel de componenten te identificeren en te isoleren die de beveiliging en integriteit van het Azure IoT Edge-platform verdedigen voor aangepaste verharding. Derden, zoals apparaatmakers, moeten gebruik maken van aangepaste beveiligingsfuncties die beschikbaar zijn met hun apparaathardware.  Zie volgende stappensectie voor koppelingen die laten zien hoe u de Azure IoT-beveiligingsbeheer verharden met de Trusted Platform Module (TPM) op Linux- en Windows-platforms. Deze voorbeelden maken gebruik van software of virtuele TPM's, maar zijn rechtstreeks van toepassing op het gebruik van discrete TPM-apparaten.  

## <a name="next-steps"></a>Volgende stappen

Lees de blog over [Het beveiligen van de intelligente rand](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Maak en informeer een [IoT Edge-apparaat met een virtuele TPM op een Virtuele Linux-machine.](how-to-auto-provision-simulated-device-linux.md)

Maak en inplaats een [IoT Edge-apparaat met een gesimuleerde TPM op Windows.](how-to-auto-provision-simulated-device-windows.md)
