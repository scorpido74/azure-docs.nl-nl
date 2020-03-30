---
title: Problemen met de diagnose van Windows Virtual Desktop - Azure
description: De functie Diagnostische gegevens van Windows Virtual Desktop gebruiken om problemen te diagnosticeren.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254259"
---
# <a name="identify-and-diagnose-issues"></a>Problemen identificeren en diagnosticeren

Windows Virtual Desktop biedt een diagnostische functie waarmee de beheerder problemen kan identificeren via één interface. De rollen Virtuele bureaublad van Windows registreren een diagnostische activiteit wanneer een gebruiker met het systeem communiceert. Elk logboek bevat relevante informatie, zoals de Windows Virtual Desktop-rollen die betrokken zijn bij de transactie, foutmeldingen, tenantgegevens en gebruikersinformatie. Diagnostische activiteiten worden gemaakt door zowel eindgebruikers als administratieve acties en kunnen worden ingedeeld in drie hoofdbuckets:

* Activiteiten voor feedabonnementen: de eindgebruiker activeert deze activiteiten wanneer hij verbinding probeert te maken met hun feed via Microsoft Remote Desktop-toepassingen.
* Verbindingsactiviteiten: de eindgebruiker activeert deze activiteiten wanneer deze verbinding probeert te maken met een bureaublad of RemoteApp via Microsoft Remote Desktop-toepassingen.
* Beheeractiviteiten: de beheerder activeert deze activiteiten wanneer deze beheerbewerkingen op het systeem uitvoeren, zoals het maken van hostpools, het toewijzen van gebruikers aan app-groepen en het maken van roltoewijzingen.
  
Verbindingen die Windows Virtual Desktop niet bereiken, worden niet weergegeven in diagnostische resultaten omdat de diagnostische rolservice zelf deel uitmaakt van Windows Virtual Desktop. Er kunnen problemen optreden met de verbindingsproblemen van Windows Virtual Desktop wanneer de eindgebruiker problemen ondervindt met de netwerkverbinding.

Download en [importeer de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) om te gebruiken in uw PowerShell-sessie als u dat nog niet hebt gedaan. Voer daarna de volgende cmdlet uit om u aan te melden bij uw account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>Problemen met PowerShell diagnosticeren

Windows Virtual Desktop Diagnostics gebruikt slechts één PowerShell-cmdlet, maar bevat veel optionele parameters om problemen te beperken en te isoleren. In de volgende secties worden de cmdlets weergegeven die u uitvoeren om problemen te diagnosticeren. De meeste filters kunnen samen worden toegepast. Waarden die tussen haakjes worden `<tenantName>`vermeld, zoals , moeten worden vervangen door de waarden die van toepassing zijn op uw situatie.

>[!IMPORTANT]
>De diagnostische functie is voor het oplossen van problemen met één gebruiker. Alle query's met PowerShell moeten de parameters *-UserName* of *-ActivityID* bevatten. Gebruik Log Analytics voor het bewaken van mogelijkheden. Zie [Log analytics gebruiken voor de diagnostische functie](diagnostics-log-analytics.md) voor meer informatie over het verzenden van diagnostische gegevens naar uw werkruimte. 

### <a name="filter-diagnostic-activities-by-user"></a>Diagnostische activiteiten filteren op gebruiker

De parameter **-UserName** retourneert een lijst met diagnostische activiteiten die door de opgegeven gebruiker zijn geïnitieerd, zoals weergegeven in de volgende voorbeeldcmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

De parameter **-UserName** kan ook worden gecombineerd met andere optionele filterparameters.

### <a name="filter-diagnostic-activities-by-time"></a>Diagnostische activiteiten filteren op tijd

U de lijst met geretourneerde diagnostische activiteiten filteren met de parameters **StartTime** en **-EndTime.** De parameter **-StartTime** retourneert een lijst met diagnostische activiteiten vanaf een specifieke datum, zoals in het volgende voorbeeld wordt weergegeven.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

De parameter **-EndTime** kan worden toegevoegd aan een cmdlet met de parameter **-StartTime** om een specifieke periode op te geven waarvoor u resultaten wilt ontvangen. In het volgende voorbeeld wordt een lijst met diagnostische activiteiten van 1 augustus tot en met 10 augustus weergegeven.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

De parameters **-StartTime** en **-EndTime** kunnen ook worden gecombineerd met andere optionele filterparameters.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Diagnostische activiteiten filteren op activiteitstype

