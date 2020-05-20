---
title: Ondersteunings regio's voor gekoppelde Log Analytics-werk ruimte
description: In dit artikel wordt uitgelegd hoe u regio toewijzingen kunt ondersteunen tussen een Automation-account en een Log Analytics-werk ruimte.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/23/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 229fab5550d7b03fcbba80b5f4d9433d3b31e5b1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680437"
---
# <a name="support-regions-for-linked-log-analytics-workspace"></a>Ondersteunings regio's voor gekoppelde Log Analytics-werk ruimte

In Azure Automation kunt u de functies Updatebeheer, Wijzigingen bijhouden en inventarisatie en VM's buiten bedrijfsuren starten/stoppen instellen voor uw virtuele machines. Er worden echter alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account in uw abonnement. De regio toewijzingen zijn alleen van toepassing op het Automation-account en de Log Analytics-werk ruimte. De Log Analytics-werk ruimte en het Automation-account moeten zich in hetzelfde abonnement bevinden, maar kunnen zich in verschillende resource groepen bevinden die in dezelfde regio zijn geïmplementeerd. Zie [log Analytics-werk ruimte en het Automation-account](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)voor meer informatie.

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

Als u besluit dat u uw Automation-account niet meer wilt integreren met een Log Analytics-werk ruimte, kunt u uw account rechtstreeks van de Azure Portal ontkoppelen. Voordat u doorgaat, moet u eerst Updatebeheer, Wijzigingen bijhouden en inventarisatie [uitschakelen](move-account.md#disable-features) en VM's buiten bedrijfsuren starten/stoppen als u deze gebruikt. Als u de functies niet uitschakelt, kunt u de ontkoppelings bewerking niet volt ooien. 

Als de functies zijn uitgeschakeld, kunt u de onderstaande stappen volgen om het Automation-account te ontkoppelen.

> [!NOTE]
> Sommige functies, waaronder eerdere versies van de Azure SQL-bewakings oplossing, hebben mogelijk Automation-assets gemaakt die moeten worden verwijderd voordat de koppeling van de werk ruimte wordt opgeheven.

1. Open uw Automation-account vanuit het Azure Portal. Selecteer op de pagina Automation-account de optie **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Op de pagina werk ruimte ontkoppelen selecteert u **werk ruimte ontkoppelen**. U ontvangt een prompt waarin u wordt gevraagd of u wilt door gaan.

3. Terwijl Azure Automation het account ontkoppelt van uw Log Analytics-werk ruimte, kunt u de voortgang bijhouden onder **meldingen** in het menu.

4. Als u Updatebeheer hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn:

    * Update schema's: elk heeft een naam die overeenkomt met een update-implementatie die u hebt gemaakt.
    * Hybrid worker-groepen die zijn gemaakt voor de functie: elk heeft een naam die vergelijkbaar is met `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Als u VM's buiten bedrijfsuren starten/stoppen hebt gebruikt, kunt u desgewenst de volgende items verwijderen die niet meer nodig zijn:

    * VM-runbook-schema's starten en stoppen
    * VM-runbooks starten en stoppen
    * Variabelen

U kunt de werk ruimte ook ontkoppelen van uw Automation-account in de werk ruimte.

1. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**. 
2. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Updatebeheer in [updatebeheer Overview](../automation-update-management.md).
* Meer informatie over Wijzigingen bijhouden en inventaris in [Wijzigingen bijhouden en inventaris overzicht](../change-tracking.md).
* Meer informatie over VM's buiten bedrijfsuren starten/stoppen in [VM's buiten bedrijfsuren starten/stoppen Overview](../automation-solution-vm-management.md).
