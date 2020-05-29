---
title: Vm's uit Azure Automation Updatebeheer verwijderen
description: In dit artikel leest u hoe u Vm's verwijdert uit Updatebeheer.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 796cf18ae4dbab50eb7f968bda065ae0351f2ae8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84169403"
---
# <a name="remove-vms-from-update-management"></a>VM's verwijderen uit Updatebeheer

Wanneer u klaar bent met het implementeren van updates voor Vm's in uw omgeving, kunt u deze verwijderen uit de functie [updatebeheer](automation-update-management.md) .

## <a name="to-remove-your-vms"></a>Uw Vm's verwijderen

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Gebruik de volgende opdracht om de UUID te identificeren van een virtuele machine die u wilt verwijderen uit het beheer.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Open in uw Log Analytics-werk ruimte onder **Algemeen**de opgeslagen Zoek opdrachten voor de scope configuratie `MicrosoftDefaultScopeConfig-Updates` .

4. Klik voor de opgeslagen zoek opdracht `MicrosoftDefaultComputerGroup` op het weglatings teken aan de rechter kant en selecteer **bewerken**. 

5. Verwijder de UUID voor de virtuele machine.

6. Herhaal de stappen voor alle andere Vm's die u wilt verwijderen.

7. Sla de opgeslagen zoek opdracht op wanneer u klaar bent met het bewerken ervan. 

## <a name="next-steps"></a>Volgende stappen

* Als u wilt blijven werken met Updatebeheer, raadpleegt u [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md).
* Zie problemen [met updatebeheer oplossen](troubleshoot/update-management.md)voor informatie over het oplossen van algemene problemen met functies.
* Zie problemen met [Windows Update agent oplossen](troubleshoot/update-agent-issues.md)voor problemen met de Windows Update-Agent.
* Zie problemen met de [Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor problemen met de Linux-Update Agent.