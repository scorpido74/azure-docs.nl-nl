---
title: Overzicht van de implementatie van Azure Firewall Manager Preview
description: Meer informatie over de implementatiestappen op hoog niveau die nodig zijn voor Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443122"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Overzicht van de implementatie van Azure Firewall Manager Preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Er is meer dan één manier om Azure Firewall Manager Preview te implementeren, maar het volgende algemene proces wordt aanbevolen.

## <a name="general-deployment-process"></a>Algemeen implementatieproces

### <a name="hub-virtual-networks"></a>Virtuele hubnetwerken

1.  Een firewallbeleid maken

    - Een nieuw beleid maken
<br>*Of*<br>
    - Een basisbeleid afleiden en een lokaal beleid aanpassen
<br>*Of*<br>
    - Regels importeren uit een bestaande Azure Firewall. Zorg ervoor dat NAT-regels worden verwijderd uit beleidsregels die op meerdere firewalls moeten worden toegepast
1. Maak uw hub en spaakarchitectuur
   - Een Hub Virtual Network maken met Azure Firewall Manager en peer spoke virtuele netwerken met behulp van virtuele netwerkpeering
<br>*Of*<br>
    - Maak een virtueel netwerk en voeg virtuele netwerkverbindingen en peer spoke virtuele netwerken toe met behulp van virtuele netwerkpeering

3. Selecteer beveiligingsproviders en associeer firewallbeleid. Momenteel is alleen Azure Firewall een ondersteunde provider.

   - Dit wordt gedaan terwijl u een Hub Virtual Network maakt
<br>*Of*<br>
    - Een bestaand virtueel netwerk converteren naar een Hub Virtual Network. Het is ook mogelijk om meerdere virtuele netwerken om te zetten.

4. Gebruikersdefinieerroutes configureren om verkeer naar uw Hub Virtual Network-firewall te leiden.


### <a name="secured-virtual-hubs"></a>Beveiligde virtuele hubs

1. Maak uw hub en spaakarchitectuur

   - Maak een beveiligde virtuele hub met Azure Firewall Manager en voeg virtuele netwerkverbindingen toe.<br>*Of*<br>
   - Maak een virtuele WAN-hub en voeg virtuele netwerkverbindingen toe.
2. Geselecteerde beveiligingsproviders

   - Gedaan tijdens het maken van een beveiligde virtuele hub.<br>*Of*<br>
   - Converteer een bestaande Virtual WAN Hub naar Secure Virtual Hub.
3. Een firewallbeleid maken en koppelen aan uw hub

   - Alleen van toepassing als u Azure Firewall gebruikt.
   - SECaaS-beleid (Security as a Service) van derden wordt geconfigureerd via de ervaring met het beheer van partners.
4. Route-instellingen configureren om verkeer naar uw beveiligde hub te leiden

   - Leid eenvoudig verkeer naar uw beveiligde hub voor het filteren en logboeken zonder UDR (User Defined Routes) op gesproken virtuele netwerken via de pagina Beveiligde virtuele hubrouteinstelling.

> [!NOTE]
> - U niet meer dan één hub per virtuele wan per regio hebben. Maar u meerdere virtuele WAN's in de regio toevoegen om dit te bereiken.
> - U geen overlappende IP-spaties voor hubs in een vWAN hebben.
> - Uw Hub VNet-verbindingen moeten zich in dezelfde regio bevinden als de hub.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Uw cloudnetwerk beveiligen met Azure Firewall Manager Preview met de Azure-portal](secure-cloud-network.md)