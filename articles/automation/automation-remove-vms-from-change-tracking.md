---
title: Vm's uit Azure Automation Wijzigingen bijhouden en inventaris verwijderen
description: In dit artikel leest u hoe u Vm's uit Wijzigingen bijhouden en inventaris kunt verwijderen.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 1bd94beaa40f6ff793b50e261138cc31453f7016
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749163"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Vm's uit Wijzigingen bijhouden en inventaris verwijderen

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

Wanneer u klaar bent met het implementeren van wijzigingen in de virtuele machines in uw omgeving, kunt u deze verwijderen uit de functie [Wijzigingen bijhouden en inventarisatie](change-tracking.md) .

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

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

* [Overzicht van Wijzigingen bijhouden en inventaris](change-tracking.md)
* [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)
* [De werk ruimte ontkoppelen van het Automation-account voor Wijzigingen bijhouden en inventaris](automation-unlink-workspace-change-tracking.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](automation-enable-changes-from-auto-acct.md)
* [Wijzigingen bijhouden en inventaris inschakelen via de Azure Portal](automation-enable-changes-from-browse.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook](automation-enable-changes-from-runbook.md)
* [Wijzigingen bijhouden en inventaris inschakelen vanuit een Azure VM](automation-enable-changes-from-vm.md)
* [Problemen met wijzigingen in een virtuele Azure-machine oplossen](automation-tutorial-troubleshoot-changes.md)
* [Problemen met Wijzigingen bijhouden-en inventaris problemen oplossen](troubleshoot/change-tracking.md)