---
title: Problemen met onboarding Azure Automation-beheeroplossingen oplossen
description: Meer informatie over het oplossen van onboarding-fouten met de oplossingen Voor het bijwerken van wijzigingen en voorraad
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536961"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Fouten oplossen bij onboarding-updatebeheer, wijzigingstracking en voorraad

U fouten tegenkomen bij onboarding-oplossingen zoals Updatemanagement of Wijzigingstracking en -voorraad. In dit artikel worden de verschillende fouten beschreven die kunnen optreden en hoe u deze oplossen.

## <a name="known-issues"></a>Bekende problemen

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Scenario: Voor het hernoemen van een geregistreerd knooppunt moet u zich opnieuw registreren / opnieuw registreren

#### <a name="issue"></a>Probleem

Er wordt een knooppunt geregistreerd bij Azure Automation en vervolgens wordt de computernaam van het besturingssysteem gewijzigd.  Rapporten van het knooppunt blijven worden weergegeven met de oorspronkelijke naam.

#### <a name="cause"></a>Oorzaak

Als u de naam van geregistreerde knooppunten wijzigt, wordt de naam van het knooppunt in Azure Automation niet bijgewerkt.

#### <a name="resolution"></a>Oplossing

Registreer het knooppunt uit azure-automatiseringsstatusconfiguratie en registreer het opnieuw.  Rapporten die vóór die tijd aan de service zijn gepubliceerd, zijn niet meer beschikbaar.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: Certificaten opnieuw ondertekenen via https-proxy wordt niet ondersteund

#### <a name="issue"></a>Probleem

Klanten hebben gemeld dat wanneer ze verbinding maken via een proxy-oplossing die https-verkeer beëindigt en vervolgens het verkeer opnieuw versleutelt met behulp van een nieuw certificaat, de service de verbinding niet toestaat.

#### <a name="cause"></a>Oorzaak

Azure Automation biedt geen ondersteuning voor het opnieuw ondertekenen van certificaten die worden gebruikt om verkeer te versleutelen.

#### <a name="resolution"></a>Oplossing

Er is geen tijdelijke oplossing voor dit probleem.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: Onboarding mislukt met het bericht - De oplossing kan niet worden ingeschakeld

#### <a name="issue"></a>Probleem

