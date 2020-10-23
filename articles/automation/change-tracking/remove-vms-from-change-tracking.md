---
title: Vm's uit Azure Automation Wijzigingen bijhouden en inventaris verwijderen
description: In dit artikel leest u hoe u Vm's uit Wijzigingen bijhouden en inventaris kunt verwijderen.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 9a7f36299d235ca2578a1ff3518650c5632ef046
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209814"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>VM's verwijderen uit Wijzigingen bijhouden en inventaris

Wanneer u klaar bent met het bijhouden van wijzigingen op de Vm's in uw omgeving, kunt u stoppen met het beheer van de virtuele machines met de functie [Wijzigingen bijhouden en inventarisatie](overview.md) . Als u het beheer wilt stoppen, bewerkt u de opgeslagen Zoek query `MicrosoftDefaultComputerGroup` in uw log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

## <a name="sign-into-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Uw Vm's verwijderen

1. Start in de Azure Portal **Cloud shell** vanuit de bovenste navigatie van de Azure Portal. Als u niet bekend bent met Azure Cloud Shell, raadpleegt u [overzicht van Azure Cloud shell](/cloud-shell/overview).

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

Als u deze functie opnieuw wilt inschakelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](enable-from-automation-account.md), [Wijzigingen bijhouden en inventaris inschakelen door te bladeren door de Azure Portal](enable-from-portal.md), [Wijzigingen bijhouden en inventaris van een runbook](enable-from-runbook.md)in te scha kelen of [Wijzigingen bijhouden en inventaris van een Azure-VM](enable-from-vm.md)in te scha kelen.