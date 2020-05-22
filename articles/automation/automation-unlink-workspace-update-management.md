---
title: De werk ruimte ontkoppelen van het Automation-account voor Updatebeheer
description: In dit artikel wordt beschreven hoe u een Log Analytics werkruimte ontkoppelt van het Automation-account voor Updatebeheer
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4261b3c3fa7aab830f5f57e86ee25f8ba5894849
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749065"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>De werk ruimte ontkoppelen van het Automation-account voor Updatebeheer

U kunt ervoor kiezen uw Automation-account niet te integreren met een Log Analytics-werk ruimte tijdens [updatebeheer](automation-update-management.md) bewerkingen. In dit artikel leest u hoe u de werk ruimte ontkoppelt van uw account.

1. Meld u aan bij Azure op https://portal.azure.com .

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

* [Updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)
* [Vm's uit Updatebeheer verwijderen](automation-remove-vms-from-update-management.md)
* [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md)
