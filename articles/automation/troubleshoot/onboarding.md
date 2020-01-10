---
title: Problemen met onboarding Azure Automation-beheer oplossingen oplossen
description: Meer informatie over het oplossen van fouten met onboarding met de oplossingen voor Updatebeheer, Wijzigingen bijhouden en inventaris
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 737b963074a2bec851882bddd78ad0b89f48d1d9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769894"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Problemen oplossen bij het voorbereiden van Updatebeheer, Wijzigingen bijhouden en inventaris

Er kunnen fouten optreden bij het voorbereiden van oplossingen zoals Updatebeheer of Wijzigingen bijhouden en de inventaris. In dit artikel worden de verschillende fouten beschreven die zich kunnen voordoen en hoe u deze kunt oplossen.

## <a name="known-issues"></a>Bekende problemen

### <a name="node-rename"></a>Scenario: voor het wijzigen van de naam van een geregistreerd knoop punt moet u de registratie of het REGI ster opnieuw uitvoeren

#### <a name="issue"></a>Probleem

Een knoop punt is geregistreerd bij Azure Automation en vervolgens wordt de naam van het besturings systeem gewijzigd.  Rapporten van het knoop punt worden nog steeds weer gegeven met de oorspronkelijke naam.

#### <a name="cause"></a>Oorzaak

Bij het wijzigen van de naam van geregistreerde knoop punten wordt de knooppunt naam in Azure Automation niet bijgewerkt.

#### <a name="resolution"></a>Resolutie

Hef de registratie van het knoop punt uit de configuratie van Azure Automation status op en registreer dit vervolgens opnieuw.  Rapporten die worden gepubliceerd naar de service vóór dat moment, zijn niet meer beschikbaar.


### <a name="resigning-cert"></a>Scenario: het opnieuw ondertekenen van certificaten via een HTTPS-proxy wordt niet ondersteund

#### <a name="issue"></a>Probleem

Klanten hebben gerapporteerd dat bij het maken van verbinding via een proxy-oplossing die HTTPS-verkeer beëindigt en vervolgens verkeer opnieuw versleutelt met een nieuw certificaat, de service de verbinding niet toestaat.

#### <a name="cause"></a>Oorzaak

Azure Automation biedt geen ondersteuning voor het opnieuw ondertekenen van certificaten die worden gebruikt voor het versleutelen van verkeer.

#### <a name="resolution"></a>Resolutie

Er is geen oplossing voor dit probleem.

## <a name="general-errors"></a>Algemene fouten

### <a name="missing-write-permissions"></a>Scenario: onboarding mislukt met het bericht-de oplossing kan niet worden ingeschakeld

#### <a name="issue"></a>Probleem

U ontvangt een van de volgende berichten wanneer u probeert een virtuele machine op een oplossing uit te voeren:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt door onjuiste of ontbrekende machtigingen voor de virtuele machine, de werk ruimte of de gebruiker.

#### <a name="resolution"></a>Resolutie

