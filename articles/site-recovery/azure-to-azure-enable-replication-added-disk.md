---
title: Replicatie inschakelen voor een toegevoegde Azure VM-schijf in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u replicatie inschakelt voor een schijf die is toegevoegd aan een Azure VM die is ingeschakeld voor noodherstel met Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973795"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Replicatie inschakelen voor een schijf die is toegevoegd aan een Azure VM


In dit artikel wordt beschreven hoe u replicatie inschakelt voor gegevensschijven die zijn toegevoegd aan een Azure VM die al is ingeschakeld voor noodherstel naar een andere Azure-regio, met azure [siteherstel](site-recovery-overview.md).

Replicatie inschakelen voor een schijf die u aan een vm toevoegt, wordt ondersteund voor Azure VM's met beheerde schijven.

Wanneer u een nieuwe schijf toevoegt aan een Azure VM die wordt gerepliceerd naar een andere Azure-regio, gebeurt het volgende:

-   Replicatiestatus voor de VM toont een waarschuwing en een notitie in de portal geeft aan dat een of meer schijven beschikbaar zijn voor bescherming.
-   Als u de beveiliging van de toegevoegde schijven inschakelt, verdwijnt de waarschuwing na de eerste replicatie van de schijf.
-   Als u ervoor kiest om replicatie voor de schijf niet in te schakelen, u ervoor kiezen de waarschuwing te verwijderen.

![Nieuwe schijf toegevoegd](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u noodherstel al hebt ingesteld voor de vm waaraan u de schijf toevoegt. Als u dit nog niet hebt gedaan, volgt u de [zelfstudie Azure naar Azure voor noodherstel.](azure-to-azure-tutorial-enable-replication.md)

## <a name="enable-replication-for-an-added-disk"></a>Replicatie inschakelen voor een toegevoegde schijf

Ga als volgt te werk om replicatie voor een toegevoegde schijf in te schakelen:

1. Klik in de kluis > **gerepliceerde items**op de vm waaraan u de schijf hebt toegevoegd.
2. Klik op **Schijven**en selecteer vervolgens de gegevensschijf waarvoor u replicatie wilt inschakelen (deze schijven hebben een **niet-beveiligde** status).
3.  Klik **in Schijfgegevens**op **Replicatie inschakelen**.

    ![Replicatie inschakelen voor toegevoegde schijf](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Nadat de replicatietaak inschakelen en de eerste replicatie is voltooid, wordt de waarschuwing voor de replicatiestatus voor het schijfprobleem verwijderd.



## <a name="next-steps"></a>Volgende stappen

[Meer informatie](site-recovery-test-failover-to-azure.md) over het uitvoeren van een testfailover.
