---
title: 'Quickstart: Azure Automanage inschakelen voor virtuele machines in de Azure Portal'
description: Meer informatie over het snel inschakelen van automatisch beheer voor virtuele machines op een nieuwe of bestaande virtuele machine in de Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: d66c19ce3a9786a5ca0f1390acb398c2a9cf502f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445795"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Quickstart: Azure Automanage inschakelen voor virtuele machines in de Azure Portal

Ga aan de slag met Azure Automanage voor virtuele machines door de Azure Portal te gebruiken om automatisch beheer in te schakelen op een nieuwe of bestaande virtuele machine.


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

> [!IMPORTANT]
> U moet de rol **Inzender** hebben om Automatisch beheer in te schakelen met een bestaand account voor Automatisch beheer. Als u Automatisch beheer inschakelt met een nieuw account voor Automatisch beheer, hebt u de volgende machtigingen nodig: De rol van **Eigenaar** of **Inzender** en de rollen **Beheerders voor gebruikerstoegang**.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Automanage inschakelen voor virtuele machines op een bestaande virtuele machine

1. Zoek in de zoekbalk naar **Automatisch beheer: aanbevolen procedures voor virtuele Azure-machines** en selecteer deze optie.

2. Selecteer **Op bestaande VM inschakelen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Op bestaande VM inschakelen.":::

3. Ga als volgt te werk op de blade **Machines selecteren**:
    1. Filter de lijst met virtuele machines op uw **Abonnement** en **Resourcegroep**.
    1. Schakel het selectievakje in van elke virtuele machine die u wilt onboarden.
    1. Klik op de knop **Selecteren**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Op bestaande VM inschakelen.":::

4. Klik onder **Configuratieprofiel**op **Bladeren en profielen en voorkeuren wijzigen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Op bestaande VM inschakelen.":::

5. Ga als volgt te werk op de blad **Configuratieprofiel + voorkeuren selecteren**:
    1. Selecteer een profiel aan de linkerkant: *Dev/test* voor het testen, *Prod* voor productie.
    1. Klik op de knop **Selecteren**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Op bestaande VM inschakelen.":::

6. Klik op de knop **Inschakelen**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Automanage inschakelen voor virtuele machines op een nieuwe virtuele machine

Meld u [hier](https://aka.ms/automanageportalnextstep) aan bij het Azure-portaal om een nieuwe virtuele machine te maken en Automatisch beheer in te schakelen.

1. Volg de stappen voor het maken in [Quickstart: Een Windows-VM maken in de Azure Portal](..\virtual-machines\windows\quick-create-portal.md).

2. Nadat uw virtuele machine is geïmplementeerd, gaat u naar de pagina met de implementatiestatus, die wordt aanbevolen onder **Volgende stappen** onderaan.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Op bestaande VM inschakelen.":::

3. Selecteer onder **Volgende stappen** de optie **Aanbevolen procedures voor het inschakelen van automatisch beheren**.

4. Op de pagina **Automatisch beheer: aanbevolen procedures voor virtuele Azure-machines** wordt **Machines** automatisch ingevuld door de zojuist gemaakte virtuele machine.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Op bestaande VM inschakelen.":::

5. Klik onder **Configuratieprofiel**op **Bladeren en profielen en voorkeuren wijzigen**.

6. Ga als volgt te werk op de blad **Configuratieprofiel + voorkeuren selecteren**:
    1. Selecteer een profiel aan de linkerkant: *Dev/test* voor het testen, *Prod* voor productie.
    1. Klik op de knop **Selecteren**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Op bestaande VM inschakelen.":::

7. Klik op de knop **Inschakelen**.

## <a name="disable-automanage-for-vms"></a>Automanage uitschakelen voor virtuele machines

Stop snel het gebruik van Azure Automanage voor virtuele machines door automatisch beheer uit te schakelen.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Op bestaande VM inschakelen.":::

1. Ga naar de pagina **Automatisch beheer: aanbevolen procedures voor virtuele Azure-machines**. U vindt daar een lijst met alle automatisch beheerde virtuele machines.
1. Schakel het selectievakje in naast de virtuele machine die u wilt uitschakelen.
1. Klik op de knop **Automatisch beheer uitschakelen**.
1. Lees aandachtig door de berichten in het pop-upvenster voordat u akkoord gaat met het **uitschakelen**.


## <a name="clean-up-resources"></a>Resources opschonen

Als u een nieuwe resourcegroep hebt gemaakt om Azure Automanage te proberen voor virtuele machines en deze niet meer nodig hebt, kunt u de resourcegroep verwijderen. Als u de groep verwijdert, worden ook de virtuele machines en alle resources in de resourcegroep verwijderd.

Azure Automanage maakt standaard resourcegroepen voor het opslaan van resources. Controleer de resourcegroepen met de naamconventie 'DefaultResourceGroupRegionName' en 'AzureBackupRGRegionName' om alle resources op te schonen.

1. Selecteer de **Resourcegroep**.
1. Selecteer **Verwijderen** op de pagina van de resourcegroep.
1. Bevestig de naam van de resourcegroep als daar om wordt gevraagd. Selecteer vervolgens **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Azure Automanage ingeschakeld voor virtuele machines. 

Ontdek hoe u aangepaste voorkeuren kunt maken en toepassen wanneer u automatisch beheer op uw virtuele machine inschakelt. 

> [!div class="nextstepaction"]
> [Azure Automanage voor virtuele machines - Aangepast configuratieprofiel](virtual-machines-custom-preferences.md)
