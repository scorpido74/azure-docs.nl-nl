---
title: Wat is subnetdelegatie in het virtuele Azure-netwerk?
description: Meer informatie over subnetdelegatie in het virtuele Azure-netwerk
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281335"
---
# <a name="what-is-subnet-delegation"></a>Wat is subnetdelegatie?

Subnetdelegatie stelt u in staat om een specifiek subnet aan te wijzen voor een Azure PaaS-service naar keuze die in uw virtuele netwerk moet worden geïnjecteerd. Subnet delegatie biedt volledige controle aan de klant over het beheer van de integratie van Azure-services in hun virtuele netwerken.

Wanneer u een subnet delegeren aan een Azure-service, u die service toestaan om een aantal basisregels voor netwerkconfiguratie voor dat subnet vast te stellen, waarmee de Azure-service hun exemplaren op een stabiele manier uitvoeren. Als gevolg hiervan kan de Azure-service een aantal voorwaarden voor of na implementatie instellen, zoals:
- de service implementeren in een gedeeld versus speciaal subnet.
- voeg aan de service een set netwerkintentiebeleid na implementatie toe die nodig is om de service naar behoren te laten werken.

##  <a name="advantages-of-subnet-delegation"></a>Voordelen van subnetdelegatie

Het delegeren van een subnet aan specifieke diensten biedt de volgende voordelen:

- helpt bij het aanwijzen van een subnet voor een of meer Azure-services en het beheren van de exemplaren in het subnet volgens vereisten. De eigenaar van het virtuele netwerk kan bijvoorbeeld het volgende definiëren voor een gedelegeerd subnet om resources en toegang als volgt beter te beheren:
    - netwerkfilterbeleid met netwerkbeveiligingsgroepen.
    - routeringsbeleid met door de gebruiker gedefinieerde routes.
    - services-integratie met serviceeindpuntconfiguraties.
- helpt geïnjecteerde services om beter te integreren met het virtuele netwerk door hun voorwaarden voor implementaties te definiëren in de vorm van netwerkintentiebeleid. Dit zorgt ervoor dat alle acties die van invloed kunnen zijn op de werking van de geïnjecteerde service kunnen worden geblokkeerd bij PUT.


## <a name="who-can-delegate"></a>Wie kan delegeren?
Subnetdelegatie is een oefening die de virtuele netwerkeigenaren moeten uitvoeren om een van de subnetten voor een specifieke Azure-service aan te wijzen. Azure Service implementeert op zijn beurt de exemplaren in dit subnet voor verbruik door de klantworkloads.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Impact van subnetdelegatie op uw subnet
Elke Azure-service definieert hun eigen implementatiemodel, waar ze kunnen definiëren welke eigenschappen ze wel of niet ondersteunen in een gedelegeerd subnet voor injectiedoeleinden, zoals:
- gedeeld subnet met andere Azure Services of VM / virtuele machine schaal ingesteld in hetzelfde subnet, of het ondersteunt alleen een speciaal subnet met alleen exemplaren van deze service in.
- ondersteunt NSG-associatie met het gedelegeerde subnet.
- ondersteuning nsg gekoppeld aan het gedelegeerde subnet kan ook worden gekoppeld aan een ander subnet.
- hiermee kan routetabelkoppeling met het gedelegeerde subnet worden ingeschakeld.
- hiermee kan de routetabel die is gekoppeld aan het gedelegeerde subnet worden gekoppeld aan een ander subnet.
- bepaalt het minimumaantal IP-adressen in het gedelegeerde subnet.
- voorschrijft de IP-adresruimte in het gedelegeerde subnet om afkomstig te zijn van de ruimte voor privé-IP-adres (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- voorschrijft dat de aangepaste DNS-configuratie een Azure DNS-vermelding heeft.

Geïnjecteerde services kunnen ook hun eigen beleid als volgt toevoegen:
- **Beveiligingsbeleid**: Verzameling van beveiligingsregels die nodig zijn voor een bepaalde service om te werken.
- **Routebeleid**: Verzameling van routes die nodig zijn voor een bepaalde service om te werken.

## <a name="what-subnet-delegation-does-not-do"></a>Welke subnetdelegatie doet niet

De Azure-services die in een gedelegeerd subnet worden geïnjecteerd, hebben nog steeds de basisset van eigenschappen die beschikbaar zijn voor niet-gedelegeerde subnetten, zoals:
-  Azure-services kunnen instanties injecteren in subnetten van klanten, maar kunnen geen invloed hebben op de bestaande workloads.
-  Het beleid of de routes die deze services toepassen zijn flexibel en kunnen door de klant worden overschreven.

## <a name="next-steps"></a>Volgende stappen

- [Een subnet delegeren](manage-subnet-delegation.md)