U ontvangt een van de volgende berichten wanneer u probeert een virtuele machine aan boord te nemen voor een oplossing:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt door onjuiste of ontbrekende machtigingen op de virtuele machine, de werkruimte of voor de gebruiker.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat u over de juiste machtigingen beschikt om aan boord van de virtuele machine te gaan. Bekijk de [machtigingen die nodig zijn om machines aan boord te nemen](../automation-role-based-access-control.md#onboarding-permissions) en probeer de oplossing opnieuw aan boord te krijgen. Als u de `The solution cannot be enabled on this VM because the permission to read the workspace is missing`fout ontvangt, `Microsoft.OperationalInsights/workspaces/read` moet u ervoor zorgen dat u de toestemming hebt om te kunnen vinden of de vm aan boord is van een werkruimte.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: Onboarding mislukt met het bericht - Kan automatiseringsaccount niet configureren voor diagnostische logboekregistratie

#### <a name="issue"></a>Probleem

U ontvangt het volgende bericht wanneer u probeert een virtuele machine aan boord te nemen van een oplossing:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt als de prijscategorie niet overeenkomt met het factureringsmodel van het abonnement. Zie [Gebruik en geschatte kosten bewaken in Azure Monitor](https://aka.ms/PricingTierWarning)voor meer informatie.

#### <a name="resolution"></a>Oplossing

Maak uw Log Analytics-werkruimte handmatig en herhaal het onboardingproces om de werkruimte te selecteren die is gemaakt.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probleem

Deze foutcode betekent dat de opgeslagen zoekcomputergroepquery die wordt gebruikt om de oplossing te targeten, niet correct is opgemaakt. 

#### <a name="cause"></a>Oorzaak

Mogelijk hebt u de query gewijzigd of is deze mogelijk gewijzigd door het systeem.

#### <a name="resolution"></a>Oplossing

U de query voor deze oplossing verwijderen en de oplossing opnieuw aan boord nemen, waardoor de query opnieuw wordt gemaakt. De query is te vinden in uw werkruimte onder **Opgeslagen zoekopdrachten**. De naam van de query is **MicrosoftDefaultComputerGroup**en de categorie van de query is de naam van de oplossing die aan deze query is gekoppeld. Als meerdere oplossingen zijn ingeschakeld, wordt de **MicrosoftDefaultComputerGroup** meerdere keren weergegeven onder **Opgeslagen zoekopdrachten**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: Beleidsschending

#### <a name="issue"></a>Probleem

Deze foutcode betekent dat de implementatie is mislukt vanwege schending van een of meer beleidsregels.

#### <a name="cause"></a>Oorzaak 

Er is een beleid dat de bewerking blokkeert.

#### <a name="resolution"></a>Oplossing

Om de oplossing succesvol te implementeren, moet u overwegen het aangegeven beleid te wijzigen. Aangezien er veel verschillende soorten beleidsregels zijn die kunnen worden gedefinieerd, zijn de vereiste specifieke wijzigingen afhankelijk van het beleid dat wordt geschonden. Als er bijvoorbeeld een beleid is gedefinieerd in een resourcegroep die de toestemming heeft geweigerd om de inhoud van bepaalde typen resources binnen die resourcegroep te wijzigen, u bijvoorbeeld een van de volgende handelingen doen:

* Verwijder het beleid helemaal.
* Probeer aan boord te gaan van een andere resourcegroep.
* Herzie het beleid door bijvoorbeeld:
  * Het beleid opnieuw targeten op een specifieke resource (zoals op een specifiek automatiseringsaccount).
  * De set resources die beleid is geconfigureerd om te weigeren, herzien.

Controleer de meldingen in de rechterbovenhoek van de Azure-portal of navigeer naar de brongroep die uw automatiseringsaccount bevat en selecteer **Implementaties** onder **Instellingen** om de mislukte implementatie weer te geven. Voor meer informatie over Azure Policy: [Overzicht van Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: fouten bij het ontkoppelen van een werkruimte

#### <a name="issue"></a>Probleem

U ontvangt de volgende fout wanneer u een werkruimte probeert los te koppelen:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u nog steeds oplossingen hebt die actief zijn in uw Log Analytics-werkruimte die afhankelijk zijn van de gekoppelde werkruimte Automatiseringsaccount en Logboekanalyse.

### <a name="resolution"></a>Oplossing

Als u dit wilt oplossen, moet u de volgende oplossingen uit uw werkruimte verwijderen als u ze gebruikt:

* Updatebeheer
* Tracering wijzigen
* VM's starten/stoppen buiten kantooruren

Zodra u de oplossingen hebt verwijderd, u de koppeling van uw werkruimte ontkoppelen. Het is belangrijk om bestaande artefacten uit die oplossingen ook op te schonen vanuit uw werkruimte en automatiseringsaccount.  

* Updatebeheer
  * Update-implementaties (schema's) verwijderen uit uw automatiseringsaccount
* VM's starten/stoppen buiten kantooruren
  * Verwijder alle vergrendelingen op oplossingsonderdelen in uw automatiseringsaccount onder > **Instellingenvergrendelingen**. **Settings**
  * Zie [De VM Start/Stop tijdens off-hours](../automation-solution-vm-management.md#remove-the-solution)oplossing verwijderen voor extra stappen om de VM Start/Stop tijdens kantooruren te verwijderen.

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>MMA-extensiefouten

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Bij het implementeren van een oplossing worden verschillende gerelateerde resources geïmplementeerd. Een van die bronnen is de Microsoft Monitoring Agent Extension of Log Analytics agent voor Linux. Dit zijn Virtual Machine Extensions geïnstalleerd door de gastagent van de virtuele machine die verantwoordelijk is voor de communicatie met de geconfigureerde Log Analytics-werkruimte, met het oog op latere coördinatie van het downloaden van binaire bestanden en andere bestanden waarvan de oplossing waarop u bent instappen afhankelijk is zodra deze wordt uitgevoerd.
U wordt meestal eerst op de hoogte van MMA- of Log Analytics-agent voor Linux-installatiefouten van een melding die wordt weergegeven in de meldingenhub. Als u op die melding klikt, krijgt u meer informatie over de specifieke fout. Navigatie naar de resourcegroepenbron en vervolgens naar het element Implementaties daarin bevat ook details over de implementatiefouten die zijn opgetreden.
De installatie van de MMA- of Log Analytics-agent voor Linux kan om verschillende redenen mislukken en de stappen die moeten worden genomen om deze fouten aan te pakken, variëren afhankelijk van het probleem. Er volgen specifieke stappen voor het oplossen van problemen.

In de volgende sectie worden verschillende problemen beschreven die u tegenkomen bij het instappen die een fout veroorzaken bij de implementatie van de MMA-extensie.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: Er is een uitzondering opgetreden tijdens een WebClient-aanvraag

De MMA-extensie op de virtuele machine kan niet communiceren met externe bronnen en de implementatie mislukt.

#### <a name="issue"></a>Probleem

Hieronder volgen voorbeelden van foutberichten die worden geretourneerd:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Oorzaak

Enkele mogelijke oorzaken van deze fout zijn:

* Er is een proxy geconfigureerd in de VM, die alleen specifieke poorten toestaat.

* Een firewall-instelling heeft de toegang tot de vereiste poorten en adressen geblokkeerd.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat u over de juiste poorten en adressen beschikt die openstaan voor communicatie. Zie [uw netwerk plannen](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met poorten en adressen.

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: Installatie mislukt vanwege tijdelijke omgevingsproblemen

De installatie van de Microsoft Monitoring Agent-extensie is mislukt tijdens de implementatie vanwege een andere installatie of actie die de installatie blokkeert

#### <a name="issue"></a>Probleem

De volgende voorbeelden van foutmeldingen kunnen worden geretourneerd:

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

* Een andere installatie is in volle gang
* Het systeem wordt geactiveerd om opnieuw op te starten tijdens de implementatie van sjablonen

#### <a name="resolution"></a>Oplossing

Deze fout is een tijdelijke fout in de natuur. Probeer de implementatie opnieuw om de extensie te installeren.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: Time-out voor installatie

De installatie van de MMA-extensie is niet voltooid vanwege een time-out.

#### <a name="issue"></a>Probleem

Het volgende voorbeeld is een foutbericht dat kan worden geretourneerd:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op omdat de virtuele machine tijdens de installatie zwaar wordt belast.

### <a name="resolution"></a>Oplossing

Probeer de MMA-extensie te installeren wanneer de VM onder een lagere belasting staat.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts op [Azure-Forums](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met – het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
