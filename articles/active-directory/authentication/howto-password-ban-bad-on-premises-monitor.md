---
title: On-premises Azure AD-wachtwoordbeveiliging controleren
description: Informatie over het controleren en controleren van logboeken voor Azure AD Password Protection voor een on-premises Active Directory Domain Services-omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb533d5565009fb22d686e4082c9b4bfaae6dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671663"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Logboeken controleren en controleren voor on-premises Azure AD Password Protection-omgevingen

Na de implementatie van Azure AD Password Protection zijn monitoring en rapportage essentiële taken. Dit artikel gaat in detail om u te helpen inzicht te krijgen in verschillende bewakingstechnieken, waaronder waar elke service informatie registreert en hoe u rapporteren over het gebruik van Azure AD Password Protection.

Monitoring en rapportage worden gedaan door gebeurtenislogboekberichten of door PowerShell-cmdlets uit te voeren. De DC-agent en proxyservices registreren beide gebeurtenislogboekberichten. Alle powershell-cmdlets die hieronder worden beschreven, zijn alleen beschikbaar op de proxyserver (zie de AzureADPasswordProtection PowerShell-module). De DC-agentsoftware installeert geen PowerShell-module.

## <a name="dc-agent-event-logging"></a>Logboekregistratie van DC-agentgebeurtenissen

Op elke domeincontroller schrijft de DC-agentservicesoftware de resultaten van elke afzonderlijke wachtwoordvalidatiebewerking (en andere status) naar een lokaal gebeurtenislogboek:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Het logboek van de DC-agentbeheerder is de primaire bron van informatie over hoe de software zich gedraagt.

Houd er rekening mee dat het logboek Trace standaard is uitgeschakeld.

Gebeurtenissen die zijn vastgelegd door de verschillende DC-agentonderdelen vallen binnen de volgende bereiken:

|Onderdeel |Gebeurtenis-ID-bereik|
| --- | --- |
|DC Agent-wachtwoordfilter dll| 10000-19999|
|DC-agentservicehostingproces| 20000-29999|
|Validatielogica voor dc-agentservicebeleid| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Gebeurtenislogboek DC-agentAdmin

### <a name="password-validation-outcome-events"></a>Resultaatgebeurtenissen voor wachtwoordvalidatie

Op elke domeincontroller schrijft de DC-agentservicesoftware de resultaten van elke afzonderlijke wachtwoordvalidatie naar het gebeurtenislogboek van de DC-agentbeheerder.

Voor een succesvolle wachtwoordvalidatiebewerking is er over het algemeen één gebeurtenis die is geregistreerd via het wachtwoordfilter dll van de DC-agent. Voor een mislukte wachtwoordvalidatiebewerking zijn er over het algemeen twee gebeurtenissen geregistreerd, een van de DC-agentservice en een van het wachtwoordfilterdll van dc-agent.

Afzonderlijke gebeurtenissen om deze situaties vast te leggen worden geregistreerd op basis van de volgende factoren:

* Of een bepaald wachtwoord wordt ingesteld of gewijzigd.
* Of de validatie van een bepaald wachtwoord is geslaagd of mislukt.
* Of de validatie is mislukt vanwege het algemene beleid van Microsoft, het organisatiebeleid of een combinatie.
* Of de modus alleen controleren momenteel is ingeschakeld of uitgeschakeld voor het huidige wachtwoordbeleid.

De belangrijkste gebeurtenissen die betrekking hebben op wachtwoordvalidatie zijn als volgt:

|   |Wachtwoord wijzigen |Wachtwoordset|
| --- | :---: | :---: |
|Pass |10014 |10015|
|Fail (vanwege het wachtwoordbeleid van de klant)| 10016, 30002| 10017, 30003|
|Fail (als gevolg van microsoft-wachtwoordbeleid)| 10016, 30004| 10017, 30005|
|Fail (vanwege gecombineerd microsoft- en klantwachtwoordbeleid)| 10016, 30026| 10017, 30027|
|Audit-only Pass (zou hebben gefaald klant wachtwoord beleid)| 10024, 30008| 10025, 30007|
|Audit-only Pass (zou het wachtwoordbeleid van Microsoft hebben mislukt)| 10024, 30010| 10025, 30009|
|Audit-only Pass (zou zijn mislukt gecombineerd Microsoft en klant wachtwoord beleid)| 10024, 30028| 10025, 30029|

De gevallen in de bovenstaande tabel die verwijzen naar "gecombineerd beleid" verwijzen naar situaties waarin het wachtwoord van een gebruiker ten minste één token bleek te bevatten uit zowel de lijst met microsoft-verboden wachtwoorden als de lijst met geblokkeerde wachtwoorden van de klant.

