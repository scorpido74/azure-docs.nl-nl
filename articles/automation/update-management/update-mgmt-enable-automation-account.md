---
title: Azure Automation Updatebeheer inschakelen vanuit het Automation-account
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit een Automation-account.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450245"
---
# <a name="enable-update-management-from-an-automation-account"></a>Updatebeheer inschakelen vanaf een Automation-account

In dit artikel wordt beschreven hoe u uw Automation-account kunt gebruiken om de [updatebeheer](update-mgmt-overview.md) -functie voor vm's in uw omgeving in te scha kelen. Om Azure VM's in te schakelen op schaal, moet u een bestaande VM inschakelen met behulp van Updatebeheer.

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../index.yml) voor het beheren van computers.
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Kies de Log Analytics werk ruimte en het Automation-account en selecteer **inschakelen** om updatebeheer in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

    ![Updatebeheer inschakelen](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Virtuele Azure-machines inschakelen

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Selecteer **+ virtuele machines van Azure toevoegen** en selecteer een of meer virtuele machines in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weer gegeven en kunnen niet worden geselecteerd. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

3. Selecteer **inschakelen** om de geselecteerde vm's toe te voegen aan de computer groep opgeslagen zoek opdracht voor de functie.

    ![Virtuele Azure-machines inschakelen](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Vm's buiten Azure inschakelen

Machines die niet in azure zijn, moeten hand matig worden toegevoegd.

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer **niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browser venster geopend met [instructies voor het installeren en configureren van de log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md) , zodat de computer kan beginnen met het rapporteren van updatebeheer. Als u een machine inschakelt die momenteel wordt beheerd door Operations Manager, is een nieuwe agent niet vereist. De werkruimte gegevens worden toegevoegd aan de agent configuratie.

## <a name="enable-machines-in-the-workspace"></a>Computers in de werk ruimte inschakelen

Hand matig geïnstalleerde computers of machines die al aan uw werk ruimte rapporteren, moeten worden toegevoegd aan Azure Automation om Updatebeheer in te scha kelen.

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer **machines beheren**. De knop **machines beheren** kan grijs worden weer gegeven als u eerder de optie **inschakelen op alle beschik bare en toekomstige computers** hebt gekozen

    ![Opgeslagen Zoek opdrachten](media/update-mgmt-enable-automation-account/managemachines.png)

3. Als u Updatebeheer voor alle beschik bare computers wilt inschakelen, selecteert u op de pagina computers beheren de optie **inschakelen op alle beschik bare computers** . Met deze actie wordt het besturings element uitgeschakeld om computers afzonderlijk toe te voegen. Met deze taak worden alle namen van de computers die rapporteren aan de werk ruimte toegevoegd aan de computer groep opgeslagen Zoek query. Als u deze actie selecteert, wordt de knop **machines beheren** uitgeschakeld.

4. Als u de functie wilt inschakelen voor alle beschik bare machines en toekomstige computers, selecteert u **inschakelen op alle beschik bare en toekomstige computers**. Met deze optie worden de opgeslagen Zoek opdrachten en Scope configuraties uit de werk ruimte verwijderd en wordt de functie geopend voor alle Azure-en niet-Azure-computers die rapporteren aan de werk ruimte. Als u deze actie selecteert, wordt de knop **machines beheren** permanent uitgeschakeld, omdat er nog geen scope configuratie is.

5. Indien nodig kunt u de scope configuraties weer toevoegen door de oorspronkelijke opgeslagen Zoek opdrachten opnieuw toe te voegen. Zie [limiet updatebeheer-implementatie bereik](update-mgmt-scope-configuration.md)voor meer informatie.

6. Als u de functie wilt inschakelen voor een of meer computers, selecteert u **inschakelen op geselecteerde computers** en selecteert u naast elke machine **toevoegen** . Met deze taak worden de geselecteerde computer namen toegevoegd aan de computer groep opgeslagen Zoek query voor de functie.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele machines beheren](update-mgmt-manage-updates-for-vm.md)voor meer informatie over het gebruik van updatebeheer voor vm's.

* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.