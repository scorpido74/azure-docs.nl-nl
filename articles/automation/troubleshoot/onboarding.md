---
title: Problemen oplossen met de onboarding van Azure Automation-beheeroplossingen
description: Meer informatie over het oplossen van onboardingfouten voor oplossingen.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679236"
---
# <a name="troubleshoot-solution-onboarding"></a>Problemen met de onboarding van oplossingen oplossen

Mogelijk ontvangt u fouten bij het inwerken van de updatebeheeroplossing of de oplossing Voor het bijhouden en inventaris wijzigen. In dit artikel worden de verschillende fouten beschreven die kunnen optreden en hoe u deze oplossen.

## <a name="known-issues"></a>Bekende problemen

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: Voor het hernoemen van een geregistreerd knooppunt moet u zich opnieuw registreren of opnieuw registreren

#### <a name="issue"></a>Probleem

Er wordt een knooppunt geregistreerd bij Azure Automation en vervolgens wordt de naam van de computer van het besturingssysteem gewijzigd. Rapporten van het knooppunt blijven worden weergegeven met de oorspronkelijke naam.

#### <a name="cause"></a>Oorzaak

Als u de naam van geregistreerde knooppunten wijzigt, wordt de naam van het knooppunt in Azure Automation niet bijgewerkt.

#### <a name="resolution"></a>Oplossing

Registreer het knooppunt uit azure-automatiseringsstatusconfiguratie en registreer het opnieuw. Rapporten die vóór die tijd aan de service zijn gepubliceerd, zijn niet meer beschikbaar.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: Certificaten opnieuw ondertekenen via https-proxy wordt niet ondersteund

#### <a name="issue"></a>Probleem

Wanneer u verbinding maakt via een proxy-oplossing die HTTPS-verkeer beëindigt en vervolgens het verkeer opnieuw versleutelt met een nieuw certificaat, staat de service de verbinding niet toe.

#### <a name="cause"></a>Oorzaak

Azure Automation biedt geen ondersteuning voor het opnieuw ondertekenen van certificaten die worden gebruikt om verkeer te versleutelen.

#### <a name="resolution"></a>Oplossing

Er is momenteel geen tijdelijke oplossing voor dit probleem.

## <a name="general-errors"></a>Algemene fouten

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: Onboarding mislukt met het bericht - De oplossing kan niet worden ingeschakeld

#### <a name="issue"></a>Probleem

