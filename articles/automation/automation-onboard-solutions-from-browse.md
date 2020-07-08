---
title: Azure Automation Updatebeheer van de Azure Portal inschakelen
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit de Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 6eab9b543bfec90c0c338bdc758d690499f083e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204919"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Updatebeheer inschakelen via Azure Portal

In dit artikel wordt beschreven hoe u de [updatebeheer](automation-update-management.md) functie voor vm's kunt inschakelen door te bladeren door de Azure Portal. Om Azure VM's in te schakelen op schaal, moet u een bestaande VM inschakelen met behulp van Updatebeheer. 

Het aantal resource groepen dat u kunt gebruiken voor het beheren van uw Vm's, wordt beperkt door de [implementatie limieten van Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). Implementaties van Resource Manager, niet te verwarren met update-implementaties, zijn beperkt tot vijf resource groepen per implementatie. Twee van deze resource groepen zijn gereserveerd voor het configureren van de Log Analytics-werk ruimte, het Automation-account en gerelateerde resources. Hiermee kunt u drie resource groepen selecteren voor beheer door Updatebeheer. Deze limiet is alleen van toepassing op gelijktijdige installatie, niet het aantal resource groepen dat kan worden beheerd door een automatiserings functie.

> [!NOTE]
> Als Updatebeheer wordt ingeschakeld, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com.

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Ga in het Azure Portal naar **virtuele machines**.

2. Gebruik de selectie vakjes om de Vm's te kiezen die u wilt toevoegen aan Updatebeheer. U kunt machines toevoegen voor Maxi maal drie verschillende resource groepen tegelijk. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

    ![Lijst met Vm's](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > Gebruik de filter besturings elementen om Vm's uit verschillende abonnementen, locaties en resource groepen te selecteren. U kunt op het selectie vakje aan de bovenkant klikken om alle virtuele machines in een lijst te selecteren.

    ![Updatebeheer inschakelen](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Klik op **Services** en selecteer **updatebeheer** voor de functie updatebeheer. 

4. De lijst met virtuele machines wordt gefilterd om alleen de virtuele machines weer te geven die zich in hetzelfde abonnement en dezelfde locatie bevinden. Als uw virtuele machines zich in meer dan drie resource groepen bevinden, worden de eerste drie resource groepen geselecteerd.

5. Een bestaande Log Analytics-werk ruimte en een Automation-account zijn standaard geselecteerd. Als u een andere Log Analytics-werk ruimte en een Automation-account wilt gebruiken, klikt u op **aangepast** om ze te selecteren op de pagina aangepaste configuratie. Wanneer u een werk ruimte voor Log Analytics kiest, wordt er een controle uitgevoerd om te bepalen of deze is gekoppeld aan een Automation-account. Als een gekoppeld Automation-account wordt gevonden, wordt het volgende scherm weer gegeven. Klik op **OK**wanneer u klaar bent.

    ![Werk ruimte en account selecteren](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Als de geselecteerde werk ruimte niet is gekoppeld aan een Automation-account, wordt het volgende scherm weer gegeven. Selecteer een Automation-account en klik op **OK** wanneer u klaar bent.

    ![Geen werk ruimte](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Schakel het selectie vakje naast een wille keurige virtuele machine die u niet wilt inschakelen uit. De selectie van Vm's die niet kunnen worden ingeschakeld, is al opheffen.

8. Klik op **inschakelen** om de functie in te scha kelen die u hebt geselecteerd. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Updates en patches voor uw Azure-VM's beheren](automation-tutorial-update-management.md) voor het gebruiken van Updatebeheer voor VM's.
* Zie [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
* Zie [Problemen met Windows-updateagent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-agent.
* Zie problemen met de [Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux-Update Agent.
