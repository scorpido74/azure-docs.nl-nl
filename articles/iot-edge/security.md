---
title: Beveiligingsframework - Azure IoT Edge | Microsoft Documenten
description: Meer informatie over de beveiligings-, verificatie- en autorisatiestandaarden die zijn gebruikt om Azure IoT Edge te ontwikkelen en die moeten worden overwogen bij het ontwerpen van uw oplossing
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3212493963805de3c8845ec494d87fc92d72998a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760110"
---
# <a name="security-standards-for-azure-iot-edge"></a>Beveiligingsstandaarden voor Azure IoT Edge

Azure IoT Edge pakt de risico's aan die inherent zijn aan het verplaatsen van uw gegevens en analyses naar de intelligente rand. De IoT Edge-beveiligingsstandaarden brengen flexibiliteit in evenwicht voor verschillende implementatiescenario's met de bescherming die u van alle Azure-services verwacht.

IoT Edge draait op verschillende merken en modellen van hardware, ondersteunt verschillende besturingssystemen en is van toepassing op uiteenlopende implementatiescenario's. In plaats van concrete oplossingen te bieden voor specifieke scenario's, is IoT Edge een uitbreidbaar beveiligingskader dat is gebaseerd op gefundeerde principes die zijn ontworpen voor schaalgrootte. Het risico van een implementatiescenario is afhankelijk van vele factoren, waaronder:

* Eigendom van oplossingen
* Implementatiegeografie
* Gegevensgevoeligheid
* Privacy
* Verticaal van toepassing
* Voorschriften

In dit artikel vindt u een overzicht van het IoT Edge-beveiligingsframework. Zie [De intelligente rand beveiligen](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)voor meer informatie.

## <a name="standards"></a>Standaarden

Normen bevorderen het gemak van controle en het gemak van de uitvoering, die beide kenmerken van veiligheid zijn. Een beveiligingsoplossing moet zich lenen voor controle onder evaluatie om vertrouwen op te bouwen en mag geen hindernis zijn voor implementatie. Het ontwerp van het framework voor het beveiligen van Azure IoT Edge is gebaseerd op beproefde en beproefde beveiligingsprotocollen voor vertrouwdheid en hergebruik.

## <a name="authentication"></a>Authentication

Wanneer u een IoT-oplossing implementeert, moet u weten dat alleen vertrouwde actoren, apparaten en modules toegang hebben tot uw oplossing. Verificatie op basis van certificaten is het primaire mechanisme voor verificatie voor het Azure IoT Edge-platform. Dit mechanisme is afgeleid van een reeks normen voor public key infrastructure (PKiX) door de Internet Engineering Task Force (IETF).

Alle apparaten, modules en actoren die met het Azure IoT Edge-apparaat werken, moeten unieke certificaatidentiteiten hebben. Deze richtlijnen zijn van toepassing of de interacties fysiek zijn of via een netwerkverbinding. Niet elk scenario of onderdeel kan zich lenen voor verificatie op basis van certificaten, dus de uitbreidbaarheid van het beveiligingsframework biedt veilige alternatieven.

Zie [Azure IoT Edge-certificaatgebruik](iot-edge-certs.md)voor meer informatie.

## <a name="authorization"></a>Autorisatie

Het principe van de minste bevoegdheden zegt dat gebruikers en onderdelen van een systeem alleen toegang moeten hebben tot de minimale set resources en gegevens die nodig zijn om hun rollen uit te voeren. Apparaten, modules en actoren mogen alleen toegang krijgen tot de bronnen en gegevens binnen hun machtigingsbereik en alleen wanneer deze architectonisch zijn toegestaan. Sommige machtigingen zijn configureerbaar met voldoende bevoegdheden en andere worden architectonisch afgedwongen. Sommige modules kunnen bijvoorbeeld zijn geautoriseerd om verbinding te maken met Azure IoT Hub. Er is echter geen reden waarom een module in een IoT Edge-apparaat toegang zou moeten krijgen tot de tweeling van een module in een ander IoT Edge-apparaat.

Andere autorisatieregelingen omvatten certificaatondertekeningsrechten en op rollen gebaseerde toegangscontrole (RBAC).

## <a name="attestation"></a>Attest

Attestation zorgt voor de integriteit van softwarebits, wat belangrijk is voor het detecteren en voorkomen van malware. Het Azure IoT Edge-beveiligingsframework classificert attest onder drie hoofdcategorieën:

* Statische verklaring
* Runtime attest
* Softwareattest

### <a name="static-attestation"></a>Statische verklaring

Statische attest controleert de integriteit van alle software op een apparaat tijdens het opstarten, inclusief het besturingssysteem, alle runtimes en configuratie-informatie. Omdat statische attest optreedt tijdens het opstarten, wordt het vaak aangeduid als veilige boot. Het beveiligingsframework voor IoT Edge-apparaten strekt zich uit tot fabrikanten en bevat veilige hardwaremogelijkheden die statische attestationprocessen garanderen. Deze processen omvatten veilige boot en veilige firmware-upgrade. Door nauw samen te werken met siliciumleveranciers worden overbodige firmwarelagen geëlimineerd, waardoor het dreigingsoppervlak tot een minimum wordt beperkt.

