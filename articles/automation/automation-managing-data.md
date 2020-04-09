---
title: Azure Automation-gegevens beheren
description: Dit artikel bevat meerdere onderwerpen voor het beheren van een Azure Automation-omgeving.  Bevat momenteel gegevensbewaring en back-ups van Azure Automation Disaster Recovery in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984654"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-gegevens beheren

Dit artikel bevat meerdere onderwerpen voor het beheren van gegevens in een Azure Automation-omgeving.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="data-retention"></a>Bewaartijd van gegevens

Wanneer u een resource verwijdert in Azure Automation, wordt deze gedurende een aantal dagen bewaard voor controledoeleinden voordat deze permanent wordt verwijderd. U de resource gedurende deze periode niet zien of gebruiken. Dit beleid is ook van toepassing op resources die behoren tot een verwijderd automatiseringsaccount.

In de volgende tabel wordt het bewaarbeleid voor verschillende resources samengevat.

| Gegevens | Beleid |
|:--- |:--- |
| Accounts |Een account wordt 30 dagen na het verwijderen van een account definitief verwijderd. |
| Assets |Een actief wordt permanent verwijderd 30 dagen nadat een gebruiker het verwijdert, of 30 dagen nadat een gebruiker een account heeft verwijderd dat het actief bevat. |
| DSC-knooppunten |Een DSC-knooppunt wordt 30 dagen na niet-registratie van een Automatiseringsaccount met Azure-portal of de cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) in Windows PowerShell definitief verwijderd. Een knooppunt wordt ook permanent verwijderd 30 dagen nadat een gebruiker het account dat het knooppunt bevat, verwijdert. |
| Taken |Een taak wordt 30 dagen na wijziging verwijderd en definitief verwijderd, bijvoorbeeld nadat de taak is voltooid, is gestopt of is opgeschort. |
| Modules |Een module wordt permanent verwijderd 30 dagen nadat een gebruiker deze verwijdert, of 30 dagen nadat een gebruiker het account dat de module bevat, heeft verwijderd. |
| Knooppuntconfiguraties/MOF-bestanden |Een oude knooppuntconfiguratie wordt 30 dagen na het genereren van een nieuwe knooppuntconfiguratie permanent verwijderd. |
| Knooppuntrapporten |Een knooppuntrapport wordt 90 dagen na het genereren van een nieuw rapport voor dat knooppunt permanent verwijderd. |
| Runbooks |Een runbook wordt permanent verwijderd 30 dagen nadat een gebruiker de bron heeft verwijderd, of 30 dagen nadat een gebruiker het account met de bron heeft verwijderd. |

Het bewaarbeleid is van toepassing op alle gebruikers en kan momenteel niet worden aangepast. Als u gegevens echter voor een langere periode moet bewaren, u [azure automation-taakgegevens doorsturen naar Azure Monitor-logboeken.](automation-manage-send-joblogs-log-analytics.md)

## <a name="data-backup"></a>Back-up van gegevens

Wanneer u een Automatiseringsaccount in Azure verwijdert, worden alle objecten in het account verwijderd. De objecten omvatten runbooks, modules, configuraties, instellingen, taken en assets. Ze kunnen niet worden hersteld nadat het account is verwijderd. U de volgende informatie gebruiken om een back-up van de inhoud van uw Automatiseringsaccount te maken voordat u deze verwijderd.

### <a name="runbooks"></a>Runbooks

U uw runbooks exporteren naar scriptbestanden via de Azure-portal of de cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. U deze scriptbestanden importeren in een ander Automatiseringsaccount, zoals besproken in [Runbooks beheren in Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integratiemodules

U geen integratiemodules exporteren vanuit Azure Automation. U moet ze beschikbaar stellen buiten het Automatiseringsaccount.

### <a name="assets"></a>Assets

U Azure Automation-assets niet exporteren: certificaten, verbindingen, referenties, schema's en variabelen. In plaats daarvan u de Azure-portal en Azure-cmdlets gebruiken om de details van deze elementen te noteren. Gebruik deze gegevens vervolgens om elementen te maken die worden gebruikt door runbooks die u importeert naar een ander Automatiseringsaccount.

U de waarden voor versleutelde variabelen of de wachtwoordvelden van referenties niet ophalen met behulp van cmdlets. Als u deze waarden niet kent, u deze ophalen in een runbook. Zie [Variabele elementen in Azure Automation](shared-resources/variables.md)voor het ophalen van variabele waarden. Zie [Referentie-elementen in Azure Automation](shared-resources/credentials.md)voor meer informatie over het ophalen van referentiewaarden.

 ### <a name="dsc-configurations"></a>DSC-configuraties

U uw DSC-configuraties exporteren naar scriptbestanden met behulp van de Azure-portal of de cmdlet [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) in Windows PowerShell. U deze configuraties importeren en gebruiken in een ander Automatiseringsaccount.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicatie in Azure Automation

Geo-replicatie is standaard in Azure Automation-accounts. U kiest een primaire regio bij het instellen van uw account. De georeplicatieservice voor automatisering wijst automatisch een secundair e-gebied toe aan het account. De service maakt vervolgens continu een back-up van accountgegevens van de primaire regio naar de secundaire regio. De volledige lijst met primaire en secundaire regio's is te vinden op [Business continuity and disaster recovery (BCDR): Azure Paired Regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

De back-up die is gemaakt door de georeplicatieservice Automation is een volledige kopie van automatiseringselementen, configuraties en dergelijke. Deze back-up kan worden gebruikt als de primaire regio uitvalt en gegevens verliest. In het onwaarschijnlijke geval dat gegevens voor een primaire regio verloren gaan, probeert Microsoft deze te herstellen. Als het bedrijf de primaire gegevens niet kan herstellen, maakt het gebruik van automatische failover en informeert het u over de situatie via uw Azure-abonnement. 

De georeplicatieservice voor automatisering is niet rechtstreeks toegankelijk voor externe klanten als er een regionale storing optreedt. Als u de configuratie van automatisering en het uitvoeren van boeken wilt behouden tijdens regionale fouten:

1. Selecteer een secundaire regio die u wilt koppelen aan het geografische gebied van uw primaire automatiseringsaccount.

2. Maak een Automatiseringsaccount in de secundaire regio.

3. Exporteer in het primaire account uw runbooks als scriptbestanden.

4. Importeer de runbooks naar uw Automatiseringsaccount in het secundaire gebied.

## <a name="next-steps"></a>Volgende stappen

* Zie Beveiligde assets versleutelen in Azure Automation voor meer informatie over beveiligde assets in Azure [Automation.](automation-secure-asset-encryption.md)

* Zie [Actieve georeplicatie maken en gebruiken](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)voor meer informatie over georeplicatie.