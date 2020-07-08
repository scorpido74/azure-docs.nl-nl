---
title: Azure AD SSPR-gegevens vereisten-Azure Active Directory
description: Gegevens vereisten voor Azure AD self-service voor wachtwoord herstel en hoe deze kunnen worden voldaan
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80652361"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Wacht woord opnieuw instellen implementeren zonder registratie door eind gebruiker vereist

Voor het implementeren van Azure Active Directory (Azure AD) selfservice voor wachtwoord herstel (SSPR), moeten er verificatie gegevens aanwezig zijn. Sommige organisaties hebben hun gebruikers hun verificatie gegevens zelf invoeren. Andere organisaties synchroniseren liever met gegevens die al bestaan in Active Directory. Deze gesynchroniseerde gegevens worden beschikbaar gesteld aan Azure AD en SSPR zonder tussen komst van de gebruiker als u aan de volgende vereisten voldoet:

* De gegevens in uw on-premises Directory goed indelen.
* Configureer [Azure AD Connect met behulp van de Express-instellingen](../hybrid/how-to-connect-install-express.md).

Om goed te kunnen werken, moeten telefoon nummers de notatie *+ CountryCode phonenumber*hebben, bijvoorbeeld + 1 4255551234.

> [!NOTE]
> Er moet een spatie zijn tussen de land code en het telefoon nummer.
>
> Het opnieuw instellen van een wacht woord biedt geen ondersteuning voor telefoon uitbreidingen. Zelfs in de indeling van de + 1-4255551234X12345 worden uitbrei dingen verwijderd voordat de oproep wordt geplaatst.

## <a name="fields-populated"></a>Velden ingevuld

Als u de standaard instellingen in Azure AD Connect gebruikt, worden de volgende toewijzingen gemaakt:

| On-premises Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | Bureautelefoon |
| mobiel | Mobiele telefoon |

Nadat een gebruiker zijn of haar mobiele telefoon nummer heeft geverifieerd, wordt het *telefoon* veld onder **verificatie contact gegevens** in azure AD ook ingevuld met dit nummer.

## <a name="authentication-contact-info"></a>Contact gegevens voor verificatie

Op de pagina **verificatie methoden** voor een Azure AD-gebruiker in de Azure Portal, kan een globale beheerder de contact gegevens voor de verificatie hand matig instellen, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

![Contact gegevens voor verificatie voor een gebruiker in azure AD][Contact]

* Als het veld **telefoon** is ingevuld en **mobiele telefoon** is ingeschakeld in het SSPR-beleid, ziet de gebruiker dat nummer op de registratie pagina voor het opnieuw instellen van het wacht woord en tijdens de werk stroom voor het opnieuw instellen van wacht woorden.
* Het veld **alternatief telefoon nummer** wordt niet gebruikt voor het opnieuw instellen van wacht woorden.
* Als het veld **e-mail** is ingevuld en **e-mail adres** is ingeschakeld in het SSPR-beleid, ziet de gebruiker dat e-mail bericht op de registratie pagina voor het opnieuw instellen van het wacht woord en tijdens de werk stroom voor wacht woord opnieuw instellen.
* Als het veld **alternatief e-mail** gevuld is en **e-mail adres** is ingeschakeld in het SSPR-beleid, ziet de gebruiker dat e-mail bericht **niet** op de registratie pagina voor het opnieuw instellen van het wacht woord, maar ze worden weer geven tijdens de werk stroom voor wacht woord opnieuw instellen.

## <a name="security-questions-and-answers"></a>Beveiligings vragen en-antwoorden

De beveiligings vragen en-antwoorden worden veilig opgeslagen in uw Azure AD-Tenant en zijn alleen toegankelijk voor gebruikers via de [SSPR-registratie Portal](https://aka.ms/ssprsetup). Beheerders kunnen de inhoud van de vragen en antwoorden van een andere gebruiker niet weer geven, instellen of wijzigen.

## <a name="what-happens-when-a-user-registers"></a>Wat er gebeurt wanneer een gebruiker zich registreert

Wanneer een gebruiker zich registreert, stelt de registratie pagina de volgende velden in:

* **Telefoon voor authenticatie**
* **E-mail verificatie**
* **Beveiligings vragen en-antwoorden**

Als u een waarde voor **mobiele telefoon** of **alternatief e-mail adres**hebt ingevoerd, kunnen gebruikers deze waarden direct gebruiken om hun wacht woord opnieuw in te stellen, zelfs als ze niet zijn geregistreerd voor de service. Bovendien zien gebruikers die waarden wanneer ze zich voor de eerste keer registreren en ze kunnen ze wijzigen als ze dat willen. Nadat de registratie is geslaagd, worden deze waarden respectievelijk opgeslagen in de velden **verificatie telefoon** en **verificatie-e** .

## <a name="set-and-read-the-authentication-data-through-powershell"></a>De verificatie gegevens instellen en lezen via Power shell

De volgende velden kunnen worden ingesteld via Power shell:

* **Alternatief e-mailadres**
* **Mobiele telefoon**
* **Telefoon nummer van Office**: kan alleen worden ingesteld als u niet synchroniseert met een on-premises Directory

### <a name="use-powershell-version-1"></a>Power shell versie 1 gebruiken

Als u aan de slag wilt gaan, moet u [de Azure AD Power shell-module downloaden en installeren](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Nadat u de app hebt geïnstalleerd, kunt u de volgende stappen gebruiken om elk veld te configureren.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Verificatie gegevens instellen met Power shell versie 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>De verificatie gegevens lezen met Power shell versie 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>De e-mail opties voor verificatie via telefoon en verificatie lezen

Gebruik de volgende opdrachten om het **e-mail adres** voor verificatie van de **telefoon** en verificatie te lezen wanneer u Power shell versie 1 gebruikt:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Power shell versie 2 gebruiken

Als u aan de slag wilt gaan, moet u [de Azure AD-versie 2 Power shell-module downloaden en installeren](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Nadat u de app hebt geïnstalleerd, kunt u de volgende stappen gebruiken om elk veld te configureren.

Voer de volgende opdrachten uit om snel te installeren vanuit recente versies van Power shell die ondersteuning bieden voor installatie-module. (De eerste regel controleert of de module al is geïnstalleerd.)

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Verificatie gegevens instellen met Power shell versie 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>De verificatie gegevens lezen met Power shell versie 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wacht woord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets is verbroken. Hoe kan ik problemen met SSPR oplossen?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globale beheerders kunnen de contact gegevens voor verificatie van een gebruiker wijzigen"
