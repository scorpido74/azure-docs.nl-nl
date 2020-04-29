---
title: Publicatie handleiding voor Azure Applications Managed Application aanbieding | Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een beheerde toepassing in azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084869"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure-toepassingen: publicatie vereisten voor beheerde toepassingen bieden

In dit artikel worden de vereisten voor het type beheerde toepassings aanbieding beschreven. Dit is een manier om een Azure-toepassings aanbieding te publiceren in azure Marketplace. Beheerde toepassingen zijn Transact-aanbiedingen die worden geïmplementeerd en gefactureerd via Azure Marketplace. De aanroep van een actie die een gebruiker ziet is ' nu ophalen '.

Gebruik het type beheerde toepassing-aanbieding wanneer de volgende voor waarden zijn vereist:

- U implementeert een oplossing op basis van een abonnement voor uw klant met behulp van een virtuele machine of een volledige oplossing op basis van IaaS.
- U of uw klant vereisen dat de oplossing wordt beheerd door een partner.

>[!NOTE]
>Een partner kan bijvoorbeeld een SI-of managed service provider (MSP) zijn.  

## <a name="managed-application-offer"></a>Aanbieding voor beheerde toepassing

|Vereisten |Details  |
|---------|---------|
|Geïmplementeerd in het Azure-abonnement van een klant | Beheerde apps moeten worden geïmplementeerd in het abonnement van de klant en kunnen worden beheerd door derden. |
|Facturering en meting    |  De resources worden ingericht in het Azure-abonnement van de klant. PAYGO-virtuele machines (betalen per gebruik) worden met de klant in rekening gebracht via micro soft, gefactureerd via het Azure-abonnement van de klant (PAYGO). <br> In het geval van uw eigen licentie kunt u, terwijl micro soft de infrastructuur kosten voor het abonnement van de klant in rekening brengt, uw software licentie kosten rechtstreeks aan de klant door geven.        |
|Azure-compatibele virtuele harde schijf (VHD)    |   Vm's moeten zijn gebouwd op Windows of Linux.<ul> <ul> <li>Zie [Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)voor meer informatie over het maken van een virtuele Linux-harde schijf.</li> <li>Zie [een Azure-toepassings aanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md)voor meer informatie over het maken van een Windows-VHD.</li> </ul> |

>[!NOTE]
> Beheerde apps moeten via Marketplace kunnen worden geïmplementeerd. Als de communicatie van de klant een probleem is, kunt u contact met geïnteresseerde klanten bereiken nadat u het delen van leads hebt ingeschakeld.  

>[!Note]
>Opt-in voor Cloud Solution Providers (CSP)-partner kanaal is nu beschikbaar. Zie [Cloud Solution Providers](./cloud-solution-providers.md)(Engelstalig) voor meer informatie over het marketing uw aanbod via de micro soft CSP-partner kanalen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over Azure Marketplace als u dit nog niet hebt gedaan.
- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- [Maak een Azure-toepassings aanbieding](./partner-center-portal/create-new-azure-apps-offer.md) voor meer informatie.
