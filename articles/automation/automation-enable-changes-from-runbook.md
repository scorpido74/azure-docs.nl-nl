---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 2728964d5bbf83b42251068ffbdea223ff6bd85e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171086"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook

In dit artikel wordt beschreven hoe u een runbook kunt gebruiken om de functie voor [Wijzigingen bijhouden en inventarisatie](change-tracking.md) in te scha kelen voor virtuele machines in uw omgeving. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie. 

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](automation-offering-get-started.md) voor het beheren van computers.
* Een [virtuele machine](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen 

1. Selecteer in de Azure Portal **Automation-accounts**en selecteer vervolgens uw Automation-account in de lijst.
1. Selecteer **inventaris** onder **configuratie beheer**.
1. Selecteer een bestaande Log Analytics werk ruimte of maak een nieuwe. 
1. Klik op **Inschakelen**.

    ![Wijzigingen bijhouden en Inventaris inschakelen](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Te beheren Azure-VM selecteren

Als Wijzigingen bijhouden en inventarisatie is ingeschakeld, kunt u een Azure VM toevoegen voor beheer door de functie.

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

2. Klik op **virtuele machines van Azure toevoegen** om uw virtuele machine toe te voegen.

3. Kies uw virtuele machine in de lijst en klik op **inschakelen**. Met deze actie worden Wijzigingen bijhouden en inventarisatie voor de virtuele machine ingeschakeld.

   ![Wijzigingen bijhouden en inventaris voor de virtuele machine inschakelen](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Als u probeert een andere functie in te scha kelen voordat de installatie van Wijzigingen bijhouden en de inventarisatie is voltooid, ontvangt u dit bericht:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

Het is vereist om bij te werken naar de meest recente Azure-modules en de module [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) te importeren om wijzigingen bijhouden en inventaris voor uw virtuele machine correct in te scha kelen.

1. Selecteer **Modules** onder **Gedeelde resources** in uw Automation-account. 
2. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie. 
3. Klik op **Ja** om alle bestaande Azure-modules bij te werken naar de nieuwste versie.

    ![Modules bijwerken](media/automation-enable-changes-from-runbook/update-modules.png)

4. Ga terug naar **Modules** onder **Gedeelde resources**. 
5. Selecteer **Bladeren galerie** om de module galerie te openen. 
6. Zoek naar AZ. OperationalInsights en importeer deze module in het Automation-account.

    ![De module OperationalInsights importeren](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Een runbook importeren om Wijzigingen bijhouden en inventarisatie in te scha kelen

1. Selecteer **Runbooks** onder **Procesautomatisering** in uw Automation-account.
2. Selecteer **Bladeren in galerie**.
3. Zoeken naar `update and change tracking`.
4. Selecteer het runbook en klik op **Importeren** op de pagina Bron weergeven. 
5. Klik op **OK** om het runbook in het Automation-account te importeren.

   ![Runbook importeren voor installatie](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Klik op de pagina Runbook op **Bewerken** en selecteer **Publiceren**. 
7. Klik in het deelvenster Runbook publiceren op **Ja** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet Wijzigingen bijhouden en inventaris inschakelen voor een virtuele Azure-machine om dit runbook te starten. Het vereist een bestaande VM en resourcegroep met de functie ingeschakeld als parameters.

1. Open het runbook **Enable-MultipleSolution**.

   ![Runbooks voor meerdere oplossingen](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Klik op de startknop en voer in de volgende velden parameterwaarden in:

   * **VMNAME** : de naam van een bestaande virtuele machine die aan wijzigingen bijhouden en inventaris moet worden toegevoegd. Laat dit veld leeg als uy alle VM's in de resourcegroep wilt toevoegen.
   * **VMRESOURCEGROUP**: de naam van de resourcegroep voor de in te schakelen VM's.
   * **SUBSCRIPTIONID**: de abonnement-id van de nieuwe in te schakelen VM. Laat dit veld leeg om het abonnement van de werkruimte te gebruiken. Wanneer u een andere abonnement-id gebruikt, voegt u het Uitvoeren als-account voor uw Automation-account toe als inzender voor het abonnement.
   * **ALREADYONBOARDEDVM** : de naam van de virtuele machine die al hand matig is ingeschakeld voor wijzigingen.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: de naam van de resourcegroep waartoe de VM behoort.
   * **Solution type** : Voer **change tracking**in.

   ![Parameters voor het runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Selecteer **OK** om de runbooktaak te starten.
1. Bewaak de voortgang en eventuele fouten op de pagina van de runbooktaak.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Planningen beheren in Azure Automation](shared-resources/schedules.md) om een runbook te plannen.
* Zie [Wijzigingen bijhouden en inventaris beheren](change-tracking-file-contents.md)voor meer informatie over het werken met de functie.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met de functie.