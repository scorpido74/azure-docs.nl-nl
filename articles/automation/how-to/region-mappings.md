---
title: Toewijzingen van Azure Automation- en Log Analytics-werkruimtes
description: In dit artikel worden de toewijzingen beschreven die zijn toegestaan tussen een automatiseringsaccount en een Log Analytics Workspace ter ondersteuning van de oplossing
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849510"
---
# <a name="workspace-mappings"></a>Werkruimtetoewijzingen

Wanneer oplossingen zoals Update Management, Change Tracking en Inventory of de Start/Stop VM's tijdens off-hours oplossing inschakelen, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte en een automatiseringsaccount. Deze toewijzing is alleen van toepassing op het automatiseringsaccount en de werkruimte Log Analytics. De resources die rapporteren aan uw automatiseringsaccount of logboekanalysewerkruimte, kunnen zich in andere regio's bevinden.

## <a name="supported-mappings"></a>Ondersteunde toewijzingen

In de volgende tabel worden de ondersteunde toewijzingen weergegeven:

|**Werkruimtegebied Logboekanalyse**|**Azure-automatiseringsregio**|
|---|---|
|**VS**||
|EastUS<sup>1</sup>|EastUS2 EastUS2|
|WestUS2 WestUS2|WestUS2 WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentraal|CanadaCentraal|
|**Azië en Stille Oceaan**||
|AustraliëZuidoost|AustraliëZuidoost|
|ZuidoostAzië|ZuidoostAzië|
|Centraal-India|Centraal-India|
|JapanOost|JapanOost|
|**Europa**||
|UKZuid|UKZuid|
|West-Europa|West-Europa|
|**US Gov**||
|USGovVirginia USGovVirginia|USGovVirginia USGovVirginia|

<sup>1</sup> EastUS-toewijzing voor Log Analytics-werkruimten voor automatiseringsaccounts is geen exacte regio voor het toewijzen van regio's, maar is de juiste toewijzing.

<sup>2</sup> Vanwege capaciteitsbeperkingen is de regio niet beschikbaar bij het maken van nieuwe bronnen. Dit omvat automatiseringsaccounts en Logboekanalyse-werkruimten. Reeds bestaande gekoppelde middelen in de regio moeten echter blijven werken.

## <a name="unlink-workspace"></a>Werkruimte ontkoppelen

Als u besluit uw Automatiseringsaccount niet langer te willen integreren in een Log Analytics-werkruimte, u uw account rechtstreeks loskoppelen van de Azure-portal. Voordat u verder gaat, moet u eerst de updatebeheer, voorraad bijhouden en inventaris wijzigen of de VM's voor het starten/stoppen tijdens off-hours-oplossingen verwijderen als u ze gebruikt. Als u ze niet verwijdert, wordt dit proces verhinderd om door te gaan. Bekijk het artikel voor de specifieke oplossing die u hebt geïmporteerd om inzicht te krijgen in de stappen die nodig zijn om de oplossing te verwijderen.

Nadat u deze oplossingen hebt verwijderd, u de volgende stappen uitvoeren om de koppeling van uw Automatiseringsaccount te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakingsoplossing, hebben mogelijk automatiseringselementen gemaakt en moeten mogelijk ook worden verwijderd voordat de werkruimte wordt ontkoppeld.

1. Open in de Azure-portal uw automatiseringsaccount en selecteer op de pagina Automatiseringsaccount De geselecteerde **werkruimte gekoppeld** onder de sectie **Gerelateerde resources** aan de linkerkant.

2. Klik op de pagina Werkruimte ontkoppelen op **Werkruimte ontkoppelen**. U ontvangt een melding dat u door wilt gaan.

3. Terwijl Azure Automation probeert de koppeling van het account van uw Log Analytics-werkruimte te ontkoppelen, u de voortgang bijhouden onder **Meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Updateschema's - Elk heeft namen die overeenkomen met de update-implementaties die u hebt gemaakt)

* Hybride werknemersgroepen die zijn gemaakt voor de `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`oplossing - Elke wordt op dezelfde manier genoemd als ).

Als u de vm's start/stop tijdens de oplossing buiten kantooruren hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Vm-runbookschema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U ook de koppeling van uw werkruimte loskoppelen van uw automatiseringsaccount vanuit uw Log Analytics-werkruimte. Selecteer op uw werkruimte **automatiseringsaccount** onder **Gerelateerde resources**. Selecteer op de pagina Automatiseringsaccount de optie **Account ontkoppelen**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aan boord gaan van de volgende oplossingen:

Beheer en wijzigingstracking en -voorraad bijwerken:

* Van een [virtuele machine](../automation-onboard-solutions-from-vm.md)
* Vanaf uw [Automatiseringsaccount](../automation-onboard-solutions-from-automation-account.md)
* Bij [het browsen op meerdere machines](../automation-onboard-solutions-from-browse.md)
* Uit een [runbook](../automation-onboard-solutions.md)

VM's starten/stoppen buiten kantooruren

* [Begin-/stopVM's implementeren tijdens off-uren](../automation-solution-vm-management.md)
