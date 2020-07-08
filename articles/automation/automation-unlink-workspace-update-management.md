---
title: De werk ruimte ontkoppelen van het Automation-account voor Updatebeheer
description: In dit artikel wordt beschreven hoe u een Log Analytics werkruimte ontkoppelt van het Automation-account voor Updatebeheer
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83835781"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>De werk ruimte ontkoppelen van het Automation-account voor Updatebeheer

U kunt ervoor kiezen uw Automation-account niet te integreren met een Log Analytics-werk ruimte tijdens [updatebeheer](automation-update-management.md) bewerkingen. In dit artikel leest u hoe u de werk ruimte ontkoppelt van uw account.

1. Aanmelden bij Azure op https://portal.azure.com.

2. Verwijder Updatebeheer voor uw virtuele machines. Zie [Vm's verwijderen uit updatebeheer](automation-remove-vms-from-update-management.md).

3. Als Updatebeheer eerdere versies van de Azure SQL-bewaking bevat, is het mogelijk dat de installatie van de functie automatiserings assets heeft gemaakt die u moet verwijderen. Voor Updatebeheer wilt u mogelijk de volgende items verwijderen die niet meer nodig zijn:

   * Update schema's: elk is een naam die overeenkomt met de update-implementatie die u hebt gemaakt.
   * Hybrid worker-groepen die zijn gemaakt voor Updatebeheer-elk heeft de naam op dezelfde manier als machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Open uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources** aan de linkerkant.

5. Klik op de pagina werk ruimte ontkoppelen op **werk ruimte ontkoppelen** en reageer op prompts.

   ![Pagina werk ruimte ontkoppelen](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Terwijl Azure Automation probeert de koppeling van de Log Analytics-werk ruimte te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

U kunt de Log Analytics-werk ruimte ook ontkoppelen van uw Automation-account vanuit de werk ruimte:

1. Selecteer in uw werk ruimte **Automation-account** onder **gerelateerde resources**. 
2. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)voor meer informatie over het gebruik van de functie.
* Zie [problemen met updatebeheer oplossen](troubleshoot/update-management.md)voor informatie over het oplossen van functie fouten.
* Zie problemen [met Windows Update agent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van fouten in Windows Update Agent.
* Zie problemen [met de Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met Linux-update agenten.
