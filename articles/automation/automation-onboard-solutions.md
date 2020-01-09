---
title: Onboarding van oplossingen voor updates en het bijhouden van wijzigingen voor Azure Automation
description: Meer informatie over oplossingen voor het vrijgeven van updates en het bijhouden van wijzigingen voor Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: d0024b8c43e76e3dd26b4b73c4ae0e09890b3b46
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421846"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Onboarding van oplossingen voor updates en het bijhouden van wijzigingen voor Azure Automation

In deze zelfstudie leert u automatisch onboarding uit te voeren van oplossingen voor updates, het bijhouden van wijzigingen en inventarisatie van VM's voor Azure Automation:

> [!div class="checklist"]
> * Onboarding van een Azure-VM uitvoeren
> * Oplossingen inschakelen
> * Modules installeren en bijwerken
> * Het onboarding-runbook importeren
> * Het runbook starten

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="onboard-an-azure-vm"></a>Onboarding van een Azure-VM uitvoeren

Er zijn meerdere manieren om computers vrij te maken, u kunt de oplossing [van een virtuele machine](automation-onboard-solutions-from-vm.md)onboarden, [van het surfen op meerdere computers](automation-onboard-solutions-from-browse.md) [vanuit uw Automation-account](automation-onboard-solutions-from-automation-account.md)of per runbook. Deze zelfstudie begeleidt u bij het inschakelen van Updatebeheer via een runbook. Voor het uitvoeren van onboarding van Azure-VM's op grote schaal moet onboarding van een bestaande VM worden uitgevoerd met de oplossing Wijzigingen bijhouden of Updatebeheer. In deze stap voert u onboarding van een virtuele machine met Updatebeheer en Wijzigingen bijhouden uit.

### <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

De oplossing Wijzigingen bijhouden en Inventaris biedt de mogelijkheid om [Wijzigingen bijhouden](automation-vm-change-tracking.md) en [Inventaris](automation-vm-inventory.md) uit te voeren op uw virtuele machines. In deze stap schakelt u de oplossing in op een virtuele machine.

1. Selecteer in het menu links **Automation-accounts**, en selecteer vervolgens uw Automation-account in de lijst.
1. Selecteer **Inventaris** onder **CONFIGURATIEBEHEER**.
1. Selecteer een bestaande Log Analytics-werkruimte of maak een nieuwe. Klik op de knop **Inschakelen**.

![Onboarding van update-oplossing uitvoeren](media/automation-onboard-solutions/inventory-onboard.png)

Wanneer wordt gemeld dat de onboarding van de oplossing Wijzigingen bijhouden en Inventarisatie is voltooid, klikt u op **Updatebeheer** onder **CONFIGURATIEBEHEER**.

### <a name="enable-update-management"></a>Updatebeheer inschakelen

Met de oplossing Updatebeheer kunt u updates en patches voor uw Azure-VM's beheren. U kunt de status van beschikbare updates beoordelen, de installatie van vereiste updates plannen en de implementatieresultaten bekijken om te controleren of updates correct zijn toegepast op de VM. In deze stap schakelt u de oplossing in voor uw virtuele machine.

1. Selecteer **Updatebeheer** onder **UPDATEBEHEER** in uw Automation-account.
1. De geselecteerde Log analytics-werkruimte is dezelfde werkruimte als in de vorige stap. Klik op **Inschakelen** om de onboarding van de oplossing Updatebeheer uit te voeren.

![Onboarding van update-oplossing uitvoeren](media/automation-onboard-solutions/update-onboard.png)

Terwijl de oplossing Updatebeheer wordt geïnstalleerd, wordt een blauwe voortgangsbalk weergegeven. Als de oplossing is ingeschakeld, selecteert u **Wijzigingen bijhouden** onder **CONFIGURATIEBEHEER** om naar de volgende stap te gaan.

### <a name="select-azure-vm-to-be-managed"></a>Te beheren Azure-VM selecteren

Nu de oplossingen zijn ingeschakeld, kunt u een Azure-VM toevoegen voor de onboarding van deze oplossingen.

1. Selecteer via uw Automation-account, op de pagina **Wijzigingen bijhouden**, **+ Azure-VM toevoegen** om uw virtuele machine toe te voegen.

1. Selecteer uw virtuele machine in de lijst en selecteer **Inschakelen**. Met deze actie wordt de oplossing Wijzigingen bijhouden en Inventaris voor de virtuele machine ingeschakeld.

   ![Update-oplossing voor de virtuele machine inschakelen](media/automation-onboard-solutions/enable-change-tracking.png)

1. Wanneer wordt gemeld dat de onboarding van de virtuele machine is voltooid, selecteert u **Updatebeheer** onder **UPDATEBEHEER** via uw Automation-account.

1. Selecteer **+ Azure-VM toevoegen** om uw virtuele machine toe te voegen.