Zorg ervoor dat u over de juiste machtigingen beschikt om de virtuele machine onboarding te kunnen uitvoeren. Bekijk de [machtigingen die nodig zijn voor het onboarden van machines](../automation-role-based-access-control.md#onboarding) en probeer het opnieuw. Als het fout `The solution cannot be enabled on this VM because the permission to read the workspace is missing`wordt weer gegeven, controleert u of u de `Microsoft.OperationalInsights/workspaces/read` machtiging hebt om te controleren of de virtuele machine onboarded is voor een werk ruimte.

### <a name="diagnostic-logging"></a>Scenario: onboarding mislukt met het bericht-het configureren van het Automation-account voor diagnostische logboek registratie is mislukt

#### <a name="issue"></a>Probleem

Het volgende bericht wordt weer gegeven wanneer u probeert een virtuele machine op een oplossing uit te voeren:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Oorzaak

Deze fout kan optreden als de prijs categorie niet overeenkomt met het facturerings model van het abonnement. Zie [verbruik en geschatte kosten in azure monitor bewaken](https://aka.ms/PricingTierWarning)voor meer informatie.

#### <a name="resolution"></a>Resolutie

Maak uw Log Analytics-werk ruimte hand matig en herhaal het voorbereidings proces om de werk ruimte te selecteren die u hebt gemaakt.

### <a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probleem

Deze fout code geeft aan dat de query voor de opgeslagen Zoek computer groep die is gebruikt om de oplossing te richten, niet op de juiste wijze is ingedeeld. 

#### <a name="cause"></a>Oorzaak

Mogelijk hebt u de query gewijzigd of is deze door het systeem gewijzigd.

#### <a name="resolution"></a>Resolutie

U kunt de query voor deze oplossing verwijderen en de oplossing onboarden, waardoor de query opnieuw wordt gemaakt. De query kan worden gevonden in uw werk ruimte, onder **opgeslagen Zoek opdrachten**. De naam van de query is **MicrosoftDefaultComputerGroup**en de categorie van de query is de naam van de oplossing die is gekoppeld aan deze query. Als er meerdere oplossingen zijn ingeschakeld, wordt in de **MicrosoftDefaultComputerGroup** meerdere keren weer gegeven onder **opgeslagen Zoek opdrachten**.

### <a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Probleem

Deze fout code geeft aan dat de implementatie is mislukt vanwege een schending van een of meer beleids regels.

#### <a name="cause"></a>Oorzaak 

Er is een beleid aanwezig dat de bewerking niet kan volt ooien.

#### <a name="resolution"></a>Resolutie

Als u de oplossing wilt implementeren, moet u overwegen om het aangegeven beleid te wijzigen. Omdat er veel verschillende soorten beleids regels kunnen worden gedefinieerd, zijn de specifieke wijzigingen vereist, afhankelijk van het beleid dat is geschonden. Als er bijvoorbeeld een beleid is gedefinieerd voor een resource groep die geen toestemming heeft gekregen om de inhoud van bepaalde typen resources in die resource groep te wijzigen, kunt u bijvoorbeeld een van de volgende handelingen uitvoeren:

* Verwijder het beleid samen.
* Probeer uit te proberen voor een andere resource groep.
* Wijzig het beleid, bijvoorbeeld:
  * Het beleid opnieuw instellen op een specifieke resource (zoals een specifiek Automation-account).
  * Het wijzigen van de set resources waarvan het beleid is geconfigureerd om te weigeren.

Controleer de meldingen in de rechter bovenhoek van de Azure Portal of navigeer naar de resource groep die uw Automation-account bevat en selecteer **implementaties** onder **instellingen** om de mislukte implementatie te bekijken. Voor meer informatie over Azure Policy gaat u naar: [overzicht van Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Scenario: fouten bij het ontkoppelen van een werk ruimte

#### <a name="issue"></a>Probleem

U ontvangt de volgende fout wanneer u een werk ruimte probeert te ontkoppelen:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer er nog steeds oplossingen actief zijn in uw Log Analytics-werk ruimte, afhankelijk van uw Automation-account en Log Analytics werk ruimte die wordt gekoppeld.

### <a name="resolution"></a>Resolutie

Als u dit wilt oplossen, moet u de volgende oplossingen uit de werk ruimte verwijderen als u deze gebruikt:

* Updatebeheer
* Tracering wijzigen
* VM's starten/stoppen buiten kantooruren

Wanneer u de oplossingen hebt verwijderd, kunt u uw werk ruimte ontkoppelen. Het is belang rijk dat u eventuele bestaande artefacten uit deze oplossingen opschoont vanuit uw werk ruimte en het Automation-account.  

* Updatebeheer
  * Update-implementaties (Schema's) verwijderen uit uw Automation-account
* VM's starten/stoppen buiten kantooruren
  * Verwijder alle vergren delingen van oplossings onderdelen in uw Automation-account onder **instellingen** > **sloten**.
  * Voor aanvullende stappen voor het verwijderen van de VM's buiten bedrijfsuren starten/stoppen oplossing raadpleegt u [de virtuele machine starten/stoppen buiten kantoor uren](../automation-solution-vm-management.md##remove-the-solution).

## <a name="mma-extension-failures"></a>MMA-extensie fouten

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Bij het implementeren van een oplossing worden er diverse gerelateerde resources geïmplementeerd. Een van deze resources is de uitbrei ding voor micro soft monitoring agent of Log Analytics agent voor Linux. Dit zijn de extensies voor virtuele machines die zijn geïnstalleerd door de gast agent van de virtuele machine die verantwoordelijk is voor de communicatie met de geconfigureerde Log Analytics-werk ruimte, voor het doel van een latere coördinatie van het downloaden van binaire bestanden en andere de oplossing die u voorbereidt, is afhankelijk van de uitvoering.
Normaal gesp roken wordt u eerst op de hoogte van MMA-of Log Analytics agent voor Linux-installatie fouten van een melding die wordt weer gegeven in de notification hub. Als u op deze melding klikt, krijgt u meer informatie over de specifieke fout. Navigatie naar de resource groepen resource en vervolgens naar het element implementaties in het bestand bevat ook informatie over de implementatie fouten die zijn opgetreden.
De installatie van de MMA-of Log Analytics-agent voor Linux kan om verschillende redenen mislukken en de stappen die u moet ondernemen om deze fouten te verhelpen, zijn afhankelijk van het probleem. De specifieke stappen voor probleem oplossing volgen.

In de volgende sectie worden verschillende problemen beschreven die u kunt voordoen bij het voorbereiden van een storing in de implementatie van de MMA-uitbrei ding.

### <a name="webclient-exception"></a>Scenario: er is een uitzonde ring opgetreden tijdens een webclient-aanvraag

De MMA-extensie op de virtuele machine kan niet communiceren met externe resources en de implementatie mislukt.

#### <a name="issue"></a>Probleem

Hier volgen enkele voor beelden van fout berichten die worden geretourneerd:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Oorzaak

Enkele mogelijke oorzaken van deze fout zijn:

* Er is een proxy geconfigureerd in de VM, waarbij alleen specifieke poorten zijn toegestaan.

* Een firewall instelling heeft de toegang tot de vereiste poorten en adressen geblokkeerd.

#### <a name="resolution"></a>Resolutie

Zorg ervoor dat u de juiste poorten en adressen voor communicatie hebt geopend. Zie [uw netwerk plannen](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met poorten en adressen.

### <a name="transient-environment-issue"></a>Scenario: de installatie is mislukt vanwege problemen met de tijdelijke omgeving

De installatie van de micro soft Monitoring Agent-extensie is mislukt tijdens de implementatie vanwege een andere installatie of actie die de installatie blokkeert

#### <a name="issue"></a>Probleem

Hier volgen enkele voor beelden van fout berichten die kunnen worden weer gegeven:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Oorzaak

Enkele mogelijke oorzaken van deze fout zijn:

* Er wordt een andere installatie uitgevoerd
* Het systeem wordt geactiveerd om opnieuw op te starten tijdens het implementeren van de sjabloon

#### <a name="resolution"></a>Resolutie

Deze fout is een tijdelijke fout. Voer de implementatie opnieuw uit om de extensie te installeren.

### <a name="installation-timeout"></a>Scenario: time-out voor installatie

De installatie van de MMA-extensie is niet voltooid vanwege een time-out.

#### <a name="issue"></a>Probleem

In het volgende voor beeld wordt een fout bericht weer gegeven dat kan worden geretourneerd:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op omdat de virtuele machine tijdens de installatie intensief wordt belast.

### <a name="resolution"></a>Resolutie

Probeer de MMA-extensie te installeren wanneer de virtuele machine minder wordt geladen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport), het officiële Microsoft Azure-account voor het verbeteren van de gebruikerservaring door de Azure-community in contact te brengen met de juiste resources: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
