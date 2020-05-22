---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: a11cc7f0c3e3c47fa1101272a73659368349fa84
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749170"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook

In dit artikel wordt beschreven hoe u een runbook kunt gebruiken om de functie voor [Wijzigingen bijhouden en inventarisatie](change-tracking.md) in te scha kelen voor virtuele machines in uw omgeving. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie. 

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen 

1. Selecteer in de Azure Portal **Automation-accounts**en selecteer vervolgens uw Automation-account in de lijst.
1. Selecteer **inventaris** onder **configuratie beheer**.
1. Selecteer een bestaande Log Analytics werk ruimte of maak een nieuwe. 
1. Klik op **Inschakelen**.

    ![Wijzigingen bijhouden en Inventaris inschakelen](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecteer de Azure-VM die u wilt beheren

Als Wijzigingen bijhouden en inventarisatie is ingeschakeld, kunt u een Azure VM toevoegen voor beheer door de functie.

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

2. Klik op **virtuele machines van Azure toevoegen** om uw virtuele machine toe te voegen.

3. Kies uw virtuele machine in de lijst en klik op **inschakelen**. Met deze actie worden Wijzigingen bijhouden en inventarisatie voor de virtuele machine ingeschakeld.

   ![Wijzigingen bijhouden en inventaris voor de virtuele machine inschakelen](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Als u probeert een andere functie in te scha kelen voordat de installatie van Wijzigingen bijhouden en de inventarisatie is voltooid, ontvangt u dit bericht:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

Het is vereist om bij te werken naar de meest recente Azure-modules en de module [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) te importeren om wijzigingen bijhouden en inventaris voor uw virtuele machine correct in te scha kelen.

1. Selecteer in uw Automation-account **modules** onder **gedeelde bronnen**. 
2. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie. 
3. Klik op **Ja** om alle bestaande Azure-modules bij te werken naar de meest recente versie.

    ![Modules bijwerken](media/automation-enable-changes-from-runbook/update-modules.png)

4. Ga terug naar **modules** onder **gedeelde bronnen**. 
5. Selecteer **Bladeren galerie** om de module galerie te openen. 
6. Zoek naar AZ. OperationalInsights en importeer deze module in het Automation-account.

    ![De module OperationalInsights importeren](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Een runbook importeren om Wijzigingen bijhouden en inventarisatie in te scha kelen

1. Selecteer in uw Automation-account **Runbooks** onder **proces automatisering**.
2. Selecteer **Bladeren in galerie**.
3. Zoek naar `update and change tracking`.
4. Selecteer het runbook en klik op **importeren** op de pagina bron weer geven. 
5. Klik op **OK** om het runbook te importeren in het Automation-account.

   ![Runbook importeren voor installatie](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Klik op de pagina Runbook op **bewerken**en selecteer vervolgens **publiceren**. 
7. Klik in het deel venster Runbook publiceren op **Ja** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet Wijzigingen bijhouden en inventaris inschakelen voor een virtuele Azure-machine om dit runbook te starten. Hiervoor is een bestaande virtuele machine en resource groep vereist waarvoor de functie is ingeschakeld voor para meters.

1. Open het runbook **Enable-MultipleSolution** .

   ![Runbooks voor meerdere oplossingen](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Klik op de knop Start en voer parameter waarden in de volgende velden in:

   * **VMNAME** : de naam van een bestaande virtuele machine die aan wijzigingen bijhouden en inventaris moet worden toegevoegd. Laat dit veld leeg om alle virtuele machines in de resource groep toe te voegen.
   * **VMRESOURCEGROUP** : de naam van de resource groep voor de virtuele machines die u wilt inschakelen.
   * **SUBSCRIPTIONID** : de abonnements-id van de nieuwe virtuele machine die u wilt inschakelen. Laat dit veld leeg om het abonnement van de werk ruimte te gebruiken. Wanneer u een andere abonnements-ID gebruikt, voegt u het uitvoeren als-account voor uw Automation-account toe als bijdrager voor het abonnement.
   * **ALREADYONBOARDEDVM** : de naam van de virtuele machine die al hand matig is ingeschakeld voor wijzigingen.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** : de naam van de resource groep waartoe de virtuele machine behoort.
   * **Solution type** : Voer **change tracking**in.

   ![Parameters voor het runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Selecteer **OK** om de runbooktaak te starten.
1. Bewaak de voortgang en eventuele fouten op de pagina van de runbooktaak.

## <a name="next-steps"></a>Volgende stappen

* [Runbooks plannen](automation-schedules.md)
* [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)
* [Werken met Scope configuraties voor Wijzigingen bijhouden en inventaris](automation-scope-configurations-change-tracking.md)
* [Software op een virtuele machine identificeren](automation-tutorial-installed-software.md)
* [De werk ruimte ontkoppelen van het Automation-account voor Wijzigingen bijhouden en inventaris](automation-unlink-workspace-change-tracking.md)
* [Vm's uit Wijzigingen bijhouden en inventaris verwijderen](automation-remove-vms-from-change-tracking.md)
* [Problemen met wijzigingen in een virtuele Azure-machine oplossen](automation-tutorial-troubleshoot-changes.md)
* [Problemen met Wijzigingen bijhouden-en inventaris problemen oplossen](troubleshoot/change-tracking.md)
