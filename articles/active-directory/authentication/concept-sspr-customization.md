---
title: Self-service voor wacht woord opnieuw instellen aanpassen-Azure Active Directory
description: Aanpassings opties voor de selfservice voor wachtwoord herstel van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dfd035f73ea529ddb55bac6ce601185fda51a4d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381942"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>De Azure AD-functionaliteit aanpassen voor Self-service voor wachtwoord herstel

IT-professionals die self-service voor wachtwoord herstel (SSPR) in azure Active Directory (Azure AD) willen implementeren, kunnen de ervaring aanpassen aan de behoeften van hun gebruikers.

## <a name="customize-the-contact-your-administrator-link"></a>Pas de koppeling ' Neem contact op met de beheerder ' aan

Gebruikers met een self-service voor wacht woord opnieuw instellen hebben de koppeling ' Neem contact op met de beheerder ' in de portal voor het opnieuw instellen van wacht woorden. Als een gebruiker deze koppeling selecteert, wordt een van de volgende twee dingen gedaan:

* Als dit de standaard status heeft:
   * E-mail wordt verzonden naar uw beheerders en vraagt om hulp bij het wijzigen van het wacht woord van de gebruiker. Zie de onderstaande [Voorbeeld-e-mail](#sample-email) .
* Indien aangepast:
   * Stuurt uw gebruiker naar een webpagina of e-mail adres dat door de beheerder is opgegeven voor ondersteuning.

> [!TIP]
> Als u dit aanpast, wordt aangeraden om dit in te stellen voor gebruikers die al bekend zijn met het oog op ondersteuning

> [!WARNING]
> Als u deze instelling aanpast met een e-mail adres en account waarvoor een wacht woord opnieuw moet worden ingesteld, kan de gebruiker mogelijk niet om hulp vragen.

### <a name="sample-email"></a>Voor beeld-e-mail

![Voorbeeld aanvraag voor het opnieuw instellen van e-mail die is verzonden naar de beheerder][Contact]

De contact-e-mail wordt in de volgende volg orde verzonden naar de volgende ontvangers:

1. Als de rol **Wachtwoord beheerder** is toegewezen, worden beheerders met deze rol hiervan op de hoogte gesteld.
2. Als er geen wachtwoord beheerders zijn toegewezen, worden beheerders met de rol **gebruikers beheerder** hiervan op de hoogte gesteld.
3. Als geen van de vorige rollen zijn toegewezen, worden de **globale beheerders** hiervan op de hoogte gesteld.

In alle gevallen worden Maxi maal 100 ontvangers hiervan op de hoogte gebracht.

Zie [beheerders rollen toewijzen in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de verschillende beheerders rollen en hoe u deze toewijst.

### <a name="disable-contact-your-administrator-emails"></a>E-mail berichten van contact opnemen met de beheerder uitschakelen

Als uw organisatie beheerders niet wil informeren over aanvragen voor het opnieuw instellen van wacht woorden, kunt u de volgende configuratie inschakelen:

* Schakel self-service voor wachtwoord herstel in voor alle eind gebruikers. Deze optie bevindt zich onder > **Eigenschappen**voor **wacht woord opnieuw instellen** . Als u niet wilt dat gebruikers hun eigen wacht woord opnieuw instellen, kunt u de toegang tot een lege groep bereiken. *Deze optie wordt niet aanbevolen.*
* De Help Desk aanpassen om een web-URL of mailto:-adres op te geven dat gebruikers kunnen gebruiken om hulp te krijgen. Deze optie is onder **wacht woord opnieuw instellen** > **aanpassing** > **aangepaste Help Desk-e-mail of-URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>De AD FS-aanmeldings pagina voor SSPR aanpassen

Beheerders van Active Directory Federation Services (AD FS) kunnen een koppeling toevoegen aan de aanmeldings pagina met behulp van de instructies in het artikel beschrijving van het toevoegen van een [aanmeldings pagina](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) .

Als u een koppeling naar de aanmeldings pagina van AD FS wilt toevoegen, gebruikt u de volgende opdracht op uw AD FS-server. Gebruikers kunnen deze pagina gebruiken om de SSPR-werk stroom in te voeren.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>De aanmeldings pagina en het uiterlijk van het toegangs venster aanpassen

U kunt de aanmeldings pagina aanpassen. U kunt een logo toevoegen dat samen met de afbeelding wordt weer gegeven die past bij de huis stijl van uw bedrijf.

De afbeeldingen die u kiest, worden in de volgende omstandigheden weer gegeven:

* Nadat een gebruiker de gebruikers naam heeft ingevoerd
* Als de gebruiker toegang heeft tot de aangepaste URL:
   * Door de para meter `whr` door te geven aan de pagina voor het opnieuw instellen van wacht woorden, zoals `https://login.microsoftonline.com/?whr=contoso.com`
   * Door de para meter `username` door te geven aan de pagina voor het opnieuw instellen van wacht woorden, zoals `https://login.microsoftonline.com/?username=admin@contoso.com`

Meer informatie over het configureren van de huis stijl van een bedrijf vindt u in het artikel [huis stijl van bedrijf toevoegen aan uw aanmeldings pagina in azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Mapnaam

U kunt het kenmerk Directory naam onder **Azure Active Directory** > **Eigenschappen**wijzigen. U kunt een beschrijvende organisatie naam die wordt weer gegeven in de portal en in automatische communicatie. Deze optie is het meest zichtbaar in automatische e-mail berichten in de volgende formulieren:

* De beschrijvende naam in het e-mail bericht, bijvoorbeeld ' micro soft namens de CONTOSO-demo '
* De regel onderwerp in het e-mail bericht, bijvoorbeeld ' CONTOSO-demo account e-mail verificatie code '

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor wachtwoordherstel](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets is verbroken. Hoe kan ik problemen met SSPR oplossen?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Neem contact op met de beheerder voor meer informatie over het opnieuw instellen van uw wacht woord e-mail adres"
