---
title: Azure Automation-gegevens beheren
description: Dit artikel bevat meerdere onderwerpen over het beheren van een Azure Automation omgeving.  Op dit moment neemt het bewaren van gegevens op en maakt het back-ups van Azure Automation herstel na nood gevallen in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984654"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-gegevens beheren

Dit artikel bevat meerdere onderwerpen over het beheren van gegevens in een Azure Automation omgeving.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor de installatie-instructies voor AZ module op uw Hybrid Runbook Worker. Voor uw Automation-account kunt u uw modules bijwerken naar de nieuwste versie met behulp van [het bijwerken van Azure PowerShell-modules in azure Automation](automation-update-azure-modules.md).

## <a name="data-retention"></a>Bewaartijd van gegevens

Wanneer u een resource in Azure Automation verwijdert, wordt deze gedurende een aantal dagen voor controle doeleinden bewaard voordat deze definitief worden verwijderd. U kunt de resource niet zien of gebruiken tijdens deze periode. Dit beleid is ook van toepassing op bronnen die horen bij een verwijderd Automation-account.

De volgende tabel bevat een overzicht van het Bewaar beleid voor verschillende resources.

| Gegevens | Beleid |
|:--- |:--- |
| Accounts |Een account wordt definitief verwijderd 30 dagen nadat een gebruiker het heeft verwijderd. |
| Activa |Een activum wordt definitief verwijderd 30 dagen nadat een gebruiker het heeft verwijderd, of 30 dagen nadat een gebruiker een account heeft verwijderd dat de Asset bevat. |
| DSC-knoop punten |Een DSC-knoop punt wordt permanent verwijderd 30 dagen nadat de registratie van een Automation-account is ongedaan gemaakt met Azure Portal of de cmdlet [unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) in Windows Power shell. Een knoop punt wordt ook 30 dagen nadat een gebruiker het account dat het knoop punt bevat verwijderd, definitief verwijderd. |
| Taken |Een taak wordt verwijderd en wordt 30 dagen na de wijziging permanent verwijderd, bijvoorbeeld nadat de taak is voltooid, is gestopt of is onderbroken. |
| Modules |Een module wordt definitief verwijderd 30 dagen nadat een gebruiker deze heeft verwijderd, of 30 dagen nadat een gebruiker het account heeft verwijderd dat de module bevat. |
| Knooppunt configuraties/MOF-bestanden |Een oude knooppunt configuratie wordt definitief verwijderd 30 dagen nadat een nieuwe knooppunt configuratie is gegenereerd. |
| Knooppunt rapporten |Een knooppunt rapport wordt 90 dagen na het genereren van een nieuw rapport voor dat knoop punt definitief verwijderd. |
| Runbooks |Een runbook wordt definitief verwijderd 30 dagen nadat een gebruiker de resource heeft verwijderd, of 30 dagen nadat een gebruiker het account heeft verwijderd dat de resource bevat. |

Het Bewaar beleid is van toepassing op alle gebruikers en kan momenteel niet worden aangepast. Als u gegevens gedurende een langere periode wilt blijven gebruiken, kunt u de [gegevens van Azure Automation-taak naar Azure monitor-logboeken door sturen](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Gegevens back-up

Wanneer u een Automation-account in azure verwijdert, worden alle objecten in het account verwijderd. De objecten omvatten runbooks, modules, configuraties, instellingen, taken en assets. Ze kunnen niet worden hersteld nadat het account is verwijderd. U kunt de volgende informatie gebruiken om een back-up te maken van de inhoud van uw Automation-account voordat u deze verwijdert.

### <a name="runbooks"></a>Runbooks

U kunt uw runbooks exporteren naar script bestanden met behulp van de Azure Portal of de cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows Power shell. U kunt deze script bestanden importeren in een ander Automation-account, zoals beschreven in [Runbooks beheren in azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Integratiemodules

U kunt geen integratie modules exporteren van Azure Automation. U moet deze beschikbaar maken buiten het Automation-account.

### <a name="assets"></a>Activa

U kunt Azure Automation assets niet exporteren: certificaten, verbindingen, referenties, schema's en variabelen. In plaats daarvan kunt u de Azure Portal-en Azure-cmdlets gebruiken om de details van deze activa te noteren. Gebruik vervolgens deze gegevens om activa te maken die worden gebruikt door runbooks die u importeert in een ander Automation-account.

U kunt de waarden voor versleutelde variabelen of de wachtwoord velden van referenties niet ophalen met behulp van cmdlets. Als u deze waarden niet weet, kunt u ze ophalen in een runbook. Zie [variabele assets in azure Automation](shared-resources/variables.md)voor het ophalen van variabele waarden. Zie [referentie-assets in azure Automation](shared-resources/credentials.md)voor meer informatie over het ophalen van referentie waarden.

 ### <a name="dsc-configurations"></a>DSC-configuraties

U kunt uw DSC-configuraties exporteren naar script bestanden met behulp van de Azure Portal of de cmdlet [export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) in Windows Power shell. U kunt deze configuraties importeren en gebruiken in een ander Automation-account.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicatie in Azure Automation

Geo-replicatie is standaard in Azure Automation-accounts. U kiest een primaire regio bij het instellen van uw account. Met de interne Automation geo-Replication-service wordt automatisch een secundaire regio aan het account toegewezen. De service maakt vervolgens voortdurend een back-up van de account gegevens van de primaire regio naar de secundaire regio. U kunt de volledige lijst met primaire en secundaire regio's vinden op [bedrijfs continuïteit en herstel na nood gevallen (BCDR): gekoppelde Azure-regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

De back-up die is gemaakt door de geo-replicatie service voor automatisering is een volledige kopie van Automation-assets, configuraties en dergelijke. Deze back-up kan worden gebruikt als de primaire regio uitvalt en gegevens verliest. In het onwaarschijnlijke geval dat de gegevens voor een primaire regio verloren zijn gegaan, probeert micro soft deze te herstellen. Als het bedrijf de primaire gegevens niet kan herstellen, wordt automatische failover gebruikt en wordt u geïnformeerd over de situatie via uw Azure-abonnement. 

De geo-replicatie service voor automatisering is niet rechtstreeks toegankelijk voor externe klanten als er sprake is van een regionale storing. Als u de automatiserings configuratie en runbooks wilt behouden tijdens regionale storingen:

1. Selecteer een secundaire regio om te koppelen aan de geografische regio van uw primaire Automation-account.

2. Maak een Automation-account in de secundaire regio.

3. Exporteer uw runbooks in het primaire account als script bestanden.

4. Importeer de runbooks in uw Automation-account in de secundaire regio.

## <a name="next-steps"></a>Volgende stappen

* Zie [Secure assets in azure Automation versleutelen](automation-secure-asset-encryption.md)voor meer informatie over beveiligde assets in azure Automation.

* Zie [actieve geo-replicatie maken en gebruiken](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)om meer te weten te komen over geo-replicatie.