U diagnostische activiteiten ook filteren op activiteitstype met de parameter **-ActivityType.** Met de volgende cmdlet wordt een lijst met verbindingen voor eindgebruikers retourneren:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

Met de volgende cmdlet wordt een lijst met beheertaken voor beheerders retourneren:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

De cmdlet **Get-RdsDiagnosticActivities** biedt momenteel geen ondersteuning voor het opgeven van feed als activitytype.

### <a name="filter-diagnostic-activities-by-outcome"></a>Diagnostische activiteiten filteren op resultaat

U de lijst met geretourneerde diagnostische activiteiten filteren op resultaat met de parameter **-Uitkomst.** In het volgende voorbeeld geeft cmdlet een lijst met succesvolle diagnostische activiteiten.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

In het volgende voorbeeld wordt een lijst met mislukte diagnostische activiteiten weergegeven.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

De parameter **-Outcome** kan ook worden gecombineerd met andere optionele filterparameters.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Een specifieke diagnostische activiteit ophalen met activiteits-id

De parameter **ActivityId** retourneert een specifieke diagnostische activiteit als deze bestaat, zoals wordt weergegeven in de volgende voorbeeldcmdlet.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Foutberichten voor een mislukte activiteit weergeven op activiteits-id

Als u de foutberichten voor een mislukte activiteit wilt weergeven, moet u de cmdlet uitvoeren met de parameter **-Gedetailleerd.** U de lijst met fouten bekijken door de cmdlet **Select-Object** uit te voeren.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Gedetailleerde diagnostische activiteiten ophalen

De parameter **-Gedetailleerd** bevat aanvullende details voor elke geretourneerde diagnostische activiteit. De indeling voor elke activiteit is afhankelijk van het activiteitstype. De parameter **-Gedetailleerd** kan worden toegevoegd aan elke **query get-rdsdiagnosticactivities,** zoals in het volgende voorbeeld wordt weergegeven.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Veelvoorkomende foutscenario's

Foutscenario's zijn intern gecategoriseerd in de service en extern aan Windows Virtual Desktop.

* Intern probleem: hiermee worden scenario's opgegeven die niet kunnen worden beperkt door de tenantbeheerder en moeten worden opgelost als ondersteuningsprobleem. Bij het geven van feedback via de [Windows Virtual Desktop Tech Community,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)omvatten de activiteit-ID en het geschatte tijdsbestek van het moment waarop het probleem zich heeft voorgedaan.
* Extern probleem: betrekking hebben op scenario's die door de systeembeheerder kunnen worden beperkt. Deze zijn extern aan Windows Virtual Desktop.

In de volgende tabel worden veelvoorkomende fouten weergegeven die uw beheerders kunnen tegenkomen.

>[!NOTE]
>Deze lijst bevat de meest voorkomende fouten en wordt bijgewerkt op een regelmatige cadans. Om ervoor te zorgen dat u over de meest actuele informatie beschikt, moet u dit artikel minstens één keer per maand bekijken.

### <a name="external-management-error-codes"></a>Foutcodes extern beheer

