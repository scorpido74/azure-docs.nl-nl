---
title: Azure Automation Updatebeheer inschakelen vanaf een virtuele machine in azure
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit een Azure-VM.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743962"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Updatebeheer van een virtuele machine in azure inschakelen

In dit artikel wordt beschreven hoe u een virtuele machine van Azure kunt gebruiken om de functie [updatebeheer](automation-update-management.md) op andere computers in te scha kelen. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Updatebeheer. 

> [!NOTE]
> Als Updatebeheer wordt ingeschakeld, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. In de [Azure Portal](https://portal.azure.com)selecteert u **virtuele machines** of zoekt en selecteert u **virtuele machines** op de start pagina.

2. Selecteer de virtuele machine waarvoor u Updatebeheer wilt inschakelen. Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account. U

3. Selecteer op de pagina VM onder **bewerkingen**de optie **updatebeheer**.

4. U moet gemachtigd zijn `Microsoft.OperationalInsights/workspaces/read` om te bepalen of de virtuele machine is ingeschakeld voor een werk ruimte. Zie [machtigingen die nodig zijn om computers in te scha kelen](automation-role-based-access-control.md#feature-setup-permissions)voor meer informatie over aanvullende machtigingen die vereist zijn. Zie [updatebeheer inschakelen vanuit een Automation-account](automation-onboard-solutions-from-automation-account.md)voor meer informatie over het inschakelen van meerdere computers tegelijk.

5. Kies de Log Analytics-werk ruimte en het Automation-account en klik op **inschakelen** om updatebeheer in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten. 

    ![Updatebeheer inschakelen](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>De scope configuratie controleren

Updatebeheer gebruikt een scope configuratie in de werk ruimte om de computers te richten die moeten worden ingeschakeld voor de functie. De scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten die wordt gebruikt om het bereik van de functie te beperken tot specifieke computers. Zie [werken met Scope configuraties voor updatebeheer](automation-scope-configurations-update-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele Azure-machines beheren](automation-tutorial-update-management.md)voor meer informatie over het gebruik van updatebeheer voor vm's.
* Zie [werken met Scope configuraties voor updatebeheer](automation-scope-configurations-update-management.md)voor Scope configuraties.
* Als u de Log Analytics-werk ruimte niet meer nodig hebt, raadpleegt u de instructies in de [werk ruimte ontkoppelen van het Automation-account voor updatebeheer](automation-unlink-workspace-update-management.md).
* Zie [Vm's verwijderen uit updatebeheer](automation-remove-vms-from-update-management.md)als u vm's uit updatebeheer wilt verwijderen.
* Zie [problemen met updatebeheer oplossen](troubleshoot/update-management.md)om algemene updatebeheer fouten op te lossen.
* Zie problemen met [Windows Update agent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-Agent.
* Zie problemen met de[Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux-Update Agent.
