---
title: Azure Automation Updatebeheer inschakelen vanaf een virtuele machine in azure
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit een Azure-VM.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 254ff2906d2b80b6c34ba930daffbb2c65f2b22f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117928"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Updatebeheer inschakelen vanaf een virtuele Azure-machine

In dit artikel wordt beschreven hoe u een virtuele machine van Azure kunt gebruiken om de functie [updatebeheer](automation-update-management.md) op andere computers in te scha kelen. Om Azure VM's in te schakelen op schaal, moet u een bestaande VM inschakelen met behulp van Updatebeheer. 

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
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

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Het bereik voor de implementatie beperken

Updatebeheer gebruikt een scope configuratie in de werk ruimte om de computers te richten op het ontvangen van updates. Zie [limiet updatebeheer-implementatie bereik](automation-scope-configurations-update-management.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Updates en patches voor uw Azure-VM's beheren](automation-tutorial-update-management.md) voor het gebruiken van Updatebeheer voor VM's.
* Zie [limiet updatebeheer-implementatie bereik](automation-scope-configurations-update-management.md)voor Scope configuraties.
* Als u de Log Analytics-werkruimte niet meer nodig hebt, raadpleegt u de instructies in [Werkruimte ontkoppelen van Automation-account voor Updatebeheer](automation-unlink-workspace-update-management.md).
* Als u VM's uit Updatebeheer wilt verwijderen, raadpleegt u [VM's uit Updatebeheer verwijderen](automation-remove-vms-from-update-management.md).
* Zie [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
* Zie [Problemen met Windows-updateagent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-agent.
* Zie [problemen met Linux Update-agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux Update-agent.
