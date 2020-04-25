---
title: Self-service voor wachtwoord herstel-Azure Active Directory
description: Meer informatie over de verschillende opties voor de Azure Active Directory selfservice voor wachtwoord herstel
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
ms.openlocfilehash: e8b6d08dd2073de80ac0f7fd08f510d9cda80545
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143241"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Self-service voor wachtwoord herstel beleid en beperkingen in Azure Active Directory

In dit artikel worden de wachtwoord beleidsregels en complexiteits vereisten beschreven die zijn gekoppeld aan gebruikers accounts in uw Azure Active Directory-Tenant (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Administrator reset policy differences (Verschillen herstelbeleid beheerder)

**Micro soft dwingt een sterk standaard beleid voor wachtwoord herstel voor *twee poorten* af voor elke rol van Azure-beheerder**. Dit beleid kan afwijken van de instelling die u voor uw gebruikers hebt gedefinieerd en dit beleid kan niet worden gewijzigd. U moet de functionaliteit voor het opnieuw instellen van wacht woorden altijd testen als een gebruiker zonder dat er Azure-beheerders rollen zijn toegewezen.

Beheerders kunnen met een twee poort beleid **geen beveiligings vragen gebruiken**.

Voor het twee-poort beleid zijn twee soorten verificatie gegevens vereist, zoals een *e-mail adres*, een *verificator-app*of een *telefoon nummer*. Een beleid met twee poorts is van toepassing op de volgende omstandigheden:

* Alle volgende Azure-beheerders rollen worden beïnvloed:
  * Helpdesk beheerder
  * Serviceondersteuningsbeheerder
  * Factureringsbeheerder
  * Ondersteuning voor partner Tier1
  * Ondersteuning voor partner Tier2
  * Exchange-beheerder
  * Skype voor Bedrijven-beheerder
  * Gebruikers beheerder
  * Schrijvers van mappen
  * Globale beheerder of bedrijfs beheerder
  * SharePoint-beheerder
  * Beheerder voor naleving
  * Toepassings beheerder
  * Beveiligingsbeheerder
  * Beheerder van geprivilegieerde rol
  * Intune-beheerder
  * Service beheerder van de toepassings proxy
  * Dynamics 365-beheerder
  * Power BI-servicebeheerder
  * Verificatie beheerder
  * Beheerder voor geprivilegieerde authenticatie

* Als er 30 dagen zijn verstreken in een proef abonnement; of
* Er is een aangepast domein geconfigureerd voor uw Azure AD-Tenant, zoals *contoso.com*. of
* De identiteiten van de on-premises Directory Azure AD Connect worden gesynchroniseerd

### <a name="exceptions"></a>Uitzonderingen

Voor een one-Gate-beleid is één verificatie gegevens vereist, zoals een e-mail adres of telefoon nummer. Een beleid met één poort is van toepassing op de volgende omstandigheden:

* Het is binnen de eerste 30 dagen van een proef abonnement. of
* Er is geen aangepast domein geconfigureerd voor uw Azure AD-Tenant. Daarom wordt de standaard waarde **. onmicrosoft.com*gebruikt. Het standaard domein **. onmicrosoft.com* wordt niet aanbevolen voor productie gebruik. maar
* De identiteiten van Azure AD Connect zijn niet gesynchroniseerd

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-beleids regels die van toepassing zijn op alle gebruikers accounts

Elk gebruikers account dat moet worden aangemeld bij Azure AD, moet een unieke kenmerk waarde van het user principal name (UPN) hebben die aan hun account is gekoppeld. De volgende tabel bevat een overzicht van de beleids regels die van toepassing zijn op on-premises Active Directory Domain Services gebruikers accounts die zijn gesynchroniseerd met de Cloud en naar gebruikers accounts met alleen Clouds:

| Eigenschap | UserPrincipalName-vereisten |
| --- | --- |
| Toegestane tekens |<ul> <li>A – Z</li> <li>a-z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tekens niet toegestaan |<ul> <li>\@ \" Een wille keurig teken dat de gebruikers naam niet van het domein scheidt.</li> <li>Kan geen punt teken bevatten: "." direct voor het\@ \" symbool "</li></ul> |
| Lengte beperkingen |<ul> <li>De totale lengte mag niet langer zijn dan 113 tekens</li><li>Er mogen Maxi maal 64 tekens zijn vóór het\@ \" symbool</li><li>Er mogen Maxi maal 48 tekens na het\@ \" symbool</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Password policies that only apply to cloud user accounts (Wachtwoordbeleid dat alleen van toepassing is gebruikersaccounts in de cloud)

In de volgende tabel worden de wachtwoord beleids instellingen beschreven die worden toegepast op gebruikers accounts die zijn gemaakt en beheerd in azure AD:

| Eigenschap | Vereisten |
| --- | --- |
| Toegestane tekens |<ul><li>A – Z</li><li>a-z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \: ',. ? / \`~ " ( ) ;</li> <li>lege ruimte</li></ul> |
| Tekens niet toegestaan | Unicode-tekens. |
| Wachtwoord beperkingen |<ul><li>Mini maal acht tekens en Maxi maal 256 tekens.</li><li>Vereist drie van de volgende vier:<ul><li>Kleine letters.</li><li>Hoofd letters.</li><li>Getallen (0-9).</li><li>Symbolen (Zie de vorige wachtwoord beperkingen).</li></ul></li></ul> |
| Geldigheids duur van wacht woord (maximale wachtwoord duur) |<ul><li>Standaard waarde: **90** dagen.</li><li>De waarde kan worden geconfigureerd met behulp `Set-MsolPasswordPolicy` van de-cmdlet uit de Azure Active Directory-module voor Windows Power shell.</li></ul> |
| Melding over het verlopen van het wacht woord (wanneer gebruikers op de hoogte worden gesteld van het verlopen van wacht woorden) |<ul><li>Standaard waarde: **14** dagen (voordat wacht woord verloopt).</li><li>De waarde kan worden geconfigureerd met behulp `Set-MsolPasswordPolicy` van de-cmdlet.</li></ul> |
| Wacht woord verlopen (wacht woorden nooit verlopen) |<ul><li>Standaard waarde: **False** (geeft aan dat het wacht woord een verval datum heeft).</li><li>De waarde kan worden geconfigureerd voor afzonderlijke gebruikers accounts met behulp `Set-MsolUser` van de-cmdlet.</li></ul> |
| Geschiedenis van wachtwoord wijzigingen | Het laatste wacht woord *kan niet* opnieuw worden gebruikt wanneer de gebruiker een wacht woord wijzigt. |
| Geschiedenis van wacht woord opnieuw instellen | Het laatste wacht woord *kan* opnieuw worden gebruikt wanneer de gebruiker een verg eten wacht woord opnieuw instelt. |
| Account vergrendeling | Na 10 mislukte aanmeldings pogingen met het verkeerde wacht woord, wordt de gebruiker één minuut geblokkeerd. Bij verdere onjuiste aanmeldings pogingen wordt de gebruiker vergrendeld om de duur van de tijd te verg Roten. [Slimme vergren deling](howto-password-smart-lockout.md) houdt de laatste drie ongeldige hashes met een onjuist wacht woord bij om te voor komen dat de vergrendelings teller voor hetzelfde wacht woord wordt verhoogd. Als iemand hetzelfde onjuiste wacht woord meermaals invoert, wordt het account niet vergrendeld. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Beleids regels voor wachtwoord verloop instellen in azure AD

Een *globale beheerder* of *gebruikers beheerder* voor een micro soft-cloud service kan de *Microsoft Azure ad-module voor Windows PowerShell* gebruiken om gebruikers wachtwoorden in te stellen die niet verlopen. U kunt ook Windows Power shell-cmdlets gebruiken om de configuratie nooit-expires te verwijderen of om te zien welke gebruikers wachtwoorden zijn ingesteld op nooit verlopen.

Deze richt lijnen zijn van toepassing op andere providers, zoals intune en Office 365, die ook afhankelijk zijn van Azure AD voor identiteits-en Directory Services. Verlopen van wacht woorden is het enige deel van het beleid dat kan worden gewijzigd.

> [!NOTE]
> Alleen wacht woorden voor gebruikers accounts die niet zijn gesynchroniseerd via adreslijst synchronisatie, kunnen zodanig worden geconfigureerd dat ze niet verlopen. Zie [ad verbinden met Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)voor meer informatie over Directory synchronisatie.

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Set or check the password policies by using PowerShell (Wachtwoordbeleid instellen of controleren met behulp van PowerShell)

Als u aan de slag wilt gaan, [downloadt en installeert u de Azure AD Power shell-module](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0) en [verbindt u deze met uw Azure AD-Tenant](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples). Nadat de module is geïnstalleerd, gebruikt u de volgende stappen om elk veld te configureren.

### <a name="check-the-expiration-policy-for-a-password"></a>Het verloop beleid voor een wacht woord controleren

1. Maak verbinding met Windows Power shell met behulp van de referenties van uw gebruikers beheerder of de beheerder van uw bedrijf.
1. Voer een van de volgende opdrachten uit:

   * Als u wilt weten of het wacht woord van een enkele gebruiker is ingesteld op nooit verlopen, voert u de volgende cmdlet uit met behulp van de UPN (bijvoorbeeld *aprilr\@contoso.onmicrosoft.com*) of de gebruikers-id van de gebruiker die u wilt controleren:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Als u de instelling **wacht woord verloopt nooit** wilt zien voor alle gebruikers, voert u de volgende cmdlet uit:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Wacht woord instellen op verlopen

1. Maak verbinding met Windows Power shell met behulp van de referenties van uw gebruikers beheerder of de beheerder van uw bedrijf.
1. Voer een van de volgende opdrachten uit:

   * Als u het wacht woord van een gebruiker wilt instellen, zodat het wacht woord is verlopen, voert u de volgende cmdlet uit met behulp van de UPN of de gebruikers-ID van de gebruiker:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Als u de wacht woorden van alle gebruikers in de organisatie wilt instellen zodat deze verlopen, gebruikt u de volgende cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Stel een wacht woord in om nooit te verlopen

1. Maak verbinding met Windows Power shell met behulp van de referenties van uw gebruikers beheerder of de beheerder van uw bedrijf.
1. Voer een van de volgende opdrachten uit:

   * Als u het wacht woord van een gebruiker wilt instellen op nooit verlopen, voert u de volgende cmdlet uit met behulp van de UPN of de gebruikers-ID van de gebruiker:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Voer de volgende cmdlet uit om de wacht woorden in te stellen van alle gebruikers in een organisatie die nooit verlopen.

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Wacht woorden zijn `-PasswordPolicies DisablePasswordExpiration` ingesteld op nog steeds leeftijd `pwdLastSet` op basis van het kenmerk. Als u op `pwdLastSet` basis van het kenmerk de verval datum wijzigt `-PasswordPolicies None`in, moeten alle wacht woorden `pwdLastSet` die een ouder zijn dan 90 dagen, worden gewijzigd wanneer de gebruiker zich de volgende keer aanmeldt. Deze wijziging kan van invloed zijn op een groot aantal gebruikers.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: gebruikers in staat stellen hun account te ontgrendelen of wacht woorden opnieuw in te stellen met Azure Active Directory self-service voor wachtwoord herstel](tutorial-enable-sspr.md)om aan de slag te gaan met SSPR.

Zie problemen met het [opnieuw instellen van wacht woorden oplossen](active-directory-passwords-troubleshoot.md) als u of gebruikers problemen hebben met SSPR
