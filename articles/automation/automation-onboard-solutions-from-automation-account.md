---
title: Meer informatie over het gebruik van oplossingen voor updatebeheer, wijzigingstracking en voorraad in Azure Automation
description: Meer informatie over het aan boord gaan van een Virtuele Azure-machine met updatebeheer-, wijzigingstracking- en voorraadoplossingen die deel uitmaken van Azure Automation
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278673"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Oplossingen voor het beheer van updates aan boord, bij het bijhouden van wijzigingen en voorraad

Azure Automation biedt oplossingen voor het beheren van beveiligingsupdates voor besturingssystemen, het bijhouden van wijzigingen en het inventariseren van wat op uw computers is geïnstalleerd. Er zijn vele manieren om machines aan boord te nemen, u de oplossing aan boord [nemen vanaf een virtuele machine,](automation-onboard-solutions-from-vm.md)van browsen op meerdere [machines,](automation-onboard-solutions-from-browse.md)vanaf uw Automation-account of per [runbook.](automation-onboard-solutions.md) In dit artikel wordt deze oplossingen van uw Automation-account onboarding.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Aanmelden bij Azure op https://portal.azure.com

## <a name="enable-solutions"></a>Oplossingen inschakelen

Navigeer naar uw automatiseringsaccount en selecteer **Voorraad** of **Tracking wijzigen** onder **Configuratiebeheer**.

