---
title: Subnetextensie in Azure | Microsoft Documenten
description: Meer informatie over subnetextensie in Azure.
services: virtual-network
documentationcenter: na
author: anupam-p
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2019
ms.author: anupand
ms.openlocfilehash: f718471c3f79e9a33b0e03b088f8c8d2ae0231d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587509"
---
# <a name="subnet-extension"></a>Subnetextensie
Workloadmigratie naar de public cloud vereist een zorgvuldige planning en coördinatie. Een van de belangrijkste overwegingen kan de mogelijkheid zijn om uw IP-adressen te behouden. Dat kan vooral belangrijk zijn als uw toepassingen ip-adresafhankelijkheid hebben of als u nalevingsvereisten hebt om specifieke IP-adressen te gebruiken. Azure Virtual Network lost dit probleem voor u op door u vnet en subnetten te laten maken met behulp van een IP-adresbereik naar keuze.

Migraties kunnen een beetje uitdagend worden wanneer de bovenstaande vereiste is gekoppeld aan een extra vereiste om sommige toepassingen on-premises te houden. In een situatie moet u de toepassingen splitsen tussen Azure en on-premises, zonder de IP-adressen aan weerszijden opnieuw te nummeren. Bovendien moet u toestaan dat de toepassingen communiceren alsof ze zich in hetzelfde netwerk bevinden.

Een oplossing voor het bovenstaande probleem is subnet extensie. Door een netwerk uit te breiden, kunnen toepassingen over hetzelfde uitzenddomein praten wanneer ze op verschillende fysieke locaties bestaan, waardoor de noodzaak om uw netwerktopologie opnieuw te ontwerpen wordt voorkomen. 

Hoewel het uitbreiden van uw netwerk in het algemeen geen goede praktijk is, kunnen onderstaande use cases het noodzakelijk maken.

- **Gefaseerde migratie**: Het meest voorkomende scenario is dat u uw migratie wilt faseren. U wilt eerst een paar toepassingen en na verloop van tijd de rest van de toepassingen migreren naar Azure.
- **Latentie**: Lage latentievereisten kunnen een andere reden zijn om sommige toepassingen on-premises te houden om ervoor te zorgen dat ze zo dicht mogelijk bij uw datacenter staan.
- **Naleving:** Een andere use case is dat u mogelijk nalevingsvereisten hebt om sommige van uw toepassingen on-premises te houden.
 
> [!NOTE] 
> U moet uw subnetten niet uitbreiden, tenzij het nodig is. In de gevallen waarin u uw subnetten uitbreidt, moet u proberen er een tussenstap van te maken. Probeer na verloop van tijd toepassingen opnieuw te nummeren in uw on-premises netwerk en migreer ze naar Azure.

In het volgende gedeelte bespreken we hoe u uw subnetten uitbreiden naar Azure.


## <a name="extend-your-subnet-to-azure"></a>Uw subnet uitbreiden naar Azure
 U uw on-premises subnetten uitbreiden naar Azure met behulp van een op laag-3 overlaynetwerkgebaseerde oplossing. De meeste oplossingen maken gebruik van een overlay-technologie zoals VXLAN om het layer-2-netwerk uit te breiden met behulp van een layer-3 overlay-netwerk. Het onderstaande diagram toont een algemene oplossing. In deze oplossing bestaat aan beide zijden hetzelfde subnet, dat wil zeggen Azure en on-premises. 

![Voorbeeld van subnetextensie](./media/subnet-extension/subnet-extension.png)

De IP-adressen van het subnet worden toegewezen aan VM's in Azure en on-premises. Zowel Azure als on-premises hebben een NVA ingevoegd in hun netwerken. Wanneer een VM in Azure probeert te praten met een VM in on-premises netwerk, vangt de Azure NVA het pakket op, kapselt het in en stuurt het via VPN/Express Route naar het on-premises netwerk. De on-premises NVA ontvangt het pakket, decapuleert het en stuurt het door naar de beoogde ontvanger in haar netwerk. Het retourverkeer maakt gebruik van een vergelijkbaar pad en logica.

In het bovenstaande voorbeeld communiceren en leren de Azure NVA en de on-premises NVA over IP-adressen achter elkaar. Complexere netwerken kunnen ook een kaartservice hebben, die de mapping tussen de NVA's en de IP-adressen erachter in kaart brengt. Wanneer een NVA een pakket ontvangt, wordt de kaartservice gevraagd om het adres van de NVA te achterhalen waarop het doel-IP-adres achter zich ligt.

In het volgende gedeelte vindt u meer informatie over subnetextensieoplossingen die we op Azure hebben getest.

## <a name="next-steps"></a>Volgende stappen 
[Breid uw subnet uit naar Azure met behulp van leveranciersoplossingen.](https://github.com/microsoft/Azure-LISP)