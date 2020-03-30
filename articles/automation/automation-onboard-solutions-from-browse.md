---
title: Meer informatie over het gebruik van oplossingen voor updatebeheer, wijzigingstracking en voorraad voor meerdere VM's in Azure Automation
description: Meer informatie over het aan boord gaan van een Virtuele Azure-machine met updatebeheer-, wijzigingstracking- en voorraadoplossingen die deel uitmaken van Azure Automation
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 385806dca7dcac9fd0aac4c1bf9e1072e7fe5ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979478"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Updatebeheer-, wijzigingstracking- en voorraadoplossingen inschakelen op meerdere VM's

Azure Automation biedt oplossingen voor het beheren van beveiligingsupdates voor besturingssystemen, het bijhouden van wijzigingen en het inventariseren van wat op uw computers is geïnstalleerd. Er zijn meerdere manieren om machines aan boord te nemen, u de oplossing aan boord [nemen vanaf een virtuele machine,](automation-onboard-solutions-from-vm.md)vanaf uw [Automation-account,](automation-onboard-solutions-from-automation-account.md)bij het browsen op virtuele machines of per [runbook.](automation-onboard-solutions.md) In dit artikel wordt deze oplossingen onboarding uitgevoerd wanneer u virtuele machines in Azure doorbladert.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com

## <a name="enable-solutions"></a>Oplossingen inschakelen

Navigeer in de Azure-portal naar **virtuele machines.**

Schakel met de selectievakjes de virtuele machines in die u aan boord wilt hebben met Change Tracking en Inventory of Update Management. Onboarding is beschikbaar voor maximaal drie verschillende resourcegroepen tegelijk. Azure VM's kunnen in elke regio bestaan, ongeacht de locatie van uw automatiseringsaccount.

