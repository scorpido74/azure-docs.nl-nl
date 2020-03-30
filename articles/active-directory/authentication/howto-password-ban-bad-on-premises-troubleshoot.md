---
title: On-premises Azure AD-wachtwoordbeveiliging oplossen
description: Meer informatie over het oplossen van problemen met Azure AD Password Protection voor een on-premises Active Directory Domain Services-omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263645"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Problemen oplossen: on-premises Azure AD-wachtwoordbeveiliging

Na de implementatie van Azure AD Password Protection is mogelijk een oplossing nodig. Dit artikel gaat in detail om u te helpen een aantal veelvoorkomende stappen voor het oplossen van problemen te begrijpen.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>De DC-agent kan geen proxy in de map vinden

Het belangrijkste symptoom van dit probleem is 30017 gebeurtenissen in de DC agent Admin gebeurtenis logboek.

De gebruikelijke oorzaak van dit probleem is dat een proxy nog niet is geregistreerd. Als een proxy is geregistreerd, kan er enige vertraging optreden als gevolg van ad-replicatielatentie totdat een bepaalde DC-agent die proxy kan zien.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>De DC-agent kan niet communiceren met een proxy

Het belangrijkste symptoom van dit probleem is 30018 gebeurtenissen in de DC agent Admin gebeurtenis logboek. Dit probleem kan verschillende mogelijke oorzaken hebben:

1. De DC-agent bevindt zich in een geïsoleerd gedeelte van het netwerk dat geen netwerkverbinding met de geregistreerde proxy(s) toestaat. Dit probleem kan goedaardig zijn zolang andere DC-agents kunnen communiceren met de proxy(s) om wachtwoordbeleidsregels van Azure te downloaden. Eenmaal gedownload, zal dit beleid vervolgens worden verkregen door de geïsoleerde DC via replicatie van de beleidsbestanden in de sysvol aandeel.

1. De proxyhostmachine blokkeert de toegang tot het RPC-eindpunt mapper-eindpunt (poort 135)

   Het installatieprogramma Azure AD Password Protection Proxy maakt automatisch een windows firewall-inkomende regel die toegang tot poort 135 mogelijk maakt. Als deze regel later wordt verwijderd of uitgeschakeld, kunnen DC-agents niet communiceren met de proxyservice. Als de ingebouwde Windows Firewall is uitgeschakeld in plaats van een ander firewallproduct, moet u die firewall configureren om toegang tot poort 135 toe te staan.

1. De proxyhostmachine blokkeert de toegang tot het RPC-eindpunt (dynamisch of statisch) dat wordt beluisterd door de proxyservice

   Het installatieprogramma Azure AD Password Protection Proxy maakt automatisch een windows firewall-inkomende regel waarmee toegang wordt verkrijg tot binnenkomende poorten die worden beluisterd door de Azure AD Password Protection Proxy-service. Als deze regel later wordt verwijderd of uitgeschakeld, kunnen DC-agents niet communiceren met de proxyservice. Als de ingebouwde Windows Firewall is uitgeschakeld in plaats van een ander firewallproduct, moet u die firewall zo configureren dat deze firewall toegang geeft tot binnenkomende poorten die worden beluisterd door de Azure AD Password Protection Proxy-service. Deze configuratie kan specifieker worden gemaakt als de proxyservice is geconfigureerd om te `Set-AzureADPasswordProtectionProxyConfiguration` luisteren op een specifieke statische RPC-poort (met behulp van de cmdlet).

1. De proxyhostmachine is niet geconfigureerd om domeincontrollers de mogelijkheid te bieden zich aan te melden bij de machine. Dit gedrag wordt beheerd via de toewijzing van gebruikersbevoegdheden 'Toegang tot deze computer vanaf het netwerk'. Alle domeincontrollers in alle domeinen in het forest moeten deze bevoegdheid krijgen. Deze instelling wordt vaak beperkt als onderdeel van een grotere netwerkverhardingsinspanning.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxyservice kan niet communiceren met Azure

1. Zorg ervoor dat de proxymachine verbinding heeft met de eindpunten die worden vermeld in de [implementatievereisten.](howto-password-ban-bad-on-premises-deploy.md)

