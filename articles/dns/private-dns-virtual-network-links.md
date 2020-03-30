---
title: Wat is een subbron van subbronnen voor virtuele netwerkkoppelingen van Azure DNS-privézones
description: Overzicht van subbron van virtuele netwerkkoppelingen een Azure DNS-privézone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646758"
---
# <a name="what-is-a-virtual-network-link"></a>Wat is een virtuele netwerkkoppeling?

Zodra u een privé-DNS-zone in Azure hebt gemaakt, is deze niet onmiddellijk toegankelijk vanuit een virtueel netwerk. U moet het koppelen aan een virtueel netwerk voordat een VM die in dat netwerk wordt gehost, toegang heeft tot de privé-DNS-zone.
Als u een privé-DNS-zone wilt koppelen aan een virtueel netwerk, moet u een virtuele netwerkkoppeling maken onder de privé-DNS-zone. Elke privé-DNS-zone heeft een verzameling onderliggende bronnen voor virtuele netwerkkoppelingen. Elk van deze bronnen vertegenwoordigt een verbinding met een virtueel netwerk.

U een virtueel netwerk koppelen aan een privé-DNS-zone als een virtueel registratienetwerk of als een virtueel netwerk voor resolutie.

## <a name="registration-virtual-network"></a>Registratie virtueel netwerk

Wanneer u [een koppeling maakt](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) tussen een privé-DNS-zone en een virtueel netwerk, hebt u een optie om automatische [registratie](./private-dns-autoregistration.md) van DNS-records voor virtuele machines in te schakelen. Als u deze optie kiest, wordt het virtuele netwerk een virtueel registratienetwerk voor de privé-DNS-zone. Er wordt automatisch een DNS-record gemaakt voor de virtuele machines die u in het netwerk implementeert. DNS-records worden gemaakt voor de virtuele machines die u al hebt geïmplementeerd in het virtuele netwerk. Vanuit het perspectief van het virtuele netwerk wordt de private DNS-zone de registratiezone voor dat virtuele netwerk.
Een private DNS-zone kan meerdere registratie virtuele netwerken hebben, maar elk virtueel netwerk kan precies een registratiezone gekoppeld aan het.

## <a name="resolution-virtual-network"></a>Virtueel netwerk oplossen

Wanneer u een virtuele netwerkkoppeling maakt onder een privé-DNS-zone en ervoor kiest om dns-recordregistratie niet in te schakelen, wordt het virtuele netwerk behandeld als een alleen virtueel netwerk met resolutie. DNS-records voor virtuele machines die in dergelijke netwerken worden geïmplementeerd, worden niet automatisch gemaakt in de gekoppelde privé-DNS-zone. De virtuele machines die in een dergelijk netwerk zijn geïmplementeerd, kunnen echter de DNS-records vanuit de privé-DNS-zone met succes opvragen. Deze records kunnen handmatig door u worden gemaakt of kunnen worden ingevuld vanuit andere virtuele netwerken die zijn gekoppeld als registratienetwerken met de privé-DNS-zone.
Eén privé-DNS-zone kan virtuele netwerken met meerdere resolutie's hebben en er kan meerdere resolutiezones aan zijn gekoppeld.

## <a name="limits"></a>Limieten

Als u wilt begrijpen hoeveel registratie- en oplossingsnetwerken, u een koppeling maken naar privé-DNS-zones zie [Azure DNS-limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Andere overwegingen

* Virtuele netwerken die worden geïmplementeerd met behulp van een klassiek implementatiemodel, worden niet ondersteund.

* U slechts één koppeling maken tussen een privé-DNS-zone en een virtueel netwerk.

* Elke virtuele netwerkkoppeling onder een privé-DNS-zone moet een unieke naam hebben in de context van de privé-DNS-zone. U koppelingen met dezelfde naam hebben in verschillende privé-DNS-zones.

* Controleer na het maken van een virtuele netwerkkoppeling het veld Koppelingsstatus van de virtuele netwerkkoppelingsbron. Afhankelijk van de grootte van het virtuele netwerk kan het enkele minuten duren voordat de koppeling wordt uitgevoerd en de koppelingsstatus wordt gewijzigd in *Voltooid*.

* Wanneer u een virtueel netwerk verwijdert, worden alle virtuele netwerkkoppelingen en automatisch geregistreerde DNS-records die eraan zijn gekoppeld in verschillende privé-DNS-zones automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het koppelen van een virtueel netwerk aan een privé-DNS-zone via [Azure-portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Meer informatie over het maken van een privézone in Azure DNS met [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over enkele veelvoorkomende [privézonescenario's](./private-dns-scenarios.md) die kunnen worden gerealiseerd met privézones in Azure DNS.

* Zie [Veelgestelde vragen](./dns-faq-private.md)over privé-informatie over privézones in Azure DNS, inclusief specifiek gedrag dat u voor bepaalde soorten bewerkingen verwachten.
