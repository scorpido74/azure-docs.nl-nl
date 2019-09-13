---
title: Fysieke beveiliging HSM - Azure toegewezen HSM | Microsoft Docs
description: Informatie over de fysieke beveiliging van de Azure toegewezen HSM-apparaten in datacenters
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: ede1af4625d06af4e280eda86d09ae1db3dfdfd1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881026"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure fysieke toegewezen HSM-beveiliging

Azure toegewezen HSM kunt u voldoen aan vereisten voor geavanceerde beveiliging voor opslag van sleutels. Het is beheerde volgende strikte beveiligingsprocedures gedurende de volledige levenscyclus om te voldoen aan de behoeften van klanten.

## <a name="security-through-procurement"></a>Beveiliging door middel van een Inkoopanalyse

Microsoft heeft een proces voor het veilig. We beheren de bewakingsketen en zorg ervoor dat het apparaat verzonden het apparaat die binnenkomen in onze datacenters. De apparaten die in de onrechtmatige wijzigingen-gebeurtenis plastic back-ups maakt. Ze worden opgeslagen in een beveiligde opslagruimte pas uitgevoerd in de galerie met gegevens van het datacenter.  De rekken met daarin de HSM-apparaten worden beschouwd als hoge zakelijke impact(HBI). De apparaten zijn vergrendeld en onder Videobewaking te allen tijde front-end en back.

## <a name="security-through-deployment"></a>Beveiliging door middel van implementatie

HSM's zijn geïnstalleerd in rekken, samen met de bijbehorende netwerkonderdelen. Wanneer geïnstalleerd, moeten deze worden geconfigureerd voordat ze beschikbaar worden gesteld als onderdeel van de Azure toegewezen HSM-Service. Deze activiteit configuratie wordt uitgevoerd door Microsoft-medewerkers die een achtergrondcontrole ondergaan hebben. 'Just-In-Time' (JIT)-beheer wordt gebruikt voor het beperken van toegang tot alleen de juiste werknemers en alleen de tijd die nodig is. De procedures en systemen gebruikt ook voor zorgen dat alle activiteiten met betrekking tot de HSM-apparaten worden geregistreerd.

## <a name="security-in-operations"></a>Beveiliging in operations

HSM's zijn hardware-apparaten (de werkelijke HSM wordt een PCI-kaart in het toestel) zodat het mogelijk dat er onderdeel niveau problemen optreden. Potentiële problemen omvatten maar zijn niet beperkt tot ventilator en power supply fouten. Dit type gebeurtenis wordt onderhoud vereisen of ondersteuning voor probleemoplossing activiteiten swappable onderdelen vervangen.

### <a name="component-replacement"></a>Onderdeel-vervanging

Nadat een apparaat is ingericht en onder Klantenbeheer van de de hot swappable voeding is de enige onderdelen die zouden worden vervangen. Dit onderdeel is buiten de beveiligingsgrenzen en niet leidt tot een gebeurtenis onrechtmatige wijzigingen. Een ticketsysteem wordt gebruikt om een Microsoft-technicus voor toegang tot de achterzijde van het rack HBI autoriseren. Tijdens het verwerken van het ticket is een tijdelijke fysieke sleutel uitgegeven. Deze sleutel geeft de engineering-toegang tot het apparaat en kan ze het betreffende onderdeel te wisselen. Alle andere toegang (dat wil zeggen, knoeien gebeurtenis is veroorzaakt) wordt uitgevoerd wanneer een apparaat is niet toegewezen aan een klant dus minimaliseren van risico's voor beveiliging en beschikbaarheid.  

### <a name="device-replacement"></a>Vervanging van apparaat

In het geval van totale apparaatstoringen, wordt een proces die vergelijkbaar is met de gebruikt tijdens storingen gevolgd. Als een klant kan geen zeroize van het apparaat of het apparaat zich in een onbekende status, worden de gegevens die zijn voorzien van apparaten verwijderd en in een opslaglocatie in rack vernietiging geplaatst. Apparaten die zijn opgeslagen op de opslaglocatie wordt op een gecontroleerde en veilige manier worden vernietigd. Er zijn geen apparaten voorzien van een HBI rack gegevens laat een Microsoft-datacentrum.

### <a name="other-rack-access-activities"></a>Andere activiteiten Rack-toegang

Als een Microsoft-technicus moet toegang verkrijgen tot het rack wordt gebruikt door HSM-apparaten (bijvoorbeeld apparaatonderhoud netwerken), wordt standaard beveiligingsprocedures worden gebruikt om toegang te krijgen tot de HBI beveiligde rack. Alle toegang zich onder Videobewaking. De HSM-apparaten zijn gevalideerd voor [FIPS 140-2 Level 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) , zodat alle niet-geautoriseerde toegang tot de HSM-apparaten een signaal naar de klant ontvangen wordt en de gegevens wordt zeroized.

## <a name="logical-level-security-considerations"></a>Logische niveau beveiligingsoverwegingen

HSM's zijn ingericht met een virtueel netwerk gemaakt door de klant. Dit is van een klant privé IUP-adresruimte.  Deze configuratie biedt een niveau isolatie van waardevolle logisch netwerk en zorgt ervoor dat toegang alleen door de klant. Dit betekent dat alle logische niveau beveiligingsmechanismen de verantwoordelijkheid van de klant bevinden.

## <a name="next-steps"></a>Volgende stappen

Het is raadzaam dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging en ondersteuning bijvoorbeeld duidelijk zijn voor het apparaat wordt ingericht, toepassingsontwerp of implementatie.

* [Hoge beschikbaarheid](high-availability.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
* [Implementatie-architectuur](deployment-architecture.md)