### <a name="runtime-attestation"></a>Runtime attest

Zodra een systeem een veilig opstartproces heeft voltooid, moeten goed ontworpen systemen pogingen om malware te injecteren detecteren en de juiste tegenmaatregelen nemen. Malware-aanvallen kunnen zich richten op de poorten en interfaces van het systeem. Als kwaadwillende actoren fysieke toegang hebben tot een apparaat, kunnen ze knoeien met het apparaat zelf of zijkanaalaanvallen gebruiken om toegang te krijgen. Dergelijke ontevredenheid, of malware of ongeautoriseerde configuratie wijzigingen, kan niet worden gedetecteerd door statische attestation, omdat het wordt geïnjecteerd na het opstartproces. Tegenmaatregelen die worden aangeboden of afgedwongen door de hardware van het apparaat helpen om dergelijke bedreigingen af te weren. Het beveiligingsframework voor IoT Edge vraagt expliciet om extensies die runtime-bedreigingen bestrijden.  

### <a name="software-attestation"></a>Softwareattest

Alle gezonde systemen, inclusief intelligente randsystemen, hebben patches en upgrades nodig. Beveiliging is belangrijk voor updateprocessen, anders kunnen het potentiële bedreigingsvectoren zijn. Het beveiligingskader voor IoT Edge vraagt om updates via gemeten en ondertekende pakketten om de integriteit van en verificatie van de bron van de pakketten te verzekeren en te verifiëren. Deze standaard is van toepassing op alle besturingssystemen en applicatiesoftwarebits.

## <a name="hardware-root-of-trust"></a>Hardwareroot van vertrouwen

Voor veel intelligente edge-apparaten, met name apparaten die fysiek toegankelijk zijn voor potentiële kwaadwillende actoren, is hardwarebeveiliging de laatste verdediging voor bescherming. Tamper resistente hardware is cruciaal voor dergelijke implementaties. Azure IoT Edge moedigt veilige leveranciers van siliciumhardware aan om verschillende smaken van hardwareroot van vertrouwen aan te bieden om verschillende risicoprofielen en implementatiescenario's mogelijk te maken. Hardwarevertrouwen kan afkomstig zijn van algemene beveiligingsprotocolstandaarden zoals Trusted Platform Module (ISO/IEC 11889) en Trusted Computing Group's Device Identifier Composition Engine (DICE). Veilige enclavetechnologieën zoals TrustZones en Software Guard Extensions (SGX) bieden ook hardwarevertrouwen.

## <a name="certification"></a>Certificering

Om klanten te helpen weloverwogen beslissingen te nemen bij de aanschaf van Azure IoT Edge-apparaten voor hun implementatie, bevat het IoT Edge-framework certificeringsvereisten. Stichtingskenmerken aan deze vereisten zijn certificeringen met betrekking tot beveiligingsclaims en certificeringen met betrekking tot validatie van de beveiligingsimplementatie. Een certificering voor beveiligingsclaim betekent bijvoorbeeld dat het IoT Edge-apparaat veilige hardware gebruikt waarvan bekend is dat het opstartaanvallen kan weerstaan. Een validatie-certificering betekent dat de beveiligde hardware correct is geïmplementeerd om deze waarde in het apparaat aan te bieden. In overeenstemming met het principe van eenvoud probeert het raamwerk de certificeringslast minimaal te houden.

## <a name="extensibility"></a>Uitbreidbaarheid

Met IoT-technologie die verschillende soorten bedrijfstransformaties aandrijft, moet beveiliging parallel evolueren om nieuwe scenario's aan te pakken. Het Azure IoT Edge-beveiligingsframework begint met een solide basis waarop het uitbreidbaarheid in verschillende dimensies bouwt om op te nemen:

* Beveiligingsservices van eerste partijen, zoals de Device Provisioning Service voor Azure IoT Hub.
* Services van derden, zoals beheerde beveiligingsservices voor verschillende toepassingsverticalen (zoals industriële of gezondheidszorg) of technologische focus (zoals beveiligingsbewaking in mesh-netwerken of attestdiensten voor siliciumhardware) via een rijk netwerk van Partners.
* Oudere systemen om retrofitting op te nemen met alternatieve beveiligingsstrategieën, zoals het gebruik van andere veilige technologie dan certificaten voor verificatie en identiteitsbeheer.
* Veilige hardware voor de adoptie van opkomende veilige hardwaretechnologieën en bijdragen van siliciumpartners.

Uiteindelijk vereist het beveiligen van de intelligente rand gezamenlijke bijdragen van een open gemeenschap die wordt gedreven door het gemeenschappelijk belang bij het beveiligen van IoT. Deze bijdragen kunnen in de vorm van veilige technologieën of diensten zijn. Het Azure IoT Edge-beveiligingsframework biedt een solide basis voor beveiliging die uitbreidbaar is voor de maximale dekking om hetzelfde niveau van vertrouwen en integriteit te bieden in de intelligente rand als bij Azure-cloud.  

## <a name="next-steps"></a>Volgende stappen

Lees meer over hoe Azure IoT Edge [de intelligente rand veiligstelt.](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)