|Numerieke code|Foutcode|Voorgestelde oplossing|
|---|---|---|
|3|Ongeautoriseerde toegang|De gebruiker die de administratieve PowerShell-cmdlet probeerde uit te voeren, heeft geen machtigingen om dit te doen of heeft zijn gebruikersnaam verkeerd getypt.|
|1000|HuurderNiet gevonden|De tenantnaam die u hebt ingevoerd, komt niet overeen met bestaande tenants. Bekijk de tenantnaam voor typefouten en probeer het opnieuw.|
|1006|TenantCannotBeremovedHassessionHostPools|U een tenant niet verwijderen zolang deze objecten bevat. Verwijder eerst de sessiehostpools en probeer het opnieuw.|
|2000|HostPoolNotFound|De naam van de hostpool die u hebt ingevoerd, komt niet overeen met bestaande hostpools. Bekijk de naam van het hostpool voor typefouten en probeer het opnieuw.|
|2005|Hostpoolkannietworden verwijderdHeeftapplicationgroups|U een hostgroep niet verwijderen zolang deze objecten bevat. Verwijder eerst alle appgroepen in de hostgroep.|
|2004|Hostpoolkannietworden verwijderdheeftsessionhosts|Verwijder alle sessies hosts eerst voordat u de sessiehostpool verwijdert.|
|5001|SessionHostNotFound|De sessiehost die u hebt opgevraagd, is mogelijk offline. Controleer de status van het hostpool.|
|5008|SessionHostUserSessionsExist |U moet alle gebruikers op de sessiehost afmelden voordat u uw beoogde beheeractiviteit uitvoert.|
|6000|AppGroupNotFound|De ingevoerde naam van de app-groep die u hebt ingevoerd, komt niet overeen met bestaande app-groepen. Controleer de naam van de app-groep op typefouten en probeer het opnieuw.|
|6022|RemoteAppNotFound RemoteAppNotFound RemoteAppNotFound RemoteApp|De remoteapp-naam die u hebt ingevoerd, komt niet overeen met RemoteApps. Bekijk de naam van RemoteApp op typefouten en probeer het opnieuw.|
|6010|GepubliceerdeitemsExist|De naam van de resource die u probeert te publiceren, is dezelfde als een resource die al bestaat. Wijzig de naam van de resource en probeer het opnieuw.|
|7002|NameNotValidWhiteSpace|Gebruik geen witruimte in de naam.|
|8000|InvalidAuthorizationRoleScope Ongeldig|De ingevoerde rolnaam komt niet overeen met bestaande rolnamen. Bekijk de rolnaam voor typefouten en probeer het opnieuw. |
|8001|UserNotFound |De ingevoerde gebruikersnaam komt niet overeen met bestaande gebruikersnamen. Bekijk de naam voor typefouten en probeer het opnieuw.|
|8005|UserNotfoundInAAD |De ingevoerde gebruikersnaam komt niet overeen met bestaande gebruikersnamen. Bekijk de naam voor typefouten en probeer het opnieuw.|
|8008|Toestemming van huurder vereist|Volg [hier](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) de instructies om toestemming te geven aan uw huurder.|

### <a name="external-connection-error-codes"></a>Foutcodes voor externe verbinding

|Numerieke code|Foutcode|Voorgestelde oplossing|
|---|---|---|
|-2147467259|VerbindingmisluktAdTrustedRelationshipFailure|De sessiehost is niet correct verbonden met de Active Directory.|
|-2146233088|VerbindingFailedUserHasValidSessionButRdshIsNiet-gezond|De verbindingen zijn mislukt omdat de sessiehost niet beschikbaar is. Controleer de gezondheid van de sessiehost.|
|-2146233088|Verbinding clientdisconnect|Als u deze fout vaak ziet, controleert u of de computer van de gebruiker is verbonden met het netwerk.|
|-2146233088|VerbindingMisluktNoHealthyRdshBeschikbaar|De sessie waarmee de hostgebruiker verbinding heeft gemaakt, is niet gezond. Debug de virtuele machine.|
|-2146233088|VerbindingmisluktNietgeautoriseerd|De gebruiker heeft geen toestemming om toegang te krijgen tot de gepubliceerde app of desktop. De fout kan worden weergegeven nadat de beheerder gepubliceerde bronnen heeft verwijderd. Vraag de gebruiker de feed in de Extern bureaublad-toepassing te vernieuwen.|
|2|FileNotFound|De toepassing die de gebruiker probeerde te openen, is onjuist geïnstalleerd of ingesteld op een onjuist pad.|
|3|Ongeldige referenties|De gebruikersnaam of het wachtwoord die de gebruiker heeft ingevoerd, komt niet overeen met bestaande gebruikersnamen of wachtwoorden. Bekijk de referenties voor typefouten en probeer het opnieuw.|
|8|Verbinding verbroken|De verbinding tussen Client en Gateway of Server is verbroken. Geen actie nodig, tenzij het onverwacht gebeurt.|
|14|OnverwachtnetwerkLoskoppeling|De verbinding met het netwerk is verbroken. Vraag de gebruiker om opnieuw verbinding te maken.|
|24|ReverseConnect is mislukt|De virtuele hostmachine heeft geen directe zichtlijn naar RD Gateway. Zorg ervoor dat het IP-adres van de gateway kan worden opgelost.|

## <a name="next-steps"></a>Volgende stappen

Zie De omgeving van [Windows Virtual Desktop](environment-setup.md)voor meer informatie over rollen binnen Windows Virtual Desktop.

Zie de [PowerShell-verwijzing](/powershell/windows-virtual-desktop/overview)voor een lijst met beschikbare PowerShell-cmdlets voor Windows Virtual Desktop.
