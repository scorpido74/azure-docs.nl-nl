---
title: Azure AD SSPR-gegevensvereisten - Azure Active Directory
description: Gegevensvereisten voor het opnieuw instellen van azure AD-zelfservicewachtwoorden en hoe hieraan te voldoen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f7e120745357d3bd5735cca568bdd6971ea061
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652361"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Wachtwoordreset implementeren zonder registratie door eindgebruikers vereist

Als u Azure Active Directory (Azure AD) selfservice wachtwoordreset (SSPR) wilt implementeren, moeten verificatiegegevens aanwezig zijn. Sommige organisaties laten hun gebruikers hun verificatiegegevens zelf invoeren. Andere organisaties synchroniseren liever met gegevens die al in Active Directory zijn. Deze gesynchroniseerde gegevens worden beschikbaar gesteld aan Azure AD en SSPR zonder dat gebruikersinteractie vereist als u aan de volgende vereisten voldoet:

* Maak de gegevens goed op in uw on-premises directory.
* Configureer [Azure AD Connect met behulp van de expresinstellingen](../hybrid/how-to-connect-install-express.md).

Om goed te kunnen werken, moeten telefoonnummers in het formaat *+CountryCode PhoneNumber*, bijvoorbeeld +1 4255551234.

> [!NOTE]
> Er moet een ruimte zijn tussen de landcode en het telefoonnummer.
>
> Wachtwoordreset biedt geen ondersteuning voor telefoonextensies. Zelfs in het +1 4255551234X12345-formaat worden extensies verwijderd voordat de oproep wordt geplaatst.

## <a name="fields-populated"></a>Velden bevolkt

Als u de standaardinstellingen in Azure AD Connect gebruikt, worden de volgende toewijzingen gemaakt:

| On-premises Active Directory | Azure AD |
| --- | --- |
| telefoonNummer | Zakelijke telefoon |
| mobiel | Mobiele telefoon |

Nadat een gebruiker zijn mobiele telefoonnummer heeft geverifieerd, wordt het veld *Telefoon* onder **Contactgegevens verificatie** in Azure AD ook met dat nummer ingevuld.

## <a name="authentication-contact-info"></a>Contactgegevens voor verificatie

Op de pagina **Verificatiemethoden** voor een Azure AD-gebruiker in de Azure-portal kan een globale beheerder de contactgegevens van de verificatie handmatig instellen, zoals wordt weergegeven in de volgende voorbeeldschermafbeelding:

![Contactgegevens voor verificatie van een gebruiker in Azure AD][Contact]

* Als het veld **Telefoon** is ingevuld en **de mobiele telefoon** is ingeschakeld in het SSPR-beleid, ziet de gebruiker dat nummer op de registratiepagina voor het opnieuw instellen van wachtwoorden en tijdens de workflow voor het opnieuw instellen van wachtwoorden.
* Het veld **Alternatieve telefoon** wordt niet gebruikt voor het opnieuw instellen van het wachtwoord.
* Als het veld **E-mail** is ingevuld en **e-mail** is ingeschakeld in het SSPR-beleid, ziet de gebruiker die e-mail op de registratiepagina voor het opnieuw instellen van wachtwoorden en tijdens de workflow voor het opnieuw instellen van wachtwoorden.
* Als het veld **Alternatieve e-mail** wordt ingevuld en **e-mail** is ingeschakeld in het SSPR-beleid, ziet de gebruiker die e-mail **niet** op de registratiepagina voor het opnieuw instellen van wachtwoorden, maar ziet deze wel tijdens de workflow voor het opnieuw instellen van wachtwoorden.

## <a name="security-questions-and-answers"></a>Beveiligingsvragen en -antwoorden

De beveiligingsvragen en -antwoorden worden veilig opgeslagen in uw Azure AD-tenant en zijn alleen toegankelijk voor gebruikers via de [SSPR-registratieportal.](https://aka.ms/ssprsetup) Beheerders kunnen de inhoud van de vragen en antwoorden van andere gebruikers niet zien, instellen of wijzigen.

## <a name="what-happens-when-a-user-registers"></a>Wat gebeurt er wanneer een gebruiker zich registreert

Wanneer een gebruiker zich registreert, stelt de registratiepagina de volgende velden in:

* **Verificatietelefoon**
* **Verificatie-e-mail**
* **Beveiligingsvragen en -antwoorden**

Als u een waarde hebt opgegeven voor **mobiele telefoon** of **alternatieve e-mail,** kunnen gebruikers deze waarden onmiddellijk gebruiken om hun wachtwoorden opnieuw in te stellen, zelfs als ze zich niet hebben geregistreerd voor de service. Bovendien zien gebruikers deze waarden wanneer ze zich voor de eerste keer registreren en kunnen ze deze wijzigen als ze dat willen. Nadat ze zich hebben geregistreerd, blijven deze waarden bestaan in respectievelijk de velden **Verificatietelefoon** en **Verificatie-e-mail.**

## <a name="set-and-read-the-authentication-data-through-powershell"></a>De verificatiegegevens instellen en lezen via PowerShell

De volgende velden kunnen worden ingesteld via PowerShell:

* **Alternatief e-mailadres**
* **Mobiele telefoon**
* **Office-telefoon:** kan alleen worden ingesteld als u niet synchroniseert met een on-premises directory

### <a name="use-powershell-version-1"></a>PowerShell-versie 1 gebruiken

Om aan de slag te gaan, moet u [de Azure AD PowerShell-module downloaden en installeren.](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule) Nadat u het hebt geïnstalleerd, u de volgende stappen gebruiken om elk veld te configureren.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>De verificatiegegevens instellen met PowerShell-versie 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Lees de verificatiegegevens met PowerShell-versie 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Lees de verificatietelefoon- en verificatie-e-mailopties

Als u de **e-mail** **voor verificatietelefoon** en verificatie wilt lezen wanneer u PowerShell-versie 1 gebruikt, gebruikt u de volgende opdrachten:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell-versie 2 gebruiken

Om aan de slag te gaan, moet u [de PowerShell-module Azure AD-versie 2 downloaden en installeren.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) Nadat u het hebt geïnstalleerd, u de volgende stappen gebruiken om elk veld te configureren.

Voer de volgende opdrachten uit om snel te installeren vanuit recente versies van PowerShell die Install-Module ondersteunen. (De eerste regel controleert of de module al is geïnstalleerd.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>De verificatiegegevens instellen met PowerShell-versie 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Lees de verificatiegegevens met PowerShell-versie 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets kapot is. Hoe los ik SSPR op?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globale beheerders kunnen de contactgegevens van een gebruiker wijzigen"
