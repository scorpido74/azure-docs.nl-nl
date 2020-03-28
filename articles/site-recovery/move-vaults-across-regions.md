---
title: Een azure-siteherstelkluis verplaatsen naar een andere regio
description: Beschrijft hoe u een vault voor Herstelservices (Azure Site Recovery) verplaatst naar een andere Azure-regio
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090297"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Een vault voor Herstelservices en Azure Site Recovery-configuratie verplaatsen naar een andere Azure-regio

Er zijn verschillende scenario's waarin u uw bestaande Azure-resources mogelijk van de ene regio naar de andere wilt verplaatsen. Voorbeelden zijn om beheersbaarheid, governance redenen, of als gevolg van bedrijfsfusies en overnames. Een van de gerelateerde resources die u mogelijk wilt verplaatsen wanneer u uw Azure VM's verplaatst, is de configuratie van het noodherstel. 

Er is geen eersteklas manier om een bestaande configuratie voor noodherstel van de ene regio naar de andere te verplaatsen. Dit komt omdat u uw doelregio hebt geconfigureerd op basis van uw bron-VM-regio. Wanneer u besluit het brongebied te wijzigen, kunnen de eerder bestaande configuraties van het doelgebied niet opnieuw worden gebruikt en moeten ze opnieuw worden ingesteld. In dit artikel wordt het stapsgewijze proces gedefinieerd om de instelling voor noodherstel opnieuw te configureren en naar een andere regio te verplaatsen.

In dit document ziet u het als volgt te zien:

> [!div class="checklist"]
> * Controleer de vereisten voor de verhuizing.
> * Identificeer de bronnen die zijn gebruikt door Azure Site Recovery.
> * Replicatie uitschakelen.
> * Verwijder de bronnen.
> * Siteherstel instellen op basis van het nieuwe brongebied voor de VM's.

> [!IMPORTANT]
> Momenteel is er geen eersteklas manier om een Vault recovery services en de configuratie voor noodherstel te verplaatsen, net als naar een andere regio. In dit artikel wordt u door het proces van replicatie heen geloodst en in het nieuwe gebied ingesteld.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de configuratie voor noodherstel verwijdert en verwijdert voordat u de Azure VM's naar een andere regio probeert te verplaatsen. 

  > [!NOTE]
  > Als uw nieuwe doelregio voor de Azure VM hetzelfde is als het doelgebied voor noodherstel, u uw bestaande replicatieconfiguratie gebruiken en verplaatsen. Volg de stappen in [Move Azure IaaS VM's naar een andere Azure-regio.](azure-to-azure-tutorial-migrate.md)

- Zorg ervoor dat u een weloverwogen beslissing neemt en dat belanghebbenden worden geïnformeerd. Uw vm wordt pas beschermd tegen rampen als de verplaatsing van de vm is voltooid.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>De resources identificeren die zijn gebruikt door Azure Site Recovery
We raden u aan deze stap te doen voordat u doorgaat naar de volgende stap. Het is gemakkelijker om de relevante bronnen te identificeren terwijl de VM's worden gerepliceerd.

Ga voor elke Azure VM die wordt gerepliceerd naar**eigenschappen** **voor gerepliceerde items** > met **beveiligde items** > en identificeert u de volgende bronnen:

- Doelgroepgroep
- Cacheopslagaccount
- Doelopslagaccount (in het geval van een niet-beheerde Azure VM op schijf) 
- Doelnetwerk


## <a name="disable-the-existing-disaster-recovery-configuration"></a>De bestaande configuratie van noodherstel uitschakelen

1. Ga naar de kluis van de Herstelservices.
2. Klik in**gerepliceerde items**met **beveiligde items** > met de rechtermuisknop op de machine en selecteer **Replicatie uitschakelen**.
3. Herhaal deze stap voor alle VM's die u wilt verplaatsen.

> [!NOTE]
> De mobiliteitsservice wordt niet verwijderd van de beveiligde servers. U moet het handmatig verwijderen. Als u van plan bent de server opnieuw te beschermen, u het verwijderen van de mobiliteitsservice overslaan.

## <a name="delete-the-resources"></a>De bronnen verwijderen

1. Ga naar de kluis van de Herstelservices.
2. Selecteer **Verwijderen**.
3. Verwijder alle andere bronnen die u [eerder hebt geïdentificeerd.](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Azure VM's naar het nieuwe doelgebied verplaatsen

Volg de stappen in deze artikelen op basis van uw vereiste om Azure VM's naar het doelgebied te verplaatsen:

- [Virtuele Azure-machines verplaatsen naar een andere regio](azure-to-azure-tutorial-migrate.md)
- [Virtuele Azure-machines verplaatsen naar beschikbaarheidszones](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Siteherstel instellen op basis van het nieuwe brongebied voor de VM's

Configureer disaster recovery voor de Azure VM's die naar het nieuwe gebied zijn verplaatst door de stappen te volgen in [Noodherstel instellen voor Azure VM's.](azure-to-azure-tutorial-enable-replication.md)