Wanneer een paar gebeurtenissen samen wordt geregistreerd, worden beide gebeurtenissen expliciet geassocieerd met dezelfde CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Rapportage over wachtwoordvalidatie via PowerShell

De `Get-AzureADPasswordProtectionSummaryReport` cmdlet kan worden gebruikt om een overzicht te geven van de activiteit wachtwoordvalidatie. Een voorbeeld van deze cmdlet is als volgt:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Het bereik van de rapportage van de cmdlet kan worden beïnvloed met behulp van een van de parameters -Forest, -Domain of –DomainController. Het niet opgeven van een parameter impliceert –Forest.

De `Get-AzureADPasswordProtectionSummaryReport` cmdlet werkt door het gebeurtenislogboek van de DC-agentbeheerder op te vragen en vervolgens het totale aantal gebeurtenissen te tellen dat overeenkomt met elke weergegeven resultaatcategorie. De volgende tabel bevat de toewijzingen tussen elk resultaat en de bijbehorende gebeurtenis-id:

|Get-AzureADPasswordProtectionSummaryReport, eigenschap |Overeenkomstige gebeurtenis-id|
| :---: | :---: |
|PasswordChangesGevalideerd |10014|
|Wachtwoordsetsgevalideerd |10015|
|Wachtwoordwijzigingen afgewezen |10016|
|Wachtwoordsetsafgewezen |10017|
|PasswordChangeAuditOnly-fouten |10024|
|PasswordSetAuditOnly-fouten |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Houd er `Get-AzureADPasswordProtectionSummaryReport` rekening mee dat de cmdlet wordt verzonden in PowerShell-scriptvorm en indien nodig rechtstreeks op de volgende locatie kan worden verwezen:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Deze cmdlet werkt door het openen van een PowerShell sessie voor elke domeincontroller. Om te slagen, moet PowerShell remote session support zijn ingeschakeld op elke domeincontroller en moet de client voldoende bevoegdheden hebben. Voer 'Get-Help about_Remote_Troubleshooting' uit voor meer informatie over de externe sessievereisten van PowerShell in een PowerShell-venster.

> [!NOTE]
> Deze cmdlet werkt door het gebeurtenislogboek van elke DC-agentservice op afstand op te vragen. Als de gebeurtenislogboeken grote aantallen gebeurtenissen bevatten, kan het lang duren voordat de cmdlet is voltooid. Bovendien kunnen bulknetwerkquery's van grote gegevenssets van invloed zijn op de prestaties van de domeincontroller. Daarom moet deze cmdlet zorgvuldig worden gebruikt in productieomgevingen.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Voorbeeldgebeurtenislogboekbericht voor gebeurtenis-id 10014 (geslaagde wachtwoordwijziging)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Voorbeeldgebeurtenislogboekbericht voor gebeurtenis-id 10017 en 30003 (mislukte wachtwoordset)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Voorbeeldgebeurtenislogboekbericht voor gebeurtenis-id 30001 (wachtwoord geaccepteerd omdat er geen beleid beschikbaar is)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Voorbeeldgebeurtenislogboekbericht voor gebeurtenis-id 30006 (nieuw beleid wordt afgedwongen)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Voorbeeldgebeurtenislogboekbericht voor gebeurtenis-id 30019 (Azure AD-wachtwoordbeveiliging is uitgeschakeld)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Operationeel logboek dc-agent

De DC-agentservice registreert ook operationele gebeurtenissen in het volgende logboek:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>LOGBOEK DC-agenttracering

De DC-agentservice kan ook traceringsgebeurtenissen op verbose-niveau registreren in het volgende logboek:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Traceringslogboekregistratie is standaard uitgeschakeld.

> [!WARNING]
> Wanneer dit is ingeschakeld, ontvangt het traceerlogboek een groot aantal gebeurtenissen en kan dit gevolgen hebben voor de prestaties van de domeincontroller. Daarom moet dit verbeterde logboek alleen worden ingeschakeld wanneer een probleem dieper onderzoek vereist, en vervolgens slechts voor een minimale hoeveelheid tijd.

## <a name="dc-agent-text-logging"></a>Tekstlogboeken van DC-agent

De DC-agentservice kan worden geconfigureerd om naar een tekstlogboek te schrijven door de volgende registerwaarde in te stellen:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Tekstlogboekregistratie is standaard uitgeschakeld. Een herstart van de DC-agentservice is vereist om wijzigingen in deze waarde van kracht te laten worden. Wanneer ingeschakeld, schrijft de DC-agentservice naar een logboekbestand dat zich onder:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Het tekstlogboek ontvangt dezelfde debug-level items die kunnen worden aangemeld bij het traceerlogboek, maar is over het algemeen in een eenvoudiger indeling om te controleren en te analyseren.