1. Selecteer uw virtuele machine in de lijst en selecteer **Inschakelen**. Met deze actie wordt de oplossing Updatebeheer voor de virtuele machine ingeschakeld.

   ![Update-oplossing voor de virtuele machine inschakelen](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Als u niet wacht totdat de andere oplossing is voltooid, wordt u bij het inschakelen van de volgende oplossing een bericht ontvangen: de *installatie van een andere oplossing wordt uitgevoerd op deze of een andere virtuele machine. Wanneer deze installatie is voltooid, wordt de knop inschakelen ingeschakeld en kunt u de installatie van de oplossing op deze virtuele machine aanvragen.*

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

Dit is vereist om de VM bij te werken met de nieuwste Azure-modules en `AzureRM.OperationalInsights` te importeren om de onboarding van oplossingen te automatiseren.

## <a name="update-azure-modules"></a>Azure-modules bijwerken

Selecteer **Modules** onder **GEDEELDE RESOURCES** via uw Automation-account. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie. Selecteer **Ja** op de vraag of u alle bestaande Azure-modules wilt bijwerken naar de nieuwste versie.

![Modules bijwerken](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>De module AzureRM.OperationalInsights installeren

Selecteer op de pagina **Modules** de optie **Bladeren in galerie** om de modulegalerie te openen. Zoek AzureRM.OperationalInsights en importeer deze module in het Automation-account.

![De module OperationalInsights importeren](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Het onboarding-runbook importeren

1. Selecteer **Runbooks** onder **PROCESAUTOMATISERING** via uw Automation-account.
1. Selecteer **Bladeren in galerie**.
1. Zoek **bijwerken en wijzigingen bijhouden**, klik op het runbook en selecteer **Importeren** op de pagina **Bron weergeven**. Selecteer **OK** om het runbook in het Automation-account te importeren.

   ![Onboarding-runbook importeren](media/automation-onboard-solutions/import-from-gallery.png)

1. Selecteer **Bewerken** op de pagina **Runbook** en selecteer vervolgens **Publiceren**. Selecteer **Ja** in het dialoogvenster **Runbook publiceren** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet onboarding van de oplossing Wijzigingen bijhouden of Updatebeheer voor een Azure-VM hebben uitgevoerd om dit runbook te starten. Voor dit runbook zijn een bestaande virtuele machine en een resourcegroep vereist, waarbij onboarding van parameters voor de oplossing is uitgevoerd.

1. Open het runbook Enable-MultipleSolution.

   ![Runbooks voor meerdere oplossingen](media/automation-onboard-solutions/runbook-overview.png)

1. Klik op de startknop en voer de volgende waarden voor de parameters in.

   * **VMNAME** - Leeg laten. De naam van een bestaande virtuele machine voor onboarding van een oplossing voor updates of het bijhouden van wijzigingen. Door deze waarde leeg te laten, wordt onboarding uitgevoerd voor alle virtuele machines in de resourcegroep.
   * **VMRESOURCEGROUP** - De naam van de resourcegroep voor de virtuele machines waarvoor onboarding moet worden uitgevoerd.
   * **SUBSCRIPTIONID** - Leeg laten. De abonnements-id van de nieuwe virtuele machine waarvoor onboarding moet worden uitgevoerd. Als u deze parameter leeg laat, wordt het abonnement van de werkruimte gebruikt. Wanneer u een andere abonnements-id opgeeft, moet ook het RunAs-account voor dit Automation-account worden toegevoegd als inzender voor dit abonnement.
   * **ALREADYONBOARDEDVM** - De naam van de virtuele machine waarvoor de onboarding van de oplossing voor updates of het bijhouden van wijzigingen handmatig is uitgevoerd.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - De naam van de resourcegroep waartoe de virtuele machine behoort.
   * **SOLUTIONTYPE** - Voer **Updates** of **ChangeTracking** in.

   ![Parameters voor het runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selecteer **OK** om de runbooktaak te starten.
1. Bewaak de voortgang en eventuele fouten op de pagina van de runbooktaak.

## <a name="clean-up-resources"></a>Resources opschonen

Een virtuele machine verwijderen uit Updatebeheer:

* Verwijder in uw Log Analytics-werk ruimte de virtuele machine uit de opgeslagen zoek opdracht voor de scope configuratie `MicrosoftDefaultScopeConfig-Updates`. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.
* Verwijder [micro soft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de [log Analytics-agent voor Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Handmatige onboarding van een Azure-VM uitvoeren.
> * Vereiste Azure-modules installeren en bijwerken.
> * Een runbook voor het uitvoeren van onboarding van Azure-VM's importeren.
> * Het runbook voor het automatisch uitvoeren van onboarding van Azure-VM's starten.

Volg deze koppeling voor meer informatie over het plannen van runbooks.

> [!div class="nextstepaction"]
> [Runbooks plannen](automation-schedules.md).
