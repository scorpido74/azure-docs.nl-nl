---
title: Azure Automation Wijzigingen bijhouden en inventaris van de Azure Portal inschakelen
description: In dit artikel leest u hoe u de functie Wijzigingen bijhouden en inventaris kunt inschakelen vanuit de Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: b53c63fc1a14c456e0bca0725ca29b311301ce66
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749079"
---
# <a name="enable-change-tracking-and-inventory-from-the-azure-portal"></a>Wijzigingen bijhouden en inventaris inschakelen via de Azure Portal

In dit artikel wordt beschreven hoe u de functie voor [Wijzigingen bijhouden en inventaris](change-tracking.md) kunt inschakelen voor vm's door te bladeren door de Azure Portal. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie. 

Het aantal resource groepen dat u kunt gebruiken voor het beheren van uw Vm's, wordt beperkt door de [implementatie limieten van Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). Implementaties van Resource Manager, niet te verwarren met update-implementaties, zijn beperkt tot vijf resource groepen per implementatie. Twee van deze resource groepen zijn gereserveerd voor het configureren van de Log Analytics-werk ruimte, het Automation-account en gerelateerde resources. Dit houdt in dat u drie resource groepen kunt selecteren voor beheer door Wijzigingen bijhouden en inventaris. Deze limiet is alleen van toepassing op gelijktijdige installatie, niet het aantal resource groepen dat kan worden beheerd door een automatiserings functie.

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure op https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. Ga in het Azure Portal naar **virtuele machines**.

2. Gebruik de selectie vakjes om de Vm's te kiezen die u wilt toevoegen aan Wijzigingen bijhouden en inventarisatie. U kunt machines toevoegen voor Maxi maal drie verschillende resource groepen tegelijk. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

    ![Lijst met Vm's](media/automation-enable-changes-from-browse/vmlist.png)

    > [!TIP]
    > Gebruik de filter besturings elementen om Vm's uit verschillende abonnementen, locaties en resource groepen te selecteren. U kunt op het selectie vakje aan de bovenkant klikken om alle virtuele machines in een lijst te selecteren.

3. Selecteer **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

4. De lijst met virtuele machines wordt gefilterd om alleen de virtuele machines weer te geven die zich in hetzelfde abonnement en dezelfde locatie bevinden. Als uw virtuele machines zich in meer dan drie resource groepen bevinden, worden de eerste drie resource groepen geselecteerd.

5. Een bestaande Log Analytics-werk ruimte en een Automation-account zijn standaard geselecteerd. Als u een andere Log Analytics-werk ruimte en een Automation-account wilt gebruiken, klikt u op **aangepast** om ze te selecteren op de pagina aangepaste configuratie. Wanneer u een werk ruimte voor Log Analytics kiest, wordt er een controle uitgevoerd om te bepalen of deze is gekoppeld aan een Automation-account. Als een gekoppeld Automation-account wordt gevonden, wordt het volgende scherm weer gegeven. Klik op **OK**wanneer u klaar bent.

    ![Werk ruimte en account selecteren](media/automation-enable-changes-from-browse/selectworkspaceandaccount.png)

6. Als de geselecteerde werk ruimte niet is gekoppeld aan een Automation-account, wordt het volgende scherm weer gegeven. Selecteer een Automation-account en klik op **OK** wanneer u klaar bent.

    ![Geen werk ruimte](media/automation-enable-changes-from-browse/no-workspace.png)

7. Schakel het selectie vakje naast een wille keurige virtuele machine die u niet wilt inschakelen uit. De selectie van Vm's die niet kunnen worden ingeschakeld, is al opheffen.

8. Klik op **inschakelen** om de functie in te scha kelen die u hebt geselecteerd. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

## <a name="next-steps"></a>Volgende stappen

* [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)
* [Werken met Scope configuraties voor Wijzigingen bijhouden en inventaris](automation-scope-configurations-change-tracking.md)
* [Software op een virtuele machine identificeren](automation-tutorial-installed-software.md)
* [De werk ruimte ontkoppelen van het Automation-account voor Wijzigingen bijhouden en inventaris](automation-unlink-workspace-change-tracking.md)
* [Vm's uit Wijzigingen bijhouden en inventaris verwijderen](automation-remove-vms-from-change-tracking.md)
* [Problemen met wijzigingen in een virtuele Azure-machine oplossen](automation-tutorial-troubleshoot-changes.md)
* [Problemen met Wijzigingen bijhouden-en inventaris problemen oplossen](troubleshoot/change-tracking.md)