---
title: Client verbindingen in virtueel bureau blad van Windows Extern bureaublad-Azure
description: Problemen oplossen bij het instellen van client verbindingen in een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 22590a7ecc94efbfad5eefe27f284bdb191e876d
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676410"
---
# <a name="remote-desktop-client-connections"></a>Clientverbindingen met extern bureaublad

Gebruik dit artikel voor het oplossen van problemen met Windows-client verbindingen met virtueel bureau blad.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="you-cant-open-a-web-client"></a>U kunt geen webclient openen

Controleer of de Internet verbinding is door een andere website te openen. bijvoorbeeld [www.Bing.com](https://www.bing.com).

**Nslookup** gebruiken om te controleren of DNS de FQDN kan omzetten:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Probeer verbinding te maken met een andere client, bijvoorbeeld Extern bureaublad-client voor Windows 7 of Windows 10, en controleer of u de webclient kunt openen.

### <a name="error-opening-another-site-fails"></a>Fout: Het openen van een andere site mislukt

**Wordt** Netwerk problemen en/of storingen.

**Fix:** Neem contact op met de netwerk ondersteuning.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fout: Nslookup kan de naam niet omzetten

**Wordt** Netwerk problemen en/of storingen.

**Fix:** Contact opnemen met netwerk ondersteuning

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Fout: U kunt geen verbinding maken, maar andere clients kunnen verbinding maken

**Wordt** De browser werkt niet zoals verwacht en werkt niet meer.

**Fix:** Volg deze instructies voor het oplossen van problemen met de browser.

1. Start de browser opnieuw.
2. Wis browser cookies. Zie [cookie bestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wis de browsercache. Zie [browser cache wissen voor uw browser](https://binged.it/2RKyfdU).
4. Open de browser in de persoonlijke modus.

## <a name="web-client-stops-responding-or-disconnects"></a>Webclient reageert niet meer of de verbinding wordt verbroken

Probeer verbinding te maken met behulp van een andere browser of client.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Fout: Andere browsers en clients kunnen ook niet worden geopend

**Wordt** Problemen met netwerk-en/of bewerkings systemen of storingen

**Fix:** Neem contact op met de ondersteunings teams.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webclient vraagt om referenties

Als de webclient om referenties wordt gevraagd, volgt u deze instructies.

1. Controleer of de URL van de webclient juist is.
2. Controleer of de referenties voor de Windows Virtual Desktop-omgeving zijn gekoppeld aan de URL.
3. Wis browser cookies. Zie [cookie bestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Wis de browsercache. Zie [browser cache wissen voor uw browser](https://binged.it/2RKyfdU).
5. Open de browser in de persoonlijke modus.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Extern bureaublad-client voor Windows 7 of Windows 10 reageert niet meer of kan niet worden geopend

Gebruik de volgende Power shell-cmdlets om out-of-band-client registers (OOB) op te schonen.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Ga naar **%AppData%\RdClientRadc** en verwijder alle inhoud.

Verwijder Extern bureaublad-client voor Windows 7 en Windows 10 en installeer deze opnieuw.

## <a name="troubleshooting-end-user-connectivity"></a>Problemen met de connectiviteit van eind gebruikers oplossen

Soms hebben gebruikers toegang tot hun feed en lokale resources, maar ze hebben nog steeds configuratie-, beschik baarheids-of prestatie problemen die verhinderen dat ze toegang krijgen tot externe bronnen. In dergelijke gevallen ontvangt de gebruiker berichten die er ongeveer als volgt uitzien:

![Verbinding met extern bureaublad fout bericht.](media/eb76b666808bddb611448dfb621152ce.png)

![Kan geen verbinding maken met het fout bericht van de gateway.](media/a8fbb9910d4672147335550affe58481.png)

Volg deze algemene instructies voor het oplossen van problemen met fout codes voor client verbindingen.

1. Bevestig de gebruikers naam en het tijdstip waarop het probleem is opgetreden.
2. Open **Power shell** en breng verbinding tot stand met de Windows Virtual Desktop-Tenant waar het probleem is gerapporteerd.
3. Controleer de verbinding met de juiste Tenant met **Get-RdsTenant.**
4. Controleer met **Get-RdsHostPool** en **Get-RdsSessionHost-** cmdlets of de probleem oplossing wordt uitgevoerd voor de juiste hostgroep.
5. Voer de onderstaande opdracht uit om een lijst op te halen met alle mislukte activiteiten van het type verbinding voor het opgegeven tijd venster:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Voer de volgende opdracht uit met behulp van de **ActivityId** uit de vorige uitvoer van de cmdlet:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. De opdracht produceert uitvoer zoals hieronder wordt weer gegeven. Gebruik **ErrorCodeSymbolic** en **errorMessage** om de hoofd oorzaak op te lossen.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Fout: O_ADD_USER_TO_GROUP_FAILED/kan gebruiker niet toevoegen = ≤ gebruikers naam ≥ aan groep = Extern bureaublad gebruikers. Gemotiveerd Win32.ERROR_NO_SUCH_MEMBER

**Wordt** De VM is niet toegevoegd aan het domein waarin het gebruikers object zich bevindt.

**Fix:** Voeg de virtuele machine toe aan het juiste domein. Zie [een virtuele Windows Server-machine toevoegen aan een beheerd domein](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Fout: Nslookup kan de naam niet omzetten

**Wordt** Netwerk problemen of-storingen.

**Fix:** Contact opnemen met netwerk ondersteuning

### <a name="error-connectionfailedclientprotocolerror"></a>Fout: ConnectionFailedClientProtocolError

**Wordt** Vm's waarmee de gebruiker probeert verbinding te maken, zijn geen lid van een domein.

**Fix:** Voeg alle virtuele machines die deel uitmaken van een hostgroep toe aan de domein controller.

### <a name="error-connectionfailedusersidinformationmismatch"></a>Fout: ConnectionFailedUserSIDInformationMismatch
**Wordt** De SID van het Azure Active Directory (AD)-token van de gebruiker komt niet overeen met de SID die door de domein controller wordt geretourneerd bij het inschakelen van de gebruiker voor externe aanmelding. Deze fout treedt doorgaans op wanneer u zich probeert aan te melden bij een Azure Active Directory Domain Services-omgeving (Azure AD DS) met een gebruiker die oorspronkelijk is gebrond van een Windows Server AD.

**Fix:** Dit scenario wordt op dit moment niet ondersteund. Alleen gebruikers die zijn afgeleid van Azure Active Directory kunnen zich aanmelden bij virtuele Windows-bureau blad-Vm's die zijn verbonden met Azure AD DS.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Gebruiker maakt verbinding, maar er wordt niets weer gegeven (geen feed)

Een gebruiker kan Extern bureaublad-clients starten en kan worden geverifieerd, maar de gebruiker ziet geen pictogrammen in de feed voor webdetectie.

Controleer of de gebruiker die de problemen rapporteert, is toegewezen aan toepassings groepen met behulp van deze opdracht regel:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Controleer of de gebruiker zich aanmeldt met de juiste referenties.

Als de WebClient wordt gebruikt, controleert u of er geen problemen met de referenties in de cache zijn.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [zelf studie voor het oplossen van problemen met de zelf studie: Problemen oplossen met implementaties](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)van Resource Manager-sjablonen.
