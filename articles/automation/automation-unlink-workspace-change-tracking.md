---
title: De werk ruimte ontkoppelen van het Automation-account voor Wijzigingen bijhouden en inventaris
description: In dit artikel wordt uitgelegd hoe u een Log Analytics werkruimte ontkoppelt van het Automation-account voor Wijzigingen bijhouden en inventaris
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828250"
---
# <a name="unlink-workspace-from-automation-account"></a>De werkruimte ontkoppelen van het Automation-account

U kunt ervoor kiezen uw Automation-account niet te integreren met een Log Analytics-werk ruimte bij het inschakelen van [Wijzigingen bijhouden-en inventaris](change-tracking.md) bewerkingen. In dit artikel leest u hoe u de werk ruimte ontkoppelt van uw account.

1. Meld u aan bij Azure op https://portal.azure.com .

2. Verwijder Updatebeheer voor uw virtuele machines. Zie [vm's uit wijzigingen bijhouden en inventaris verwijderen](automation-remove-vms-from-change-tracking.md).

3. Als Wijzigingen bijhouden en inventarisatie eerdere versies van de Azure SQL-bewaking bevat, is het mogelijk dat de installatie van de functie automatiserings assets heeft gemaakt die u moet verwijderen. Zoek deze assets en verwijder ze.

4. Open uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources** aan de linkerkant.

5. Klik op de pagina werk ruimte ontkoppelen op **werk ruimte ontkoppelen** en reageer op prompts.

   ![Pagina werk ruimte ontkoppelen](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Terwijl Azure Automation probeert de koppeling van de Log Analytics-werk ruimte te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

U kunt de Log Analytics-werk ruimte ook ontkoppelen van uw Automation-account vanuit de werk ruimte:

1. Selecteer in uw werk ruimte **Automation-account** onder **gerelateerde resources**. 
2. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)als u wilt werken met wijzigingen bijhouden en inventarisatie.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met functies.
