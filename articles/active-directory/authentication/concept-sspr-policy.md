---
title: Self-service voor wachtwoord herstel-Azure Active Directory
description: Opties configureren voor het beleid voor wachtwoord herstel van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd6cacae9c7af705b0de7b59e0f25f25637a5a89
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962489"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Wachtwoord beleid en-beperkingen in Azure Active Directory

In dit artikel worden de wachtwoord beleidsregels en complexiteits vereisten beschreven die zijn gekoppeld aan gebruikers accounts in uw Azure Active Directory-Tenant (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Beleids verschillen beheerder opnieuw instellen

**Micro soft dwingt een sterk standaard beleid voor het opnieuw instellen van *twee poorten* af voor elke rol van Azure-beheerder** dit beleid kan afwijken van de versie die u voor uw gebruikers hebt gedefinieerd en kan niet worden gewijzigd. U moet de functionaliteit voor het opnieuw instellen van wacht woorden altijd testen als een gebruiker zonder dat er Azure-beheerders rollen zijn toegewezen.

Beheerders kunnen met een twee poort beleid **geen beveiligings vragen gebruiken**.

Voor het twee-poort beleid zijn twee soorten verificatie gegevens vereist, zoals een **e-mail adres**, een **verificator-app**of een **telefoon nummer**. Een beleid met twee poorts is van toepassing op de volgende omstandigheden:

* Alle volgende Azure-beheerders rollen worden beïnvloed:
  * Helpdesk beheerder
  * Ondersteunings beheerder voor service
  * Factureringsbeheerder
  * Laag1-ondersteuning voor partner
  * Laag2-ondersteuning voor partner
  * Exchange-beheerder
  * Skype voor bedrijven-beheerder
  * Gebruikers beheerder
  * Schrijvers van mappen
  * Globale beheerder of bedrijfs beheerder
  * SharePoint-beheerder
  * Beheerder voor naleving
  * Toepassings beheerder
  * Beveiligingsbeheerder
  * Beheerder van geprivilegieerde rol
  * InTune-beheerder
  * Service beheerder van de toepassings proxy
  * Dynamics 365-beheerder
  * Power BI-service beheerder
  * Verificatie beheerder
  * Beheerder voor geprivilegieerde authenticatie

* Als er 30 dagen zijn verstreken in een proef abonnement; of
* Er is een aangepast domein geconfigureerd voor uw Azure AD-Tenant, zoals *contoso.com*. of
* De identiteiten van de on-premises Directory Azure AD Connect worden gesynchroniseerd

### <a name="exceptions"></a>Uitzonderingen

Voor een one-Gate-beleid is één verificatie gegevens vereist, zoals een e-mail adres *of* telefoon nummer. Een beleid met één poort is van toepassing op de volgende omstandigheden:

* Het is binnen de eerste 30 dagen van een proef abonnement. of
* Er is geen aangepast domein geconfigureerd voor uw Azure AD-Tenant. Daarom wordt de standaard waarde * *. onmicrosoft.com*gebruikt. Houd er rekening mee dat het standaard domein * *. ononmicrosoft.com* niet wordt aanbevolen voor productie gebruik. maar
* De identiteiten van Azure AD Connect zijn niet gesynchroniseerd

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName-beleids regels die van toepassing zijn op alle gebruikers accounts

Elk gebruikers account dat moet worden aangemeld bij Azure AD, moet een unieke kenmerk waarde van het user principal name (UPN) hebben die aan hun account is gekoppeld. De volgende tabel bevat een overzicht van de beleids regels die van toepassing zijn op on-premises Active Directory gebruikers accounts die zijn gesynchroniseerd met de Cloud en naar gebruikers accounts met alleen Clouds:

| Eigenschap | UserPrincipalName-vereisten |
| --- | --- |
| Toegestane tekens |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Tekens niet toegestaan |<ul> <li>Een '\@\" teken dat de gebruikers naam niet van het domein scheidt.</li> <li>Mag geen punt bevatten: "." direct voor het\@\"-symbool</li></ul> |
| Lengte beperkingen |<ul> <li>De totale lengte mag niet langer zijn dan 113 tekens</li><li>Er mogen Maxi maal 64 tekens zijn vóór het\@\"-symbool</li><li>Er mogen Maxi maal 48 tekens na het symbool '\@\"</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Wachtwoord beleid dat alleen van toepassing is op Cloud gebruikers accounts

In de volgende tabel worden de wachtwoord beleids instellingen beschreven die worden toegepast op gebruikers accounts die zijn gemaakt en beheerd in azure AD:

| Eigenschap | Vereisten |
| --- | --- |
| Toegestane tekens |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ "();</li> <li>lege ruimte</li></ul> |
| Tekens niet toegestaan | Unicode-tekens. |
| Wachtwoord beperkingen |<ul><li>Mini maal acht tekens en Maxi maal 256 tekens.</li><li>Vereist drie van de volgende vier:<ul><li>Kleine letters.</li><li>Hoofd letters.</li><li>Getallen (0-9).</li><li>Symbolen (Zie de vorige wachtwoord beperkingen).</li></ul></li></ul> |
| Geldigheids duur van wacht woord (maximale wachtwoord duur) |<ul><li>Standaard waarde: **90** dagen.</li><li>De waarde kan worden geconfigureerd met behulp van de cmdlet `Set-MsolPasswordPolicy` van de Azure Active Directory-module voor Windows Power shell.</li></ul> |
| Melding over het verlopen van het wacht woord (wanneer gebruikers op de hoogte worden gesteld van het verlopen van wacht woorden) |<ul><li>Standaard waarde: **14** dagen (voordat wacht woord verloopt).</li><li>De waarde kan worden geconfigureerd met behulp van de cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Wacht woord verloopt (laat het wacht woord nooit verlopen) |<ul><li>Standaard waarde: **False** (geeft aan dat het wacht woord een verval datum heeft).</li><li>De waarde kan worden geconfigureerd voor afzonderlijke gebruikers accounts met behulp van de cmdlet `Set-MsolUser`.</li></ul> |
| Geschiedenis van wachtwoord wijzigingen | Het laatste wacht woord *kan niet* opnieuw worden gebruikt wanneer de gebruiker een wacht woord wijzigt. |
| Geschiedenis van wacht woord opnieuw instellen | Het laatste wacht woord *kan* opnieuw worden gebruikt wanneer de gebruiker een verg eten wacht woord opnieuw instelt. |
| Account vergrendeling | Na 10 mislukte aanmeldings pogingen met het verkeerde wacht woord, wordt de gebruiker één minuut geblokkeerd. Bij verdere onjuiste aanmeldings pogingen wordt de gebruiker vergrendeld om de duur van de tijd te verg Roten. [Slimme vergren deling](howto-password-smart-lockout.md) houdt de laatste drie ongeldige hashes met een onjuist wacht woord bij om te voor komen dat de vergrendelings teller voor hetzelfde wacht woord wordt verhoogd. Als iemand hetzelfde onjuiste wacht woord meermaals opgeeft, wordt het account niet vergrendeld. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Beleids regels voor wachtwoord verloop instellen in azure AD

Een globale beheerder of gebruikers beheerder voor een micro soft-Cloud service kan de Microsoft Azure AD-module voor Windows PowerShell gebruiken om gebruikers wachtwoorden in te stellen die niet verlopen. U kunt ook Windows Power shell-cmdlets gebruiken om de configuratie nooit-expires te verwijderen of om te zien welke gebruikers wachtwoorden zijn ingesteld op nooit verlopen. 

Deze richt lijnen zijn van toepassing op andere providers, zoals intune en Office 365, die ook afhankelijk zijn van Azure AD voor identiteits-en Directory Services. Verlopen van wacht woorden is het enige deel van het beleid dat kan worden gewijzigd.

> [!NOTE]
> Alleen wacht woorden voor gebruikers accounts die niet zijn gesynchroniseerd via adreslijst synchronisatie, kunnen zodanig worden geconfigureerd dat ze niet verlopen. Zie [ad verbinden met Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)voor meer informatie over Directory synchronisatie.

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Het wachtwoord beleid instellen of controleren met behulp van Power shell

Als u aan de slag wilt gaan, moet u [de Azure AD Power shell-module downloaden en installeren](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Nadat u de app hebt geïnstalleerd, kunt u de volgende stappen gebruiken om elk veld te configureren.

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
   > Wacht woorden die zijn ingesteld op `-PasswordPolicies DisablePasswordExpiration` nog steeds verouderen op basis van het kenmerk `pwdLastSet`. Als u instelt dat de wacht woorden van de gebruiker nooit verlopen en vervolgens 90 + dagen door lopen, verlopen de wacht woorden. Als u op basis van het kenmerk `pwdLastSet` de verval datum wijzigt in `-PasswordPolicies None`, moeten alle wacht woorden die een `pwdLastSet` hebben die ouder zijn dan 90 dagen, worden gewijzigd wanneer de gebruiker zich de volgende keer aanmeldt. Deze wijziging kan van invloed zijn op een groot aantal gebruikers.

## <a name="next-steps"></a>Volgende stappen

De volgende artikelen bevatten aanvullende informatie over het opnieuw instellen van wacht woorden via Azure AD:

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registreer u voor selfservice voor wachtwoordherstel](../user-help/active-directory-passwords-reset-register.md).
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets is verbroken. Hoe kan ik problemen met SSPR oplossen?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)
