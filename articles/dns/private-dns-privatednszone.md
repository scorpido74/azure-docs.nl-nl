---
title: Wat is een Azure DNS privé zone?
description: Overzicht van een privé-DNS-zone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 462c873221a4bdc622a9b118b6699a9719a5f88d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961206"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Wat is een privé Azure DNS zone

Azure Privé-DNS biedt een betrouw bare, veilige DNS-service voor het beheren en omzetten van domein namen in een virtueel netwerk zonder dat u een aangepaste DNS-oplossing hoeft toe te voegen. Met behulp van privé-DNS-zones kunt u uw eigen aangepaste domein namen gebruiken in plaats van de door Azure meegeleverde namen die vandaag beschikbaar zijn. 

De records in een privé-DNS-zone kunnen niet van het Internet worden omgezet. DNS-omzetting voor een privé-DNS-zone werkt alleen vanuit virtuele netwerken die eraan zijn gekoppeld.

U kunt een privé-DNS-zone koppelen aan een of meer virtuele netwerken door koppelingen naar het [virtuele netwerk](./private-dns-virtual-network-links.md)te maken.
U kunt ook de functie voor [automatische registratie](./private-dns-autoregistration.md) inschakelen voor het automatisch beheren van de levens cyclus van de DNS-records voor de virtuele machines die zijn geïmplementeerd in een virtueel netwerk.

## <a name="limits"></a>Limieten

Zie [Azure DNS limieten](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) voor meer informatie over het aantal privé-DNS-zones dat u kunt maken in een abonnement en hoeveel record sets worden ondersteund in een privé-DNS-zone

## <a name="restrictions"></a>Beperkingen

* Enkelvoudige privé-DNS-zones worden niet ondersteund. Uw privé-DNS-zone moet twee of meer labels hebben. Bijvoorbeeld contoso.com heeft twee labels gescheiden door een punt. Een persoonlijke DNS-zone kan een maximum van 34 labels hebben.
* U kunt geen zone overdrachten (NS-records) maken in een privé-DNS-zone. Als u van plan bent een onderliggend domein te gebruiken, kunt u het domein rechtstreeks als een persoonlijke DNS-zone maken en koppelen aan een virtueel netwerk zonder een naam server-delegering vanuit de bovenliggende zone in te stellen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privé zone in Azure DNS met behulp van [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure cli](./private-dns-getstarted-cli.md).

* Meer informatie over enkele veelvoorkomende [scenario's voor particuliere zones](./private-dns-scenarios.md) die kunnen worden gerealiseerd met persoonlijke zones in azure DNS.

* Voor veelgestelde vragen en antwoorden over persoonlijke zones in Azure DNS, met inbegrip van specifieke gedrag dat u kunt verwachten voor bepaalde soorten bewerkingen, raadpleegt u [privé-DNS Veelgestelde vragen](./dns-faq-private.md).
