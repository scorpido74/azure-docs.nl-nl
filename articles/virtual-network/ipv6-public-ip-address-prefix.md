---
title: Openbaar IPv6-adresvoorvoegsel in het virtuele Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over openbaar IPv6-adresvoorvoeging in het virtuele Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965177"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Gereserveerd openbaar IPv6-adresvoorvoegsel (voorbeeld)

In Azure zijn virtual stack (IPv4+IPv6) virtuele netwerken (VNet) en virtuele machines (VM's) standaard beveiligd omdat ze geen internetverbinding hebben. U eenvoudig een IPv6-internetverbinding toevoegen aan uw Azure Load Balancers en VM's met openbare IPv6-adressen die u van Azure verkrijgt.

Openbare IP's die u reserveert, zijn gekoppeld aan een Azure-gebied naar keuze en aan uw Azure-abonnement. U een gereserveerd (statisch) IPv6-openbaar IP verplaatsen tussen een van de Azure Load Balancers of VM's in uw abonnement. U de IPv6 openbare IP volledig scheiden en het zal worden gehouden voor uw gebruik wanneer u klaar bent.

> [!WARNING]
> Wees voorzichtig om uw openbare IP-adressen niet per ongeluk te verwijderen. Als u een openbaar IP-adres verwijdert, wordt het uit uw abonnement verwijderd en u het niet herstellen (zelfs niet met behulp van Azure-ondersteuning).

Naast het reserveren van afzonderlijke IPv6-adressen, u aaneengesloten bereiken van Azure IPv6-adressen (bekend als IP-voorvoegsel) reserveren voor uw gebruik.  Net als bij afzonderlijke IP-adressen zijn gereserveerde voorvoegsels gekoppeld aan een Azure-regio naar keuze en aan uw Azure-abonnement. Het reserveren van een voorspelbare, aaneengesloten bereik van adressen heeft vele toepassingen. U bijvoorbeeld de *IP-whitelisting* van uw azure-gehoste toepassingen door uw bedrijf en uw klanten sterk vereenvoudigen, omdat uw statische IP-bereiken gemakkelijk kunnen worden geprogrammeerd in on-premises firewalls.  U indien nodig afzonderlijke openbare IP-IP-ip-ip-voorvoegsel maken en wanneer u deze afzonderlijke openbare IP-ip's verwijdert, worden ze *teruggestuurd naar* uw gereserveerde bereik, zodat u ze later opnieuw gebruiken. Alle IP-adressen in uw IP-voorvoegsel zijn gereserveerd voor exclusief gebruik totdat u uw voorvoegsel verwijdert.

> [!Important]
> IPv6 voor Azure Virtual Network is momenteel in openbare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>IPv6-voorvoegselgroottes
De volgende openbare IP-voorvoegselformaten zijn beschikbaar:

-  Minimale IPv6-voorvoegselgrootte: /127 = 2 adressen
-  Maximale IPv6-voorvoegselgrootte: /124 = 16 adressen

De grootte van het voorvoegsel is opgegeven als cidr-maskergrootte (Classless Inter-Domain Routing). Een masker van /128 vertegenwoordigt bijvoorbeeld een individueel IPv6-adres, omdat IPv6-adressen uit 128 bits bestaan.

## <a name="pricing"></a>Prijzen
 
Zie Prijzen voor openbare [IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses/)voor kosten die zijn gekoppeld aan het gebruik van Azure Public IP-adressen, zowel afzonderlijke IP-adressen als IP-bereiken.

## <a name="limitations"></a>Beperkingen
IPv6 wordt alleen ondersteund op basisip-adressen van openbare instellingen met 'dynamische' toewijzing, wat betekent dat het IPv6-adres wordt gewijzigd als u uw toepassing (VM's of load balancers) in Azure verwijdert en opnieuw implementeert. Standaard IPv6 Public IP's ondersteuning uitsluitend statische (gereserveerde) toewijzing hoewel Standaard INTERNE load balancers kunnen ook ondersteuning voor dynamische toewijzing vanuit het subnet waaraan ze zijn toegewezen.  

Als best practice raden we u aan standaard ip's en standaardloadbalansen te gebruiken voor uw IPv6-toepassingen.

## <a name="next-steps"></a>Volgende stappen
- Reserveer een openbaar [IPv6-adresvoorvoegsel](ipv6-reserve-public-ip-address-prefix.md).
- Meer informatie over [IPv6-adressen](ipv6-overview.md).
- Meer informatie over [het maken en gebruiken van openbare IP's](virtual-network-public-ip-address.md) (zowel IPv4 als IPv6) in Azure.