1. Controleer of het forest en alle proxyservers zijn geregistreerd tegen dezelfde Azure-tenant.

   U deze vereiste `Get-AzureADPasswordProtectionProxy` controleren `Get-AzureADPasswordProtectionDCAgent` door de cmdlets `AzureTenant` en PowerShell uit te voeren en vervolgens de eigenschap van elk geretourneerd item te vergelijken. Voor de juiste werking moet de gerapporteerde tenantnaam hetzelfde zijn voor alle DC-agents en proxyservers.

   Als er een mismatch voorwaarde voor Azure-tenantregistratie bestaat, kan dit probleem worden opgelost door de `Register-AzureADPasswordProtectionProxy` cmdlets en/of `Register-AzureADPasswordProtectionForest` PowerShell naar behoefte uit te voeren, zodat u referenties van dezelfde Azure-tenant voor alle registraties gebruikt.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC-agent kan wachtwoordbeleidsbestanden niet versleutelen of decoderen

Azure AD Password Protection is van cruciaal belang voor de versleutelings- en decryptiefunctionaliteit die wordt geleverd door de Microsoft Key Distribution Service. Encryptie of decryptie fouten kunnen manifesteren met een verscheidenheid van symptomen en hebben verschillende mogelijke oorzaken.

1. Controleer of de KDS-service is ingeschakeld en functioneel is op alle domeincontrollers van Windows Server 2012 en hoger in een domein.

   Standaard is de servicestartmodus van de KDS-service geconfigureerd als Handmatig (Trigger Start). Deze configuratie betekent dat de eerste keer dat een client de service probeert te gebruiken, deze on-demand wordt gestart. Deze standaardmodus voor het starten van de service is acceptabel als Azure AD Password Protection werkt.

   Als de startmodus van de KDS-service is geconfigureerd voor Uitgeschakeld, moet deze configuratie worden opgelost voordat Azure AD-wachtwoordbeveiliging naar behoren werkt.

   Een eenvoudige test voor dit probleem is om de KDS-service handmatig te starten, hetzij via de MMC-console servicebeheer, hetzij met behulp van andere beheertools (voer bijvoorbeeld "net start kdssvc" uit vanaf een opdrachtpromptconsole). De KDS-service zal naar verwachting succesvol starten en blijven draaien.

   De meest voorkomende oorzaak dat de KDS-service niet kan worden gestart, is dat het object Active Directory-domeincontroller zich buiten de standaard-domeincontrollers-organisatiebevindt. Deze configuratie wordt niet ondersteund door de KDS-service en is geen beperking opgelegd door Azure AD Password Protection. De oplossing voor deze voorwaarde is om het domeincontrollerobject te verplaatsen naar een locatie onder de standaard-domeincontrollers-organisatie.

1. Incompatibele kDS-versleutelde bufferindelingwijziging van Windows Server 2012 R2 naar Windows Server 2016

   Er is een KDS-beveiligingsfix geïntroduceerd in Windows Server 2016 die het formaat van kds-versleutelde buffers wijzigt; deze buffers worden soms niet gedecodeerd op Windows Server 2012 en Windows Server 2012 R2. De omgekeerde richting is in orde - buffers die kds-versleuteld zijn op Windows Server 2012 en Windows Server 2012 R2 zullen altijd met succes decoderen op Windows Server 2016 en hoger. Als de domeincontrollers in uw Active Directory-domeinen een mix van deze besturingssystemen uitvoeren, kunnen incidentele decryptiefouten voor Azure AD Password Protection worden gerapporteerd. Het is niet mogelijk om de timing of symptomen van deze fouten nauwkeurig te voorspellen, gezien de aard van de beveiligingsoplossing, en gezien het feit dat het niet-deterministisch is welke Azure AD Password Protection DC-agent waarop de domeincontroller gegevens op een bepaald moment zal versleutelen.

   Microsoft onderzoekt een oplossing voor dit probleem, maar er is nog geen ETA beschikbaar. In de tussentijd is er geen tijdelijke oplossing voor dit probleem, behalve om geen mix van deze onverenigbare besturingssystemen uit te voeren in uw Active Directory-domein(en). Met andere woorden, u moet alleen Windows Server 2012- en Windows Server 2012 R2-domeincontrollers uitvoeren, OF u moet alleen Windows Server 2016 en hoger-domeincontrollers uitvoeren.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Zwakke wachtwoorden worden geaccepteerd, maar mogen niet

