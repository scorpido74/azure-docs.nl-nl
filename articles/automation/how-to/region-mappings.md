---
title: Azure Automation-en Log Analytics werkruimte toewijzingen
description: In dit artikel worden de toewijzingen beschreven die zijn toegestaan tussen een Automation-account en een Log Analytics-werk ruimte.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b00b25dc4eaea5bc8a3f5fbd42389aff501f14a
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901011"
---
# <a name="workspace-mappings"></a>Werkruimtetoewijzingen

In Azure Automation kunt u de volgende oplossingen inschakelen: ' Updatebeheer ', ' Wijzigingen bijhouden en inventariseren ' en ' Vm's starten/stoppen na kantoor uren '. Wanneer u dit doet, moet u er echter rekening mee houden dat alleen bepaalde regio's worden ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account in uw abonnement. Deze toewijzing is alleen van toepassing op het Automation-account en de Log Analytics-werk ruimte. De Log Analytics-werk ruimte en het Automation-account moeten zich in hetzelfde abonnement bevinden, maar kunnen zich in verschillende resource groepen bevinden die in dezelfde regio zijn geïmplementeerd.

Zie [log Analytics-werk ruimte en het Automation-account](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)voor meer informatie.

## <a name="supported-mappings"></a>Ondersteunde toewijzingen

De volgende tabel bevat de ondersteunde toewijzingen:

|**Log Analytics werkruimte regio**|**Azure Automation regio**|
|---|---|
|**VS**||
|Oost,<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**Azië en Stille Oceaan**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|West-Europa|West-Europa|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> -oostus toewijzing voor log Analytics-werk ruimten aan Automation-accounts is niet exact een regionale toewijzing, maar is de juiste toewijzing.

<sup>2</sup> vanwege capaciteits beperkingen is de regio niet beschikbaar wanneer u nieuwe resources maakt. Dit omvat Automation-accounts en Log Analytics-werk ruimten. Al bestaande gekoppelde resources in de regio moeten echter blijven werken.

## <a name="unlink-a-workspace"></a>Een werk ruimte ontkoppelen

Als u besluit dat u uw Automation-account niet meer wilt integreren met een Log Analytics-werk ruimte, kunt u uw account rechtstreeks van de Azure Portal ontkoppelen. Voordat u doorgaat, moet u eerst Updatebeheer, Wijzigingen bijhouden en inventarisatie, en ' Vm's starten/stoppen na kantoor uren ' verwijderen als u deze gebruikt. Als u deze niet verwijdert, kunt u de ontkoppelings bewerking niet volt ooien. Raadpleeg het artikel voor elke oplossing die u inschakelt om de stappen te begrijpen die nodig zijn om deze te verwijderen.

Nadat u deze hebt verwijderd, kunt u de volgende stappen uitvoeren om het Automation-account te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakings oplossing, hebben mogelijk Automation-assets gemaakt en moeten mogelijk worden verwijderd voordat u de werk ruimte ontkoppelt.

1. Open uw Automation-account vanuit het Azure Portal. Selecteer op de pagina **Automation-account** de optie **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Op de pagina **werk ruimte ontkoppelen** selecteert u **werk ruimte ontkoppelen**. U ontvangt een prompt waarin u wordt gevraagd of u wilt door gaan.

3. Terwijl Azure Automation probeert het account te ontkoppelen van uw Log Analytics-werk ruimte, kunt u de voortgang bijhouden onder **meldingen** in het menu.

4. Als u ' Updatebeheer ' hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u deze hebt verwijderd.

    * Update schema's: elk heeft een naam die overeenkomt met een update-implementatie die u hebt gemaakt.
    * Hybrid worker-groepen die zijn gemaakt voor de oplossing: elk heeft `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`een naam die vergelijkbaar is met.

5. Als u ' Vm's starten/stoppen tijdens off-uren ' hebt gebruikt, kunt u desgewenst de volgende items verwijderen die niet meer nodig zijn nadat u deze hebt verwijderd.

    * VM-runbook-schema's starten en stoppen
    * VM-runbooks starten en stoppen
    * Variabelen

U kunt de werk ruimte ook ontkoppelen van uw Automation-account in de werk ruimte.

1. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**. 
2. Selecteer op de pagina **Automation-account** de optie **account loskoppelen**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van ' Updatebeheer ' en ' Wijzigingen bijhouden en inventaris ':

    * Van een [virtuele machine](../automation-onboard-solutions-from-vm.md).
    * Vanuit uw [Automation-account](../automation-onboard-solutions-from-automation-account.md).
    * Wanneer u op [meerdere computers bladert](../automation-onboard-solutions-from-browse.md).
    * Vanuit een [runbook](../automation-onboard-solutions.md).

* Meer informatie over het gebruik van ' Vm's starten/stoppen na ' uren ':

    * [Virtuele machines starten/stoppen tijdens het uitschakelen](../automation-solution-vm-management.md)van het overzicht.
