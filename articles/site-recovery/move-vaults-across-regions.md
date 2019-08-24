---
title: Een Azure Site Recovery configuratie verplaatsen naar een andere Azure-regio | Microsoft Docs
description: Richt lijnen voor het verplaatsen van een Site Recovery configuratie naar een andere Azure-regio
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2cf06a0c4e35d22cbad260201183516db2f07436
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013466"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Een Recovery Services kluis en Azure Site Recovery configuratie verplaatsen naar een andere Azure-regio

Er zijn verschillende scenario's waarin u mogelijk uw bestaande Azure-resources van de ene regio naar de andere wilt verplaatsen. Voor beelden zijn voor beheer baarheid, governance-redenen of vanwege bedrijfs samenvoegers en-acquisities. Een van de gerelateerde resources die u mogelijk wilt verplaatsen wanneer u uw Azure-Vm's verplaatst, is de configuratie voor herstel na nood gevallen. 

Er is geen eersteklas manier om een bestaande configuratie voor herstel na nood gevallen van de ene regio naar de andere te verplaatsen. Dit komt doordat u uw doel regio hebt geconfigureerd op basis van de regio van de bron-VM. Wanneer u besluit om de bron regio te wijzigen, kunnen de bestaande configuraties van de doel regio niet opnieuw worden gebruikt en moet deze opnieuw worden ingesteld. In dit artikel wordt het stapsgewijze proces beschreven voor het opnieuw configureren van de installatie van nood herstel en het verplaatsen naar een andere regio.

In dit document gaat u als volgt te werkt:

> [!div class="checklist"]
> * Controleer de vereisten voor de verplaatsing.
> * De resources identificeren die door Azure Site Recovery zijn gebruikt.
> * Schakel replicatie uit.
> * Verwijder de resources.
> * Site Recovery instellen op basis van de nieuwe bron regio voor de Vm's.

> [!IMPORTANT]
> Er is momenteel geen manier om een Recovery Services kluis te verplaatsen en de configuratie voor herstel na nood gevallen naar een andere regio. Dit artikel begeleidt u bij het uitschakelen van de replicatie en het instellen ervan in de nieuwe regio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de configuratie voor herstel na nood gevallen verwijdert en verwijdert voordat u probeert de virtuele machines van Azure naar een andere regio te verplaatsen. 

  > [!NOTE]
  > Als uw nieuwe doel regio voor de virtuele Azure-machine hetzelfde is als de doel regio voor herstel na nood gevallen, kunt u de bestaande replicatie Configuratie gebruiken en deze verplaatsen. Volg de stappen in [Azure IaaS vm's naar een andere Azure-regio verplaatsen](azure-to-azure-tutorial-migrate.md).

- Zorg ervoor dat u een weloverwogen beslissing neemt en dat belanghebbenden hiervan op de hoogte zijn. Uw virtuele machine wordt niet beschermd tegen nood gevallen tot het verplaatsen van de virtuele machine is voltooid.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>De resources identificeren die zijn gebruikt door Azure Site Recovery
U wordt aangeraden deze stap uit te voeren voordat u verdergaat met de volgende. Het is eenvoudiger om de relevante resources te identificeren terwijl de Vm's worden gerepliceerd.

Voor elke virtuele machine van Azure die wordt gerepliceerd, gaat u naar **beveiligde items** > **Eigenschappen** van**gerepliceerde items** > en identificeert u de volgende bronnen:

- Doelresourcegroep
- Cacheopslagaccount
- Doel opslag account (in het geval van een niet-beheerde op schijven gebaseerde Azure-VM) 
- Doelnetwerk


## <a name="disable-the-existing-disaster-recovery-configuration"></a>De bestaande configuratie voor herstel na nood gevallen uitschakelen

1. Ga naar de Recovery Services kluis.
2. Klik in **beveiligde items** > **gerepliceerde items**met de rechter muisknop op de computer en selecteer **replicatie uitschakelen**.
3. Herhaal deze stap voor alle virtuele machines die u wilt verplaatsen.

> [!NOTE]
> De Mobility-service wordt niet van de beveiligde servers verwijderd. U moet deze hand matig verwijderen. Als u van plan bent om de server opnieuw te beveiligen, kunt u het verwijderen van de Mobility-service overs Laan.

## <a name="delete-the-resources"></a>De resources verwijderen

1. Ga naar de Recovery Services kluis.
2. Selecteer **Verwijderen**.
3. Verwijder alle andere resources die u [eerder hebt geïdentificeerd](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Virtuele Azure-machines verplaatsen naar de nieuwe doel regio

Volg de stappen in deze artikelen op basis van uw vereiste om virtuele Azure-machines te verplaatsen naar de doel regio:

- [Virtuele Azure-machines verplaatsen naar een andere regio](azure-to-azure-tutorial-migrate.md)
- [Virtuele Azure-machines verplaatsen naar beschikbaarheidszones](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Site Recovery instellen op basis van de nieuwe bron regio voor de Vm's

Configureer herstel na nood gevallen voor de virtuele Azure-machines die zijn verplaatst naar de nieuwe regio door de stappen te volgen in [herstel na nood geval instellen voor Azure vm's](azure-to-azure-tutorial-enable-replication.md).
