---
title: Beleid voor het opnieuw instellen van selfservicewachtwoorden - Azure Active Directory
description: Meer informatie over de verschillende beleidsopties voor het opnieuw instellen van azure Active Directory-zelfservicewachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f5987bee82dc22d3742cb5d87040930e5d2c52d
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393037"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Beleid en beperkingen voor het opnieuw instellen van selfservicewachtwoorden in Azure Active Directory

In dit artikel worden de wachtwoordbeleidsregels en complexiteitsvereisten beschreven die zijn gekoppeld aan gebruikersaccounts in uw Azure AD-tenant (Azure Directory).

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Verschillen herstelbeleid beheerder)

**Microsoft handhaaft een sterk standaard wachtwoordresetbeleid met twee poortjes voor elke *Azure-beheerdersrol.* ** Dit beleid kan afwijken van het beleid dat u voor uw gebruikers hebt gedefinieerd en dit beleid kan niet worden gewijzigd. U moet de functionaliteit voor het opnieuw instellen van wachtwoorden als gebruiker altijd testen zonder dat azure-beheerdersrollen zijn toegewezen.

Met een twee-poort-beleid **hebben beheerders niet de mogelijkheid om beveiligingsvragen te gebruiken.**

Het twee-poort-beleid vereist twee verificatiegegevens, zoals een *e-mailadres,* *authenticator-app*of een *telefoonnummer*. In de volgende omstandigheden geldt een twee-poorts beleid:

* Alle volgende Azure-beheerdersrollen worden beïnvloed:
  * Helpdeskbeheerder
  * Serviceondersteuningsbeheerder
  * Factureringsbeheerder
  * Ondersteuning voor Partner Tier1
  * Ondersteuning voor Partner Tier2
  * Exchange-beheerder
  * Skype voor Bedrijven-beheerder
  * Gebruikersbeheerder
  * Directory schrijvers
  * Globale beheerder of bedrijfsbeheerder
  * SharePoint-beheerder
  * Compliancebeheerder
  * Toepassingsbeheerder
  * Beveiligingsbeheerder
  * Bevoegde rolbeheerder
  * Intune-beheerder
  * Beheerder van de proxyservice van toepassingen
  * Dynamics 365-beheerder
  * Power BI-servicebeheerder
  * Verificatiebeheerder
  * Beheerder van geprivilegieerde verificatie

* Als er 30 dagen zijn verstreken in een proefabonnement; Of
* Er is een aangepast domein geconfigureerd voor uw Azure AD-tenant, zoals *contoso.com*; Of
* Azure AD Connect synchroniseert identiteiten van uw on-premises directory

### <a name="exceptions"></a>Uitzonderingen

Voor een one-gate-beleid is één stuk verificatiegegevens vereist, zoals een e-mailadres of telefoonnummer. In de volgende omstandigheden geldt een one-gate beleid:

* Het is binnen de eerste 30 dagen van een proefabonnement; Of
* Een aangepast domein is niet geconfigureerd voor uw Azure AD-tenant, dus het gebruik van de standaard **.onmicrosoft.com*. Het standaard **.onmicrosoft.com* domein wordt niet aanbevolen voor productiegebruik; En
* Azure AD Connect synchroniseert geen identiteiten

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-beleid dat van toepassing is op alle gebruikersaccounts

Elk gebruikersaccount dat zich moet aanmelden bij Azure AD moet een unieke UPN-kenmerkwaarde (user principal name) hebben die aan hun account is gekoppeld. In de volgende tabel worden de beleidsregels beschreven die van toepassing zijn op zowel on-premises gebruikersaccounts van Active Directory Domain Services die zijn gesynchroniseerd met de cloud als op gebruikersaccounts die alleen in de cloud worden gesynchroniseerd:

| Eigenschap | Gebruikersprincipalname-vereisten |
| --- | --- |
| Tekens toegestaan |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tekens niet toegestaan |<ul> <li>Elk\@ \" " teken dat de gebruikersnaam niet scheidt van het domein.</li> <li>Kan geen puntteken bevatten "." onmiddellijk\@ \" voorafgaand aan het symbool "</li></ul> |
| Lengtebeperkingen |<ul> <li>De totale lengte mag niet meer dan 113 tekens bedragen</li><li>Er kunnen maximaal 64 tekens\@ \" zijn voor het " symbool</li><li>Er kunnen maximaal 48 tekens\@ \" na het " symbool</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Password policies that only apply to cloud user accounts (Wachtwoordbeleid dat alleen van toepassing is gebruikersaccounts in de cloud)

In de volgende tabel worden de wachtwoordbeleidsinstellingen beschreven die zijn toegepast op gebruikersaccounts die zijn gemaakt en beheerd in Azure AD:

| Eigenschap | Vereisten |
| --- | --- |
| Tekens toegestaan |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ + = [ ] { } &#124; \ : , . ? / \`~ " ( ) ;</li> <li>Spatie</li></ul> |
| Tekens niet toegestaan | Unicode-tekens. |
| Wachtwoordbeperkingen |<ul><li>Minimaal 8 tekens en maximaal 256 tekens.</li><li>Vereist drie van de vier van de volgende:<ul><li>Kleine letters.</li><li>Hoofdletters.</li><li>Cijfers (0-9).</li><li>Symbolen (zie de vorige wachtwoordbeperkingen).</li></ul></li></ul> |
| Duur van het verlopen van wachtwoorden (Maximum wachtwoordleeftijd) |<ul><li>Standaardwaarde: **90** dagen.</li><li>De waarde is configureerbaar met behulp van de `Set-MsolPasswordPolicy` cmdlet van de Azure Active Directory Module voor Windows PowerShell.</li></ul> |
| Melding van het verlopen van wachtwoorden (wanneer gebruikers op de hoogte worden gesteld van het verlopen van het wachtwoord) |<ul><li>Standaardwaarde: **14** dagen (voordat het wachtwoord verloopt).</li><li>De waarde is configureerbaar met behulp van de `Set-MsolPasswordPolicy` cmdlet.</li></ul> |
| Wachtwoord verlopen (Wachtwoorden nooit laten verlopen) |<ul><li>Standaardwaarde: **false** (geeft aan dat het wachtwoord een vervaldatum heeft).</li><li>De waarde kan worden geconfigureerd voor afzonderlijke `Set-MsolUser` gebruikersaccounts met behulp van de cmdlet.</li></ul> |
| Geschiedenis van wachtwoordwijziging | Het laatste wachtwoord *kan niet* opnieuw worden gebruikt wanneer de gebruiker een wachtwoord wijzigt. |
| Geschiedenis voor het opnieuw instellen van wachtwoorden | Het laatste wachtwoord *kan* opnieuw worden gebruikt wanneer de gebruiker een vergeten wachtwoord opnieuw instelt. |
| Accountuitsluiting | Na 10 mislukte aanmeldingspogingen met het verkeerde wachtwoord wordt de gebruiker gedurende één minuut buitengesloten. Verdere onjuiste aanmeldingspogingen sluiten de gebruiker af voor langere tijd. [Smart lockout](howto-password-smart-lockout.md) tracks de laatste drie slechte wachtwoord hashes om te voorkomen dat de verhoging van de lockout teller voor hetzelfde wachtwoord. Als iemand meerdere keren hetzelfde slechte wachtwoord invoert, zorgt dit gedrag er niet voor dat het account wordt vergrendeld. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Beleid voor het verlopen van wachtwoorden instellen in Azure AD

Een *globale beheerder* of *gebruikersbeheerder* voor een Microsoft-cloudservice kan de *Microsoft Azure AD-module voor Windows PowerShell* gebruiken om gebruikerswachtwoorden in te stellen die niet verlopen. U ook Windows PowerShell-cmdlets gebruiken om de nooit verlopen configuratie te verwijderen of om te zien welke gebruikerswachtwoorden nooit verlopen.

Deze richtlijnen zijn van toepassing op andere providers, zoals Intune en Office 365, die ook afhankelijk zijn van Azure AD voor identiteits- en directoryservices. Het verlopen van wachtwoorden is het enige onderdeel van het beleid dat kan worden gewijzigd.

> [!NOTE]
> Alleen wachtwoorden voor gebruikersaccounts die niet zijn gesynchroniseerd via adreslijstsynchronisatie, kunnen worden geconfigureerd om niet te verlopen. Zie AD verbinden met Azure [AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)voor meer informatie over adreslijstsynchronisatie.

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Wachtwoordbeleid instellen of controleren met behulp van PowerShell)

Download en [installeer de Azure AD PowerShell-module](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)om aan de slag te gaan. Nadat de module is geïnstalleerd, gebruikt u de volgende stappen om elk veld te configureren.

### <a name="check-the-expiration-policy-for-a-password"></a>Het verloopbeleid controleren op een wachtwoord

1. Maak verbinding met Windows PowerShell met behulp van de referenties van uw gebruikersbeheerder of bedrijfsbeheerder.
1. Voer een van de volgende opdrachten uit:

   * Als u wilt zien of het wachtwoord van één gebruiker nooit verloopt, voert u de volgende cmdlet uit met behulp van de UPN (bijvoorbeeld *\@aprilr contoso.onmicrosoft.com)* of de gebruikersnaam van de gebruiker die u wilt controleren:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Voer de volgende cmdlet uit om de instelling Wachtwoord nooit voor alle gebruikers **te bekijken:**

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Een wachtwoord instellen om te verlopen

1. Maak verbinding met Windows PowerShell met behulp van de referenties van uw gebruikersbeheerder of bedrijfsbeheerder.
1. Voer een van de volgende opdrachten uit:

   * Als u het wachtwoord van één gebruiker wilt instellen zodat het wachtwoord verloopt, voert u de volgende cmdlet uit met de UPN of de gebruikers-id van de gebruiker:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Als u de wachtwoorden van alle gebruikers in de organisatie zo wilt instellen dat ze verlopen, gebruikt u de volgende cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Een wachtwoord instellen dat nooit verloopt

1. Maak verbinding met Windows PowerShell met behulp van de referenties van uw gebruikersbeheerder of bedrijfsbeheerder.
1. Voer een van de volgende opdrachten uit:

   * Als u wilt instellen dat het wachtwoord van één gebruiker nooit verloopt, voert u de volgende cmdlet uit met de UPN of de gebruikers-id van de gebruiker:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Voer de volgende cmdlet uit om de wachtwoorden van alle gebruikers in een organisatie nooit te laten verlopen:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Wachtwoorden ingesteld `-PasswordPolicies DisablePasswordExpiration` op leeftijd `pwdLastSet` op basis van het kenmerk. Op basis `pwdLastSet` van het kenmerk moeten `-PasswordPolicies None`alle wachtwoorden die `pwdLastSet` ouder zijn dan 90 dagen, als u de vervaldatum wijzigt, deze de volgende keer dat hij zich aanmeldt, wijzigen. Deze wijziging kan gevolgen hebben voor een groot aantal gebruikers.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: Gebruikers in staat stellen hun account te ontgrendelen of wachtwoorden opnieuw in te stellen met azure Active Directory selfservicewachtwoord opnieuw instellen.](tutorial-enable-sspr.md)

Als u of gebruikers problemen hebben met SSPR, [raadpleegt u Het oplossen van selfservicewachtwoordreset](active-directory-passwords-troubleshoot.md)
