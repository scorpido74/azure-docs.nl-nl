---
title: Azure-Bastion | Microsoft Docs
description: Meer informatie over Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 4cee16a4b6d4f87c8f09ad742d9a504cfc5fc394
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498331"
---
# <a name="what-is-azure-bastion"></a>Wat is Azure Bastion?

De Azure Bastion-service is een nieuwe, volledig door het platform beheerde PaaS-service die u in uw virtuele netwerk hebt ingericht. Het biedt een veilige en naadloze RDP/SSH-verbinding met uw virtuele machines rechtstreeks in de Azure Portal via SSL. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP nodig.

 Bastion biedt veilige RDP-en SSH-connectiviteit voor alle Vm's in het virtuele netwerk waarin deze is ingericht. Met Azure Bastion kunt u uw virtuele machines geen RDP/SSH-poorten beschikbaar stellen aan de buiten wereld en toch beveiligde toegang bieden met behulp van RDP/SSH. Met Azure Bastion kunt u rechtstreeks vanuit de Azure Portal verbinding maken met de virtuele machine. U hebt geen aanvullende client, agent of software nodig.

## <a name="architecture"></a>Architectuur

Azure Bastion wordt geïmplementeerd in uw virtuele netwerk en biedt een beveiligde RDP/SSH-ervaring voor alle virtuele machines in uw virtuele netwerk, zodra deze is geïmplementeerd. Zodra u een Azure Bastion-service in uw virtuele netwerk hebt ingericht, is de RDP/SSH-ervaring beschikbaar voor al uw virtuele machines in hetzelfde virtuele netwerk. De implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine.

RDP en SSH zijn een van de fundamentele middelen waarmee u verbinding kunt maken met uw workloads die worden uitgevoerd in Azure. Het beschikbaar maken van RDP/SSH-poorten via internet is niet gewenst en wordt gezien als een aanzienlijk bedreigings oppervlak. Dit wordt vaak veroorzaakt door protocol beveiligings lekken. Om dit bedreigings oppervlak te bevatten, kunt u Bastion-hosts (ook wel Jump-servers genoemd) aan de open bare kant van uw perimeter netwerk implementeren. Bastion-hostservers zijn ontworpen en geconfigureerd voor aanvallen. Bastion-servers bieden ook RDP-en SSH-connectiviteit voor de werk belastingen die zich achter de Bastion bevindt, en ook in het netwerk.

![architectuur](./media/bastion-overview/architecture.png)

In deze afbeelding ziet u de architectuur van een Azure Bastion-implementatie. In dit diagram:

* De bastion-host wordt geïmplementeerd in het virtuele netwerk.
* De gebruiker maakt verbinding met de Azure Portal met behulp van een HTML5-browser.
* De gebruiker selecteert de virtuele machine waarmee verbinding moet worden gemaakt.
* Met één klik wordt de RDP/SSH-sessie geopend in de browser.
* Op de Azure-VM is geen openbaar IP-adres vereist.

## <a name="key-features"></a>Belangrijke functies

De volgende functies zijn beschikbaar:

* **RDP en SSH rechtstreeks in azure portal:** U kunt rechtstreeks aan de RDP-en SSH-sessie rechtstreeks in de Azure Portal gaan met behulp van een enkele klik probleemloze ervaring.
* **Externe sessie via SSL en firewall traversal voor RDP/SSH:** Azure Bastion gebruikt een HTML5-gebaseerde webclient die automatisch wordt gestreamd naar uw lokale apparaat, zodat u uw RDP/SSH-sessie via SSL op poort 443 krijgt, zodat u veilig bedrijfs firewalls kunt door lopen.
* **Geen openbaar IP-adres vereist op de Azure-VM:** Azure Bastion opent de RDP/SSH-verbinding met uw virtuele Azure-machine met behulp van privé-IP op uw VM. U hebt geen openbaar IP-adres op uw virtuele machine nodig.
* **Geen gedoe met het beheren van nsg's:** Azure Bastion is een volledig beheerde platform PaaS-service van Azure die intern is gehard om u te voorzien van beveiligde RDP/SSH-connectiviteit. U hoeft geen Nsg's toe te passen op het Azure Bastion-subnet. Omdat Azure Bastion verbinding maakt met uw virtuele machines via een particulier IP-adres, kunt u uw Nsg's zo configureren dat RDP/SSH alleen van Azure Bastion wordt toegestaan. Hiermee verwijdert u het beheer van Nsg's elke keer dat u een veilige verbinding met uw virtuele machines moet maken.
* **Bescherming tegen poort scannen:** Omdat u uw virtuele machines niet op open bare internet hoeft beschikbaar te stellen, worden uw Vm's beschermd tegen poort scans door Rogue en kwaadwillende gebruikers buiten uw virtuele netwerk.
* **Bescherming tegen aanvallen die geen dag zijn. Harder op één plek:** Azure Bastion is een volledig door een platform beheerde PaaS-service. Omdat deze zich in de omtrek van uw virtuele netwerk bevindt, hoeft u zich geen zorgen te maken over de beveiliging van elke virtuele machine in uw virtuele netwerk. Het Azure-platform beschermt tegen aanvallen op nul dagen door de Azure Bastion te beveiligen en altijd up-to-date te houden.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Maak een Azure bastion host-resource](bastion-create-host-portal.md).
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
