---
title: Onboard-update-, wijzigingstracking- en voorraadoplossingen voor Azure Automation
description: Meer informatie over oplossingen voor het vrijgeven van updates en het bijhouden van wijzigingen voor Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457617"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Onboard-update-, wijzigingstracking- en voorraadoplossingen voor Azure Automation

In deze zelfstudie leert u automatisch onboarding uit te voeren van oplossingen voor updates, het bijhouden van wijzigingen en inventarisatie van VM's voor Azure Automation:

> [!div class="checklist"]
> * Onboarding van een Azure-VM uitvoeren
> * Oplossingen inschakelen
> * Modules installeren en bijwerken
> * Het onboarding-runbook importeren
> * Het runbook starten

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

* Azure-abonnement. Als je er nog geen hebt, kun je [je MSDN-abonneevoordeel activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of je aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md) voor de onboarding.

## <a name="onboard-an-azure-vm"></a>Onboarding van een Azure-VM uitvoeren

Er zijn meerdere manieren om onboarding van computers uit te voeren. U kunt onboarding van de oplossing uitvoeren [vanaf een virtuele machine](automation-onboard-solutions-from-vm.md), [door meerdere computers te doorzoeken, ](automation-onboard-solutions-from-browse.md) [via uw Automation-account](automation-onboard-solutions-from-automation-account.md) of via een runbook. Deze zelfstudie begeleidt u bij het inschakelen van Updatebeheer via een runbook. Voor het uitvoeren van onboarding van Azure-VM's op grote schaal moet onboarding van een bestaande VM worden uitgevoerd met de oplossing Wijzigingen bijhouden of Updatebeheer. In deze stap voert u onboarding van een virtuele machine met Updatebeheer en Wijzigingen bijhouden uit.

### <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

Met de oplossingen voor het bijhouden en inventaris van wijzigingen u wijzigingen en [voorraad](automation-vm-inventory.md) op uw virtuele machines [bijhouden.](automation-vm-change-tracking.md) In deze stap schakelt u de oplossingen in op een virtuele machine.

