---
title: Overzicht van het virtuele netwerk van Azure Microsoft Docs
description: Meer informatie over het tikken van een virtueel netwerk. Met het virtuele netwerk tikken kunt u een diepe kopie maken van het netwerk verkeer van de virtuele machine die kan worden gestreamd naar een pakket verzamelaar.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 47db03460ad3c5194a5445f0b25cb8e742e60c21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279609"
---
# <a name="virtual-network-tap"></a>Virtual Network TAP

Met het virtuele netwerk van Azure (Terminal Access Point) kunt u het netwerk verkeer van de virtuele machine continu streamen naar een netwerk pakket verzamelaar of een analyse programma. Het hulp programma Collector of Analytics wordt verschaft door een [virtuele netwerk apparaat](https://azure.microsoft.com/solutions/network-appliances/) -partner. Zie [partner oplossingen](#virtual-network-tap-partner-solutions)voor een lijst met partner oplossingen die zijn gevalideerd om te werken met Virtual Network tikken.

> [!IMPORTANT]
> Virtual Network TIKT is momenteel beschikbaar als preview in alle Azure-regio's. Als u een virtueel netwerk wilt gebruiken, moet u zich inschrijven voor het voor beeld door <azurevnettap@microsoft.com> een e-mail te verzenden naar met uw abonnements-id. U ontvangt een e-mailbevestiging zodra uw abonnement is geregistreerd. U kunt de mogelijkheid niet gebruiken totdat u een bevestigings-e-mail ontvangt. Deze preview is beschikbaar zonder service level agreement en mag niet worden gebruikt voor werk belastingen voor de productie. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) -voor beelden voor meer informatie.

## <a name="virtual-network-tap-partner-solutions"></a>Virtueel netwerk TIKT u op partner oplossingen

### <a name="network-packet-brokers"></a>Netwerkpakket Brokers

- [Big-Switch infrastructuur Big](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>Beveiligings analyse, beheer van netwerk/toepassings prestaties

- [Wakker-beveiliging](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch-Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cyber beveiliging](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [Netfort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [® Platform voor RSA-netwitness](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

In de volgende afbeelding ziet u hoe virtuele netwerk tikken werkt. U kunt een TAP-configuratie toevoegen aan een [netwerk interface](virtual-network-network-interface.md) die is gekoppeld aan een virtuele machine die is geïmplementeerd in uw virtuele netwerk. De bestemming is een IP-adres van een virtueel netwerk in hetzelfde virtuele netwerk als de bewaakte netwerk interface of een gekoppeld [virtueel](virtual-network-peering-overview.md) netwerk. De Collector-oplossing voor Virtual Network TIKT kan worden geïmplementeerd achter een interne Load Balancer van Azure voor hoge Beschik baarheid. Zie [partner oplossingen](#virtual-network-tap-partner-solutions)voor meer informatie over het evalueren van implementatie opties voor afzonderlijke oplossingen.

![Hoe Virtual Network TIKT werkt](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Vereisten

Voordat u een virtueel netwerk maakt, moet u een bevestigings bericht hebben ontvangen dat u in de preview hebt Inge schreven en dat er een of meer virtuele machines zijn gemaakt met [Azure Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) implementatie model en een partner oplossing voor het samen voegen van het tikken verkeer in dezelfde Azure-regio. Als u geen partner oplossing in uw virtuele netwerk hebt, raadpleegt u [partner oplossingen](#virtual-network-tap-partner-solutions) om er een te implementeren. U kunt hetzelfde virtuele netwerk tikken op resource om verkeer van meerdere netwerk interfaces in dezelfde of verschillende abonnementen samen te voegen. Als de bewaakte netwerk interfaces zich in verschillende abonnementen bevinden, moeten de abonnementen zijn gekoppeld aan dezelfde Azure Active Directory Tenant. Daarnaast kunnen de bewaakte netwerk interfaces en het bestemmings eindpunt voor het samen voegen van het TIKs verkeer zich in gekoppelde virtuele netwerken in dezelfde regio bevinden. Als u dit implementatie model gebruikt, moet u ervoor zorgen dat de [peering van het virtuele netwerk](virtual-network-peering-overview.md) is ingeschakeld voordat u tikt op het virtuele netwerk.

## <a name="permissions"></a>Machtigingen

De accounts die u gebruikt voor het Toep assen van TIKT op netwerk interfaces, moeten worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de nodige acties zijn toegewezen in de volgende tabel:

| Bewerking | Naam |
|---|---|
| Micro soft. Network/virtualNetworkTaps/* | Vereist voor het maken, bijwerken, lezen en verwijderen van een virtueel netwerk Tik op resource |
| Micro soft. Network/networkInterfaces/lezen | Vereist voor het lezen van de netwerk interface bron waarop de Tik wordt geconfigureerd |
| Micro soft. Network/tapConfigurations/* | Vereist voor het maken, bijwerken, lezen en verwijderen van de TAP-configuratie op een netwerk interface |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een virtueel netwerk tikken](tutorial-tap-virtual-network-cli.md).
