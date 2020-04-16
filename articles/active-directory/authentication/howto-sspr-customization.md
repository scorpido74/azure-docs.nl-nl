---
title: Zelfservicewachtwoord opnieuw instellen aanpassen - Azure Active Directory
description: Meer informatie over het aanpassen van gebruikersweergave- en ervaringsopties voor het opnieuw instellen van azure AD-zelfservicewachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394252"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>De gebruikerservaring aanpassen voor het opnieuw instellen van azure Active Directory-zelfservicewachtwoord

Self-service password reset (SSPR) geeft gebruikers in Azure Active Directory (Azure AD) de mogelijkheid om hun wachtwoord te wijzigen of opnieuw in te stellen, zonder tussenkomst van de beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als hij zijn wachtwoord vergeet, kunnen ze aanwijzingen volgen om zichzelf te deblokkeren en weer aan het werk te gaan. Deze mogelijkheid vermindert helpdeskoproepen en productiviteitsverlies wanneer een gebruiker zich niet kan aanmelden bij zijn apparaat of toepassing.

Als u de SSPR-ervaring voor gebruikers wilt verbeteren, u het uiterlijk van de pagina voor het opnieuw instellen van wachtwoorden, e-mailmeldingen of aanmeldingspagina's aanpassen. Met deze aanpassingsopties u de gebruiker duidelijk maken dat hij of zij op de juiste plaats is en hen het vertrouwen geven dat ze toegang hebben tot bedrijfsbronnen.
    
In dit artikel ziet u hoe u de sspr-e-mailkoppeling aanpassen voor gebruikers, bedrijfsbranding en AD FS-aanmeldingspaginakoppeling.

## <a name="customize-the-contact-your-administrator-link"></a>De koppeling Contact opnemen met uw beheerder aanpassen

Om gebruikers te helpen bij het opnieuw instellen van selfservicewachtwoorden, wordt een koppeling 'Contact opnemen met uw beheerder' weergegeven in de portal voor het opnieuw instellen van wachtwoorden. Als een gebruiker deze koppeling selecteert, doet deze een van de twee dingen:

* Als deze koppeling met contactpersonen in de standaardstatus wordt achtergelaten, wordt een e-mail naar uw beheerders verzonden en wordt hen gevraagd om hulp bij het wijzigen van het wachtwoord van de gebruiker. In de volgende voorbeelde-e-mail wordt dit standaarde-mailbericht weergegeven:

    ![Voorbeeldverzoek om e-mail te resetten dat naar de beheerder wordt verzonden](./media/howto-sspr-customization/sspr-contact-admin.png)

* Als deze is aangepast, stuurt u de gebruiker naar een webpagina of e-mailadres dat door de beheerder is opgegeven voor hulp.
    * Als u dit aanpast, raden we u aan dit in te stellen op iets waarmee gebruikers al bekend zijn voor ondersteuning.

    > [!WARNING]
    > Als u deze instelling aanpast met een e-mailadres en account waarvoor een wachtwoordopnieuw instelling nodig is, kan de gebruiker mogelijk niet om hulp vragen.

### <a name="default-email-behavior"></a>Standaarde-mailgedrag

De standaarde-mail met contactpersonen wordt in de volgende volgorde naar ontvangers verzonden:

1. Als de rol van de *helpdeskbeheerder* of de functie *wachtwoordbeheerder* is toegewezen, worden beheerders met deze rollen op de hoogte gesteld.
1. Als er geen helpdeskbeheerder of wachtwoordbeheerder is toegewezen, worden beheerders met de functie *gebruikersbeheerder* hiervan op de hoogte gesteld.
1. Als geen van de vorige rollen is toegewezen, worden de *globale beheerders* hiervan op de hoogte gesteld.

In alle gevallen worden maximaal 100 ontvangers op de hoogte gebracht.

Zie [Beheerdersrollen toewijzen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)voor meer informatie over de verschillende beheerdersrollen en hoe deze u deze toewijzen.

### <a name="disable-contact-your-administrator-emails"></a>E-mails 'Contact opnemen met uw beheerder' uitschakelen

Als uw organisatie beheerders niet op de hoogte wil stellen van aanvragen voor het opnieuw instellen van wachtwoorden, kunnen de volgende configuratieopties worden gebruikt:

* Pas de helpdeskkoppeling aan om een web-URL of e-mail te geven: adres dat gebruikers kunnen gebruiken om hulp te krijgen. Deze optie staat onder Aangepaste aangepaste**helpdeske-mail of URL**voor het aanpassen > **van** >  **wachtwoorden.**
* Schakel selfservicewachtwoordreset in voor alle gebruikers. Deze optie staat onder**Eigenschappen** **voor het opnieuw instellen van** > wachtwoorden . Als u niet wilt dat gebruikers hun eigen wachtwoorden opnieuw instellen, u toegang tot een lege groep openen. *We raden deze optie niet aan.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>De aanmeldingspagina en het toegangspaneel aanpassen

U de aanmeldingspagina aanpassen, bijvoorbeeld om een logo toe te voegen dat wordt weergegeven samen met de afbeelding die past bij uw bedrijfsbranding. Zie [Bedrijfsbranding toevoegen aan uw aanmeldingspagina in Azure AD](../fundamentals/customize-branding.md)voor meer informatie over het configureren van bedrijfsbranding.

De afbeeldingen die u kiest, worden in de volgende omstandigheden weergegeven:

* Nadat een gebruiker zijn gebruikersnaam heeft ingevoerd
* Als de gebruiker toegang heeft tot de aangepaste URL:
   * Door de `whr` parameter door te geven aan de pagina met het opnieuw instellen van het wachtwoord, zoals`https://login.microsoftonline.com/?whr=contoso.com`
   * Door de `username` parameter door te geven aan de pagina met het opnieuw instellen van het wachtwoord, zoals`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Adreslijstnaam

Om het er gebruiksvriendelijker uit te laten zien, u de naam van de organisatie wijzigen in de portal en in de geautomatiseerde communicatie. Als u het kenmerk directorynaam in de Azure-portal wilt wijzigen, bladert u naar Azure Active > **Directory-eigenschappen**. **Azure Active Directory** Deze vriendelijke organisatienaamoptie is het meest zichtbaar in geautomatiseerde e-mails, zoals in de volgende voorbeelden:

* De vriendelijke naam in de e-mail, bijvoorbeeld "*Microsoft namens CONTOSO demo*"
* De onderwerpregel in de e-mail, bijvoorbeeld "*CONTOSO demo account e-mail verificatiecode*"

## <a name="customize-the-ad-fs-sign-in-page"></a>De aanmeldingspagina ad FS aanpassen

Als u Ad FS (Active Directory Federation Services) gebruikt voor aanmeldingsgebeurtenissen voor gebruikers, u een koppeling toevoegen aan de aanmeldingspagina met behulp van de richtlijnen in het artikel om [aanmeldingspaginabeschrijving toe](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)te voegen.

Geef gebruikers een koppeling naar de pagina waarmee ze de *https://passwordreset.microsoftonline.com*SSPR-werkstroom kunnen invoeren, zoals . Als u een koppeling wilt toevoegen aan de aanmeldingspagina van AD FS, gebruikt u de volgende opdracht op uw AD FS-server:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Volgende stappen

Zie [Rapportageopties voor Azure AD-wachtwoordbeheer](howto-sspr-reporting.md)voor u wilt begrijpen om inzicht te krijgen in het gebruik van SSPR in uw omgeving.

Als u of gebruikers problemen hebben met SSPR, [raadpleegt u Het oplossen van selfservicewachtwoordreset](active-directory-passwords-troubleshoot.md)
