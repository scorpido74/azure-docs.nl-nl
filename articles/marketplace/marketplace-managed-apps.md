---
title: Publicatiegids voor beheerde toepassingsaanbiedingen voor Azure-toepassingen
description: In dit artikel worden de vereisten beschreven om een beheerde toepassing in de Marketplace te publiceren
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687569"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure-toepassingen: publicatiegids voor beheerde toepassingsaanbiedingen

Een beheerde toepassing is een van de belangrijkste manieren om een oplossing in de Marketplace te publiceren. Gebruik deze handleiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Dit zijn transactieaanbiedingen die worden geïmplementeerd en gefactureerd via de Marketplace. De call-to-action die een gebruiker ziet is 'Nu halen'.

Gebruik de Azure-app: beheerde app-aanbiedingstype wanneer de volgende voorwaarden vereist zijn:
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
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](./cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

## <a name="next-steps"></a>Volgende stappen

Als je dat nog niet gedaan hebt, 

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over de marktplaats.

Als u zich wilt registreren in partnercentrum, begint u met het maken van een nieuwe aanbieding of werkt u aan een bestaand aanbieding:

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
- Zie [een Azure-toepassingsaanbieding maken](./partner-center-portal/create-new-azure-apps-offer.md) voor meer informatie.
