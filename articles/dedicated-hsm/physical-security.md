---
title: HSM-fysieke beveiliging - Azure Dedicated HSM | Microsoft Documenten
description: Informatie over de fysieke beveiliging van Azure Dedicated HSM-apparaten in datacenters
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881026"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure Dedicated HSM fysieke beveiliging

Azure Dedicated HSM helpt u te voldoen aan geavanceerde beveiligingsvereisten voor sleutelopslag. Het wordt beheerd volgens strenge beveiligingspraktijken gedurende de volledige levenscyclus om te voldoen aan de behoeften van klanten.

## <a name="security-through-procurement"></a>Beveiliging door aanbestedingen

Microsoft volgt een veilig inkoopproces. Wij beheren de keten van bewaring en zorgen ervoor dat het specifieke apparaat besteld en verzonden is het apparaat dat aankomt bij onze datacenters. De apparaten zijn in tamper-event plastic ruggen. Ze worden opgeslagen in een beveiligde opslagruimte tot in gebruik genomen in de datagalerij van het datacenter.  De racks met de HSM-apparaten worden beschouwd als een hoge bedrijfsimpact (HBI). De apparaten zijn vergrendeld en onder videobewaking te allen tijde voor en achter.

## <a name="security-through-deployment"></a>Beveiliging via implementatie

HSM's worden geïnstalleerd in racks samen met bijbehorende netwerkcomponenten. Eenmaal geïnstalleerd, moeten ze worden geconfigureerd voordat ze beschikbaar worden gesteld als onderdeel van de Azure Dedicated HSM-service. Deze configuratieactiviteit wordt uitgevoerd door Microsoft-medewerkers die een achtergrondcontrole hebben ondergaan. "Just In Time" (JIT) administratie wordt gebruikt om de toegang tot alleen de juiste medewerkers te beperken en voor alleen de tijd dat toegang nodig is. De gebruikte procedures en systemen zorgen er ook voor dat alle activiteiten met betrekking tot de HSM-apparaten worden geregistreerd.

## <a name="security-in-operations"></a>Beveiliging bij operaties

HSM's zijn hardwareapparaten (de werkelijke HSM is een PCI-kaart in het apparaat), dus het is mogelijk dat problemen op componentniveau zich kunnen voordoen. Mogelijke problemen zijn, maar zijn niet beperkt tot ventilator- en stroomuitval. Voor dit type gebeurtenis zijn onderhouds- of breek-/fixactiviteiten nodig om verwisselbare onderdelen te vervangen.

### <a name="component-replacement"></a>Vervanging van onderdelen

Nadat een apparaat is ingericht en onder klantbeheer, is de hot-swappable voeding de enige onderdelen die zouden worden vervangen. Dit onderdeel bevindt zich buiten de beveiligingsgrens en veroorzaakt geen sabotagegebeurtenis. Een ticketing systeem wordt gebruikt om een Microsoft ingenieur om toegang te krijgen tot de achterkant van de HBI rack. Wanneer het ticket wordt verwerkt, wordt een tijdelijke fysieke sleutel uitgegeven. Deze sleutel geeft de ingenieur toegang tot het apparaat en stelt hen in staat om het getroffen onderdeel te wisselen. Elke andere toegang (dat wil zeggen, sabotage gebeurtenis veroorzaakt) zou worden gedaan wanneer een apparaat niet wordt toegewezen aan een klant, waardoor het minimaliseren van de veiligheid en beschikbaarheid risico.  

### <a name="device-replacement"></a>Apparaatvervanging

In het geval van een totale uitval van het apparaat wordt een proces gevolgd dat vergelijkbaar is met het proces dat wordt gebruikt tijdens het uitvallen van de onderdelen. Als een klant het apparaat niet kan zeroiseren of als het apparaat zich in een onbekende staat bevindt, worden de gegevensdragende apparaten verwijderd en in een vernietigingsopslag in het rack geplaatst. Apparaten die in de bak worden geplaatst, worden gecontroleerd en veilig vernietigd. Er verlaten geen gegevensdragende apparaten uit een HBI-rack een Microsoft-datacenter.

### <a name="other-rack-access-activities"></a>Andere racktoegangsactiviteiten

Als een Microsoft-technicus toegang moet krijgen tot het rack dat wordt gebruikt door HSM-apparaten (bijvoorbeeld onderhoud van netwerkapparaten), worden standaardbeveiligingsprocedures gebruikt om toegang te krijgen tot het HBI-beveiligde rack. Alle toegang zal onder videobewaking staan. De HSM-apparaten zijn gevalideerd naar [FIPS 140-2 Level 3,](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) zodat elke ongeautoriseerde toegang tot de HSM-apparaten wordt gesignaleerd aan de klant en de gegevens worden gezeroiseerd.

## <a name="logical-level-security-considerations"></a>Logische veiligheidsoverwegingen

HSM's zijn ingericht op een virtueel netwerk dat door de klant is gemaakt. Dit is de privé IUP-adresruimte van een klant.  Deze configuratie biedt een waardevolle logische isolatie op netwerkniveau en garandeert alleen toegang voor de klant. Dit houdt in dat alle logische beveiligingscontroles onder de verantwoordelijkheid van de klant vallen.

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging en ondersteuning bijvoorbeeld, goed worden begrepen voordat apparaatinrichting, applicatieontwerp of implementatie.

* [Hoge beschikbaarheid](high-availability.md)
* [Networking](networking.md)
* [Ondersteuning](supportability.md)
* [Monitoring](monitoring.md)
* [Implementatiearchitectuur](deployment-architecture.md)