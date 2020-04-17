---
title: Migreren van Orchestrator naar Azure Automation
description: Beschrijft hoe u runbooks en integratiepakketten migreert van System Center Orchestrator naar Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: c7df6e31cd021fc61129131f9bd02acc7b96e2ad
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457549"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migreren van Orchestrator naar Azure Automation (Bèta)

Runbooks in [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) zijn gebaseerd op activiteiten uit integratiepakketten die specifiek voor Orchestrator zijn geschreven terwijl runbooks in Azure Automation zijn gebaseerd op Windows PowerShell.  [Grafische runbooks](automation-runbook-types.md#graphical-runbooks) in Azure Automation hebben een vergelijkbare weergave als Orchestrator runbooks met hun activiteiten die PowerShell-cmdlets, onderliggende runbooks en assets vertegenwoordigen.

De [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) bevat hulpprogramma's waarmee u runbooks converteren van Orchestrator naar Azure Automation.  Naast het converteren van de runbooks zelf, moet u de integratiepakketten converteren met de activiteiten die de runbooks gebruiken naar integratiemodules met Windows PowerShell-cmdlets.  

Hieronder volgt het basisproces voor het converteren van Orchestrator-runbooks naar Azure Automation.  Elk van deze stappen wordt in de onderstaande secties in detail beschreven.

1. Download de [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) met de tools en modules die in dit artikel worden besproken.
2. [Standaardactiviteitenmodule](#standard-activities-module) importeren in Azure Automation.  Dit omvat geconverteerde versies van standaard Orchestrator-activiteiten die kunnen worden gebruikt door geconverteerde runbooks.
3. System [Center Orchestrator-integratiemodules](#system-center-orchestrator-integration-modules) importeren in Azure Automation voor de integratiepakketten die worden gebruikt door uw runbooks die toegang hebben tot Systeemcentrum.
4. Converteer aangepaste integratiepakketten en integratiepakketten van derden met behulp van de [Integration Pack Converter](#integration-pack-converter) en importeer in Azure Automation.
5. Orchestrator-runbooks converteren met de [Runbook Converter](#runbook-converter) en installeren in Azure Automation.
6. Maak handmatig vereiste Orchestrator-elementen in Azure Automation, omdat de Runbook Converter deze resources niet converteert.
7. Configureer een [hybride runbookworker](#hybrid-runbook-worker) in uw lokale datacenter om geconverteerde runbooks uit te voeren die toegang hebben tot lokale bronnen.

>[!NOTE]
>Dit artikel is bijgewerkt voor het gebruik van de nieuwe Azure PowerShell Az-module. De AzureRM-module kan nog worden gebruikt en krijgt bugoplossingen tot ten minste december 2020. Zie voor meer informatie over de nieuwe Az-module en compatibiliteit met AzureRM [Introductie van de nieuwe Az-module van Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)voor installatie-instructies voor az-modules op uw hybride runbookworker. Voor uw Automatiseringsaccount u uw modules bijwerken naar de nieuwste versie met [Azure PowerShell-modules bijwerken in Azure Automation.](automation-update-azure-modules.md)

## <a name="service-management-automation"></a>Automatisering van servicebeheer

[Sma (Service Management Automation)](https://technet.microsoft.com/library/dn469260.aspx) slaat runbooks op en voert deze uit in uw lokale datacenter, zoals Orchestrator, en maakt gebruik van dezelfde integratiemodules als Azure Automation. De [Runbook Converter](#runbook-converter) zet Orchestrator runbooks om in grafische runbooks die echter niet worden ondersteund in SMA.  U nog steeds de [standaardactiviteitenmodule](#standard-activities-module) en [system center-integratiemodules](#system-center-orchestrator-integration-modules) in SMA installeren, maar u moet uw runbooks handmatig [herschrijven.](https://technet.microsoft.com/library/dn469262.aspx)

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Runbooks in Orchestrator worden opgeslagen op een databaseserver en worden uitgevoerd op runbook-servers, beide in uw lokale datacenter.  Runbooks in Azure Automation worden opgeslagen in de Azure-cloud en kunnen worden uitgevoerd in uw lokale datacenter met behulp van een [hybride runbook worker.](automation-hybrid-runbook-worker.md)  Dit is hoe u meestal runbooks geconverteerd van Orchestrator, omdat ze zijn ontworpen om te draaien op lokale servers.

## <a name="integration-pack-converter"></a>Integratiepakket converter

Het Integratiepakket Converter zet integratiepakketten die zijn gemaakt met behulp van de [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) om in integratiemodules op basis van Windows PowerShell die kunnen worden geïmporteerd in Azure Automation of Service Management Automation.  

Wanneer u de Integration Pack Converter uitvoert, krijgt u een wizard te zien waarmee u een integratiepakketbestand (.oip) selecteren.  De wizard geeft vervolgens een lijst van de activiteiten die in dat integratiepakket zijn opgenomen en stelt u in staat te selecteren welke worden gemigreerd.  Wanneer u de wizard voltooit, wordt een integratiemodule met een bijbehorende cmdlet voor elk van de activiteiten in het oorspronkelijke integratiepakket.

### <a name="parameters"></a>Parameters

Alle eigenschappen van een activiteit in het integratiepakket worden omgezet in parameters van de overeenkomstige cmdlet in de integratiemodule.  Windows PowerShell-cmdlets hebben een reeks [algemene parameters](https://technet.microsoft.com/library/hh847884.aspx) die met alle cmdlets kunnen worden gebruikt. De parameter -Verbose zorgt er bijvoorbeeld voor dat een cmdlet gedetailleerde informatie over de werking ervan uitvoert.  Geen enkele cmdlet mag een parameter hebben met dezelfde naam als een algemene parameter. Als een activiteit wel een eigenschap heeft met dezelfde naam als een algemene parameter, vraagt de wizard u om een andere naam voor de parameter op te geven.

### <a name="monitor-activities"></a>Activiteiten bewaken

Monitor runbooks in Orchestrator begin met een [monitoractiviteit](https://technet.microsoft.com/library/hh403827.aspx) en voer continu uit om te worden aangeroepen door een bepaalde gebeurtenis.  Azure Automation biedt geen ondersteuning voor runbooks voor beeldschermen, zodat alle monitoractiviteiten in het integratiepakket niet worden geconverteerd.  In plaats daarvan wordt een tijdelijke aanduidingscmdlet gemaakt in de integratiemodule voor de monitoractiviteit.  Deze cmdlet heeft geen functionaliteit, maar het maakt het mogelijk om een geconverteerde runbook te installeren.  Deze runbook kan niet worden uitgevoerd in Azure Automation, maar kan wel worden geïnstalleerd zodat u het wijzigen.

### <a name="integration-packs-that-cannot-be-converted"></a>Integratiepakketten die niet kunnen worden geconverteerd

Integratiepakketten die niet met OIT zijn gemaakt, kunnen niet worden geconverteerd met de Integration Pack Converter. Er zijn ook enkele integratiepakketten van Microsoft die momenteel niet kunnen worden geconverteerd met deze tool.  Geconverteerde versies van deze integratiepakketten zijn [beschikbaar om te downloaden,](#system-center-orchestrator-integration-modules) zodat ze kunnen worden geïnstalleerd in Azure Automation of Service Management Automation.

## <a name="standard-activities-module"></a>Module Standaardactiviteiten

Orchestrator bevat een reeks [standaardactiviteiten](https://technet.microsoft.com/library/hh403832.aspx) die niet zijn opgenomen in een integratiepakket, maar door veel runbooks worden gebruikt.  De module Standaardactiviteiten is een integratiemodule met een cmdlet-equivalent voor elk van deze activiteiten.  U moet deze integratiemodule installeren in Azure Automation voordat u geconverteerde runbooks importeert die een standaardactiviteit gebruiken.

Naast het ondersteunen van geconverteerde runbooks, kunnen de cmdlets in de standaardactiviteitenmodule worden gebruikt door iemand die bekend is met Orchestrator om nieuwe runbooks te bouwen in Azure Automation.  Hoewel de functionaliteit van alle standaardactiviteiten met cmdlets kan worden uitgevoerd, kunnen ze anders werken.  De cmdlets in de geconverteerde standaardactiviteitenmodule werken hetzelfde als hun bijbehorende activiteiten en gebruiken dezelfde parameters.  Dit kan de bestaande Orchestrator runbook auteur helpen bij de overgang naar Azure Automation runbooks.

## <a name="system-center-orchestrator-integration-modules"></a>Integratiemodules voor system center orchestrator

Microsoft biedt [integratiepakketten](https://technet.microsoft.com/library/hh295851.aspx) voor het bouwen van runbooks om System Center-componenten en andere producten te automatiseren.  Sommige van deze integratiepakketten zijn momenteel gebaseerd op OIT, maar kunnen momenteel niet worden omgezet in integratiemodules vanwege bekende problemen.  [System Center Orchestrator Integration Modules](https://www.microsoft.com/download/details.aspx?id=49555) bevat geconverteerde versies van deze integratiepakketten die kunnen worden geïmporteerd in Azure Automation and Service Management Automation.  

Door de RTM-versie van deze tool worden bijgewerkte versies van de integratiepakketten op basis van OIT gepubliceerd die kunnen worden geconverteerd met de Integration Pack Converter.  Er worden ook richtlijnen verstrekt om u te helpen bij het converteren van runbooks met activiteiten uit de integratiepakketten die niet op OIT zijn gebaseerd.

## <a name="runbook-converter"></a>Runbook-converter

De Runbook Converter zet Orchestrator runbooks om in [grafische runbooks](automation-runbook-types.md#graphical-runbooks) die kunnen worden geïmporteerd in Azure Automation.  

Runbook Converter wordt geïmplementeerd als een PowerShell-module `ConvertFrom-SCORunbook` met een cmdlet die de conversie uitvoert.  Wanneer u het gereedschap installeert, wordt een snelkoppeling naar een PowerShell-sessie gemaakt die de cmdlet laadt.   

Hieronder volgt het basisproces om een Orchestrator-runbook om te zetten en te importeren in Azure Automation.  In de volgende secties vindt u meer informatie over het gebruik van het gereedschap en het werken met geconverteerde runbooks.

1. Exporteer een of meer runbooks van Orchestrator.
2. Behaal integratiemodules voor alle activiteiten in het runbook.
3. Converteer de orchestrator-runbooks in het geëxporteerde bestand.
4. Controleer informatie in logboeken om de conversie te valideren en de vereiste handmatige taken te bepalen.
5. Geconverteerde runbooks importeren in Azure Automation.
6. Maak de vereiste elementen in Azure Automation.
7. Bewerk de runbook in Azure Automation om de vereiste activiteiten te wijzigen.

### <a name="using-runbook-converter"></a>Runbook-converter gebruiken

De syntaxis **voor ConvertFrom-SCORunbook** is als volgt:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - Pad naar het exportbestand met de runbooks die moeten worden geconverteerd.
* Module - Komma afgebakende lijst van integratiemodules met activiteiten in de runbooks.
* Uitvoermap - Pad naar de map om geconverteerde grafische runbooks te maken.

Met de volgende opdracht worden de runbooks geconverteerd in een exportbestand met de naam **MyRunbooks.ois_export**.  Deze runbooks maken gebruik van de integratiepakketten Active Directory en Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Logboekbestanden

De Runbook Converter maakt de volgende logboekbestanden op dezelfde locatie als de geconverteerde runbook.  Als de bestanden al bestaan, worden ze overschreven met informatie van de laatste conversie.

| File | Inhoud |
|:--- |:--- |
| Runbook Converter - Progress.log |Gedetailleerde stappen van de conversie, inclusief informatie voor elke activiteit die is geconverteerd en waarschuwing voor elke activiteit die niet is geconverteerd. |
| Runbook Converter - Samenvatting.log |Overzicht van de laatste conversie, inclusief eventuele waarschuwingen en vervolgtaken die u moet uitvoeren, zoals het maken van een variabele die vereist is voor de geconverteerde runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Runbooks exporteren van Orchestrator

De Runbook Converter werkt met een exportbestand van Orchestrator dat een of meer runbooks bevat.  Het maakt een bijbehorende Azure Automation runbook voor elke Orchestrator runbook in het exportbestand.  

Als u een runbook vanuit Orchestrator wilt exporteren, klikt u met de rechtermuisknop op de naam van het runbook in Runbook Designer en selecteert u **Exporteren**.  Als u alle runbooks in een map wilt exporteren, klikt u met de rechtermuisknop op de naam van de map en selecteert u **Exporteren**.

### <a name="runbook-activities"></a>Runbookactiviteiten

De Runbook Converter converteert elke activiteit in het Orchestrator-runbook naar een overeenkomstige activiteit in Azure Automation.  Voor activiteiten die niet kunnen worden geconverteerd, wordt een tijdelijke aanduidingsactiviteit gemaakt in het runbook met waarschuwingstekst.  Nadat u het geconverteerde runbook hebt geïmporteerd in Azure Automation, moet u een van deze activiteiten vervangen door geldige activiteiten die de vereiste functionaliteit uitvoeren.

Alle Orchestrator-activiteiten in de [standaardactiviteitenmodule](#standard-activities-module) worden geconverteerd.  Er zijn een aantal standaard Orchestrator-activiteiten die echter niet in deze module staan en niet worden geconverteerd.  Heeft bijvoorbeeld `Send Platform Event` geen Azure Automation-equivalent omdat de gebeurtenis specifiek is voor Orchestrator.

[Monitoractiviteiten](https://technet.microsoft.com/library/hh403827.aspx) worden niet geconverteerd omdat er geen equivalent aan is in Azure Automation.  De uitzondering is monitoractiviteiten in [geconverteerde integratiepakketten](#integration-pack-converter) die worden geconverteerd naar de tijdelijke activiteit.

Elke activiteit uit een [geconverteerd integratiepakket](#integration-pack-converter) wordt geconverteerd `modules` als u het pad naar de integratiemodule met de parameter opgeeft. Voor System Center Integration Packs u de [System Center Orchestrator Integration Modules](#system-center-orchestrator-integration-modules)gebruiken.

### <a name="orchestrator-resources"></a>Orchestrator-bronnen

De Runbook Converter converteert alleen runbooks, niet andere Orchestrator-bronnen zoals tellers, variabelen of verbindingen.  Tellers worden niet ondersteund in Azure Automation.  Variabelen en verbindingen worden ondersteund, maar u moet ze handmatig maken.  De logboekbestanden informeren u of de runbook dergelijke bronnen vereist en specificeren de bijbehorende resources die u in Azure Automation moet maken om de geconverteerde runbook goed te laten werken.

Een runbook kan bijvoorbeeld een variabele gebruiken om een bepaalde waarde in een activiteit in te vullen.  De geconverteerde runbook converteert de activiteit en geeft een variabele asset op in Azure Automation met dezelfde naam als de orchestrator-variabele.  Dit wordt opgemerkt in het bestand **Runbook Converter - Summary.log** dat na de conversie is gemaakt.  U moet deze variabele asset handmatig maken in Azure Automation voordat u de runbook gebruikt.

### <a name="input-parameters"></a>Invoerparameters

Runbooks in Orchestrator accepteren `Initialize Data` invoerparameters met de activiteit.  Als de runbook die wordt geconverteerd deze activiteit bevat, wordt voor elke parameter in de activiteit een [invoerparameter](automation-graphical-authoring-intro.md#runbook-input-and-output) in de runbook Azure Automation gemaakt.  Er wordt een [besturingselementactiviteit voor werkstroomscript](automation-graphical-authoring-intro.md#activities) gemaakt in de geconverteerde runbook die elke parameter ophaalt en retourneert.  Alle activiteiten in het runbook die een invoerparameter gebruiken, verwijzen naar de uitvoer van deze activiteit.

De reden dat deze strategie wordt gebruikt is om de functionaliteit in het Orchestrator-runbook het beste te spiegelen.  Activiteiten in nieuwe grafische runbooks moeten rechtstreeks verwijzen naar invoerparameters met behulp van een runbook-invoergegevensbron.

### <a name="invoke-runbook-activity"></a>Runbook-activiteit aanroepen

Runbooks in Orchestrator start andere `Invoke Runbook` runbooks met de activiteit. Als de runbook die wordt `Wait for completion` geconverteerd deze activiteit bevat en de optie is ingesteld, wordt er een runbook-activiteit voor gemaakt in het geconverteerde runbook.  Als `Wait for completion` de optie niet is ingesteld, wordt er een werkstroomscriptactiviteit gemaakt waarmee [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) wordt gebruikt om de runbook te starten. Nadat u het geconverteerde runbook hebt geïmporteerd in Azure Automation, moet u deze activiteit wijzigen met de informatie die in de activiteit is opgegeven.

## <a name="related-articles"></a>Verwante artikelen:

* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Orchestrator Standaard activiteiten](https://technet.microsoft.com/library/hh403832.aspx)
* [Systeemcentrum-migratietoolkit downloaden](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