Dit probleem kan verschillende oorzaken hebben.

1. Uw DC-agent(s) draaien een openbare preview-softwareversie die is verlopen. Zie [Public preview DC-agentsoftware is verlopen](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Uw DC-agent(s) kan geen beleid downloaden of kan het bestaande beleid niet decoderen. Controleer op mogelijke oorzaken in de bovenstaande onderwerpen.

1. De modus Afdwingen van het wachtwoordbeleid is nog steeds ingesteld op Controleren. Als deze configuratie van kracht is, configureert u deze opnieuw naar Afdwingen met de Azure AD Password Protection-portal. Zie [Werkwijzen voor](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)meer informatie .

1. Het wachtwoordbeleid is uitgeschakeld. Als deze configuratie van kracht is, configureert u deze opnieuw om deze in te schakelen met de Azure AD Password Protection-portal. Zie [Werkwijzen voor](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)meer informatie .

1. U hebt de DC-agentsoftware niet op alle domeincontrollers in het domein geïnstalleerd. In deze situatie is het moeilijk om ervoor te zorgen dat externe Windows-clients zich richten op een bepaalde domeincontroller tijdens een wachtwoordwijzigingsbewerking. Als u denkt dat u een bepaalde DC hebt getarget waar de DC-agentsoftware is geïnstalleerd, u dit verifiëren door het gebeurtenislogboek van de DC-agent-beheerder te controleren: ongeacht de uitkomst is er ten minste één gebeurtenis om de uitkomst van het wachtwoord te documenteren Validatie. Als er geen gebeurtenis aanwezig is voor de gebruiker wiens wachtwoord is gewijzigd, is de wachtwoordwijziging waarschijnlijk verwerkt door een andere domeincontroller.

   Als alternatieve test probeert u wachtwoorden in te stellen terwijl u rechtstreeks bent ingelogd bij een DC waar de DC-agentsoftware is geïnstalleerd. Deze techniek wordt niet aanbevolen voor het produceren van Active Directory-domeinen.

   Hoewel incrementele implementatie van de DC-agentsoftware wordt ondersteund onder voorbehoud van deze beperkingen, raadt Microsoft ten zeerste aan dat de DC-agentsoftware zo snel mogelijk op alle domeincontrollers in een domein wordt geïnstalleerd.

1. Het wachtwoordvalidatiealgoritme werkt mogelijk zoals verwacht. Zie [Hoe worden wachtwoorden geëvalueerd](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe slaagt er niet in om een zwak DSRM-wachtwoord in te stellen

Active Directory valideert altijd een nieuw wachtwoord voor de reparatiemodus van Directory Services om ervoor te zorgen dat het voldoet aan de vereisten voor de complexiteit van het domein; deze validatie wordt ook aangesproken op wachtwoordfilters, zoals Azure AD Password Protection. Als het nieuwe DSRM-wachtwoord wordt geweigerd, wordt het volgende foutbericht weergegeven:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Wanneer Azure AD Password Protection de gebeurtenisgebeurtenis(s) voor wachtwoordvalidatie registreert voor een Active Directory DSRM-wachtwoord, wordt verwacht dat de gebeurtenislogboekberichten geen gebruikersnaam bevatten. Dit gedrag treedt op omdat het DSRM-account een lokaal account is dat geen deel uitmaakt van het werkelijke Active Directory-domein.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Replicapromotie voor domeincontroller mislukt vanwege een zwak DSRM-wachtwoord

Tijdens het DC-promotieproces wordt het nieuwe wachtwoord voor de reparatiemodus van Directory Services voor validatie naar een bestaand DC in het domein verzonden. Als het nieuwe DSRM-wachtwoord wordt geweigerd, wordt het volgende foutbericht weergegeven:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Net als in het bovenstaande probleem heeft elke gebeurtenis voor wachtwoordvalidatie van Azure AD Password Protection lege gebruikersnamen voor dit scenario.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Dedegradatie van de domeincontroller mislukt vanwege een zwak lokaal beheerderswachtwoord

Het wordt ondersteund om een domeincontroller te degraderen die nog steeds de DC-agentsoftware uitvoert. Beheerders moeten zich er echter van bewust zijn dat de DC-agentsoftware het huidige wachtwoordbeleid blijft afdwingen tijdens de degradatieprocedure. Het nieuwe wachtwoord voor lokale administratoraccount (opgegeven als onderdeel van de degradatiebewerking) wordt gevalideerd als elk ander wachtwoord. Microsoft raadt aan om veilige wachtwoorden te kiezen voor lokale Administrator-accounts als onderdeel van een DC-degradatieprocedure.

Zodra de degradatie is geslaagd en de domeincontroller opnieuw is opgestart en opnieuw wordt uitgevoerd als een normale lidserver, keert de DC-agentsoftware terug naar het draaien in een passieve modus. Het kan dan worden verwijderd op elk gewenst moment.

## <a name="booting-into-directory-services-repair-mode"></a>Opstarten in de reparatiemodus directoryservices

Als de domeincontroller is opgestart in de reparatiemodus van Directory Services, detecteert het wachtwoordfilter dll van de DC-agent deze voorwaarde en zorgt ervoor dat alle wachtwoordvalidatie- of handhavingsactiviteiten worden uitgeschakeld, ongeacht het momenteel actieve beleid Configuratie. Het wachtwoordfilter dll van de DC-agent registreert een 10023-waarschuwingsgebeurtenis bij het gebeurtenislogboek Admin, bijvoorbeeld:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Public preview DC agent software is verlopen

Tijdens de openbare previewperiode voor Azure AD Password Protection was de DC-agentsoftware hardgecodeerd om te stoppen met het verwerken van wachtwoordvalidatieaanvragen op de volgende datums:

* Versie 1.2.65.0 stopt per 1 september 2019 met het verwerken van wachtwoordvalidatieaanvragen.
* Versie 1.2.25.0 en eerdere wijzigingen zijn op 1 juli 2019 gestopt met het verwerken van wachtwoordvalidatieaanvragen.

Naarmate de deadline nadert, zullen alle dc-agentversies met beperkte tijd een 10021-gebeurtenis uitzenden in het gebeurtenislogboek van de DC-agent Admin op de opstarttijd die er als volgt uitziet:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Zodra de deadline is verstreken, zullen alle dc-agentversies met beperkte tijd een 10022-gebeurtenis uitzenden in het gebeurtenislogboek van de DC-agent Admin op de opstarttijd die er als volgt uitziet:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Aangezien de deadline alleen is gecontroleerd bij het opstarten, ziet u deze gebeurtenissen mogelijk pas lang nadat de deadline van de agenda is verstreken. Zodra de deadline is herkend, zullen er geen negatieve effecten optreden op de domeincontroller of de grotere omgeving, anders dan alle wachtwoorden, worden automatisch goedgekeurd.

> [!IMPORTANT]
> Microsoft raadt aan om verlopen openbare preview DC-agents onmiddellijk te upgraden naar de nieuwste versie.

Een eenvoudige manier om DC-agents in uw omgeving te `Get-AzureADPasswordProtectionDCAgent` ontdekken die moeten worden geürist, is door de cmdlet uit te voeren, bijvoorbeeld:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Voor dit onderwerp is het veld SoftwareVersion natuurlijk de belangrijkste eigenschap om naar te kijken. U powershell-filtering ook gebruiken om DC-agents die zich al op of boven de vereiste basislijnversie bevinden, uit te filteren, bijvoorbeeld:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

De Azure AD Password Protection Proxy-software is in geen enkele versie beperkt in de tijd. Microsoft raadt nog steeds aan om zowel DC- als proxyagents te upgraden naar de nieuwste versies wanneer ze worden uitgebracht. De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt om proxy-agents te vinden die upgrades vereisen, vergelijkbaar met het bovenstaande voorbeeld voor DC-agents.

Raadpleeg [het upgraden van de DC-agent](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) en [het upgraden van de proxyservice](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) voor meer informatie over specifieke upgradeprocedures.

## <a name="emergency-remediation"></a>Noodsanering

Als zich een situatie voordoet waarin de DC-agentservice problemen veroorzaakt, kan de DC-agentservice onmiddellijk worden afgesloten. Het wachtwoordfilter van de DC-agent probeert nog steeds de niet-lopende service te bellen en logt waarschuwingsgebeurtenissen (10012, 10013), maar alle binnenkomende wachtwoorden worden in die tijd geaccepteerd. De DC-agentservice kan dan ook worden geconfigureerd via Windows Service Control Manager met een opstarttype 'Uitgeschakeld' indien nodig.

Een andere herstelmaatregel zou zijn om de modus Inschakelen in te stellen op Nee in de Azure AD Password Protection-portal. Zodra het bijgewerkte beleid is gedownload, zal elke DC-agent service gaan in een rustige modus waar alle wachtwoorden worden geaccepteerd as-is. Zie [Werkwijzen voor](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)meer informatie .

## <a name="removal"></a>Verwijdering

Als wordt besloten de Azure AD-wachtwoordbeveiligingssoftware te verwijderen en alle gerelateerde statussen uit het domein(en) en forest op te ruimen, kan deze taak worden uitgevoerd met de volgende stappen:

> [!IMPORTANT]
> Het is belangrijk om deze stappen in orde uit te voeren. Als een instantie van de proxyservice wordt uitgevoerd, wordt het object serviceConnectionPoint periodiek opnieuw gemaakt. Als een instantie van de DC-agentservice wordt uitgevoerd, wordt het object serviceConnectionPoint en de sysvol-status periodiek opnieuw gemaakt.

1. Verwijder de Proxy-software van alle machines. Deze stap vereist **geen** herstart.
2. Verwijder de DC Agent-software van alle domeincontrollers. Deze stap **vereist** een herstart.
3. Verwijder alle verbindingspunten van de proxyservice handmatig in elke domeinnaamgevingscontext. De locatie van deze objecten kan worden gedetecteerd met de volgende opdracht Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Laat het sterretje ("*") niet weg aan het einde van de $keywords variabele waarde.

   Het resulterende object(s) `Get-ADObject` dat via de opdracht `Remove-ADObject`wordt gevonden, kan vervolgens handmatig worden doorgeleid of verwijderd.

4. Verwijder alle dc-agentverbindingspunten in elke domeinnaamgevingscontext handmatig. Er kan een van deze objecten per domein controller in het bos, afhankelijk van hoe ver de software werd ingezet. De locatie van dat object kan worden gedetecteerd met de volgende opdracht Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Het resulterende object(s) `Get-ADObject` dat via de opdracht `Remove-ADObject`wordt gevonden, kan vervolgens handmatig worden doorgeleid of verwijderd.

   Laat het sterretje ("*") niet weg aan het einde van de $keywords variabele waarde.

5. Verwijder handmatig de configuratiestatus op forestniveau. De forestconfiguratiestatus wordt gehandhaafd in een container in de context van active directory-configuratienaamgeving. Het kan als volgt worden ontdekt en verwijderd:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Verwijder alle sysvolgerelateerde status handmatig door de volgende map en alle inhoud ervan handmatig te verwijderen:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Indien nodig kan dit pad ook lokaal worden geopend op een bepaalde domeincontroller; de standaardlocatie zou zoiets zijn als het volgende pad:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Dit pad is anders als het sysvol-aandeel is geconfigureerd op een niet-standaardlocatie.

## <a name="next-steps"></a>Volgende stappen

[Veelgestelde vragen voor Azure AD Password Protection](howto-password-ban-bad-on-premises-faq.md)

Voor meer informatie over de wereldwijde en aangepaste verboden wachtwoord lijsten, zie het artikel [Ban slechte wachtwoorden](concept-password-ban-bad.md)
