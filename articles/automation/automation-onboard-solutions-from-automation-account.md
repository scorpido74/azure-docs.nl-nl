---
title: Azure Automation Updatebeheer inschakelen vanuit het Automation-account
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit een Automation-account.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f739134cd066f4dcc7fdf3da16c6db99a54d6265
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204936"
---
# <a name="enable-update-management-from-an-automation-account"></a>Updatebeheer inschakelen vanaf een Automation-account

In dit artikel wordt beschreven hoe u uw Automation-account kunt gebruiken om de [updatebeheer](automation-update-management.md) -functie voor vm's in uw omgeving in te scha kelen. Om Azure VM's in te schakelen op schaal, moet u een bestaande VM inschakelen met behulp van Updatebeheer. 

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com.

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Kies de Log Analytics-werk ruimte en het Automation-account en klik op **inschakelen** om updatebeheer in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

    ![Updatebeheer inschakelen](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Virtuele Azure-machines inschakelen

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Klik op **+ virtuele machines van Azure toevoegen** en selecteer een of meer virtuele machines in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weer gegeven en kunnen niet worden geselecteerd. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account. 

3. Klik op **inschakelen** om de geselecteerde vm's toe te voegen aan de computer groep opgeslagen zoek opdracht voor de functie.

    ![Virtuele Azure-machines inschakelen](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Vm's buiten Azure inschakelen

Machines die niet in azure zijn, moeten hand matig worden toegevoegd. 

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Klik op **niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browser venster geopend met [instructies voor het installeren en configureren van de log Analytics-agent voor Windows](../azure-monitor/platform/log-analytics-agent.md) , zodat de computer kan beginnen met het rapporteren van updatebeheer bewerkingen. Als u een machine inschakelt die momenteel wordt beheerd door Operations Manager, is een nieuwe agent niet vereist en wordt de werkruimte informatie ingevoerd in de bestaande agent.

## <a name="enable-machines-in-the-workspace"></a>Computers in de werk ruimte inschakelen

Hand matig geïnstalleerde computers of machines die al aan uw werk ruimte rapporteren, moeten worden toegevoegd aan Azure Automation om Updatebeheer in te scha kelen. 

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer **machines beheren**. De knop **machines beheren** kan grijs worden weer gegeven als u eerder de optie **inschakelen op alle beschik bare en toekomstige computers** hebt gekozen

    ![Opgeslagen Zoek opdrachten](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Als u Updatebeheer voor alle beschik bare computers wilt inschakelen, selecteert u op de pagina computers beheren de optie **inschakelen op alle beschik bare computers** . Met deze actie wordt het besturings element uitgeschakeld om computers afzonderlijk toe te voegen. Met deze taak worden alle namen van de computers die rapporteren aan de werk ruimte toegevoegd aan de computer groep opgeslagen Zoek query. Als u deze actie selecteert, wordt de knop **machines beheren** uitgeschakeld.

5. Als u de functie wilt inschakelen voor alle beschik bare machines en toekomstige computers, selecteert u **inschakelen op alle beschik bare en toekomstige computers**. Met deze optie worden de opgeslagen Zoek opdrachten en Scope configuraties uit de werk ruimte verwijderd en wordt de functie geopend voor alle Azure-en niet-Azure-computers die rapporteren aan de werk ruimte. Als u deze actie selecteert, wordt de knop **machines beheren** permanent uitgeschakeld, omdat er nog geen scope configuratie is.

6. Indien nodig kunt u de scope configuraties weer toevoegen door de oorspronkelijke opgeslagen Zoek opdrachten opnieuw toe te voegen. Zie [limiet updatebeheer-implementatie bereik](automation-scope-configurations-update-management.md)voor meer informatie.

7. Als u de functie wilt inschakelen voor een of meer computers, selecteert u **inschakelen op geselecteerde machines** en klikt u op **toevoegen** naast elke machine om in te scha kelen voor de functie. Met deze taak worden de geselecteerde computer namen toegevoegd aan de computer groep opgeslagen Zoek query voor de functie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Updates en patches voor uw Azure-VM's beheren](automation-tutorial-update-management.md) voor het gebruiken van Updatebeheer voor VM's.
* Zie [Problemen met Updatebeheer oplossen](troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
* Zie [Problemen met Windows-updateagent oplossen](troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-agent.
* Zie problemen met de [Linux-Update agent oplossen](troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux-Update Agent.
