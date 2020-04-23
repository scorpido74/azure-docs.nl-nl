---
title: Publicatiehandleiding voor beheerde azure-toepassingen | Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een beheerde toepassing in de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084869"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure-toepassingen: beheerde toepassingsvereisten voor publicatie

In dit artikel worden de vereisten voor het type beheerde toepassingsaanbieding uitgelegd, wat een manier is om een Azure-toepassingsaanbieding in de Azure Marketplace te publiceren. Beheerde toepassingen zijn transact-aanbiedingen die worden geïmplementeerd en gefactureerd via de Azure Marketplace. De call-to-action die een gebruiker ziet is 'Nu halen'.

Gebruik het type beheerde aanbiedingsaanbieding wanneer de volgende voorwaarden vereist zijn:

- U implementeert een op abonnementen gebaseerde oplossing voor uw klant met behulp van een VM of een volledige IaaS-oplossing.
- U of uw klant eist dat de oplossing wordt beheerd door een partner.

>[!NOTE]
>Een partner kan bijvoorbeeld een SI- of managed service provider (MSP) zijn.  

## <a name="managed-application-offer"></a>Aangeboden beheerde toepassing

|Vereisten |Details  |
|---------|---------|
|Geïmplementeerd in het Azure-abonnement van een klant | Beheerde apps moeten worden geïmplementeerd in het abonnement van de klant en kunnen worden beheerd door een derde partij. |
|Facturering en meting    |  De resources worden ingericht in het Azure-abonnement van de klant. Pay-as-you-go (PAYGO) virtuele machines worden afgehandeld met de klant via Microsoft, gefactureerd via het Azure-abonnement (PAYGO) van de klant. <br> In het geval van bring-your-own-license, terwijl Microsoft de infrastructuurkosten voor het klantabonnement in rekening brengt, voert u uw softwarelicentiekosten rechtstreeks uit aan de klant.        |
|Azure-compatibele virtuele harde schijf (VHD)    |   VM's moeten worden gebouwd op Windows of Linux.<ul> <ul> <li>Zie [Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)voor meer informatie over het maken van een Linux VHD.</li> <li>Zie [Een Azure-toepassingsaanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md)voor meer informatie over het maken van een Windows VHD.</li> </ul> |

>[!NOTE]
> Beheerde apps moeten kunnen worden geïmplementeerd via de Marketplace. Als klantcommunicatie een punt van zorg is, moet u contact opnemen met geïnteresseerde klanten nadat u het delen van leaden hebt ingeschakeld.  

>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar. Zie [Cloud Solution Providers](./cloud-solution-providers.md)voor meer informatie over het op de markt brengen van uw aanbod via de Microsoft CSP-partnerkanalen.

## <a name="next-steps"></a>Volgende stappen

- Als u dit nog niet hebt gedaan, [leest u meer](https://azuremarketplace.microsoft.com/sell) over de Azure Marketplace.
- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
- [Maak een Azure-toepassingsaanbieding](./partner-center-portal/create-new-azure-apps-offer.md) voor meer informatie.
