---
title: Overzicht van de implementatie van Azure Firewall Manager preview
description: Meer informatie over de implementatie stappen op hoog niveau die vereist zijn voor de preview-versie van Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77443122"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Overzicht van de implementatie van Azure Firewall Manager preview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Er is meer dan één manier om Azure Firewall Manager-preview te implementeren, maar het volgende algemene proces wordt aanbevolen.

## <a name="general-deployment-process"></a>Algemeen implementatieproces

### <a name="hub-virtual-networks"></a>Virtuele hub-netwerken

1.  Een firewall beleid maken

    - Een nieuw beleid maken
<br>*of*<br>
    - Een basis beleid afleiden en een lokaal beleid aanpassen
<br>*of*<br>
    - Regels importeren uit een bestaande Azure Firewall. Zorg ervoor dat u de NAT-regels verwijdert uit beleid dat moet worden toegepast op meerdere firewalls
1. Uw hub-en spoke-architectuur maken
   - Een hub maken Virtual Network met behulp van Azure Firewall Manager-en peer Spaak virtuele netwerken met behulp van peering op virtueel netwerk
<br>*of*<br>
    - Maak een virtueel netwerk en voeg virtuele netwerk verbindingen en peer-spoke-netwerken toe met behulp van peering op virtueel netwerk

3. Selecteer beveiligings providers en koppel het firewall beleid. Momenteel is alleen Azure Firewall een ondersteunde provider.

   - Dit doet u tijdens het maken van een hub Virtual Network
<br>*of*<br>
    - Een bestaand virtueel netwerk converteren naar een hub Virtual Network. Het is ook mogelijk om meerdere virtuele netwerken te converteren.

4. Configureer de gebruiker om routes te definiëren om verkeer naar uw hub Virtual Network Firewall te routeren.


### <a name="secured-virtual-hubs"></a>Beveiligde virtuele hubs

1. Uw hub-en spoke-architectuur maken

   - Maak een beveiligde virtuele hub met behulp van Azure Firewall Manager en voeg virtuele netwerk verbindingen toe.<br>*of*<br>
   - Maak een virtuele WAN-hub en voeg virtuele netwerk verbindingen toe.
2. Beveiligings providers selecteren

   - Klaar tijdens het maken van een beveiligde virtuele hub.<br>*of*<br>
   - Een bestaande virtuele WAN-hub converteren naar een beveiligde virtuele hub.
3. Een firewall beleid maken en dit koppelen aan uw hub

   - Alleen van toepassing als Azure Firewall wordt gebruikt.
   - SECaaS-beleids regels (Security as a Service) van derden worden geconfigureerd via de beheer ervaring voor partners.
4. Router instellingen configureren om verkeer door te sturen naar uw beveiligde hub

   - Stuur eenvoudig verkeer naar uw beveiligde hub voor filteren en logboek registratie zonder door de gebruiker gedefinieerde routes (UDR) op spoke Virtual Networks met behulp van de instellings pagina route-instellingen voor beveiligde virtuele hub.

> [!NOTE]
> - U kunt niet meer dan één hub per virtuele WAN per regio hebben. Maar u kunt meerdere virtuele Wan's in de regio toevoegen om dit te doen.
> - U kunt geen overlappende IP-ruimten voor hubs in een vWAN hebben.
> - Uw hub VNet-verbindingen moeten zich in dezelfde regio bevinden als de hub.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: uw Cloud netwerk beveiligen met Azure Firewall Manager Preview met behulp van de Azure Portal](secure-cloud-network.md)