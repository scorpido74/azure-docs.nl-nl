---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit een Azure VM
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een Azure-VM.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4eccdef6bd3f2bfcd0eced8281f7b998536f22a9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749184"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit een Azure VM

In dit artikel wordt beschreven hoe u een virtuele machine van Azure kunt gebruiken om de functie [Wijzigingen bijhouden en inventaris](change-tracking.md) op andere computers in te scha kelen. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie. 

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. In de [Azure Portal](https://portal.azure.com)selecteert u **virtuele machines** of zoekt en selecteert u **virtuele machines** op de start pagina.

2. Selecteer de virtuele machine waarvoor u Wijzigingen bijhouden en inventarisatie wilt inschakelen. Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

3. Selecteer op de pagina VM de optie **inventaris** of **Wijzigingen bijhouden** onder **configuratie beheer**.

4. U moet gemachtigd zijn `Microsoft.OperationalInsights/workspaces/read` om te bepalen of de virtuele machine is ingeschakeld voor een werk ruimte. Zie voor meer informatie over [aanvullende machtigingen die](automation-role-based-access-control.md#feature-setup-permissions)zijn vereist. Zie [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](automation-enable-changes-from-auto-acct.md)voor meer informatie over het inschakelen van meerdere computers tegelijk.

5. Kies de Log Analytics werk ruimte en het Automation-account en klik op **inschakelen** om wijzigingen bijhouden en inventaris voor de virtuele machine in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>De scope configuratie controleren

Wijzigingen bijhouden en inventarisatie maakt gebruik van een scope configuratie in de werk ruimte om de computers te richten die moeten worden ingeschakeld voor de functie. De scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten die wordt gebruikt om het bereik van de functie te beperken tot specifieke computers. Zie [werken met Scope configuraties voor wijzigingen bijhouden en inventarisatie](automation-scope-configurations-change-tracking.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)
* [Werken met Scope configuraties voor Wijzigingen bijhouden en inventaris](automation-scope-configurations-change-tracking.md)
* [Software op een virtuele machine identificeren](automation-tutorial-installed-software.md)
* [De werk ruimte ontkoppelen van het Automation-account voor Wijzigingen bijhouden en inventaris](automation-unlink-workspace-change-tracking.md)
* [Vm's uit Wijzigingen bijhouden en inventaris verwijderen](automation-remove-vms-from-change-tracking.md)
* [Problemen met wijzigingen in een virtuele Azure-machine oplossen](automation-tutorial-troubleshoot-changes.md)
* [Problemen met Wijzigingen bijhouden-en inventaris problemen oplossen](troubleshoot/change-tracking.md)