![Lijst van VM's](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Gebruik de filterbesturingselementen om de lijst met virtuele machines te wijzigen en klik vervolgens op het bovenste selectievakje om alle virtuele machines in de lijst te selecteren.

Klik op de opdrachtbalk op **Services** en selecteer **Bijhouden**wijzigen, **voorraad**of **Beheer bijwerken**.

> [!NOTE]
> **Change tracking** en **Inventory** gebruiken dezelfde oplossing, wanneer de ene is ingeschakeld, is de andere ook ingeschakeld.

De volgende afbeelding is voor Updatebeheer. Het bijhouden en inventaris wijzigen hebben dezelfde indeling en hetzelfde gedrag.

De lijst met virtuele machines wordt gefilterd om alleen de virtuele machines weer te geven die zich in hetzelfde abonnement en dezelfde locatie bevinden. Als uw virtuele machines zich in meer dan drie resourcegroepen bevinden, worden de eerste drie resourcegroepen geselecteerd.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Beperkingen voor onboarding

Het aantal resourcegroepen dat u gebruiken voor onboarding wordt beperkt door de [implementatielimieten van Resource Manager.](../azure-resource-manager/templates/cross-resource-group-deployment.md) Implementaties van Resourcemanager, niet te verwarren met update-implementaties, zijn beperkt tot 5 resourcegroepen per implementatie. Om de integriteit van onboarding te garanderen, zijn 2 van deze resourcegroepen gereserveerd om de log Analytics-werkruimte, het automatiseringsaccount en de bijbehorende resources te configureren. Hierdoor u 3 resourcegroepen selecteren voor implementatie. Deze limiet is alleen van toepassing op gelijktijdige onboarding, niet op het aantal resourcegroepen dat kan worden beheerd door een automatiseringsoplossing.

U ook een runbook gebruiken voor onboarding, zie [Onboard-update- en wijzigingsoplossingen voor Azure Automation.](automation-onboard-solutions.md)

Gebruik de filterbesturingselementen om virtuele machines uit verschillende abonnementen, locaties en resourcegroepen te selecteren.

![Onboard Update-beheeroplossing](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Bekijk de keuzes voor het log Analytics-werkruimte- en automatiseringsaccount. Een bestaande werkruimte en automatiseringsaccount zijn standaard geselecteerd. Als u een andere werkruimte voor Log Analytics en automatiseringsaccount wilt gebruiken, klikt u op **AANGEPAST** om deze te selecteren op de pagina **Aangepaste configuratie.** Wanneer u een Log Analytics-werkruimte kiest, wordt gecontroleerd of deze is gekoppeld aan een automatiseringsaccount. Als er een gekoppeld automatiseringsaccount wordt gevonden, ziet u het volgende scherm. Klik op **OK**.

![Werkruimte en account selecteren](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Als de geselecteerde werkruimte niet is gekoppeld aan een automatiseringsaccount, ziet u het volgende scherm. Selecteer een automatiseringsaccount en klik op **OK** als deze is voltooid.

![Geen werkruimte](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Bij het inschakelen van oplossingen worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-Account.
>
> Zie [Regiotoewijzing voor automatiseringsaccount en logboekanalysewerkruimte voor](how-to/region-mappings.md)een lijst met de ondersteunde toewijzingsparen.

Schakel het selectievakje uit naast een virtuele machine die u niet wilt inschakelen. Virtuele machines die niet kunnen worden ingeschakeld, zijn al uitgeschakeld.

Klik **op Inschakelen** om de oplossing in te schakelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

## <a name="unlink-workspace"></a>Werkruimte ontkoppelen

De volgende oplossingen zijn afhankelijk van een Log Analytics-werkruimte:

* [Updatebeheer](automation-update-management.md)
* [Tracering wijzigen](automation-change-tracking.md)
* [VM's starten/stoppen buiten kantooruren](automation-solution-vm-management.md)

Als u besluit uw Automatiseringsaccount niet langer te willen integreren in een Log Analytics-werkruimte, u uw account rechtstreeks loskoppelen van de Azure-portal. Voordat u verder gaat, moet u eerst de eerder genoemde oplossingen verwijderen, anders wordt dit proces verhinderd. Bekijk het artikel voor de specifieke oplossing die u hebt geïmporteerd om inzicht te krijgen in de stappen die nodig zijn om de oplossing te verwijderen.

Nadat u deze oplossingen hebt verwijderd, u de volgende stappen uitvoeren om de koppeling van uw Automatiseringsaccount te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakingsoplossing, hebben mogelijk automatiseringselementen gemaakt en moeten mogelijk ook worden verwijderd voordat de werkruimte wordt ontkoppeld.

1. Open in de Azure-portal uw automatiseringsaccount en selecteer op de pagina Automatiseringsaccount De geselecteerde **werkruimte gekoppeld** onder de sectie **Gerelateerde resources** aan de linkerkant.

2. Klik op de pagina Werkruimte ontkoppelen op **Werkruimte ontkoppelen**.

   ![Pagina Werkruimte ontkoppelen](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   U ontvangt een prompt waarin u wordt gevraagd of u wilt doorgaan.

3. Terwijl Azure Automation probeert de koppeling van het account van uw Log Analytics-werkruimte te ontkoppelen, u de voortgang bijhouden onder **Meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Updateschema's - Elk heeft namen die overeenkomen met de update-implementaties die u hebt gemaakt)

* Hybride werknemersgroepen die voor de oplossing zijn gemaakt - Elk wordt op dezelfde manier genoemd als machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Als u de vm's start/stop tijdens de oplossing buiten kantooruren hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Vm-runbookschema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U ook de koppeling van uw werkruimte loskoppelen van uw automatiseringsaccount vanuit uw Log Analytics-werkruimte. Selecteer op uw werkruimte **automatiseringsaccount** onder **Gerelateerde resources**. Selecteer op de pagina Automatiseringsaccount de optie **Account ontkoppelen**.

## <a name="troubleshooting"></a>Problemen oplossen

Bij het inwerken van meerdere machines kunnen er machines zijn die kunnen worden weergegeven als **Kan niet inschakelen.** Er zijn verschillende redenen waarom sommige machines mogelijk niet zijn ingeschakeld. In de volgende secties worden mogelijke redenen weergegeven voor de **status Kan niet inschakelen** op een vm wanneer u probeert aan boord te gaan.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM rapporteert aan een\<andere\>werkruimte: ' workspaceName '.  Configuratie wijzigen om deze te gebruiken voor het inschakelen

**Oorzaak:** deze fout geeft aan dat de VM die u aan boord probeert te maken, rapporten naar een andere werkruimte probeert te maken.

**Oplossing:** Klik op **Gebruiken als configuratie** om de beoogde werkruimte voor automatiseringsaccount en Logboekanalyse te wijzigen.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM rapporteert aan een werkruimte die niet beschikbaar is in dit abonnement

**Oorzaak:** de werkruimte waaraan de virtuele machine rapporteert:

* Zit in een ander abonnement, of
* Bestaat niet meer, of
* Zit in een resourcegroep waar toe u geen toegangsmachtigingen hebt

**Oplossing:** Zoek het automatiseringsaccount dat is gekoppeld aan de werkruimte die de VM rapporteert aan en aan boord van de virtuele machine door de scopeconfiguratie te wijzigen.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM-versie of -distributie van het besturingssysteem wordt niet ondersteund

**Oorzaak:** De oplossing wordt niet ondersteund voor alle Linux-distributies of alle versies van Windows.

**Oplossing:** Raadpleeg de [lijst met ondersteunde clients](automation-update-management.md#clients) voor de oplossing.

### <a name="classic-vms-cannot-be-enabled"></a>Klassieke VM's kunnen niet worden ingeschakeld

**Oorzaak:** virtuele machines die het klassieke implementatiemodel gebruiken, worden niet ondersteund.

**Oplossing:** migreer de virtuele machine naar het implementatiemodel van Resource Manager. Zie [Klassieke implementatiemodelbronnen migreren](../virtual-machines/windows/migration-classic-resource-manager-overview.md)voor meer informatie over hoe u dit doen.

### <a name="vm-is-stopped-deallocated"></a>VM is gestopt. (deallocated)

**Oorzaak:** De virtuele machine in niet in **een lopende** staat.

**Oplossing**: Om een VM aan boord te krijgen van een oplossing moet de VM worden uitgevoerd. Klik op de koppeling **VM** starten om de vm te starten zonder van de pagina af te navigeren.

## <a name="clean-up-resources"></a>Resources opschonen

Ga als lid van het werk als u een vm verwijdert uit Updatebeheer:

* Verwijder de VM in uw werkruimte Log Analytics uit `MicrosoftDefaultScopeConfig-Updates`de opgeslagen zoekopdracht naar de scopeconfiguratie. Opgeslagen zoekopdrachten zijn te vinden onder **Algemeen** in uw werkruimte.
* Verwijder de [Microsoft Monitoring-agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de [loganalyse-agent voor Linux.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

Nu de oplossing is ingeschakeld voor uw virtuele machines, gaat u naar het overzichtsartikel Updatebeheer voor meer informatie over het maken van een **update-implementatie** voor uw machines.

> [!div class="nextstepaction"]
> [Updatebeheer - Updates en patches voor uw Azure VM's beheren](./automation-tutorial-update-management.md)

Toevoeging tutorials over de oplossingen en hoe ze te gebruiken:

* [Zelfstudie - Updates voor uw virtuele machine beheren](automation-tutorial-update-management.md)

* [Zelfstudie - Software identificeren op een virtuele machine](automation-tutorial-installed-software.md)

* [Zelfstudie - Problemen met wijzigingen op een virtuele machine oplossen](automation-tutorial-troubleshoot-changes.md)