> [!WARNING]
> Wanneer dit logboek is ingeschakeld, ontvangt dit een groot aantal gebeurtenissen en kan dit gevolgen hebben voor de prestaties van de domeincontroller. Daarom moet dit verbeterde logboek alleen worden ingeschakeld wanneer een probleem dieper onderzoek vereist, en vervolgens slechts voor een minimale hoeveelheid tijd.

## <a name="dc-agent-performance-monitoring"></a>Prestatiebewaking dc-agent

De DC-agentservicesoftware installeert een prestatiemeterobject met de naam **Azure AD Password Protection**. De volgende perf-tellers zijn momenteel beschikbaar:

|Perf-tellernaam | Beschrijving|
| --- | --- |
|Wachtwoorden verwerkt |Deze teller geeft het totale aantal wachtwoorden weer dat is verwerkt (geaccepteerd of afgewezen) sinds de laatste herstart.|
|Geaccepteerde wachtwoorden |Deze teller geeft het totale aantal wachtwoorden weer dat is geaccepteerd sinds de laatste herstart.|
|Wachtwoorden afgewezen |Deze teller geeft het totale aantal wachtwoorden weer dat is afgewezen sinds de laatste herstart.|
|Aanvragen voor wachtwoordfilter in uitvoering |Deze teller geeft het aantal wachtwoordfilteraanvragen weer dat momenteel wordt uitgevoerd.|
|Piekwachtwoordfilteraanvragen |Deze teller geeft het piekaantal gelijktijdige wachtwoordfilteraanvragen sinds de laatste herstart weer.|
|Fouten in wachtwoordfilteraanvragen |In deze teller wordt het totale aantal wachtwoordfilteraanvragen weergegeven dat is mislukt als gevolg van een fout sinds de laatste herstart. Er kunnen fouten optreden wanneer de AZURE AD Password Protection DC-agentservice niet wordt uitgevoerd.|
|Aanvragen voor wachtwoordfilter/sec |Deze teller geeft de snelheid weer waarmee wachtwoorden worden verwerkt.|
|Verwerkingstijd voor wachtwoordfilters |Deze teller geeft de gemiddelde tijd weer die nodig is om een wachtwoordfilteraanvraag te verwerken.|
|Verwerkingstijd voor piekwachtwoordfilters |Deze teller geeft de verwerkingstijd van de piekwachtwoordfilteraanvraag weer sinds de laatste herstart.|
|Wachtwoorden geaccepteerd vanwege controlemodus |Deze teller geeft het totale aantal wachtwoorden weer dat normaal gesproken zou zijn afgewezen, maar is geaccepteerd omdat het wachtwoordbeleid is geconfigureerd om in de auditmodus te zijn (sinds de laatste herstart).|

## <a name="dc-agent-discovery"></a>DC-agentdetectie

De `Get-AzureADPasswordProtectionDCAgent` cmdlet kan worden gebruikt om basisinformatie weer te geven over de verschillende DC-agents die in een domein of forest worden uitgevoerd. Deze informatie wordt opgehaald uit het serviceConnectionPoint-object(en) dat is geregistreerd door de uitvoerende DC-agentservice(s).

Een voorbeeld van deze cmdlet is als volgt:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

De verschillende eigenschappen worden bijgewerkt door elke DC-agent service op een ongeveer uur basis. De gegevens zijn nog steeds onderhevig aan de latentie van Active Directory-replicatie.

Het bereik van de query van de cmdlet kan worden beïnvloed met behulp van de parameters -Forest of –Domain.

Als de HeartbeatUTC-waarde verouderd raakt, kan dit een symptoom zijn dat de Azure AD Password Protection DC-agent op die domeincontroller niet wordt uitgevoerd of is verwijderd of dat de machine is gedegradeerd en niet langer een domeincontroller is.

Als de waarde PasswordPolicyDateUTC verouderd raakt, kan dit een symptoom zijn dat de Azure AD Password Protection DC-agent op die machine niet goed werkt.

## <a name="dc-agent-newer-version-available"></a>DC-agent nieuwere versie beschikbaar

De DC-agentservice registreert een 30034-waarschuwingsgebeurtenis op het operationele logboek wanneer wordt gedetecteerd dat er een nieuwere versie van de DC-agentsoftware beschikbaar is, bijvoorbeeld:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

De bovenstaande gebeurtenis geeft niet de versie van de nieuwere software. Ga naar de link in het gebeurtenisbericht voor die informatie.

> [!NOTE]
> Ondanks de verwijzingen naar "autoupgrade" in het bovenstaande gebeurtenisbericht, ondersteunt de DC-agentsoftware deze functie momenteel niet.

