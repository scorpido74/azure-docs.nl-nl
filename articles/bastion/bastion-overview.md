---
title: Azure Bastion | Microsoft Documenten
description: Meer informatie over Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 299a69675eed1ba958c6d13cf447407450df2abb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411101"
---
# <a name="what-is-azure-bastion"></a>Wat is Azure Bastion?

De Azure Bastion-service is een nieuwe, volledig platformbeheerde PaaS-service die u inuw virtuele netwerk indient. Het biedt veilige en naadloze RDP/SSH-connectiviteit met uw virtuele machines rechtstreeks in de Azure-portal via TLS. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP nodig.

Bastion biedt veilige RDP- en SSH-connectiviteit met alle VM's in het virtuele netwerk waarin het is ingericht. Het gebruik van Azure Bastion beschermt uw virtuele machines tegen het blootstellen van RDP/SSH-poorten aan de buitenwereld, terwijl u toch veilige toegang biedt met RDP/SSH. Met Azure Bastion maakt u rechtstreeks vanuit de Azure-portal verbinding met de virtuele machine. U hebt geen extra client, agent of software nodig.

## <a name="architecture"></a>Architectuur

Azure Bastion-implementatie is per virtueel netwerk, niet per abonnement/account of virtuele machine. Zodra u een Azure Bastion-service inuw virtuele netwerk indient, is de RDP/SSH-ervaring beschikbaar voor al uw VM's in hetzelfde virtuele netwerk.

RDP en SSH zijn enkele van de fundamentele middelen waarmee u verbinding maken met uw workloads die in Azure worden uitgevoerd. Het blootstellen van RDP/ SSH-poorten via het internet is niet gewenst en wordt gezien als een significant bedreigingsoppervlak. Dit is vaak te wijten aan protocol kwetsbaarheden. Als u dit dreigingsoppervlak wilt bevatten, u bastionhosts (ook wel jump-servers genoemd) implementeren aan de openbare kant van uw perimeternetwerk. Bastion-hostservers zijn ontworpen en geconfigureerd om aanvallen te weerstaan. Bastion-servers bieden ook RDP- en SSH-connectiviteit met de workloads die achter het bastion zitten, evenals verder binnen het netwerk.

![architectuur](./media/bastion-overview/architecture.png)

Deze afbeelding toont de architectuur van een Azure Bastion-implementatie. In dit diagram:

* De Bastion-host wordt geïmplementeerd in het virtuele netwerk.
* De gebruiker maakt verbinding met de Azure-portal met behulp van een HTML5-browser.
* De gebruiker selecteert de virtuele machine waarmee u verbinding moet maken.
* Met één klik wordt de RDP/SSH-sessie geopend in de browser.
* Er is geen openbaar IP vereist op de Azure VM.

## <a name="key-features"></a>Belangrijke functies

De volgende functies zijn beschikbaar:

* **RDP en SSH rechtstreeks in Azure-portal:** U rechtstreeks naar de RDP- en SSH-sessie rechtstreeks in de Azure-portal gaan met behulp van een naadloze ervaring met één klik.
* **Externe sessie via TLS en firewall traversal voor RDP/SSH:** Azure Bastion maakt gebruik van een HTML5-gebaseerde webclient die automatisch wordt gestreamd naar uw lokale apparaat, zodat u uw RDP/SSH-sessie via TLS op poort 443 krijgt, zodat u bedrijfsfirewalls veilig doorlopen.
* **Er is geen openbaar IP-adres vereist op de Azure VM:** Azure Bastion opent de RDP/SSH-verbinding met uw virtuele Azure-machine met privé-IP op uw VM. U hebt geen openbaar IP op uw virtuele machine nodig.
* **Geen gedoe met het beheer van NSG's:** Azure Bastion is een volledig beheerde PaaS-service van Azure die intern wordt gehard om u veilige RDP/SSH-connectiviteit te bieden. U hoeft geen NSG's toe te passen op het subnet Azure Bastion. Omdat Azure Bastion via privé-IP verbinding maakt met uw virtuele machines, u uw NPC's zo configureren dat RDP/SSH alleen van Azure Bastion wordt toegestaan. Dit elimineert het gedoe van het beheren van SMG's elke keer dat u nodig hebt om veilig verbinding te maken met uw virtuele machines.
* **Bescherming tegen poortscanning:** Omdat u uw virtuele machines niet hoeft bloot te stellen aan openbaar internet, worden uw VM's beschermd tegen het scannen van poorten door malafide en kwaadwillende gebruikers die zich buiten uw virtuele netwerk bevinden.
* **Bescherm tegen zero-day exploits. Verharding op slechts één plek:** Azure Bastion is een volledig platformbeheerde PaaS-service. Omdat het zich aan de rand van uw virtuele netwerk bevindt, hoeft u zich geen zorgen te maken over het verharden van elk van de virtuele machines in uw virtuele netwerk. Het Azure-platform beschermt tegen zero-day exploits door het Azure Bastion gehard en altijd up-to-date voor u te houden.

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Bastion-hostbron maken](bastion-create-host-portal.md).
* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
