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
ms.openlocfilehash: cf8e7340ce3554bf27364335457a5b7eadcdc049
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935228"
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
> De volgende RBAC-machtiging is vereist voor het inschakelen van automanage: rol van **eigenaar** of **Inzender** samen met beheerders rollen voor **gebruikers toegang** .


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Automanage inschakelen voor Vm's op een bestaande virtuele machine

1. Zoek in de zoek balk naar en selecteer automatisch **beheer – aanbevolen procedures voor virtuele Azure-machines**.

2. Selecteer de **optie inschakelen op bestaande virtuele machine**.

3. Op de Blade **computers selecteren** :
    1. De lijst met Vm's filteren op uw **abonnement** en **resource groep**.
    1. Schakel het selectie vakje in van elke virtuele machine die u wilt vrijgeven.
    1. Klik op de knop **selecteren** .

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selecteer bestaande VM in de lijst met beschik bare Vm's.":::

4. Klik onder **configuratie profiel**op **Bladeren en wijzig de profielen en voor keuren**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Blader en wijzig de profielen en voor keuren.":::

5. Selecteer op de Blade **configuratie profiel selecteren + voor keuren** een profiel aan de linkerkant: *dev/test* voor testen, *Prod* voor productie.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Blader door het productie configuratie profiel.":::

6. Op het gekozen profiel bevat de voor **keuren voor configuratie** een vervolg keuzelijst waarin u kunt aanpassen voor bepaalde services.
    1. Klik op **nieuwe voor keuren maken**.
    1. Vul op de Blade **een configuratie voorkeur maken** het tabblad basis beginselen in:
        1. Abonnement
        1. Resourcegroep
        1. Voorkeurs naam
        1. Regio

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Vul de configuratie voorkeuren in.":::

7. Ga naar het tabblad voor keuren en pas de gewenste configuratie voorkeuren aan.
        
    > [!NOTE]
    > Alleen aanpassingen die nog steeds binnen onze aanbevolen procedures passen, zijn toegestaan bij het wijzigen van de profiel configuraties.

8. Controleer uw configuratie profiel.
9. Klik op de knop **Maken**.

10. Klik op de knop **Inschakelen**.


## <a name="disable-automanage-for-vms"></a>Automanage voor Vm's uitschakelen

U kunt het gebruik van Azure automanage voor virtuele machines snel stoppen door automanagement uit te scha kelen.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Automatisch beheer uitschakelen op een virtuele machine.":::

1. Ga naar de pagina aanbevolen procedures voor het automatisch **beheren van virtuele Azure-machines** met een lijst met alle automatische beheerde vm's.
1. Schakel het selectie vakje in naast de virtuele machine die u wilt uitschakelen.
1. Klik op de knop **Automanagent uitschakelen** .
1. Lees de berichten in het pop-upvenster met de melding aandachtig door voordat **u ermee**akkoord gaat.


## <a name="clean-up-resources"></a>Resources opschonen

Als u een nieuwe resource groep hebt gemaakt om Azure automanage te proberen voor virtuele machines en deze niet meer nodig hebt, kunt u de resource groep verwijderen. Als u de groep verwijdert, worden ook de virtuele machine en alle resources in de resource groep verwijderd.

Met Azure automanage worden standaard resource groepen gemaakt voor het opslaan van resources in. Controleer de resource groepen met de naam Conventie ' DefaultResourceGroupRegionName ' en ' AzureBackupRGRegionName ' om alle resources op te schonen.

1. Selecteer de **Resourcegroep**.
1. Selecteer **Verwijderen** op de pagina van de resourcegroep.
1. Wanneer u hierom wordt gevraagd, bevestigt u de naam van de resource groep en selecteert u vervolgens **verwijderen**.


## <a name="next-steps"></a>Volgende stappen 

Bekijk de meest gestelde vragen die worden beantwoord in onze veelgestelde vragen. 

> [!div class="nextstepaction"]
> [Veelgestelde vragen](faq.md)