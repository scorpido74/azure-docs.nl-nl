---
title: Publicatie handleiding voor Azure Applications Managed Application aanbieding
description: In dit artikel worden de vereisten beschreven voor het publiceren van een beheerde toepassing in Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: MaggiePucciEvans
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/14/2018
ms.author: evansma
ms.openlocfilehash: 764212ac148b336b07d29c29a72314c5d889d47c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934652"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-toepassingen: publicatie handleiding voor het aanbieden van beheerde toepassingen

Een beheerde toepassing is een van de belangrijkste manieren om een oplossing op Marketplace te publiceren. Gebruik deze hand leiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Dit zijn transactie aanbiedingen die worden geïmplementeerd en gefactureerd via Marketplace. De aanroep van een actie die een gebruiker ziet is ' nu ophalen '.

Gebruik het aanbiedings type Azure-app: beheerde app wanneer de volgende voor waarden zijn vereist:
- U implementeert een oplossing op basis van een abonnement voor uw klant met behulp van een virtuele machine of een volledige oplossing op basis van IaaS.
- U of uw klant vereisen dat de oplossing wordt beheerd door een partner.

>[!NOTE]
>Een partner kan bijvoorbeeld een SI-of managed service provider (MSP) zijn.  

## <a name="managed-application-offer"></a>Aanbieding voor beheerde toepassing

|Vereisten |Details  |
|---------|---------|
|Geïmplementeerd in het Azure-abonnement van een klant | Beheerde apps moeten worden geïmplementeerd in het abonnement van de klant en kunnen worden beheerd door derden. | 
|Facturering en meting    |  De resources worden ingericht in het Azure-abonnement van de klant. PAYGO-virtuele machines (betalen per gebruik) worden met de klant in rekening gebracht via micro soft, gefactureerd via het Azure-abonnement van de klant (PAYGO). <br> In het geval van uw eigen licentie kunt u, terwijl micro soft de infrastructuur kosten voor het abonnement van de klant in rekening brengt, uw software licentie kosten rechtstreeks aan de klant door geven.        |
|Azure-compatibele virtuele harde schijf (VHD)    |   Vm's moeten zijn gebouwd op Windows of Linux.<ul> <ul> <li>Zie [Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)voor meer informatie over het maken van een virtuele Linux-harde schijf.</li> <li>Zie [een virtuele harde schijf maken die compatibel is met Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)voor meer informatie over het maken van een Windows VHD.</li> </ul> |

>[!NOTE]
> Beheerde apps moeten via Marketplace kunnen worden geïmplementeerd. Als de communicatie van de klant een probleem is, kunt u contact met geïnteresseerde klanten bereiken nadat u het delen van leads hebt ingeschakeld.  

>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](./cloud-solution-providers.md) voor meer informatie over het marketing gebruik van uw aanbieding via de micro soft CSP-partner kanalen.

## <a name="next-steps"></a>Volgende stappen
Als u dit nog niet hebt gedaan, 

- [Registreer](https://azuremarketplace.microsoft.com/sell) u in de Marketplace.

Als u bent geregistreerd en een nieuwe aanbieding maakt of aan een bestaand item werkt,

- [Meld u aan bij de Cloud Partner-Portal](https://cloudpartner.azure.com) om uw aanbieding te maken of te volt ooien.
