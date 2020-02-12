---
title: Azure Automation-gegevens beheren
description: Dit artikel bevat meerdere onderwerpen over het beheren van een Azure Automation omgeving.  Op dit moment neemt het bewaren van gegevens op en maakt het back-ups van Azure Automation herstel na nood gevallen in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: daa5bab7c8d4cbe98ffe9a8a8a4b66da029fef5c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421889"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-gegevens beheren
Dit artikel bevat meerdere onderwerpen over het beheren van een Azure Automation omgeving.

## <a name="data-retention"></a>Gegevens bewaren
Wanneer u een resource in Azure Automation verwijdert, wordt deze 90 dagen bewaard voor controle doeleinden voordat deze permanent worden verwijderd.  U kunt de resource niet zien of gebruiken tijdens deze periode.  Dit beleid is ook van toepassing op bronnen die horen bij een Automation-account dat wordt verwijderd.

Met Azure Automation worden taken die ouder zijn dan 90 dagen automatisch verwijderd en permanent verwijderd.

De volgende tabel bevat een overzicht van het Bewaar beleid voor verschillende resources.

| Gegevens | Beleid |
|:--- |:--- |
| Accounts |90 dagen permanent verwijderd nadat het account is verwijderd door een gebruiker. |
| Assets |Definitief verwijderd 90 dagen nadat de Asset is verwijderd door een gebruiker of 90 dagen nadat het account dat de Asset bevat, is verwijderd door een gebruiker. |
| Modules |Nadat de module is verwijderd door een gebruiker, of 90 dagen nadat het account dat de module bevat, door een gebruiker is verwijderd, wordt 90 dagen permanent verwijderd. |
| Runbooks |Nadat de resource is verwijderd door een gebruiker, of 90 dagen nadat het account dat de resource heeft bevindt, 90 definitief is verwijderd door een gebruiker. |
| Taken |90 dagen na de laatste wijziging verwijderd en definitief verwijderd. Dit kan zich voordoen nadat de taak is voltooid, is gestopt of is onderbroken. |
| Knooppunt configuraties/MOF-bestanden |De oude configuratie van het knoop punt wordt 90 dagen na het genereren van een nieuwe knooppunt configuratie permanent verwijderd. |
| DSC-knoop punten |Nadat het knoop punt is verwijderd uit het Automation-account, 90 dagen permanent is gewist met Azure Portal of de cmdlet [unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) in Windows Power shell. Knoop punten worden ook definitief verwijderd 90 dagen nadat het account dat het knoop punt bevat, door een gebruiker is verwijderd. |
| Knooppunt rapporten |90 dagen na het genereren van een nieuw rapport voor dat knoop punt definitief verwijderd |

Het Bewaar beleid is van toepassing op alle gebruikers en kan momenteel niet worden aangepast.

Als u echter gegevens wilt bewaren voor een langere periode, kunt u runbook-taak logboeken door sturen naar Azure Monitor Logboeken.  Raadpleeg [Azure Automation-taak gegevens door sturen naar Azure monitor-logboeken](automation-manage-send-joblogs-log-analytics.md)voor meer informatie.   

## <a name="backing-up-azure-automation"></a>Back-ups maken met Azure Automation
Wanneer u een Automation-account in Microsoft Azure verwijdert, worden alle objecten in het account verwijderd, met inbegrip van runbooks, modules, configuraties, instellingen, taken en assets. De objecten kunnen niet worden hersteld nadat het account is verwijderd.  U kunt de volgende informatie gebruiken om een back-up te maken van de inhoud van uw Automation-account voordat u deze verwijdert. 

### <a name="runbooks"></a>Runbooks
U kunt uw runbooks exporteren naar script bestanden met behulp van de Azure Portal of de cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows Power shell.  Deze script bestanden kunnen worden geïmporteerd in een ander Automation-account, zoals wordt beschreven in [een Runbook maken of importeren](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Integratiemodules
U kunt geen integratie modules exporteren van Azure Automation.  U moet ervoor zorgen dat deze beschikbaar zijn buiten het Automation-account.

### <a name="assets"></a>Assets
U kunt geen [assets](/previous-versions/azure/dn939988(v=azure.100)) exporteren van Azure Automation.  Met de Azure Portal, moet u de details van de variabelen, referenties, certificaten, verbindingen en schema's noteren.  U moet vervolgens hand matig activa maken die worden gebruikt door runbooks die u importeert in een andere Automation.

U kunt [Azure-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) gebruiken om details van niet-versleutelde assets op te halen en deze op te slaan voor toekomstige Naslag informatie of gelijkwaardige assets te maken in een ander Automation-account.

U kunt de waarde voor versleutelde variabelen of het wachtwoord veld van referenties niet ophalen met behulp van cmdlets.  Als u deze waarden niet weet, kunt u ze ophalen uit een runbook met behulp van de [Get-AutomationVariable-](/previous-versions/azure/dn940012(v=azure.100)) en [Get-AutomationPSCredential-](/previous-versions/azure/dn940015(v=azure.100)) activiteiten.

U kunt geen certificaten exporteren van Azure Automation.  U moet ervoor zorgen dat certificaten buiten Azure beschikbaar zijn.

### <a name="dsc-configurations"></a>DSC-configuraties
U kunt uw configuraties exporteren naar script bestanden met behulp van de Azure Portal of de cmdlet [export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) in Windows Power shell. Deze configuraties kunnen worden geïmporteerd en gebruikt in een ander Automation-account.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicatie in Azure Automation
Geo-replicatie, standaard in Azure Automation accounts, back-ups maken van account gegevens naar een andere geografische regio voor redundantie. U kunt een primaire regio kiezen bij het instellen van uw account en vervolgens wordt er automatisch een secundaire regio aan toegewezen. De secundaire gegevens, gekopieerd uit de primaire regio, worden voortdurend bijgewerkt in het geval van gegevens verlies.  

De volgende tabel bevat de beschik bare primaire en secundaire regio's paars.

| Primair | Secundair |
| --- | --- |
| VS - zuid-centraal |VS - noord-centraal |
| US - oost 2 |VS - centraal |
| Europa - west |Europa - noord |
| Azië - zuidoost |Azië - oost |
| Japan - Oost |Japan - West |

In het onwaarschijnlijke geval dat de gegevens van de primaire regio verloren zijn gegaan, probeert micro soft deze te herstellen. Als de primaire gegevens niet kunnen worden hersteld, wordt er geo-failover uitgevoerd en worden de betrokken klanten hiervan op de hoogte gesteld via hun abonnement.


