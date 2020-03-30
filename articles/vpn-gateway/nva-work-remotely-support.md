---
title: 'Werken op afstand: Network Virtual Appliance (NVA) overwegingen voor werkzaamheden op afstand | Azure VPN-gateway'
description: Dit artikel helpt u inzicht te krijgen in de dingen waarmee u tijdens de COVID-19-pandemie moet worden betrokken bij het werken met Network Virtual Appliances (NVA's) in Azure.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/21/2020
ms.author: scottnap
ms.openlocfilehash: 8a22e8f0bde2d13b4055566d96680fadc2db6e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337098"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Op afstand werken: NVA-overwegingen (Network Virtual Appliance) voor werkzaamheden op afstand

>[!NOTE]
>In dit artikel wordt beschreven hoe u Network Virtual Appliances, Azure, Microsoft Network en het Azure-partnerecosysteem gebruiken om op afstand te werken en netwerkproblemen te beperken waarmee u wordt geconfronteerd als gevolg van de COVID-19-crisis.
>

Sommige Azure-klanten maken gebruik van Network Virtual Appliances (NVA's) van derden vanuit Azure Marketplace om kritieke services zoals Point-to-site VPN te leveren voor hun werknemers die thuis werken tijdens de COVID-19-epidemie. In dit artikel worden enkele richtlijnen op hoog niveau beschreven waarmee rekening moet worden gehouden bij het gebruik van NVA's in Azure om oplossingen voor externe toegang te bieden.

## <a name="nva-performance-considerations"></a>NVA prestatieoverwegingen

Alle grote NVA-leveranciers in Azure Marketplace moeten aanbevelingen hebben over de VM-grootte en het aantal exemplaren dat moet worden gebruikt bij het implementeren van hun oplossingen.  Hoewel bijna alle NVA-leveranciers u elke grootte laten kiezen die voor u beschikbaar is in een bepaalde regio, is het erg belangrijk dat u de aanbevelingen van leveranciers voor Azure VM-instantiegroottes volgt, omdat deze aanbevelingen de VM-formaten zijn die de leverancier heeft uitgevoerd testen met in Azure.  

### <a name="consider-the-following"></a>Overweeg het volgende

- **Capaciteit en aantal gelijktijdige gebruikers** - Dit nummer is vooral belangrijk voor Point-to-Site VPN-gebruikers, omdat elke verbonden gebruiker één versleutelde (IPSec- of SSL VPN-tunnel) maakt.  
- **Geaggregeerde doorvoer** - Wat is de totale bandbreedte die u nodig hebt om het aantal gebruikers dat u nodig hebt om die u nodig hebt om toegang op afstand te bieden tegemoet te komen.
- **De VM-grootte die u nodig hebt** - U moet altijd VM-formaten gebruiken die worden aanbevolen door de NVA-leverancier.  Voor point-to-site VPN, als je veel gelijktijdige gebruikersverbindingen hebt, moet je grotere VM-formaten gebruiken, zoals [VM2- en DSv2-serie](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Dv2- en Dsv2-serie") VM's. Deze VM's hebben meestal meer vCPU's en kunnen meer gelijktijdige VPN-sessies aan.  Naast meer virtuele cores hebben grotere VM-formaten in Azure meer totale bandbreedtecapaciteit dan kleinere VM-formaten.
    > **Belangrijk:** Elke leverancier gebruikt resources anders.  Als niet duidelijk is welke instantiegroottes u moet gebruiken om uw geschatte gebruikersbelasting aan te kunnen, moet u rechtstreeks contact opnemen met de softwareleverancier en hen om een aanbeveling vragen.
- **Aantal exemplaren** - Als u verwacht een groot aantal gebruikers en verbindingen te hebben, zijn er grenzen aan wat het opschalen van uw NVA-instantiegroottes kan bereiken.  Overweeg meerdere VM-exemplaren te implementeren.
- **IPSec VPN vs SSL VPN** - Over het algemeen presteren IPSec VPN-implementaties beter dan SSL VPN-implementaties.  
- **Licenties** - Zorg ervoor dat de softwarelicenties die u hebt gekocht voor de NVA-oplossing de plotselinge groei dekken die u ervaren tijdens de COVID-19-epidemie.  Veel NVA-licentieprogramma's beperken het aantal verbindingen of bandbreedte waartoe de oplossing in staat is.
- **Versnelde netwerken** - Overweeg een NVA-oplossing die ondersteuning biedt voor Accelerated Networking.  Versnelde netwerken maakt single root I/O virtualisatie (SR-IOV) naar een VM mogelijk, waardoor de netwerkprestaties aanzienlijk worden verbeterd. Dit krachtige pad omzeilt de host van het gegevenspad, waardoor latentie, jitter en CPU-gebruik worden verminderd voor gebruik met de meest veeleisende netwerkworkloads op ondersteunde VM-typen. Versnelde netwerken worden ondersteund op de meeste algemene en voor gegevens geoptimaliseerde instantieformaten met twee of meer vCPU's.

## <a name="monitoring-resources"></a>Controlebronnen

Elke NVA-oplossing beschikt over eigen tools en middelen voor het bewaken van de prestaties van hun NVA.  Raadpleeg de documentatie van uw leveranciers om ervoor te zorgen dat u de prestatiebeperkingen begrijpt en detecteren wanneer uw NVA in de buurt is of de capaciteit bereikt.  Daarnaast u kijken naar Azure Monitor Network Insights en basisprestatiegegevens over uw netwerkvirtuele apparaten bekijken, zoals:

- CPU-gebruik
- Netwerk in
- Netwerk uit
- Binnenkomende stromen
- Uitgaande stromen

## <a name="next-steps"></a>Volgende stappen

De meeste grote NVA-partners hebben richtlijnen geplaatst rond schaalvergroting voor plotselinge, onverwachte groei tijdens COVID-19. Hier zijn een paar nuttige links naar partnerbronnen.

[Barracuda schakel thuis werken in terwijl u uw gegevens beveiligt tijdens COVID-19](https://www.barracuda.com/covid-19/work-from-home "Werk thuis inschakelen terwijl u uw gegevens beveiligt tijdens COVID-19")

[Cisco AnyConnect-implementatie- en prestatie-/schalingsreferentie voor COVID-19-voorbereiding](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect-implementatie- en prestatie-/schalingsreferentie voor COVID-19-voorbereiding")

[Citrix COVID-19 Response Support Center](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19 Response Support Center")

[F5-richtlijnen om de dramatische toename van werknemers op afstand aan te pakken](https://www.f5.com/business-continuity "F5-richtlijnen om de dramatische toename van werknemers op afstand aan te pakken")

[Fortinet COVID-19 Updates voor klanten en partners](https://www.fortinet.com/covid-19.html "COVID-19-updates voor klanten en partners")

[Palo Alto Networks COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 Response Center")