Kies de werkruimte loganalytics en het automatiseringsaccount en klik op **Inschakelen** om de oplossing in te schakelen. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Onboard Inventaris-oplossing](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Bij het inschakelen van oplossingen worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-Account.
>
> Zie [Regiotoewijzing voor automatiseringsaccount en logboekanalysewerkruimte voor](how-to/region-mappings.md)een lijst met de ondersteunde toewijzingsparen.

De oplossing Wijzigingen bijhouden en Inventaris biedt de mogelijkheid om [Wijzigingen bijhouden](automation-vm-change-tracking.md) en [Inventaris](automation-vm-inventory.md) uit te voeren op uw virtuele machines. In deze stap schakelt u de oplossing in op een virtuele machine.

Wanneer de onboarding-melding voor wijzigingen bijhouden en inventarisoplossing is voltooid, selecteert u **Beheer bijwerken** onder **Updatebeheer**.

Met de updatebeheeroplossing u updates en patches voor uw Azure- en hybride VM's beheren. U de status van beschikbare updates beoordelen, de installatie van vereiste updates plannen en de implementatieresultaten controleren om te controleren of updates op deze updates zijn toegepast.

Op de pagina Oplossing inschakelen is de geselecteerde werkruimte Log Analytics dezelfde werkruimte die in de vorige stap wordt gebruikt. Klik **op Inschakelen** om aan boord te gaan van de updatebeheeroplossing. Het duurt maximaal 15 minuten om de oplossing in te schakelen.

![Update-oplossing aan boord](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Scopeconfiguratie

Elke oplossing maakt gebruik van een scopeconfiguratie in de werkruimte om de computers te targeten die de oplossing krijgen. De scopeconfiguratie is een groep van een of meer opgeslagen zoekopdrachten die wordt gebruikt om het bereik van de oplossing te beperken tot specifieke computers. Als u toegang wilt krijgen tot de scopeconfiguraties, selecteert u **Werkruimte**in uw automatiseringsaccount onder **Gerelateerde resources**. Selecteer vervolgens in de werkruimte onder **Werkruimtegegevensbronnen**de optie **Bereikconfiguraties**.

Als de geselecteerde werkruimte nog geen oplossingen voor updatebeheer of wijzigingstracking heeft, worden de volgende scopeconfiguraties gemaakt:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-updates**

Als de geselecteerde werkruimte al de oplossing heeft, wordt de oplossing niet opnieuw geïmplementeerd en wordt de scopeconfiguratie niet toegevoegd.

## <a name="saved-searches"></a>Opgeslagen zoekopdrachten

Wanneer een computer wordt toegevoegd aan het updatebeheer of de oplossingen voor het bijhouden en inventaris wijzigen, worden deze toegevoegd aan een van de twee opgeslagen zoekopdrachten in uw werkruimte. Deze opgeslagen zoekopdrachten zijn query's die de computers bevatten die zijn gericht op deze oplossingen.

Navigeer naar de werkruimte Log Analytics en selecteer **Opgeslagen zoekopdrachten** onder **Algemeen**. De twee opgeslagen zoekopdrachten die door deze oplossingen worden gebruikt, zijn te zien in de volgende tabel:

|Name     |Categorie  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking (ChangeTracking)       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Updates        | Updates__MicrosoftDefaultComputerGroup         |

Selecteer een opgeslagen zoekopdracht om de query weer te geven die is gebruikt om de groep te vullen. In de volgende afbeelding worden de query en de resultaten weergegeven:

![Opgeslagen zoekopdrachten](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Azure VM's aan boord

Selecteer op uw automatiseringsaccount **Voorraad-** of **Wijzigingstracking** onder **Configuratiebeheer**of **Updatebeheer** onder **Updatebeheer**.

Klik **op + Azure VM's toevoegen,** selecteer een of meer VM's in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weergegeven en kunnen niet worden geselecteerd. Azure VM's kunnen in elke regio bestaan, ongeacht de locatie van uw automatiseringsaccount. Klik op de pagina **Updatebeheer inschakelen** op **Inschakelen**. Met deze actie worden de geselecteerde VM's toegevoegd aan de computergroep die is opgeslagen bij het zoeken naar de oplossing.

![Azure VM's inschakelen](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Onboarding van een niet-Azure-machine

Machines die niet in Azure staan, moeten handmatig worden toegevoegd. Selecteer op uw automatiseringsaccount **Voorraad-** of **Wijzigingstracking** onder **Configuratiebeheer**of **Updatebeheer** onder **Updatebeheer**.

Klik **op Niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browservenster geopend met de [instructies voor het installeren en configureren van de Microsoft Monitoring Agent op de machine,](../azure-monitor/platform/log-analytics-agent.md) zodat de machine kan beginnen met rapporteren aan de oplossing. Als u een machine instelt die momenteel wordt beheerd door System Center Operations Manager, is er geen nieuwe agent vereist, de werkruimtegegevens worden ingevoerd in de bestaande agent.

## <a name="onboard-machines-in-the-workspace"></a>Boordmachines in de werkruimte

Handmatig geïnstalleerde machines of machines die al naar uw werkruimte worden verzonden, moeten worden toegevoegd aan Azure Automation om de oplossing in te schakelen. Selecteer op uw automatiseringsaccount **Voorraad-** of **Wijzigingstracking** onder **Configuratiebeheer**of **Updatebeheer** onder **Updatebeheer**.

Selecteer **Machines beheren**. Met deze actie wordt de pagina **Machines beheren** geopend. Op deze pagina u de oplossing inschakelen op een bepaalde set machines, alle beschikbare machines, of de oplossing inschakelen voor alle huidige machines en deze inschakelen op alle toekomstige machines. De knop **Machines beheren** kan grijs worden weergegeven als u eerder de optie Inschakelen op alle beschikbare en **toekomstige machines**hebt gekozen.

![Opgeslagen zoekopdrachten](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alle beschikbare machines

Als u de oplossing voor alle beschikbare machines wilt inschakelen, selecteert u **Inschakelen op alle beschikbare machines**. Met deze actie wordt het besturingselement uitgeschakeld om machines afzonderlijk toe te voegen. Met deze taak worden alle namen van de machines die naar de werkruimte rapporteren, toegevoegd aan de door de computergroep opgeslagen zoekopdracht. Als deze actie is geselecteerd, schakelt u de knop **Machines beheren** uit.

### <a name="all-available-and-future-machines"></a>Alle beschikbare en toekomstige machines

Als u de oplossing voor alle beschikbare machines en toekomstige machines wilt inschakelen, selecteert u **Inschakelen op alle beschikbare en toekomstige machines.** Met deze optie worden de opgeslagen zoekopdrachten en scopeconfiguraties uit de werkruimte verwijderd. Met deze actie wordt de oplossing geopend voor alle Azure- en niet-Azure-machines die naar de werkruimte worden verzonden. Als deze actie is geselecteerd, schakelt deze actie de knop **Machines beheren** permanent uit omdat er geen scopeconfiguratie meer over is.

U de scopeconfiguraties weer toevoegen door de initiële opgeslagen zoekopdrachten terug te voegen. Zie [Opgeslagen zoekopdrachten](#saved-searches)voor meer informatie.

### <a name="selected-machines"></a>Geselecteerde machines

Als u de oplossing voor een of meer machines wilt inschakelen, selecteert u **Inschakelen op geselecteerde machines** en klikt u op **Toevoegen** naast elke machine die u aan de oplossing wilt toevoegen. Met deze taak worden de geselecteerde machinenamen toegevoegd aan de computergroep opgeslagen zoekopdracht voor de oplossing.

## <a name="unlink-workspace"></a>Werkruimte ontkoppelen

De volgende oplossingen zijn afhankelijk van een Log Analytics-werkruimte:

* [Updatebeheer](automation-update-management.md)
* [Tracering wijzigen](automation-change-tracking.md)
* [VM's starten/stoppen buiten kantooruren](automation-solution-vm-management.md)

Als u besluit uw Automatiseringsaccount niet langer te willen integreren in een Log Analytics-werkruimte, u uw account rechtstreeks loskoppelen van de Azure-portal.  Voordat u verdergaat, moet u eerst de eerder genoemde oplossingen verwijderen, anders wordt dit proces verhinderd. Bekijk het artikel voor de specifieke oplossing die u hebt geïmporteerd om te begrijpen welke stappen nodig zijn om de oplossing te verwijderen.

Nadat u deze oplossingen hebt verwijderd, u de volgende stappen uitvoeren om de koppeling van uw Automatiseringsaccount te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakingsoplossing, hebben mogelijk automatiseringselementen gemaakt en moeten mogelijk ook worden verwijderd voordat de werkruimte wordt ontkoppeld.

1. Open in de Azure-portal uw automatiseringsaccount en selecteer op de pagina Automatiseringsaccount De geselecteerde **werkruimte Gekoppeld** onder de sectie **Gerelateerde resources** aan de linkerkant.

2. Klik op de pagina Werkruimte ontkoppelen op **Werkruimte ontkoppelen**.

   ![Pagina Werkruimte ontkoppelen](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   U ontvangt een melding dat u door wilt gaan.

3. Terwijl Azure Automation probeert de koppeling van het account van uw Log Analytics-werkruimte te ontkoppelen, u de voortgang bijhouden onder **Meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Updateschema's - Elk heeft namen die overeenkomen met de update-implementaties die u hebt gemaakt.

* Hybride werknemersgroepen die voor de oplossing zijn gemaakt - Elk wordt op dezelfde manier genoemd als machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Als u de VM's starten en stoppen tijdens de oplossing buiten kantooruren hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Vm-runbookschema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U ook de koppeling van uw werkruimte loskoppelen van uw automatiseringsaccount vanuit uw Log Analytics-werkruimte. Selecteer op uw werkruimte **automatiseringsaccount** onder **Gerelateerde resources**. Selecteer op de pagina Automatiseringsaccount de optie **Account ontkoppelen**.

## <a name="clean-up-resources"></a>Resources opschonen

Ga als lid van het werk als u een vm verwijdert uit Updatebeheer:

* Verwijder de VM in uw werkruimte Log Analytics uit `MicrosoftDefaultScopeConfig-Updates`de opgeslagen zoekopdracht naar de scopeconfiguratie. Opgeslagen zoekopdrachten zijn te vinden onder **Algemeen** in uw werkruimte.
* Verwijder de [Microsoft Monitoring-agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) of de [loganalyse-agent voor Linux.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

Ga verder naar de tutorials over de oplossingen om te leren hoe ze te gebruiken.

* [Zelfstudie - Updates voor uw virtuele machine beheren](automation-tutorial-update-management.md)

* [Zelfstudie - Software identificeren op een virtuele machine](automation-tutorial-installed-software.md)

* [Zelfstudie - Problemen met wijzigingen op een virtuele machine oplossen](automation-tutorial-troubleshoot-changes.md)
