---
title: Updatebeheer, Wijzigingen bijhouden en inventarisatie oplossingen van een Azure-VM onboarden
description: Leer hoe u een virtuele machine van Azure kunt vrijgeven met Updatebeheer-, Wijzigingen bijhouden-en inventaris oplossingen die deel uitmaken van Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537029"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Updatebeheer, Wijzigingen bijhouden en inventarisatie oplossingen van een virtuele machine in azure onboarden

Azure Automation biedt oplossingen om u te helpen bij het beheren van beveiligings updates van het besturings systeem, het bijhouden van wijzigingen en de inventarisatie wat op uw computers is geïnstalleerd. Er zijn meerdere manieren om computers vrij te maken. U kunt de oplossing van een virtuele machine opheffen, van [uw Automation-account](automation-onboard-solutions-from-automation-account.md), [van het surfen op meerdere machines](automation-onboard-solutions-from-browse.md)of met behulp van een [runbook](automation-onboard-solutions.md). In dit artikel vindt u informatie over het onboarden van deze oplossingen van een virtuele machine van Azure.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-the-solutions"></a>De oplossingen inschakelen

Schakel eerst één of alle drie de oplossingen in op uw virtuele machine:

1. In de [Azure Portal](https://portal.azure.com)selecteert u **virtuele machines** of zoekt en selecteert u **virtuele machines** op de start pagina.
2. Selecteer de virtuele machine waarvoor u een oplossing wilt inschakelen.
3. Selecteer op de pagina VM onder **bewerkingen** **Update beheer**, **inventaris**of **bijhouden van wijzigingen**. De virtuele machine kan bestaan in elke regio, ongeacht de locatie van uw Automation-account. Wanneer u een oplossing op basis van een virtuele machine uitschakelt, `Microsoft.OperationalInsights/workspaces/read` moet u de machtiging hebben om te bepalen of de virtuele machine onboarded is voor een werk ruimte. Zie voor meer informatie over vereiste extra machtigingen de [machtigingen die nodig zijn voor het onboarden van computers](automation-role-based-access-control.md#onboarding-permissions). Zie [Onboarding updatebeheer, wijzigingen bijhouden en Inventory Solutions](automation-onboard-solutions-from-automation-account.md)voor meer informatie over het voorbereiden van meerdere computers tegelijk.

4. Selecteer de Azure Log Analytics-werk ruimte en het Automation-account en klik vervolgens op **inschakelen** om de oplossing in te scha kelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Onboarding van de Updatebeheer oplossing](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Ga naar de andere oplossingen en selecteer **inschakelen**. De vervolg keuzelijsten Log Analytics werk ruimte en Automation-account zijn uitgeschakeld omdat deze oplossingen gebruikmaken van dezelfde werk ruimte en hetzelfde Automation-account als de eerder ingeschakelde oplossing.

> [!NOTE]
> Wijzigingen bijhouden en inventaris gebruiken dezelfde oplossing. Wanneer een van deze oplossingen is ingeschakeld, is de andere ook ingeschakeld.

## <a name="scope-configuration"></a>Scope configuratie

Elke oplossing maakt gebruik van een scope configuratie in de werk ruimte om te richten op de computers die de oplossing ophalen. De scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten die wordt gebruikt om het bereik van de oplossing te beperken tot specifieke computers. Voor toegang tot de scope configuraties:

1. In uw Automation-account, onder **gerelateerde resources**, selecteert u **werk ruimte**. 
2. Selecteer in de werk ruimte onder **gegevens bronnen voor werk ruimte** **Scope configuraties**.
3. Als de geselecteerde werk ruimte nog niet beschikt over de Updatebeheer-of Wijzigingen bijhouden oplossing, worden de volgende Scope configuraties gemaakt:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Als de geselecteerde werk ruimte al de oplossing heeft, wordt de oplossing niet opnieuw geïmplementeerd en wordt de scope configuratie niet toegevoegd.

4. Selecteer de weglatings tekens (**...**) op een van de configuraties en klik vervolgens op **bewerken**. 
5. Selecteer in het deel venster **Scope configuratie bewerken** de optie **computer groepen selecteren**. In het deel venster **computer groepen** worden de opgeslagen Zoek opdrachten weer gegeven die worden gebruikt voor het maken van de scope configuratie.

## <a name="saved-searches"></a>Opgeslagen Zoek opdrachten

Wanneer een computer wordt toegevoegd aan de Updatebeheer, Wijzigingen bijhouden of inventaris oplossing, wordt de computer toegevoegd aan een van twee opgeslagen Zoek opdrachten in uw werk ruimte. De opgeslagen Zoek opdrachten zijn query's die de computers bevatten waarop deze oplossingen zijn gericht.

Ga naar uw werkruimte. Onder **Algemeen**selecteert u **opgeslagen Zoek opdrachten**. De twee opgeslagen Zoek opdrachten die door deze oplossingen worden gebruikt, worden in de volgende tabel weer gegeven:

|Naam     |Categorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Change tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Updates        | Updates__MicrosoftDefaultComputerGroup         |

Selecteer een van de opgeslagen Zoek opdrachten om de query weer te geven die wordt gebruikt om de groep in te vullen. In de volgende afbeelding ziet u de query en de resultaten:

![Opgeslagen Zoek opdrachten](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Werkruimte ontkoppelen

De volgende oplossingen zijn afhankelijk van een Log Analytics-werk ruimte:

* [Updatebeheer](automation-update-management.md)
* [Tracering wijzigen](automation-change-tracking.md)
* [VM's starten/stoppen buiten kantooruren](automation-solution-vm-management.md)

Als u besluit dat u uw Automation-account niet meer wilt integreren met een Log Analytics-werk ruimte, kunt u uw account rechtstreeks van de Azure Portal ontkoppelen.  Voordat u doorgaat, moet u eerst de eerder genoemde oplossingen verwijderen, anders kan dit proces niet worden voortgezet. Raadpleeg het artikel voor de specifieke oplossing die u hebt geïmporteerd om inzicht te krijgen in de stappen die nodig zijn om deze te verwijderen.

Nadat u deze oplossingen hebt verwijderd, kunt u de volgende stappen uitvoeren om het Automation-account te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakings oplossing, hebben mogelijk Automation-assets gemaakt en moeten mogelijk ook worden verwijderd voordat u de werk ruimte ontkoppelt.

1. Open in de Azure Portal uw Automation-account en selecteer **gekoppelde werk ruimte** in de sectie **gerelateerde resources** aan de linkerkant.

2. Klik op de pagina werk ruimte ontkoppelen op **werk ruimte ontkoppelen**.

   ![Pagina werk ruimte ontkoppelen](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   U ontvangt een prompt waarin u wordt gevraagd of u wilt doorgaan.

3. Terwijl Azure Automation probeert het account te ontkoppelen van uw Log Analytics-werk ruimte, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Update schema's: elk heeft een naam die overeenkomt met de update-implementaties die u hebt gemaakt.

* Hybrid worker-groepen gemaakt voor de oplossing: elke groep krijgt dezelfde naam als machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Als u de oplossing VM's buiten bedrijfsuren starten/stoppen hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* VM-runbook-schema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U kunt ook uw werk ruimte ontkoppelen van uw Automation-account vanuit uw Log Analytics-werk ruimte. Selecteer in uw werk ruimte **Automation-account** onder **gerelateerde resources**. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="clean-up-resources"></a>Resources opschonen

Een virtuele machine verwijderen uit Updatebeheer:

* In uw Log Analytics-werk ruimte verwijdert u de virtuele machine uit de opgeslagen zoek opdracht `MicrosoftDefaultScopeConfig-Updates`voor de scope configuratie. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.
* Verwijder de [log Analytics-agent voor Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de [log Analytics-agent voor Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Ga verder met de zelf studies voor de oplossingen voor meer informatie over het gebruik ervan:

* [Zelf studie-updates voor uw virtuele machine beheren](automation-tutorial-update-management.md)

* [Zelf studie-software op een virtuele machine identificeren](automation-tutorial-installed-software.md)

* [Zelf studie: problemen met wijzigingen in een virtuele machine oplossen](automation-tutorial-troubleshoot-changes.md)
