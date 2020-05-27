---
title: Vm's uit Azure Automation Wijzigingen bijhouden en inventaris verwijderen
description: In dit artikel leest u hoe u Vm's uit Wijzigingen bijhouden en inventaris kunt verwijderen.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 22cb49c414e21e5c47330f2c67fc2cf30e3364b2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836665"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>VM's verwijderen uit Wijzigingen bijhouden en inventaris

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

* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)als u wilt blijven werken met wijzigingen bijhouden en inventarisatie.
* Zie [problemen met wijzigingen bijhouden en voorraad](troubleshoot/change-tracking.md)problemen oplossen voor informatie over het oplossen van algemene problemen met functies.