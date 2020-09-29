---
title: Een aangepaste voor keur maken in azure automanage voor Vm's
description: Meer informatie over het aanpassen van het configuratie profiel in azure automanage voor Vm's en het instellen van uw eigen voor keuren.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 7a716ed9bca6d34ad4dbcd2566837a839f71153b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450310"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Een aangepaste voor keur maken in azure automanage voor Vm's

Het automatisch beheren van Azure voor de aanbevolen procedures voor virtuele machines bevat standaard configuratie profielen die zo nodig kunnen worden aangepast. In dit artikel wordt uitgelegd hoe u uw eigen configuratie profiel voorkeuren kunt instellen wanneer u automatisch beheer inschakelt op een nieuwe of bestaande virtuele machine.

Momenteel worden aanpassingen op [Azure backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) en [micro soft antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)ondersteund.


> [!NOTE]
> U kunt het configuratie profiel of de voor keur op uw virtuele machine niet wijzigen terwijl automanage is ingeschakeld. U moet automatisch beheer voor die virtuele machine uitschakelen en vervolgens automanage opnieuw inschakelen met het gewenste configuratie profiel en voor keuren.


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

> [!IMPORTANT]
> De volgende RBAC-machtiging is vereist voor het inschakelen van automanage: rol **Owner** of **Inzender** samen met beheerders rollen voor **gebruikers toegang** .


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Automanage inschakelen voor virtuele machines op een bestaande virtuele machine

1. Zoek in de zoekbalk naar **Automatisch beheer: aanbevolen procedures voor virtuele Azure-machines** en selecteer deze optie.

2. Selecteer **Op bestaande VM inschakelen**.

3. Ga als volgt te werk op de blade **Machines selecteren**:
    1. Filter de lijst met virtuele machines op uw **Abonnement** en **Resourcegroep**.
    1. Schakel het selectievakje in van elke virtuele machine die u wilt onboarden.
    1. Klik op de knop **Selecteren**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

4. Klik onder **Configuratieprofiel**op **Bladeren en profielen en voorkeuren wijzigen**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

5. Selecteer op de Blade **configuratie profiel selecteren + voor keuren** een profiel aan de linkerkant: *dev/test* voor testen, *Prod* voor productie.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

6. Op het gekozen profiel bevat de voor **keuren voor configuratie** een vervolg keuzelijst waarin u kunt aanpassen voor bepaalde services.
    1. Klik op **nieuwe voor keuren maken**.
    1. Vul op de Blade **een configuratie voorkeur maken** het tabblad basis beginselen in:
        1. Abonnement
        1. Resourcegroep
        1. Voorkeurs naam
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

7. Ga naar het tabblad voor keuren en pas de gewenste configuratie voorkeuren aan.
        
    > [!NOTE]
    > Alleen aanpassingen die nog steeds binnen onze aanbevolen procedures passen, zijn toegestaan bij het wijzigen van de profiel configuraties.

8. Controleer uw configuratie profiel.
9. Klik op de knop **Maken**.

10. Klik op de knop **Inschakelen**.


## <a name="disable-automanage-for-vms"></a>Automanage uitschakelen voor virtuele machines

Stop snel het gebruik van Azure Automanage voor virtuele machines door automatisch beheer uit te schakelen.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Bestaande virtuele machine selecteren in de lijst met beschikbare virtuele machines.":::

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

Bekijk de meest gestelde vragen die worden beantwoord in onze veelgestelde vragen. 

> [!div class="nextstepaction"]
> [Veelgestelde vragen](faq.md)