U ontvangt een van de volgende berichten wanneer u probeert een vm aan boord te nemen voor een oplossing:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt door onjuiste of ontbrekende machtigingen op de vm of werkruimte of voor de gebruiker.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat u over de juiste [machtigingen beschikt die nodig zijn om machines aan boord te nemen](../automation-role-based-access-control.md#onboarding-permissions) en probeer vervolgens de oplossing opnieuw aan boord te nemen. Als u de `The solution cannot be enabled on this VM because the permission to read the workspace is missing`fout ontvangt, `Microsoft.OperationalInsights/workspaces/read` moet u ervoor zorgen dat u de toestemming hebt om te kunnen vinden of de vm aan boord is van een werkruimte.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: Onboarding mislukt met het bericht: kan automatiseringsaccount niet configureren voor diagnostische logboekregistratie

#### <a name="issue"></a>Probleem

U ontvangt het volgende bericht wanneer u probeert een vm aan boord te nemen voor een oplossing:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt als de prijscategorie niet overeenkomt met het factureringsmodel van het abonnement. Zie [Gebruik en geschatte kosten bewaken in Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Oplossing

Maak uw Log Analytics-werkruimte handmatig en herhaal het onboardingproces om de werkruimte te selecteren die is gemaakt.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Probleem

Deze foutcode betekent dat de opgeslagen zoekcomputergroepquery die wordt gebruikt om de oplossing te targeten, niet correct is opgemaakt. 

#### <a name="cause"></a>Oorzaak

Mogelijk hebt u de query gewijzigd of heeft het systeem deze gewijzigd.

#### <a name="resolution"></a>Oplossing

U de query voor de oplossing verwijderen en vervolgens de oplossing opnieuw aan boord nemen, waardoor de query opnieuw wordt gemaakt. De query is te vinden in uw werkruimte onder **Opgeslagen zoekopdrachten**. De naam van de query is **MicrosoftDefaultComputerGroup**en de categorie van de query is de naam van de bijbehorende oplossing. Als meerdere oplossingen zijn ingeschakeld, wordt de **query MicrosoftDefaultComputerGroup** meerdere keren weergegeven onder **Opgeslagen zoekopdrachten**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: Beleidsschending

#### <a name="issue"></a>Probleem

Deze foutcode geeft aan dat de implementatie is mislukt vanwege schending van een of meer beleidsregels.

#### <a name="cause"></a>Oorzaak 

Een beleid blokkeert de bewerking van het voltooien.

#### <a name="resolution"></a>Oplossing

Om de oplossing succesvol te implementeren, moet u overwegen het aangegeven beleid te wijzigen. Aangezien er veel verschillende soorten beleidsregels zijn die kunnen worden gedefinieerd, zijn de vereiste wijzigingen afhankelijk van het beleid dat wordt geschonden. Als een beleid bijvoorbeeld is gedefinieerd in een resourcegroep die de toestemming weigert om de inhoud van sommige opgenomen bronnen te wijzigen, u een van deze oplossingen kiezen:

* Verwijder het beleid helemaal.
* Probeer de oplossing aan boord te krijgen voor een andere resourcegroep.
* Target het beleid opnieuw op een specifieke resource, bijvoorbeeld een Automatiseringsaccount.
* Herzie de set resources die het beleid is geconfigureerd om te weigeren.

Controleer de meldingen in de rechterbovenhoek van de Azure-portal of navigeer naar de brongroep die uw Automatiseringsaccount bevat en selecteer **Implementaties** onder **Instellingen** om de mislukte implementatie weer te geven. Zie [Overzicht van Azure-beleid](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)voor meer informatie over Azure Policy .

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: fouten bij het ontkoppelen van een werkruimte

#### <a name="issue"></a>Probleem

U ontvangt de volgende fout wanneer u een werkruimte probeert los te koppelen:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u nog steeds oplossingen hebt die actief zijn in uw Log Analytics-werkruimte die afhankelijk zijn van de gekoppelde automatiseringsaccount en de werkruimte Log Analytics.

### <a name="resolution"></a>Oplossing

Verwijder de volgende oplossingen uit uw werkruimte als u ze gebruikt:

* Updatebeheer
* Wijzigingen bijhouden en Inventaris
* VM's starten/stoppen buiten kantooruren

Zodra u de oplossingen hebt verwijderd, u de koppeling van uw werkruimte ontkoppelen. Het is belangrijk om bestaande artefacten uit deze oplossingen op te schonen vanuit uw werkruimte en uw Automatiseringsaccount 

* Verwijder Update-implementaties (schema's) voor Updatebeheer uit uw Automatiseringsaccount.
* Verwijder alle vergrendelingen van oplossingsonderdelen in uw Automatiseringsaccount onder **Instellingenvergrendelingen** > **Locks**voor VM's starten/stoppen tijdens buitenkantooruren. Zie [Vm's starten/stoppen verwijderen tijdens de oplossing buiten kantooruren](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Fouten in Logboekanalyse voor Windows-extensie

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Een installatie van de Log Analytics-agent voor Windows-extensie kan om verschillende redenen mislukken. In de volgende sectie worden onboarding-problemen beschreven die fouten kunnen veroorzaken tijdens de implementatie van de log-analyse-agent voor Windows-extensie.

>[!NOTE]
>Log Analytics-agent voor Windows is de naam die momenteel wordt gebruikt in Azure Automation voor de Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: Er is een uitzondering opgetreden tijdens een WebClient-aanvraag

De logboekanalyse voor Windows-extensie op de VM kan niet communiceren met externe bronnen en de implementatie mislukt.

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

* Een proxy die is geconfigureerd in de VM staat alleen specifieke poorten toe.
* Een firewall-instelling heeft de toegang tot de vereiste poorten en adressen geblokkeerd.

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat u over de juiste poorten en adressen beschikt die openstaan voor communicatie. Zie [uw netwerk plannen](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met poorten en adressen.

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: Installatie mislukt vanwege tijdelijke omgevingsproblemen

De installatie van de Log Analytics for Windows-extensie is mislukt tijdens de implementatie vanwege een andere installatie of actie die de installatie blokkeert

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

* Een andere installatie is in volle gang.
* Het systeem wordt geactiveerd om opnieuw op te starten tijdens de implementatie van sjablonen.

#### <a name="resolution"></a>Oplossing

Deze fout is van voorbijgaande aard. Probeer de implementatie opnieuw om de extensie te installeren.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: Time-out voor installatie

De installatie van de Log Analytics for Windows-extensie is niet voltooid vanwege een time-out.

#### <a name="issue"></a>Probleem

Het volgende is een voorbeeld van een foutbericht dat mogelijk wordt geretourneerd:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Oorzaak

Dit type fout treedt op omdat de VM tijdens de installatie zwaar wordt belast.

### <a name="resolution"></a>Oplossing

Probeer de logboekanalyse-agent voor Windows-extensie te installeren wanneer de VM onder een lagere belasting staat.

## <a name="next-steps"></a>Volgende stappen

Als je het probleem hierboven niet ziet of het probleem niet oplossen, probeer je een van de volgende kanalen voor extra ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Forums.](https://azure.microsoft.com/support/forums/)
* Maak [@AzureSupport](https://twitter.com/azuresupport)verbinding met het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.
* Een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.
