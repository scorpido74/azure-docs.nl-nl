---
title: Azure Automation-en Log Analytics werkruimte toewijzingen
description: In dit artikel worden de toewijzingen beschreven die zijn toegestaan tussen een Automation-account en een Log Analytics werk ruimte ter ondersteuning van de oplossing
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849510"
---
# <a name="workspace-mappings"></a>Werkruimte toewijzingen

Bij het inschakelen van oplossingen als Updatebeheer, Wijzigingen bijhouden en inventaris of de VM's buiten bedrijfsuren starten/stoppen oplossing, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Deze toewijzing is alleen van toepassing op het Automation-account en de Log Analytics-werk ruimte. De resources die worden gerapporteerd aan uw Automation-account of Log Analytics werk ruimte kunnen zich in andere regio's bevinden.

## <a name="supported-mappings"></a>Ondersteunde toewijzingen

De volgende tabel bevat de ondersteunde toewijzingen:

|**Log Analytics werkruimte regio**|**Azure Automation Region**|
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

<sup>1</sup> -oostus toewijzing voor log Analytics werk ruimten aan Automation-accounts is geen exacte regio voor het toewijzen van regio's, maar is de juiste toewijzing.

<sup>2</sup> vanwege capaciteits beperkingen is de regio niet beschikbaar bij het maken van nieuwe resources. Dit omvat Automation-accounts en Log Analytics-werk ruimten. Bestaande gekoppelde resources in de regio moeten echter blijven werken.

## <a name="unlink-workspace"></a>Werk ruimte ontkoppelen

Als u besluit dat u uw Automation-account niet meer wilt integreren met een Log Analytics-werk ruimte, kunt u uw account rechtstreeks van de Azure Portal ontkoppelen. Voordat u doorgaat, moet u eerst de Updatebeheer, Wijzigingen bijhouden en inventaris of de VM's buiten bedrijfsuren starten/stoppen oplossingen verwijderen als u deze gebruikt. Als u ze niet verwijdert, kan dit proces niet worden voortgezet. Raadpleeg het artikel voor de specifieke oplossing die u hebt geïmporteerd om inzicht te krijgen in de stappen die nodig zijn om deze te verwijderen.

Nadat u deze oplossingen hebt verwijderd, kunt u de volgende stappen uitvoeren om het Automation-account te ontkoppelen.

> [!NOTE]
> Sommige oplossingen, waaronder eerdere versies van de Azure SQL-bewakings oplossing, hebben mogelijk Automation-assets gemaakt en moeten mogelijk ook worden verwijderd voordat u de werk ruimte ontkoppelt.

1. Open uw Automation-account vanuit het Azure Portal en selecteer op de pagina Automation-account de optie **gekoppelde werk ruimte** onder de sectie **Verwante resources** aan de linkerkant.

2. Klik op de pagina werk ruimte ontkoppelen op **werk ruimte ontkoppelen**. U ontvangt een prompt waarin u wordt gevraagd of u wilt door gaan.

3. Terwijl Azure Automation probeert het account te ontkoppelen van uw Log Analytics-werk ruimte, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Als u de oplossing Updatebeheer hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* Update schema's: elk heeft een naam die overeenkomt met de update-implementaties die u hebt gemaakt.

* Hybrid worker-groepen gemaakt voor de oplossing: elke groep krijgt dezelfde naam als `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Als u de oplossing VM's buiten bedrijfsuren starten/stoppen hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn nadat u de oplossing hebt verwijderd.

* VM-runbook-schema's starten en stoppen
* VM-runbooks starten en stoppen
* Variabelen

U kunt ook uw werk ruimte ontkoppelen van uw Automation-account vanuit uw Log Analytics-werk ruimte. Selecteer in uw werk ruimte **Automation-account** onder **gerelateerde resources**. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het voorbereiden van de volgende oplossingen:

Updatebeheer en Wijzigingen bijhouden en inventaris:

* Van een [virtuele machine](../automation-onboard-solutions-from-vm.md)
* Vanuit uw [Automation-account](../automation-onboard-solutions-from-automation-account.md)
* Bij het [surfen op meerdere computers](../automation-onboard-solutions-from-browse.md)
* Vanuit een [runbook](../automation-onboard-solutions.md)

VM's starten/stoppen buiten kantooruren

* [VM's buiten bedrijfsuren starten/stoppen implementeren](../automation-solution-vm-management.md)
