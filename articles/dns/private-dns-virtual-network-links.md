---
title: Wat is een virtuele netwerk koppeling subresource van Azure DNS privé zones
description: Overzicht van virtuele netwerk koppeling subresource a Azure DNS privé zone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75646758"
---
# <a name="what-is-a-virtual-network-link"></a>Wat is een virtuele netwerk koppeling?

Zodra u een privé-DNS-zone hebt gemaakt in azure, is deze niet direct toegankelijk vanuit een virtueel netwerk. U moet deze koppelen aan een virtueel netwerk voordat een VM die wordt gehost in dat netwerk toegang heeft tot de privé-DNS-zone.
Als u een privé-DNS-zone wilt koppelen aan een virtueel netwerk, moet u een koppeling maken met een virtueel netwerk in de privé-DNS-zone. Elke privé-DNS-zone heeft een verzameling onderliggende resources van een virtuele netwerk koppeling. Elk van deze bronnen vertegenwoordigt een verbinding met een virtueel netwerk.

U kunt een virtueel netwerk koppelen aan een privé-DNS-zone als een registratie-virtueel netwerk of als een virtueel netwerk voor omzetting.

## <a name="registration-virtual-network"></a>Virtuele registratie-netwerk

Wanneer u [een koppeling maakt](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) tussen een privé-DNS-zone en een virtueel netwerk, hebt u de mogelijkheid om de [registratie](./private-dns-autoregistration.md) van DNS-records voor virtuele machines in te scha kelen. Als u deze optie kiest, wordt het virtuele netwerk een virtueel netwerk voor registratie voor de privé-DNS-zone. Er wordt automatisch een DNS-record gemaakt voor de virtuele machines die u in het netwerk implementeert. DNS-records worden gemaakt voor de virtuele machines die u al in het virtuele netwerk hebt geïmplementeerd. Vanuit het perspectief van het virtuele netwerk wordt de particuliere DNS-zone de registratie zone voor dat virtuele netwerk.
Eén privé-DNS-zone kan meerdere virtuele registratie-netwerken hebben, maar aan elk virtueel netwerk kan slechts één registratie zone zijn gekoppeld.

## <a name="resolution-virtual-network"></a>Omzetting van virtueel netwerk

Wanneer u een koppeling met een virtueel netwerk maakt in een persoonlijke DNS-zone en ervoor kiest om de registratie van de DNS-record niet in te scha kelen, wordt het virtuele netwerk beschouwd als een virtueel netwerk met alleen een oplossing. DNS-records voor virtuele machines die in dergelijke netwerken worden geïmplementeerd, worden niet automatisch in de gekoppelde persoonlijke DNS-zone gemaakt. De virtuele machines die in een dergelijk netwerk zijn geïmplementeerd, kunnen echter wel een query uitvoeren op de DNS-records van de privé-DNS-zone. Deze records kunnen hand matig worden gemaakt door u of kunnen worden gevuld met andere virtuele netwerken die zijn gekoppeld als registratie netwerken met de privé-DNS-zone.
Eén privé-DNS-zone kan meerdere virtuele omzettings netwerken hebben en er kunnen meerdere oplossings zones aan een virtueel netwerk zijn gekoppeld.

## <a name="limits"></a>Limieten

Als u wilt weten hoeveel registratie-en oplossings netwerken u kunt koppelen aan privé-DNS-zones, raadpleegt u [Azure DNS limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Andere overwegingen

* Virtuele netwerken die zijn geïmplementeerd met het klassieke implementatie model, worden niet ondersteund.

* U kunt slechts één koppeling maken tussen een privé-DNS-zone en een virtueel netwerk.

* Elke virtuele netwerk koppeling onder een privé-DNS-zone moet een unieke naam hebben binnen de context van de privé-DNS-zone. U kunt links met dezelfde naam in verschillende particuliere DNS-zones hebben.

* Nadat u een koppeling met een virtueel netwerk hebt gemaakt, controleert u het veld koppelings status van de resource van de koppeling van het virtuele netwerk. Afhankelijk van de grootte van het virtuele netwerk, kan het enkele minuten duren voordat de koppeling is gemaakt en de status van de koppeling is gewijzigd in *voltooid*.

* Wanneer u een virtueel netwerk verwijdert, worden alle koppelingen naar het virtuele netwerk en automatisch geregistreerde DNS-records die zijn gekoppeld aan deze in verschillende particuliere DNS-zones automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het koppelen van een virtueel netwerk aan een privé-DNS-zone met behulp van [Azure Portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Meer informatie over het maken van een privé zone in Azure DNS met behulp van [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure cli](./private-dns-getstarted-cli.md).

* Meer informatie over enkele veelvoorkomende [scenario's voor particuliere zones](./private-dns-scenarios.md) die kunnen worden gerealiseerd met persoonlijke zones in azure DNS.

* Voor veelgestelde vragen en antwoorden over persoonlijke zones in Azure DNS, met inbegrip van specifieke gedrag dat u kunt verwachten voor bepaalde soorten bewerkingen, raadpleegt u [privé-DNS Veelgestelde vragen](./dns-faq-private.md).
