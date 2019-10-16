---
title: Meer informatie over het onboarden van Updatebeheer-, Wijzigingen bijhouden-en inventaris oplossingen in Azure Automation
description: Meer informatie over het voorbereiden van een virtuele machine van Azure met Updatebeheer, Wijzigingen bijhouden en inventaris oplossingen die deel uitmaken van Azure Automation
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 4/11/2019
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: e117c6f8aa8526392678f37a05ec61b55983a1c7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374426"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Updatebeheer-, Wijzigingen bijhouden-en inventaris oplossingen voor onboarding

Azure Automation biedt oplossingen voor het beheren van beveiligings updates van het besturings systeem, het bijhouden van wijzigingen en het inventariseren van wat op uw computers is geïnstalleerd. Er zijn veel manieren om computers vrij te maken, u kunt de oplossing [van een virtuele machine](automation-onboard-solutions-from-vm.md)onboarden, [van het surfen op meerdere computers](automation-onboard-solutions-from-browse.md), van uw Automation-account of per [runbook](automation-onboard-solutions.md). In dit artikel vindt u informatie over het onboarden van deze oplossingen vanuit uw Automation-account.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com

## <a name="enable-solutions"></a>Oplossingen inschakelen

Navigeer naar uw Automation-account en selecteer **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

Kies de Log Analytics-werk ruimte en het Automation-account en klik op **inschakelen** om de oplossing in te scha kelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Inventaris oplossing voor onboarding](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Bij het inschakelen van oplossingen worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-Account.
>
> Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

De oplossing Wijzigingen bijhouden en Inventaris biedt de mogelijkheid om [Wijzigingen bijhouden](automation-vm-change-tracking.md) en [Inventaris](automation-vm-inventory.md) uit te voeren op uw virtuele machines. In deze stap schakelt u de oplossing in op een virtuele machine.

Wanneer wordt gemeld dat de onboarding van de oplossing Wijzigingen bijhouden en Inventarisatie is voltooid, klikt u op **Updatebeheer** onder **CONFIGURATIEBEHEER**.

Met de oplossing Updatebeheer kunt u updates en patches voor uw Azure-VM's beheren. U kunt de status van beschikbare updates beoordelen, de installatie van vereiste updates plannen en de implementatieresultaten bekijken om te controleren of updates correct zijn toegepast op de VM. Met deze actie is de oplossing voor uw virtuele machine ingeschakeld.

Selecteer **Update beheer** onder **Update beheer**. De geselecteerde werk ruimte Log Analytics is dezelfde werk ruimte die wordt gebruikt in de vorige stap. Klik op **Inschakelen** om de onboarding van de oplossing Updatebeheer uit te voeren. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Update oplossing voor onboarding](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Scope configuratie

Elke oplossing maakt gebruik van een scope configuratie in de werk ruimte om te richten op de computers die de oplossing ophalen. De scope configuratie is een groep van een of meer opgeslagen Zoek opdrachten die wordt gebruikt om het bereik van de oplossing te beperken tot specifieke computers. Voor toegang tot de scope configuraties, in uw Automation-account onder **gerelateerde resources**, selecteert u **werk ruimte**. Selecteer vervolgens in de werk ruimte onder **gegevens bronnen**voor de werk ruimte **Scope configuraties**.

Als de geselecteerde werk ruimte nog niet beschikt over de Updatebeheer-of Wijzigingen bijhouden oplossingen, worden de volgende Scope configuraties gemaakt:

* **MicrosoftDefaultScopeConfig-change tracking**

* **MicrosoftDefaultScopeConfig-updates**

Als de geselecteerde werk ruimte al de oplossing heeft, wordt de oplossing niet opnieuw geïmplementeerd en wordt de scope configuratie niet toegevoegd.

## <a name="saved-searches"></a>Opgeslagen Zoek opdrachten

Wanneer een computer wordt toegevoegd aan de Updatebeheer of de oplossingen voor Wijzigingen bijhouden en inventaris, worden deze toegevoegd aan een van twee opgeslagen Zoek opdrachten in uw werk ruimte. Deze opgeslagen Zoek opdrachten zijn query's die de computers bevatten waarop deze oplossingen zijn gericht.

Navigeer naar uw Automation-account en selecteer **opgeslagen Zoek opdrachten** onder **Algemeen**. De twee opgeslagen Zoek opdrachten die door deze oplossingen worden gebruikt, kunnen worden weer gegeven in de volgende tabel:

|Naam     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Change tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Updates        | Updates__MicrosoftDefaultComputerGroup         |

Selecteer een opgeslagen zoek opdracht om de query weer te geven die wordt gebruikt om de groep te vullen. In de volgende afbeelding ziet u de query en de resultaten:

![Opgeslagen Zoek opdrachten](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Onboarding van Azure-Vm's

Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**of **Update beheer** onder **Update beheer**.

Klik op **+ virtuele machines van Azure toevoegen**en selecteer een of meer virtuele machines in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weer gegeven en kunnen niet worden geselecteerd. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account. Klik op de pagina **updatebeheer inschakelen** op **inschakelen**. Met deze actie worden de geselecteerde virtuele machines toegevoegd aan de computer groep opgeslagen zoek opdracht voor de oplossing.

![Virtuele Azure-machines inschakelen](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Onboarding van een niet-Azure-machine

Machines die niet in azure zijn, moeten hand matig worden toegevoegd. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**of **Update beheer** onder **Update beheer**.

Klik op **niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browser venster geopend met de [instructies voor het installeren en configureren van micro soft Monitoring Agent op de computer](../azure-monitor/platform/log-analytics-agent.md) , zodat de computer kan beginnen met het rapporteren van de oplossing. Als u een machine onboardt die momenteel wordt beheerd door System Center Operations Manager, is een nieuwe agent niet vereist, de werkruimte gegevens worden ingevoerd in de bestaande agent.

## <a name="onboard-machines-in-the-workspace"></a>Computers onboarden in de werk ruimte

Hand matig geïnstalleerde computers of machines die al aan uw werk ruimte rapporteren, moeten worden toegevoegd aan Azure Automation om de oplossing in te scha kelen. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**of **Update beheer** onder **Update beheer**.

Selecteer **machines beheren**. Met deze actie wordt de pagina **computers beheren** geopend. Op deze pagina kunt u de oplossing inschakelen op een geselecteerde set machines, alle beschik bare machines, of de oplossing inschakelen voor alle huidige machines en deze inschakelen op alle toekomstige computers. De knop **machines beheren** kan grijs worden weer gegeven als u eerder de optie **inschakelen op alle beschik bare en toekomstige computers**hebt gekozen.

![Opgeslagen Zoek opdrachten](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alle beschik bare computers

Als u de oplossing voor alle beschik bare computers wilt inschakelen, selecteert u **inschakelen op alle beschik bare computers**. Met deze actie wordt het besturings element uitgeschakeld om computers afzonderlijk toe te voegen. Met deze taak worden alle namen van de computers die rapporteren aan de werk ruimte toegevoegd aan de computer groep opgeslagen Zoek query. Als u deze actie selecteert, wordt de knop **machines beheren** uitgeschakeld.

### <a name="all-available-and-future-machines"></a>Alle beschik bare en toekomstige computers

Als u de oplossing voor alle beschik bare machines en toekomstige computers wilt inschakelen, selecteert u **inschakelen op alle beschik bare en toekomstige computers**. Met deze optie worden de opgeslagen Zoek opdrachten en Scope configuraties uit de werk ruimte verwijderd. Met deze actie wordt de oplossing geopend voor alle Azure-en niet-Azure-computers die rapporteren aan de werk ruimte. Als u deze actie selecteert, wordt de knop **machines beheren** permanent uitgeschakeld omdat er geen scope configuratie meer is.

U kunt de scope configuraties weer toevoegen door de eerste opgeslagen Zoek opdrachten terug toe te voegen. Zie [opgeslagen Zoek opdrachten](#saved-searches)voor meer informatie.

### <a name="selected-machines"></a>Geselecteerde machines

Als u de oplossing voor een of meer computers wilt inschakelen, selecteert u **op geselecteerde machines inschakelen** en klikt u op **toevoegen** naast elke computer die u aan de oplossing wilt toevoegen. Met deze taak worden de geselecteerde computer namen toegevoegd aan de computer groep opgeslagen Zoek query voor de oplossing.

## <a name="unlink-workspace"></a>Werk ruimte ontkoppelen

De volgende oplossingen zijn afhankelijk van een Log Analytics-werk ruimte:

* [Updatebeheer](automation-update-management.md)
* [Tracering wijzigen](automation-change-tracking.md)
* [VM's buiten bedrijfsuren starten/stoppen](automation-solution-vm-management.md)

Als u besluit dat u uw Automation-account niet meer wilt integreren met een Log Analytics-werk ruimte, kunt u uw account rechtstreeks van de Azure Portal ontkoppelen.  Voordat u doorgaat, moet u eerst de eerder genoemde oplossingen verwijderen, anders kan dit proces niet worden voortgezet. Raadpleeg het artikel over de specifieke oplossing die u hebt geïmporteerd om inzicht te krijgen in de stappen die nodig zijn om deze te verwijderen.

Nadat u deze oplossingen hebt verwijderd, kunt u de volgende stappen uitvoeren om het Automation-account te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakings oplossing, hebben mogelijk Automation-assets gemaakt en moeten mogelijk ook worden verwijderd voordat u de werk ruimte ontkoppelt.

1. Open uw Automation-account vanuit het Azure Portal en selecteer op de pagina Automation-account de optie **gekoppelde werk ruimte** in de sectie met de naam **gerelateerde resources** aan de linkerkant.

2. Klik op de pagina werk ruimte ontkoppelen op **werk ruimte ontkoppelen**.

   ![Pagina werk ruimte ontkoppelen](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   U ontvangt een prompt waarin u wordt gevraagd of u wilt door gaan.

3. Terwijl Azure Automation probeert het account te ontkoppelen van uw Log Analytics-werk ruimte, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Update schema's: elk heeft een naam die overeenkomt met de update-implementaties die u hebt gemaakt.

* Hybrid worker-groepen gemaakt voor de oplossing: elke groep krijgt dezelfde naam als machine1. contoso. com _9ceb8108-26c9-4051-b6b3-227600d715c8).

Als u de virtuele machines starten en stoppen hebt gebruikt tijdens een oplossing buiten kantoor uren, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* VM-runbook-schema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U kunt ook uw werk ruimte ontkoppelen van uw Automation-account vanuit uw Log Analytics-werk ruimte. Selecteer in uw werk ruimte **Automation-account** onder **gerelateerde resources**. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="clean-up-resources"></a>Resources opschonen

Een virtuele machine verwijderen uit Updatebeheer:

* Verwijder in uw Log Analytics-werk ruimte de virtuele machine uit de opgeslagen zoek opdracht voor de scope configuratie `MicrosoftDefaultScopeConfig-Updates`. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.
* Verwijder [micro soft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de [log Analytics-agent voor Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studies over de oplossingen voor meer informatie over het gebruik ervan.

* [Zelf studie-updates voor uw virtuele machine beheren](automation-tutorial-update-management.md)

* [Zelf studie-software op een virtuele machine identificeren](automation-tutorial-installed-software.md)

* [Zelf studie: problemen met wijzigingen in een virtuele machine oplossen](automation-tutorial-troubleshoot-changes.md)
