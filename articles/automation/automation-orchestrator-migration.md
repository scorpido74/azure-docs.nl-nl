---
title: Migreren van Orchestrator naar Azure Automation
description: Hierin wordt beschreven hoe u runbooks en integratie pakketten migreert van System Center Orchestrator naar Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b34554798130d9741318e0f518c32a41f82a17e3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849663"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migreren van Orchestrator naar Azure Automation (bèta)
Runbooks in [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) zijn gebaseerd op activiteiten van integratie pakketten die specifiek voor Orchestrator zijn geschreven terwijl runbooks in azure Automation zijn gebaseerd op Windows Power shell.  [Grafische runbooks](automation-runbook-types.md#graphical-runbooks) in azure Automation hebben een vergelijk bare vormgeving van Orchestrator-runbooks met hun activiteiten die Power shell-cmdlets, onderliggende runbooks en assets vertegenwoordigen.

De [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) bevat hulpprogram ma's om u te helpen bij het converteren van Runbooks van orchestrator naar Azure Automation.  Naast de conversie van de runbooks zelf moet u de integratie pakketten converteren met de activiteiten die de runbooks gebruiken om modules te integreren met Windows Power shell-cmdlets.  

Hieronder vindt u het basis proces voor het converteren van Orchestrator-runbooks naar Azure Automation.  Elk van deze stappen wordt gedetailleerd beschreven in de volgende secties.

1. Down load de [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) die de hulpprogram ma's en modules bevat die in dit artikel worden besproken.
2. Importeer de [module Standard activities](#standard-activities-module) in azure Automation.  Dit omvat geconverteerde versies van Standard Orchestrator-activiteiten die kunnen worden gebruikt door geconverteerde runbooks.
3. Importeer [System Center Orchestrator-integratie modules](#system-center-orchestrator-integration-modules) in azure Automation voor de integratie pakketten die worden gebruikt door uw runbooks die toegang hebben tot System Center.
4. Converteer aangepaste integratie pakketten van derden met behulp van het [integratie pakket Converter](#integration-pack-converter) en importeer ze in azure Automation.
5. Converteer Orchestrator-runbooks met behulp van het [Runbook-conversie programma](#runbook-converter) en installeer in azure Automation.
6. U moet de vereiste Orchestrator-assets hand matig in Azure Automation maken, omdat deze resources niet worden geconverteerd met het Runbook-conversie programma.
7. Configureer een [Hybrid Runbook worker](#hybrid-runbook-worker) in uw lokale Data Center voor het uitvoeren van geconverteerde runbooks die toegang hebben tot lokale bronnen.

## <a name="service-management-automation"></a>Service Management Automation
Met [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) worden runbooks opgeslagen en uitgevoerd in uw lokale Data Center als Orchestrator en worden dezelfde integratie modules gebruikt als Azure Automation. Het [Runbook-conversie programma](#runbook-converter) converteert Orchestrator-runbooks naar grafische runbooks, maar deze worden niet ondersteund in SMA.  U kunt nog steeds de [module Standard-activiteiten](#standard-activities-module) en [System Center Orchestrator-integratie modules](#system-center-orchestrator-integration-modules) in SMA installeren, maar u moet [uw runbooks hand matig herschrijven](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Runbooks in Orchestrator worden opgeslagen op een database server en worden uitgevoerd op runbook-servers, zowel in uw lokale Data Center.  Runbooks in Azure Automation worden opgeslagen in de Azure-Cloud en kunnen worden uitgevoerd in uw lokale Data Center met behulp van een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md).  Zo kunt u runbooks die worden geconverteerd van orchestrator, meestal uitvoeren omdat ze zijn ontworpen om te worden uitgevoerd op lokale servers.

## <a name="integration-pack-converter"></a>Integratie pakket conversieprogramma
Integratie pakketten die zijn gemaakt met behulp van de [Orchestrator Integration Toolkit (OIT),](https://technet.microsoft.com/library/hh855853.aspx) worden door het conversie pakket Converter geconverteerd naar integratie modules op basis van Windows Power shell die in Azure Automation of Service Management Automation kunnen worden geïmporteerd.  

Wanneer u het integratie pakket conversieprogramma uitvoert, wordt een wizard weer gegeven waarmee u een integratie pakket bestand (. OIP) kunt selecteren.  De wizard geeft een lijst van de activiteiten die zijn opgenomen in dat integratie pakket en Hiermee kunt u selecteren welke moet worden gemigreerd.  Wanneer u de wizard voltooit, wordt er een integratie module gemaakt die een bijbehorende cmdlet bevat voor elk van de activiteiten in het oorspronkelijke integratie pakket.

### <a name="parameters"></a>Parameters
Alle eigenschappen van een activiteit in het integratie pakket worden geconverteerd naar para meters van de bijbehorende cmdlet in de integratie module.  Windows Power shell-cmdlets hebben een aantal [algemene para meters](https://technet.microsoft.com/library/hh847884.aspx) die kunnen worden gebruikt met alle cmdlets.  De para meter-verbose zorgt er bijvoorbeeld voor dat een cmdlet gedetailleerde informatie over de bijbehorende bewerking uitvoert.  Geen cmdlet kan een para meter hebben met dezelfde naam als een gemeen schappelijke para meter.  Als een activiteit een eigenschap heeft die dezelfde naam heeft als een gemeen schappelijke para meter, wordt u door de wizard gevraagd een andere naam op te geven voor de para meter.

### <a name="monitor-activities"></a>Activiteiten controleren
Runbooks in Orchestrator bewaken start met een [monitor activiteit](https://technet.microsoft.com/library/hh403827.aspx) en voert voortdurend wachten om te worden aangeroepen door een bepaalde gebeurtenis.  Azure Automation biedt geen ondersteuning voor het controleren van runbooks, waardoor monitor activiteiten in het integratie pakket niet worden geconverteerd.  In plaats daarvan wordt een tijdelijke aanduiding voor cmdlets gemaakt in de integratie module voor de monitor activiteit.  Deze cmdlet heeft geen functionaliteit, maar kan elk geconverteerde runbook dat gebruikt wordt, wel installeren.  Dit runbook kan niet worden uitgevoerd in Azure Automation, maar kan wel worden geïnstalleerd, zodat u het kunt wijzigen.

### <a name="integration-packs-that-cannot-be-converted"></a>Integratie pakketten die kunnen niet worden geconverteerd
Integratie pakketten die niet met OIT zijn gemaakt, kunnen niet worden geconverteerd met het integratie pakket conversieprogramma. Er zijn ook enkele integratie pakketten van micro soft die momenteel niet met dit hulp programma kunnen worden geconverteerd.  Er zijn geconverteerde versies van deze integratie pakketten [gedownload](#system-center-orchestrator-integration-modules) zodat ze kunnen worden geïnstalleerd in Azure Automation of Service Management Automation.

## <a name="standard-activities-module"></a>Module standaard activiteiten
Orchestrator bevat een set [standaard activiteiten](https://technet.microsoft.com/library/hh403832.aspx) die niet zijn opgenomen in een integratie pakket, maar die worden gebruikt door veel runbooks.  De module Standard activities is een integratie module die een equivalente cmdlet voor elk van deze activiteiten bevat.  U moet deze integratie module in Azure Automation installeren voordat u geconverteerde runbooks importeert die gebruikmaken van een standaard activiteit.

Naast de ondersteuning van geconverteerde runbooks, kunnen de cmdlets in de module Standard activities worden gebruikt door iemand die vertrouwd is met Orchestrator om nieuwe runbooks te bouwen in Azure Automation.  Hoewel de functionaliteit van alle standaard activiteiten kan worden uitgevoerd met-cmdlets, kunnen ze anders functioneren.  De cmdlets in de module geconverteerde standaard activiteiten werken hetzelfde als de bijbehorende activiteiten en gebruiken dezelfde para meters.  Dit kan de bestaande Orchestrator runbook author in hun overgang naar Azure Automation runbooks helpen.

## <a name="system-center-orchestrator-integration-modules"></a>Integratie modules van System Center Orchestrator
Micro soft biedt [integratie pakketten](https://technet.microsoft.com/library/hh295851.aspx) voor het bouwen van Runbooks om System Center-onderdelen en andere producten te automatiseren.  Sommige van deze integratie pakketten zijn momenteel gebaseerd op OIT, maar kunnen momenteel niet worden geconverteerd naar integratie modules vanwege bekende problemen.  [System Center Orchestrator Integration modules](https://www.microsoft.com/download/details.aspx?id=49555) bevat geconverteerde versies van deze integratie pakketten die kunnen worden geïmporteerd in Azure Automation en Service Management Automation.  

Door de RTM-versie van dit hulp programma worden bijgewerkte versies van de integratie pakketten gebaseerd op OIT die kunnen worden geconverteerd met het integratie pakket conversie programma, worden gepubliceerd.  Er worden ook richt lijnen gegeven om u te helpen bij het converteren van runbooks met activiteiten van de integratie pakketten die niet zijn gebaseerd op OIT.

## <a name="runbook-converter"></a>Runbook-Converter
Het Runbook-conversie programma converteert Orchestrator-runbooks naar [grafische runbooks](automation-runbook-types.md#graphical-runbooks) die in azure Automation kunnen worden geïmporteerd.  

Runbook Converter wordt geïmplementeerd als een Power shell-module met een cmdlet met de naam **ConvertFrom-SCORunbook** die de conversie uitvoert.  Wanneer u het hulp programma installeert, wordt er een snelkoppeling gemaakt naar een Power shell-sessie die de cmdlet laadt.   

Hieronder volgt het basis proces voor het converteren van een Orchestrator-runbook en het importeren in Azure Automation.  In de volgende secties vindt u meer informatie over het gebruik van het hulp programma en het werken met geconverteerde runbooks.

1. Een of meer runbooks exporteren vanuit Orchestrator.
2. Verkrijg integratie modules voor alle activiteiten in het runbook.
3. Converteer de Orchestrator-runbooks in het geëxporteerde bestand.
4. Lees de informatie in Logboeken om de conversie te valideren en om vereiste hand matige taken te bepalen.
5. Importeer geconverteerde runbooks in Azure Automation.
6. Maak vereiste assets in Azure Automation.
7. Bewerk het runbook in Azure Automation om de vereiste activiteiten te wijzigen.

### <a name="using-runbook-converter"></a>Runbook-converter gebruiken
De syntaxis voor **ConvertFrom-SCORunbook** is als volgt:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath: het pad naar het export bestand met de runbooks die moeten worden geconverteerd.
* Module: door Komma's gescheiden lijst met integratie modules met activiteiten in de runbooks.
* OutputFolder: het pad naar de map voor het maken van geconverteerde grafische runbooks.

Met de volgende voorbeeld opdracht worden de runbooks geconverteerd in een export bestand met de naam **MyRunbooks. ois_export**.  Deze runbooks gebruiken de Active Directory-en Data Protection Manager-integratie pakketten.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Logboekbestanden
Met het Runbook-conversie programma worden de volgende logboek bestanden gemaakt op dezelfde locatie als het geconverteerde Runbook.  Als de bestanden al bestaan, worden deze overschreven met informatie van de laatste conversie.

| Bestand | Inhoud |
|:--- |:--- |
| Runbook-Converter-Progress. log |Gedetailleerde stappen van de conversie, inclusief informatie voor elke activiteit die is geconverteerd en waarschuwing voor elke activiteit die niet wordt geconverteerd. |
| Runbook-Converter-samenvattings. log |Samen vatting van de laatste conversie, inclusief waarschuwingen en opvolgings taken die u moet uitvoeren, zoals het maken van een variabele die vereist is voor het geconverteerde runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Runbooks exporteren vanuit Orchestrator
Het Runbook-conversie programma werkt met een export bestand van Orchestrator dat een of meer runbooks bevat.  Er wordt een bijbehorende Azure Automation runbook gemaakt voor elk Orchestrator-runbook in het export bestand.  

Als u een runbook van Orchestrator wilt exporteren, klikt u met de rechter muisknop op de naam van het runbook in Runbook Designer en selecteert u **exporteren**.  Als u alle runbooks in een map wilt exporteren, klikt u met de rechter muisknop op de naam van de map en selecteert u **exporteren**.

### <a name="runbook-activities"></a>Runbook-activiteiten
Het Runbook-conversie programma converteert elke activiteit in het Orchestrator-Runbook naar een bijbehorende activiteit in Azure Automation.  Voor activiteiten die niet kunnen worden geconverteerd, wordt een tijdelijke aanduiding van een activiteit in het runbook gemaakt met waarschuwings tekst.  Nadat u het geconverteerde runbook in Azure Automation hebt geïmporteerd, moet u deze activiteiten vervangen door geldige activiteiten die de vereiste functionaliteit uitvoeren.

Alle Orchestrator-activiteiten in de [module standaard activiteiten](#standard-activities-module) worden geconverteerd.  Er zijn een aantal Standard-Orchestrator-activiteiten die zich niet in deze module bevinden en die niet worden geconverteerd.  Bijvoorbeeld, **platform gebeurtenis** heeft geen Azure Automation equivalent omdat de gebeurtenis specifiek is voor Orchestrator.

[Bewakings activiteiten](https://technet.microsoft.com/library/hh403827.aspx) worden niet geconverteerd omdat er geen equivalent is in azure Automation.  De uitzonde ring is bewakings activiteiten in [geconverteerde integratie pakketten](#integration-pack-converter) die worden geconverteerd naar de activiteit van de tijdelijke aanduiding.

Alle activiteiten uit een [geconverteerd integratie pakket](#integration-pack-converter) worden geconverteerd als u het pad naar de integratie module met de para meter **modules** opgeeft.  Voor System Center-integratie pakketten kunt u gebruikmaken van de [System Center Orchestrator-integratie modules](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Orchestrator-resources
Met het Runbook Converter worden alleen runbooks geconverteerd, niet andere Orchestrator-bronnen zoals tellers, variabelen of verbindingen.  Tellers worden niet ondersteund in Azure Automation.  Variabelen en verbindingen worden ondersteund, maar u moet ze hand matig maken.  Met de logboek bestanden wordt u gewaarschuwd als het runbook dergelijke bronnen vereist en de bijbehorende resources opgeven die u in Azure Automation moet maken om het geconverteerde runbook goed te laten functioneren.

Een runbook kan bijvoorbeeld een variabele gebruiken om een bepaalde waarde in een activiteit te vullen.  Met het geconverteerde runbook wordt de activiteit geconverteerd en wordt een variabele-Asset opgegeven in Azure Automation met dezelfde naam als de Orchestrator-variabele.  Dit wordt vermeld in het logboek bestand met **Runbook-samen vattingen** dat na de conversie is gemaakt.  U moet deze variabele Asset hand matig maken in Azure Automation voordat u het runbook gebruikt.

### <a name="input-parameters"></a>Invoerparameters
Runbooks in Orchestrator accepteren invoer parameters met de activiteit **gegevens initialiseren** .  Als het runbook dat wordt geconverteerd deze activiteit bevat, wordt een [invoer parameter](automation-graphical-authoring-intro.md#runbook-input-and-output) in het Azure Automation runbook gemaakt voor elke para meter in de activiteit.  Er wordt een activiteit voor het [besturings element werk stroom script](automation-graphical-authoring-intro.md#activities) gemaakt in het geconverteerde runbook waarmee elke para meter wordt opgehaald en geretourneerd.  Alle activiteiten in het runbook die een invoer parameter gebruiken, verwijzen naar de uitvoer van deze activiteit.

De reden hiervoor is dat deze strategie het beste spiegelt van de functionaliteit in het Orchestrator runbook.  Activiteiten in nieuwe grafische runbooks moeten rechtstreeks verwijzen naar invoer parameters met behulp van een invoer gegevens bron van een Runbook.

### <a name="invoke-runbook-activity"></a>Runbook-activiteit aanroepen
Runbooks in Orchestrator starten andere runbooks met de activiteit **Runbook aanroepen** . Als het runbook dat wordt geconverteerd, deze activiteit bevat en de optie **wachten op voltooiing** is ingesteld, wordt er een runbook-activiteit voor gemaakt in het geconverteerde runbook.  Als de optie **wachten op voltooiing** niet is ingesteld, wordt er een werk stroom script activiteit gemaakt die **Start-AzureAutomationRunbook** gebruikt om het runbook te starten.  Nadat u het geconverteerde runbook in Azure Automation hebt geïmporteerd, moet u deze activiteit wijzigen met de informatie die is opgegeven in de activiteit.

## <a name="related-articles"></a>Verwante artikelen:
* [System Center 2012-Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Standaard activiteiten van Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [System Center Orchestrator Migration Toolkit downloaden](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

