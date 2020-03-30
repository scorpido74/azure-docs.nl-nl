---
title: Azure Automation-gegevens beheren
description: Dit artikel bevat meerdere onderwerpen voor het beheren van een Azure Automation-omgeving.  Bevat momenteel gegevensbewaring en back-ups van Azure Automation Disaster Recovery in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132885"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-gegevens beheren

Dit artikel bevat meerdere onderwerpen voor het beheren van een Azure Automation-omgeving.

## <a name="data-retention"></a>Bewaartijd van gegevens

Wanneer u een resource verwijdert in Azure Automation, wordt deze gedurende 30 dagen bewaard voor controledoeleinden voordat deze permanent wordt verwijderd. U de resource gedurende deze periode niet zien of gebruiken. Dit beleid is ook van toepassing op resources die behoren tot een automatiseringsaccount dat wordt verwijderd.

Azure Automation verwijdert en verwijdert taken die ouder zijn dan 30 dagen automatisch.

In de volgende tabel wordt het bewaarbeleid voor verschillende resources samengevat.

| Gegevens | Beleid |
|:--- |:--- |
| Accounts |Permanent verwijderd 30 dagen nadat het account is verwijderd door een gebruiker. |
| Assets |Permanent verwijderd 30 dagen nadat het item is verwijderd door een gebruiker, of 30 dagen nadat het account dat het actief bevat, door een gebruiker is verwijderd. |
| Modules |Permanent verwijderd 30 dagen nadat de module is verwijderd door een gebruiker, of 30 dagen nadat het account dat de module bevat, door een gebruiker is verwijderd. |
| Runbooks |Permanent verwijderd 30 dagen nadat de bron is verwijderd door een gebruiker, of 30 dagen nadat het account dat de bron bevat, door een gebruiker is verwijderd. |
| Taken |30 dagen na de laatste gewijzigde functie verwijderd en definitief verwijderd. Dit kan zijn nadat de taak is voltooid, is gestopt of is opgeschort. |
| Knooppuntconfiguraties/MOF-bestanden |De oude knooppuntconfiguratie wordt 30 dagen na het genereren van een nieuwe knooppuntconfiguratie permanent verwijderd. |
| DSC-knooppunten |Permanent verwijderd 30 dagen nadat het knooppunt is ongeregistreerd uit automatiseringsaccount met Azure-portal of de cmdlet [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) in Windows PowerShell. Knooppunten worden ook permanent verwijderd 30 dagen nadat het account dat het knooppunt bevat, door een gebruiker is verwijderd. |
| Knooppuntrapporten |Permanent verwijderd 90 dagen nadat een nieuw rapport is gegenereerd voor dat knooppunt |

Het bewaarbeleid is van toepassing op alle gebruikers en kan momenteel niet worden aangepast.

Als u gegevens echter voor een langere periode moet bewaren, u taaklogboeken uitvoeren naar Azure Monitor-logboeken. Voor meer informatie, bekijk [de taakgegevens azure automation naar Azure Monitor-logboeken.](automation-manage-send-joblogs-log-analytics.md)

## <a name="backing-up-azure-automation"></a>Back-ups maken van Azure Automation

Wanneer u een automatiseringsaccount in Microsoft Azure verwijdert, worden alle objecten in het account verwijderd, inclusief runbooks, modules, configuraties, instellingen, taken en assets. De objecten kunnen niet worden hersteld nadat het account is verwijderd.  U de volgende informatie gebruiken om een back-up te maken van de inhoud van uw automatiseringsaccount voordat u deze verwijderd.

### <a name="runbooks"></a>Runbooks

U uw runbooks exporteren naar scriptbestanden via de Azure-portal of de cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. Deze scriptbestanden kunnen worden geïmporteerd naar een ander automatiseringsaccount zoals besproken in [Het maken of importeren van een runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Integratiemodules

U geen integratiemodules exporteren vanuit Azure Automation. U moet ervoor zorgen dat ze beschikbaar zijn buiten het automatiseringsaccount.

### <a name="assets"></a>Assets

U geen [elementen](/previous-versions/azure/dn939988(v=azure.100)) exporteren vanuit Azure Automation.  Met de Azure-portal moet u de details van variabelen, referenties, certificaten, verbindingen en planningen noteren.  U moet vervolgens handmatig alle elementen maken die worden gebruikt door runbooks die u importeert in een andere automatisering.

U [Azure-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) gebruiken om details van niet-versleutelde elementen op te halen en deze op te slaan voor toekomstige referentie of gelijkwaardige elementen in een ander automatiseringsaccount te maken.

U de waarde voor versleutelde variabelen of het wachtwoordveld van referenties niet ophalen met behulp van cmdlets. Als u deze waarden niet kent, u deze ophalen uit een runbook met behulp van de activiteiten [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) en [Get-AutomationPSCredential.](/previous-versions/azure/dn940015(v=azure.100))

U certificaten niet exporteren vanuit Azure Automation. U moet ervoor zorgen dat certificaten buiten Azure beschikbaar zijn.

### <a name="dsc-configurations"></a>DSC-configuraties

U uw configuraties exporteren naar scriptbestanden via de Azure-portal of de cmdlet [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) in Windows PowerShell. Deze configuraties kunnen worden geïmporteerd en gebruikt in een ander automatiseringsaccount.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicatie in Azure Automation

Georeplicatie, standaard in Azure Automation-accounts, maakt een back-up van accountgegevens naar een andere geografische regio voor redundantie. U een primaire regio kiezen bij het instellen van uw account en vervolgens wordt er automatisch een secundaire regio aan toegewezen. De secundaire gegevens, gekopieerd uit het primaire gebied, worden voortdurend bijgewerkt in geval van gegevensverlies.  

In de volgende tabel worden de beschikbare primaire en secundaire regiokoppelingen weergegeven.

| Primair | Secundair |
| --- | --- |
| VS - zuid-centraal |VS - noord-centraal |
| US - oost 2 |VS - centraal |
| Europa -west |Europa - noord |
| Azië - zuidoost |Azië - oost |
| Japan - oost |Japan - west |

In het onwaarschijnlijke geval dat gegevens in een primair gebied verloren gaan, probeert Microsoft deze te herstellen. Als de primaire gegevens niet kunnen worden hersteld, wordt geo-failover uitgevoerd en worden de betrokken klanten hiervan op de hoogte gesteld via hun abonnement.
