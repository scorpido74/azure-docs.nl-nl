---
title: Toewijzingen van Azure Automation- en Log Analytics-werkruimtes
description: In dit artikel worden de toewijzingen beschreven die zijn toegestaan tussen een Automatiseringsaccount en een Log Analytics Workspace ter ondersteuning van de oplossing
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7f6508648be1e857a29f46b57e8309a7ec797291
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681839"
---
# <a name="workspace-mappings"></a>Werkruimtetoewijzingen

Wanneer oplossingen zoals UpdateManagement, Change Tracking en Inventory of Start/Stop VM's tijdens bepaalde vrije uren worden inschakeling en worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte en een Automatiseringsaccount. Deze toewijzing is alleen van toepassing op het automatiseringsaccount en de werkruimte Log Analytics. De resources die rapporteren aan uw automatiseringsaccount of logboekanalysewerkruimte, kunnen zich in andere regio's bevinden.

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

<sup>2</sup> Vanwege capaciteitsbeperkingen is de regio niet beschikbaar bij het maken van nieuwe bronnen. Dit omvat Automatiseringsaccounts en Log Analytics-werkruimten. Reeds bestaande gekoppelde middelen in de regio moeten echter blijven werken.

## <a name="unlink-workspace"></a>Werkruimte ontkoppelen

Als u besluit dat u uw Automatiseringsaccount niet langer wilt integreren met een Log Analytics-werkruimte, u uw account rechtstreeks loskoppelen van de Azure-portal. Voordat u verder gaat, moet u eerst de updatebeheer, het bijhouden en de voorraad wijzigen en VM's starten/stoppen tijdens off-hours-oplossingen verwijderen als u ze gebruikt. Als u ze niet verwijdert, u de ontkoppelingsbewerking niet voltooien. Bekijk het artikel voor de specifieke oplossing die u hebt geïmporteerd om te begrijpen welke stappen nodig zijn om de oplossing te verwijderen.

Nadat u deze oplossingen hebt verwijderd, u de volgende stappen uitvoeren om de koppeling van uw Automatiseringsaccount te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakingsoplossing, hebben mogelijk automatiseringselementen gemaakt en moeten mogelijk worden verwijderd voordat de werkruimte wordt ontkoppeld.

1. Open uw Automatiseringsaccount vanuit de Azure-portal. Selecteer op de pagina Automatiseringsaccount de optie **Gekoppelde werkruimte** onder **Gerelateerde resources**.

2. Klik op de pagina Werkruimte ontkoppelen op **Werkruimte ontkoppelen**. U ontvangt een snelle verificatie als u door wilt gaan.

3. Terwijl Azure Automation probeert de koppeling van het account van uw Log Analytics-werkruimte te ontkoppelen, u de voortgang bijhouden onder **Meldingen** in het menu.

4. Als u de oplossing Updatebeheer hebt gebruikt, u optioneel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

    * Updateschema's - Elk heeft een naam die overeenkomt met een update-implementatie die u hebt gemaakt.
    * Hybride werknemersgroepen die zijn gemaakt voor de `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`oplossing - Elk heeft een naam die lijkt op .

5. Als u de start-/stop-VM's tijdens de oplossing voor buitenkantoor hebt gebruikt, u optioneel de volgende items verwijderen die niet nodig zijn nadat u de oplossing hebt verwijderd.

    * Vm-runbookschema's starten en stoppen
    * VM-runbooks starten en stoppen
    * Variabelen

U de koppeling van uw werkruimte ook loskoppelen van uw Automatiseringsaccount in de werkruimte. 

1. Selecteer in de werkruimte **automatiseringsaccount** onder **Gerelateerde resources**. 
2. Selecteer op de pagina Automatiseringsaccount de optie **Account ontkoppelen**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van oplossingen voor updatebeheer en wijzigingstracking en -voorraad:

    * Van een [virtuele machine](../automation-onboard-solutions-from-vm.md)
    * Vanaf uw [Automatiseringsaccount](../automation-onboard-solutions-from-automation-account.md)
    * Bij [het browsen op meerdere machines](../automation-onboard-solutions-from-browse.md)
    * Uit een [runbook](../automation-onboard-solutions.md)

* Meer informatie over het aan boord gaan van de VM's voor starten/stoppen tijdens de oplossing voor buitenkantooruren:

    * [Begin-/stopVM's implementeren tijdens vrije uren](../automation-solution-vm-management.md)
