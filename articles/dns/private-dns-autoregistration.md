---
title: Wat is de functie voor automatische registratie van Azure DNS-privézones
description: Overzicht van de functie voor automatische registratie van Azure DNS-privézones
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961239"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Wat is de functie voor automatische registratie van Azure DNS-privézones

De autoregistratiefunctie azure DNS-privézones neemt de pijn weg van DNS-recordbeheer voor virtuele machines die in een virtueel netwerk worden geïmplementeerd. Wanneer u [een virtueel netwerk koppelt aan](./private-dns-virtual-network-links.md) een privé-DNS-zone en automatische registratie inschakelt voor alle virtuele machines, worden de DNS-records voor de virtuele machines die in het virtuele netwerk worden geïmplementeerd, automatisch gemaakt in de privé-DNS-zone. Naast forward forward records (A records) worden reverse lookup records (PTR records) ook automatisch gemaakt voor de virtuele machines.
Als u meer virtuele machines aan het virtuele netwerk toevoegt, worden DNS-records voor deze virtuele machines ook automatisch gemaakt in de gekoppelde privé-DNS-zone.

Wanneer u een virtuele machine verwijdert, worden de DNS-records voor de virtuele machine automatisch verwijderd uit de privé-DNS-zone.

U automatische registratie inschakelen door de optie 'Automatische registratie inschakelen' te selecteren tijdens het maken van een virtuele netwerkkoppeling.

![Automatische registratie inschakelen](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Beperkingen

* Automatisch registreren werkt alleen voor virtuele machines. Voor alle andere bronnen, zoals interne load balancers enz., u dns-records handmatig maken in de privé-DNS-zone die is gekoppeld aan het virtuele netwerk.
* DNS-records worden alleen automatisch gemaakt voor de primaire virtuele machine NIC . Als uw virtuele machines meer dan één NIC hebben, u de DNS-records voor andere netwerkinterfaces handmatig maken.
* automatische registratie voor IPv6 (AAAA-records) wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privézone in Azure DNS met [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over enkele veelvoorkomende [privézonescenario's](./private-dns-scenarios.md) die kunnen worden gerealiseerd met privézones in Azure DNS.

* Zie [Veelgestelde vragen](./dns-faq-private.md)over privé-informatie over privézones in Azure DNS, inclusief specifiek gedrag dat u voor bepaalde soorten bewerkingen verwachten.
