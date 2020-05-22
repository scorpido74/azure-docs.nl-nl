---
title: Vm's uit Azure Automation Updatebeheer verwijderen
description: In dit artikel leest u hoe u Vm's verwijdert uit Updatebeheer.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 29d6edd5463de78bba039e4ed6219575d924ac10
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749142"
---
# <a name="remove-vms-from-update-management"></a>Vm's uit Updatebeheer verwijderen

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

Wanneer u klaar bent met het implementeren van updates voor Vm's in uw omgeving, kunt u deze verwijderen uit de functie [updatebeheer](automation-update-management.md) .

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Gebruik de volgende opdracht om de UUID te identificeren van een virtuele machine die u wilt verwijderen uit het beheer.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Open in uw Log Analytics-werk ruimte onder **Algemeen**de opgeslagen Zoek opdrachten.

4. Klik voor de opgeslagen zoek opdracht `MicrosoftDefaultComputerGroup` op het weglatings teken aan de rechter kant en selecteer **bewerken**. 

5. Verwijder de UUID voor de virtuele machine.

6. Herhaal de stappen voor alle andere Vm's die u wilt verwijderen.

7. Sla de opgeslagen zoek opdracht op wanneer u klaar bent met het bewerken ervan. 

## <a name="next-steps"></a>Volgende stappen

* [Updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)
* [De werk ruimte ontkoppelen van het Automation-account voor Updatebeheer](automation-unlink-workspace-update-management.md)
* [Updatebeheer van een Automation-account inschakelen](automation-onboard-solutions-from-automation-account.md)
* [Updatebeheer van de Azure Portal inschakelen](automation-onboard-solutions-from-browse.md)
* [Updatebeheer inschakelen vanuit een runbook](automation-onboard-solutions.md)
* [Updatebeheer van een virtuele machine in azure inschakelen](automation-onboard-solutions-from-vm.md)
* [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md)
* [Problemen met Windows Update agent oplossen](troubleshoot/update-agent-issues.md)
* [Problemen met Linux Update agent oplossen](troubleshoot/update-agent-issues-linux.md)