1. Selecteer in de Azure-portal **Automatiseringsaccounts**en selecteer vervolgens uw automatiseringsaccount in de lijst.
1. Selecteer **Voorraad** onder **Configuratiebeheer**.
1. Selecteer een bestaande Log Analytics-werkruimte of maak een nieuwe werkruimte. 
1. Klik op **Inschakelen**.

    ![Onboarding van update-oplossing uitvoeren](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Updatebeheer inschakelen

Met de oplossing Updatebeheer kunt u updates en patches voor uw Azure-VM's beheren. U kunt de status van beschikbare updates beoordelen, de installatie van vereiste updates plannen en de implementatieresultaten bekijken om te controleren of updates correct zijn toegepast op de VM. In deze stap schakelt u de oplossing in voor uw virtuele machine.

1. Selecteer In uw automatiseringsaccount **Updatebeheer** in de sectie **Updatebeheer.**
1. De geselecteerde werkruimte Logboekanalyse is de werkruimte die in de vorige stap wordt gebruikt. Klik op **Inschakelen** om de onboarding van de oplossing Updatebeheer uit te voeren. Terwijl de oplossing Updatebeheer wordt geïnstalleerd, wordt een blauwe voortgangsbalk weergegeven. 

    ![Onboarding van update-oplossing uitvoeren](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Te beheren Azure-VM selecteren

Nu de oplossingen zijn ingeschakeld, kunt u een Azure-VM toevoegen voor de onboarding van deze oplossingen.

1. Selecteer in uw Automatiseringsaccount De optie **Tracking wijzigen** onder **Configuratiebeheer**. 
2. Klik op de pagina Tracking wijzigen op **Azure VM's toevoegen** om uw VM toe te voegen.

3. Selecteer uw VM in de lijst en klik op **Inschakelen**. Met deze actie kunnen de oplossingen voor het bijhouden en inventaris wijzigen van de VM worden mogelijk.

   ![Update-oplossing voor de virtuele machine inschakelen](media/automation-onboard-solutions/enable-change-tracking.png)

4. Wanneer de vm-onboarding-melding is voltooid, selecteert u **Beheer bijwerken** onder **Updatebeheer**.

5. Selecteer **Azure VM's toevoegen** om uw VM toe te voegen.

6. Selecteer uw virtuele machine in de lijst en selecteer **Inschakelen**. Met deze actie u de oplossing Updatebeheer voor de VM gebruiken.

   ![Update-oplossing voor de virtuele machine inschakelen](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Als u niet wacht tot de andere oplossing is voltooid, ontvangt u bij Het inschakelen van de volgende oplossing het bericht:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

Het is vereist om te updaten naar de nieuwste Azure-modules en de [Az.OperationalInsights-module](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) te importeren om de onboarding van oplossingen met succes te automatiseren.

## <a name="update-azure-modules"></a>Azure-modules bijwerken

1. Selecteer **modules** onder Gedeelde **resources**in uw automatiseringsaccount. 
2. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie. 
3. Klik **op Ja** om alle bestaande Azure-modules bij te werken naar de nieuwste versie.

![Modules](media/automation-onboard-solutions/update-modules.png) a bijwerken

### <a name="install-azoperationalinsights-module"></a>Az.OperationalInsights module installeren

1. Selecteer **modules** onder Gedeelde **resources**in het account Automatisering . 
2. Selecteer **Galerie bladeren** om de modulegalerie te openen. 
3. Zoek naar Az.OperationalInsights en importeer deze module in het automation account.

![De module OperationalInsights importeren](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Het onboarding-runbook importeren

1. Selecteer in uw automatiseringsaccount **Runbooks** onder **Procesautomatisering**.
1. Selecteer **Bladeren in galerie**.
1. Zoeken naar `update and change tracking`.
3. Selecteer het runbook en klik op **Importeren** op de pagina Bron weergeven. 
4. Klik op **OK** om het runbook te importeren in het automatiseringsaccount.

   ![Onboarding-runbook importeren](media/automation-onboard-solutions/import-from-gallery.png)

6. Klik op de pagina Runbook op **Bewerken**en selecteer **Vervolgens Publiceren**. 
7. Klik in het deelvenster Runbook publiceren op **Ja** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet oplossingen voor het bijhouden van wijzigingen of het bijwerken van een Azure VM hebben aanboord om deze runbook te starten. Voor dit runbook zijn een bestaande virtuele machine en een resourcegroep vereist, waarbij onboarding van parameters voor de oplossing is uitgevoerd.

1. Open het runbook **Enable-MultipleSolution.**

   ![Runbooks voor meerdere oplossingen](media/automation-onboard-solutions/runbook-overview.png)

1. Klik op de startknop en voer de volgende waarden voor de parameters in.

   * **VMNAME** - Leeg laten. De naam van een bestaande virtuele machine voor onboarding van een oplossing voor updates of het bijhouden van wijzigingen. Door deze waarde leeg te laten, wordt onboarding uitgevoerd voor alle virtuele machines in de resourcegroep.
   * **VMRESOURCEGROUP** - De naam van de resourcegroep voor de virtuele machines waarvoor onboarding moet worden uitgevoerd.
   * **SUBSCRIPTIONID** - Leeg laten. De abonnements-id van de nieuwe virtuele machine waarvoor onboarding moet worden uitgevoerd. Als u deze parameter leeg laat, wordt het abonnement van de werkruimte gebruikt. Wanneer u een andere abonnements-id opgeeft, moet ook het RunAs-account voor dit Automation-account worden toegevoegd als inzender voor dit abonnement.
   * **ALREADYONBOARDEDVM** - De naam van de virtuele machine waarvoor de onboarding van de oplossing voor updates of het bijhouden van wijzigingen handmatig is uitgevoerd.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - De naam van de resourcegroep waartoe de VM behoort.
   * **SOLUTIONTYPE** - Update **of** **ChangeTracking**invoeren .

   ![Parameters voor het runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selecteer **OK** om de runbooktaak te starten.
1. Bewaak de voortgang en eventuele fouten op de pagina van de runbooktaak.

## <a name="clean-up-resources"></a>Resources opschonen

Ga als lid van het werk als u een vm verwijdert uit Updatebeheer:

1. Verwijder de VM in uw werkruimte Log Analytics uit `MicrosoftDefaultScopeConfig-Updates`de opgeslagen zoekopdracht naar de scopeconfiguratie. Opgeslagen zoekopdrachten zijn te vinden onder **Algemeen** in uw werkruimte.
2. Verwijder de [agent Log Analytics voor Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de agent Log Analytics voor [Linux.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Handmatige onboarding van een Azure-VM uitvoeren.
> * Vereiste Azure-modules installeren en bijwerken.
> * Een runbook voor het uitvoeren van onboarding van Azure-VM's importeren.
> * Het runbook voor het automatisch uitvoeren van onboarding van Azure-VM's starten.

Volg deze koppeling voor meer informatie over het plannen van runbooks.

> [!div class="nextstepaction"]
> [Runbooks plannen](automation-schedules.md).
