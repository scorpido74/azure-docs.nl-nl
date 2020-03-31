---
title: Zelfservicewachtwoord opnieuw instellen aanpassen - Azure Active Directory
description: Aanpassingsopties voor het opnieuw instellen van azure AD-zelfservicewachtwoorden
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
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979460"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>De Azure AD-functionaliteit aanpassen voor het opnieuw instellen van selfservicewachtwoorden

IT-professionals die selfservice password reset (SSPR) willen implementeren in Azure Active directory (Azure AD) kunnen de ervaring aanpassen aan de behoeften van hun gebruikers.

## <a name="customize-the-contact-your-administrator-link"></a>De koppeling Contact opnemen met uw beheerder aanpassen

Selfservice-wachtwoordresetgebruikers hebben een koppeling 'Contact opnemen met uw beheerder' beschikbaar in de portal voor het opnieuw instellen van wachtwoorden. Als een gebruiker deze koppeling selecteert, doet hij een van de twee dingen:

* Als u de standaardstatus blijft staan:
   * E-mail wordt verzonden naar uw beheerders en vraagt hen om hulp te bieden bij het wijzigen van het wachtwoord van de gebruiker. Zie de [voorbeelde-mail](#sample-email) hieronder.
* Indien aangepast:
   * Hiermee stuurt u uw gebruiker naar een webpagina of e-mailadres dat door de beheerder is opgegeven voor hulp.

> [!TIP]
> Als u dit aanpast, raden we u aan dit in te stellen op iets dat gebruikers al kennen voor ondersteuning

> [!WARNING]
> Als u deze instelling aanpast met een e-mailadres en account waarvoor een wachtwoordopnieuw instelling nodig is, kan de gebruiker mogelijk niet om hulp vragen.

### <a name="sample-email"></a>Voorbeeld e-mail

![Voorbeeldverzoek om e-mail die naar beheerder wordt verzonden opnieuw in te stellen][Contact]

De e-mail met contactpersonen wordt in de volgende volgorde naar de volgende ontvangers verzonden:

1. Als de rol van de **helpdeskbeheerder** of de functie **wachtwoordbeheerder** is toegewezen, worden beheerders met deze rollen op de hoogte gesteld.
1. Als er geen helpdeskbeheerders of wachtwoordbeheerder zijn toegewezen, worden beheerders met de functie **gebruikersbeheerder** hiervan op de hoogte gesteld.
1. Als geen van de vorige rollen is toegewezen, worden de **globale beheerders** hiervan op de hoogte gesteld.

In alle gevallen worden maximaal 100 ontvangers op de hoogte gebracht.

Zie [Beheerdersrollen toewijzen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de verschillende beheerdersrollen en hoe deze u deze toewijzen.

### <a name="disable-contact-your-administrator-emails"></a>E-mails 'Contact opnemen met uw beheerder' uitschakelen

Als uw organisatie beheerders niet op de hoogte wil stellen van aanvragen voor het opnieuw instellen van wachtwoorden, u de volgende configuratie inschakelen:

* Schakel selfservicewachtwoordreset in voor alle eindgebruikers. Deze optie staat onder**Eigenschappen** **voor het opnieuw instellen van** > wachtwoorden . Als u niet wilt dat gebruikers hun eigen wachtwoorden opnieuw instellen, u toegang tot een lege groep openen. *We raden deze optie niet aan.*
* Pas de helpdeskkoppeling aan om een web-URL of e-mail te geven: adres dat gebruikers kunnen gebruiken om hulp te krijgen. Deze optie staat onder Aangepaste aangepaste**helpdeske-mail of URL**voor het aanpassen > **van** >  **wachtwoorden.**

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>De ad fs-aanmeldingspagina aanpassen voor SSPR

Beheerders van Active Directory Federation Services (AD FS) kunnen een koppeling toevoegen aan hun aanmeldingspagina met behulp van de richtlijnen in het artikel [Aanmeldingspaginabeschrijving toevoegen.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)

Als u een koppeling wilt toevoegen aan de aanmeldingspagina van AD FS, gebruikt u de volgende opdracht op uw AD FS-server. Gebruikers kunnen deze pagina gebruiken om de SSPR-workflow in te voeren.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>De aanmeldingspagina en het access panel-look and feel aanpassen

U de aanmeldingspagina aanpassen. U een logo toevoegen dat samen met de afbeelding wordt weergegeven die past bij uw bedrijfsbranding.

De afbeeldingen die u kiest, worden in de volgende omstandigheden weergegeven:

* Nadat een gebruiker zijn gebruikersnaam heeft ingevoerd
* Als de gebruiker toegang heeft tot de aangepaste URL:
   * Door de `whr` parameter door te geven aan de pagina met het opnieuw instellen van het wachtwoord, zoals`https://login.microsoftonline.com/?whr=contoso.com`
   * Door de `username` parameter door te geven aan de pagina met het opnieuw instellen van het wachtwoord, zoals`https://login.microsoftonline.com/?username=admin@contoso.com`

Meer informatie over het configureren van bedrijfsbranding vindt u in het artikel [Bedrijfsbranding toevoegen aan uw aanmeldingspagina in Azure AD.](../fundamentals/customize-branding.md)

### <a name="directory-name"></a>Adreslijstnaam

U het kenmerk directorynaam wijzigen onder **Azure Active Directory** > **Properties**. U een vriendelijke organisatienaam weergeven die te zien is in het portaal en in de geautomatiseerde communicatie. Deze optie is het meest zichtbaar in geautomatiseerde e-mails in de volgende formulieren:

* De vriendelijke naam in de e-mail, bijvoorbeeld "Microsoft namens CONTOSO demo"
* De onderwerpregel in de e-mail, bijvoorbeeld "CONTOSO demo account e-mail verificatiecode"

## <a name="next-steps"></a>Volgende stappen

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets kapot is. Hoe los ik SSPR op?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Neem contact op met uw beheerder voor hulp bij het opnieuw instellen van uw voorbeeld van e-mail met wachtwoord"
