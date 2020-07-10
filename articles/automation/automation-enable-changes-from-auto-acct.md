---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit Automation-account
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een Automation-account.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 302e0cc9b77605afd4012e95fc7c0ab8a22fef3e
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186313"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account

In dit artikel wordt beschreven hoe u uw Automation-account kunt gebruiken om de functie voor [Wijzigingen bijhouden en inventarisatie](change-tracking.md) in te scha kelen voor virtuele machines in uw omgeving. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie. 

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](./index.yml) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. Navigeer naar uw Automation-account en selecteer **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Kies de Log Analytics werk ruimte en het Automation-account en klik op **inschakelen** om wijzigingen bijhouden en inventarisatie in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

    ![Wijzigingen bijhouden en Inventaris inschakelen](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="enable-azure-vms"></a>Virtuele Azure-machines inschakelen

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Klik op **+ virtuele machines van Azure toevoegen** en selecteer een of meer virtuele machines in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weer gegeven en kunnen niet worden geselecteerd. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account. 

3. Klik op **inschakelen** om de geselecteerde vm's toe te voegen aan de computer groep opgeslagen zoek opdracht voor de functie. Zie [beperken wijzigingen bijhouden en inventarisatie bereik](automation-scope-configurations-change-tracking.md)voor meer informatie.

    ![Virtuele Azure-machines inschakelen](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Vm's buiten Azure inschakelen

Machines die niet in azure zijn, moeten hand matig worden toegevoegd. 

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Klik op **niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browser venster geopend met [instructies voor het installeren en configureren van de log Analytics-agent voor Windows](../azure-monitor/platform/log-analytics-agent.md) , zodat de computer kan beginnen met het rapporteren van wijzigingen bijhouden-en inventarisatie bewerkingen. Als u een machine inschakelt die momenteel wordt beheerd door Operations Manager, is een nieuwe agent niet vereist en wordt de werkruimte informatie ingevoerd in de bestaande agent.

## <a name="enable-machines-in-the-workspace"></a>Computers in de werk ruimte inschakelen

Hand matig geïnstalleerde computers of machines die al aan uw werk ruimte rapporteren, moeten worden toegevoegd aan Azure Automation voor Wijzigingen bijhouden en inventaris moeten worden ingeschakeld. 

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Selecteer **machines beheren**. De knop **machines beheren** kan grijs worden weer gegeven als u eerder de optie **inschakelen op alle beschik bare en toekomstige computers** hebt gekozen

    ![Opgeslagen Zoek opdrachten](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Als u Wijzigingen bijhouden en inventaris voor alle beschik bare computers wilt inschakelen, selecteert u **inschakelen op alle beschik bare computers** op de pagina machines beheren. Met deze actie wordt het besturings element uitgeschakeld om computers afzonderlijk toe te voegen. Met deze taak worden alle namen van de computers die rapporteren aan de werk ruimte toegevoegd aan de computer groep opgeslagen Zoek query. Als u deze actie selecteert, wordt de knop **machines beheren** uitgeschakeld.

4. Als u de functie wilt inschakelen voor alle beschik bare machines en toekomstige computers, selecteert u **inschakelen op alle beschik bare en toekomstige computers**. Met deze optie worden de opgeslagen Zoek opdrachten en Scope configuraties uit de werk ruimte verwijderd en wordt de functie geopend voor alle Azure-en niet-Azure-computers die rapporteren aan de werk ruimte. Als u deze actie selecteert, wordt de knop **machines beheren** permanent uitgeschakeld, omdat er nog geen scope configuratie is.

5. Indien nodig kunt u de scope configuraties weer toevoegen door de oorspronkelijke opgeslagen Zoek opdrachten opnieuw toe te voegen. Zie [beperken wijzigingen bijhouden en inventarisatie bereik](automation-scope-configurations-change-tracking.md)voor meer informatie.

6. Als u de functie wilt inschakelen voor een of meer computers, selecteert u **inschakelen op geselecteerde machines** en klikt u op **toevoegen** naast elke machine om in te scha kelen voor de functie. Met deze taak worden de geselecteerde computer namen toegevoegd aan de computer groep opgeslagen Zoek query voor de functie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)als u de functie wilt gebruiken.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met de functie.
