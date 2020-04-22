---
title: Publicatiegids voor virtuele machines voor Azure Marketplace
description: In dit artikel worden de vereisten beschreven voor het publiceren van een virtuele machine en een gratis proefversie van de software die vanaf de Marketplace moet worden geïmplementeerd.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687502"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Publicatiegids voor virtuele machines

Afbeeldingen van virtuele machines zijn een van de belangrijkste manieren om een oplossing in de Azure Marketplace te publiceren. Gebruik deze handleiding om inzicht te krijgen in de vereisten voor deze aanbieding. 

Dit zijn transactieaanbiedingen die worden geïmplementeerd en gefactureerd via de Marketplace. De call-to-action die een gebruiker ziet is 'Nu halen'.

## <a name="free-trial"></a>Gratis proefversie 

U ervoor zorgen dat gebruikers uw aanbieding testen door toegang te krijgen tot softwarelicenties voor beperkte termijn wanneer u het BYOL-factureringsmodel (Bring Your Own License) gebruikt. 

## <a name="test-drive"></a>Test Drive

U implementeert een of meer virtuele machines via infrastructure-as-a-service (IaaS) of software-as-a-service (SaaS) apps. Een voordeel van de publicatieoptie voor teststations is de geautomatiseerde inrichting van een virtuele machine of een volledige oplossing onder leiding van een door een partner gehoste rondleiding. Een testrit biedt een evaluatie zonder extra kosten voor uw klant. Uw klant hoeft geen bestaande Azure-klant te zijn om de testervaring te gebruiken. 

Neem contact met ons op [bij amp-testdrive](mailto:amp-testdrive@microsoft.com) om aan de slag te gaan. 

|Vereisten  |Details |
|---------|---------|
| Je hebt een Marketplace-app   |    Een of meer virtuele machines via IaaS of SaaS.      |

## <a name="interactive-demo"></a>Interactieve demo

U zorgt voor een begeleide ervaring van uw oplossing aan uw klanten door middel van een interactieve demonstratie. Het voordeel van interactieve demo publicatie optie is dat u een proefervaring zonder ingewikkelde inrichting van uw complexe oplossing. 

## <a name="virtual-machine-offer"></a>Virtuele machine aanbieding

Gebruik het type virtuele machineaanbieding wanneer u een virtueel toestel implementeert op het abonnement dat aan uw klant is gekoppeld. VM's zijn volledig commerce ingeschakeld met behulp van pay-as-you-go of bring-your-own-license (BYOL) licentiemodellen. Microsoft host de handelstransactie en factureert uw klant namens u. U profiteert van het gebruik van de gewenste betalingsrelatie tussen uw klant en Microsoft, inclusief eventuele Enterprise Agreements.

> [!NOTE]
> Op dit moment kunnen de monetaire verplichtingen die aan een Enterprise Agreement zijn gekoppeld, worden gebruikt tegen het Azure-gebruik van uw VM, maar niet tegen uw softwarelicentiekosten.  
> 
> [!NOTE]
> U de detectie en implementatie van uw VM beperken tot een specifieke set klanten door de afbeelding en prijzen te publiceren als een privé-aanbieding. Privéaanbiedingen ontgrendelen de mogelijkheid om exclusieve aanbiedingen voor uw naaste klanten te maken en aangepaste software en voorwaarden aan te bieden. De aangepaste termen stellen u in staat om een verscheidenheid aan scenario's te markeren, waaronder veldgestuurde deals met gespecialiseerde prijzen en termen, evenals vroege toegang tot beperkte releasesoftware. Met privéaanbiedingen u specifieke prijzen of producten aan een beperkt aantal klanten geven door een nieuwe SKU met die gegevens te maken.  
> *   Ga voor meer informatie over privéaanbiedingen naar de pagina Privéaanbiedingen op Azure Marketplace op [azure.microsoft.com/blog/private-offers-on-azure-marketplace.](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)  

| Vereiste | Details |  
|:--- |:--- | 
| Facturering en meting | Uw vm moet byol of pay-as-you-go maandelijkse facturering ondersteunen. |  
| Azure-compatibele virtuele harde schijf (VHD) | VM's moeten worden gebouwd op Windows of Linux. <ul> <li>Zie [Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)voor meer informatie over het maken van een Linux VHD.</li> <li>Zie Een VHD maken voor meer informatie over het maken van een Windows [VHD.](./partner-center-portal/azure-vm-create-offer.md)</li> </ul> |  

>[!Note]
>Cloud Solution Providers (CSP) partnerkanaal opt-in is nu beschikbaar.  Raadpleeg [Cloud Solution Providers](./cloud-solution-providers.md) voor meer informatie over het op de markt brengen van uw aanbieding via de Microsoft CSP-partnerkanalen.

## <a name="next-steps"></a>Volgende stappen

Als je dat nog niet gedaan hebt, 

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over de marktplaats.

Als u bent geregistreerd en een nieuwe aanbieding maakt of aan een bestaand aanbod werkt,

- [Meld u aan bij Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te voltooien.
- Zie [een virtuele machineaanbieding maken](./partner-center-portal/azure-vm-create-offer.md) voor meer informatie.
