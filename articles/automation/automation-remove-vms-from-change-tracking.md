---
title: Vm's uit Azure Automation Wijzigingen bijhouden en inventaris verwijderen
description: In dit artikel leest u hoe u Vm's uit Wijzigingen bijhouden en inventaris kunt verwijderen.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84169451"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>VM's verwijderen uit Wijzigingen bijhouden en inventaris

Wanneer u klaar bent met het implementeren van wijzigingen in de virtuele machines in uw omgeving, kunt u deze verwijderen uit de functie [Wijzigingen bijhouden en inventarisatie](change-tracking.md) .

## <a name="to-remove-your-vms"></a>Uw Vm's verwijderen

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

2. Gebruik de volgende opdracht om de UUID te identificeren van een virtuele machine die u wilt verwijderen uit het beheer.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Open in uw Log Analytics-werk ruimte onder **Algemeen**de opgeslagen Zoek opdrachten voor de scope configuratie `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. Klik voor de opgeslagen zoek opdracht `MicrosoftDefaultComputerGroup` op het weglatings teken aan de rechter kant en selecteer **bewerken**. 

5. Verwijder de UUID voor de virtuele machine.

6. Herhaal de stappen voor alle andere Vm's die u wilt verwijderen.

7. Sla de opgeslagen zoek opdracht op wanneer u klaar bent met het bewerken ervan. 

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)als u wilt blijven werken met wijzigingen bijhouden en inventarisatie.
* Zie [problemen met wijzigingen bijhouden en voorraad](troubleshoot/change-tracking.md)problemen oplossen voor informatie over het oplossen van algemene problemen met functies.