---
title: Wat is een Azure DNS-privézone
description: Overzicht van een privé-DNS-zone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646792"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Wat is een privé-Azure DNS-zone

Azure Private DNS biedt een betrouwbare, veilige DNS-service voor het beheren en oplossen van domeinnamen in een virtueel netwerk zonder dat u een aangepaste DNS-oplossing hoeft toe te voegen. Door privé-DNS-zones te gebruiken, u uw eigen aangepaste domeinnamen gebruiken in plaats van de door Azure verstrekte namen die vandaag beschikbaar zijn. 

De records in een privé-DNS-zone zijn niet oplosbaar vanaf het internet. DNS-resolutie tegen een privé-DNS-zone werkt alleen vanuit virtuele netwerken die eraan zijn gekoppeld.

U een privé-DNS-zone koppelen aan een of meer virtuele netwerken door [virtuele netwerkkoppelingen](./private-dns-virtual-network-links.md)te maken.
U ook de functie voor automatische registratie inschakelen om automatisch de levenscyclus van de [DNS-records](./private-dns-autoregistration.md) voor de virtuele machines die in een virtueel netwerk worden geïmplementeerd, te beheren.

## <a name="limits"></a>Limieten

Als u wilt begrijpen hoeveel privé-DNS-zones u maken in een abonnement en hoeveel recordsets worden ondersteund in een privé-DNS-zone, [raadpleegt u Azure DNS-limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Beperkingen

* Private DNS-zones met één label worden niet ondersteund. Uw privé-DNS-zone moet twee of meer labels hebben. Bijvoorbeeld contoso.com twee labels heeft die door een stip worden gescheiden. Een private DNS-zone kan maximaal 34 labels hebben.
* U geen zonedelegaties (NS-records) maken in een privé-DNS-zone. Als u een onderliggend domein wilt gebruiken, u het domein rechtstreeks maken als een privé-DNS-zone en het koppelen aan een virtueel netwerk zonder een naamserverdelegatie vanuit de bovenliggende zone in te stellen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privézone in Azure DNS met [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over enkele veelvoorkomende [privézonescenario's](./private-dns-scenarios.md) die kunnen worden gerealiseerd met privézones in Azure DNS.

* Zie [Veelgestelde vragen](./dns-faq-private.md)over privé-informatie over privézones in Azure DNS, inclusief specifiek gedrag dat u voor bepaalde soorten bewerkingen verwachten.
