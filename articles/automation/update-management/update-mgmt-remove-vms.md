---
title: Vm's uit Azure Automation Updatebeheer verwijderen
description: In dit artikel leest u hoe u machines kunt verwijderen die met Updatebeheer worden beheerd.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648633"
---
# <a name="remove-vms-from-update-management"></a>VM's verwijderen uit Updatebeheer

Wanneer u klaar bent met het beheren van updates op uw Vm's in uw omgeving, kunt u het beheer van Vm's beëindigen met de functie [updatebeheer](update-mgmt-overview.md) . Als u het beheer wilt stoppen, bewerkt u de opgeslagen Zoek query `MicrosoftDefaultComputerGroup` in uw log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

## <a name="sign-into-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Uw Vm's verwijderen

1. Start in de Azure Portal **Cloud shell** vanuit de bovenste navigatie van de Azure Portal. Als u niet bekend bent met Azure Cloud Shell, raadpleegt u [overzicht van Azure Cloud shell](../../cloud-shell/overview.md).

2. Gebruik de volgende opdracht om de UUID te identificeren van een computer die u wilt verwijderen uit het beheer.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Ga in het Azure Portal naar **log Analytics-werk ruimten**. Selecteer uw werk ruimte in de lijst.

4. Selecteer in de werk ruimte Log Analytics **Logboeken** en kies vervolgens **query Verkenner** in het menu best acties.

5. Vouw in **query Explorer** in het rechterdeel venster het gedeelte **opgeslagen Queries\Updates** uit en selecteer de opgeslagen Zoek query `MicrosoftDefaultComputerGroup` om deze te bewerken.

6. Controleer in de query-editor de query en zoek de UUID voor de virtuele machine. Verwijder de UUID voor de virtuele machine en herhaal de stappen voor andere Vm's die u wilt verwijderen.

7. Sla de opgeslagen zoek opdracht op wanneer u klaar bent met het bewerken van het bestand door **Opslaan** te selecteren in de bovenste balk.

>[!NOTE]
>Machines worden nog steeds weer gegeven nadat u de registratie ervan ongedaan hebt gemaakt, omdat er een rapport wordt gemaakt over alle computers die in de afgelopen 24 uur zijn beoordeeld. Nadat u de computer hebt verwijderd, moet u 24 uur wachten voordat deze niet meer worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Als u het beheer van uw virtuele machine opnieuw wilt inschakelen, raadpleegt u [updatebeheer inschakelen door te bladeren door de Azure Portal](update-mgmt-enable-portal.md) of [updatebeheer in te scha kelen vanaf een virtuele Azure-machine](update-mgmt-enable-vm.md).