## <a name="proxy-service-event-logging"></a>Logboekregistratie van proxyservicegebeurtenissen

De proxyservice zendt een minimale set gebeurtenissen uit aan de volgende gebeurtenislogboeken:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Houd er rekening mee dat het logboek Trace standaard is uitgeschakeld.

> [!WARNING]
> Wanneer ingeschakeld, ontvangt het traceerlogboek een groot aantal gebeurtenissen en dit kan van invloed zijn op de prestaties van de proxyhost. Daarom moet dit logboek alleen worden ingeschakeld wanneer een probleem dieper onderzoek vereist, en vervolgens slechts voor een minimale hoeveelheid tijd.

Gebeurtenissen worden geregistreerd door de verschillende proxycomponenten met behulp van de volgende bereiken:

|Onderdeel |Gebeurtenis-ID-bereik|
| --- | --- |
|Proxyservicehostingproces| 10000-19999|
|Proxy service core business logic Proxy service core business logic Proxy service core| 20000-29999|
|PowerShell-cmdlets| 30000-39999|

## <a name="proxy-service-text-logging"></a>Tekstlogboeken van proxyservice

De proxyservice kan worden geconfigureerd om naar een tekstlogboek te schrijven door de volgende registerwaarde in te stellen:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD waarde)

Tekstlogboekregistratie is standaard uitgeschakeld. Een herstart van de proxyservice is vereist om wijzigingen in deze waarde van kracht te laten worden. Wanneer ingeschakeld, schrijft de proxyservice naar een logboekbestand dat zich onder:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Het tekstlogboek ontvangt dezelfde debug-level items die kunnen worden aangemeld bij het traceerlogboek, maar is over het algemeen in een eenvoudiger indeling om te controleren en te analyseren.

> [!WARNING]
> Wanneer dit is ingeschakeld, ontvangt dit logboek een groot aantal gebeurtenissen en kan dit van invloed zijn op de prestaties van de machine. Daarom moet dit verbeterde logboek alleen worden ingeschakeld wanneer een probleem dieper onderzoek vereist, en vervolgens slechts voor een minimale hoeveelheid tijd.

## <a name="powershell-cmdlet-logging"></a>PowerShell-logboekregistratie

PowerShell-cmdlets die resulteren in een statuswijziging (bijvoorbeeld Register-AzureADPasswordProtectionProxy) loggen normaal gesproken een uitkomstgebeurtenis in het operationeel logboek.

Bovendien schrijven de meeste Azure AD Password Protection PowerShell-cmdlets naar een tekstlogboek dat zich onder:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Als er een cmdletfout optreedt en de oorzaak en de oplossing niet direct zichtbaar is, kunnen deze tekstlogboeken ook worden geraadpleegd.

## <a name="proxy-discovery"></a>Proxydetectie

De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt om basisinformatie weer te geven over de verschillende Azure AD Password Protection Proxy-services die in een domein of forest worden uitgevoerd. Deze informatie wordt opgehaald uit het serviceConnectionPoint-object(en) dat is geregistreerd door de uitvoerende proxyservice(s).

Een voorbeeld van deze cmdlet is als volgt:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

De verschillende eigenschappen worden bijgewerkt door elke Proxy-service op een ongeveer uurbasis. De gegevens zijn nog steeds onderhevig aan de latentie van Active Directory-replicatie.

Het bereik van de query van de cmdlet kan worden beïnvloed met behulp van de parameters -Forest of –Domain.

Als de HeartbeatUTC-waarde verouderd raakt, kan dit een symptoom zijn dat de Azure AD Password Protection Proxy op die machine niet wordt uitgevoerd of is verwijderd.

## <a name="proxy-agent-newer-version-available"></a>Nieuwe versie van de proxyagent beschikbaar

De proxyservice registreert een waarschuwingsgebeurtenis in 20002 bij het operationele logboek wanneer wordt gedetecteerd dat er een nieuwere versie van de proxysoftware beschikbaar is, bijvoorbeeld:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

De bovenstaande gebeurtenis geeft niet de versie van de nieuwere software. Ga naar de link in het gebeurtenisbericht voor die informatie.

Deze gebeurtenis wordt uitgezonden, zelfs als de proxy-agent is geconfigureerd met auto-upgrade ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

[Problemen oplossen voor Azure AD-wachtwoordbeveiliging](howto-password-ban-bad-on-premises-troubleshoot.md)

Voor meer informatie over de wereldwijde en aangepaste verboden wachtwoord lijsten, zie het artikel [Ban slechte wachtwoorden](concept-password-ban-bad.md)
