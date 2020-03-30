---
title: Branding toevoegen aan de aanmeldingspagina van uw organisatie - Azure AD
description: Instructies voor het toevoegen van de branding van uw organisatie aan de aanmeldingspagina van Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441fdd14cc2c734b6ce532f3ad1d30663b2f56c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049796"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Branding toevoegen aan de aanmeldingspagina van Azure Active Directory van uw organisatie
Gebruik het logo en de aangepaste kleurenschema's van uw organisatie om een consistente uitstraling te bieden op uw aanmeldingspagina's van Azure Active Directory (Azure AD). Uw aanmeldingspagina's worden weergegeven wanneer gebruikers zich aanmelden bij de webgebaseerde apps van uw organisatie, zoals Office 365, waarmee Azure AD wordt gebruikt als identiteitsprovider.

>[!Note]
>Voor het toevoegen van aangepaste branding moet u Azure Active Directory Premium 1, Premium 2 of Basic-edities gebruiken of een Office 365-licentie hebben. Zie [Aanmelden voor Azure AD Premium voor](active-directory-get-started-premium.md)meer informatie over licenties en edities.<br><br>De Azure AD-edities Premium en Basic zijn beschikbaar voor klanten in China via het wereldwijde exemplaar van Azure Active Directory. De Azure AD Premium en Basic-edities worden momenteel niet ondersteund in de Azure-service die wordt beheerd door 21Vianet in China. Neem contact met ons op via het [Azure Active Directory Forum](https://feedback.azure.com/forums/169401-azure-active-directory/) voor meer informatie.

## <a name="customize-your-azure-ad-sign-in-page"></a>Uw aanmeldingspagina voor Azure AD aanpassen
U uw Azure AD-aanmeldingspagina's aanpassen, die worden weergegeven wanneer gebruikers `https://outlook.com/contoso.com`zich aanmelden bij de tenantspecifieke apps van uw organisatie, zoals of bij het doorgeven van een domeinvariabele, zoals `https://passwordreset.microsoftonline.com/?whr=contoso.com`.

Uw aangepaste branding wordt niet onmiddellijk weergegeven wanneer uw\.gebruikers naar sites gaan zoals www office.com. In plaats daarvan moet de gebruiker zich aanmelden voordat uw aangepaste branding wordt weergegeven. Nadat de gebruiker zich heeft aangemeld, kan het 15 minuten of langer duren voordat de branding wordt weergegeven. 

> [!NOTE]
> Alle merkelementen zijn optioneel. Als u bijvoorbeeld een bannerlogo opgeeft zonder achtergrondafbeelding, wordt op de aanmeldingspagina uw logo weergegeven met een standaardachtergrondafbeelding van de bestemmingssite (bijvoorbeeld Office 365).<br><br>Bovendien wordt aanmeldingspaginabranding niet overgedragen naar persoonlijke Microsoft-accounts. Als uw gebruikers of zakelijke gasten zich aanmelden met een persoonlijk Microsoft-account, geeft de aanmeldingspagina niet de branding van uw organisatie weer.

### <a name="to-customize-your-branding"></a>Uw branding aanpassen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer vervolgens **Bedrijfsbranding**en selecteer **Vervolgens Configureren**.

    ![Contoso - Pagina Bedrijfsbranding, optie configureren gemarkeerd](media/customize-branding/company-branding-configure-button.png)

3. Geef op de pagina **Bedrijfsbranding configureren** de volgende informatie op of u deze opgeeft.

    >[!Important]
    >Alle aangepaste afbeeldingen die u op deze pagina toevoegt, hebben afbeeldingsgrootte (pixels) en mogelijk bestandsgrootte (KB) beperkingen. Vanwege deze beperkingen moet u waarschijnlijk een foto-editor gebruiken om de juiste afbeeldingen te maken.

    - **Algemene instellingen**

        ![Pagina met bedrijfsbranding configureren, met algemene instellingen voltooid](media/customize-branding/configure-company-branding-general-settings.png)

        - **Language.** De taal wordt automatisch ingesteld als standaard taal en kan niet worden gewijzigd.
        
        - **Achtergrondafbeelding van de aanmeldingspagina.** Selecteer een afbeeldingsbestand .png of .jpg dat wordt weergegeven als achtergrond voor uw aanmeldingspagina's. 
        
            De afbeelding mag niet groter zijn dan 1920x1080 pixels en moet een bestandsgrootte van minder dan 300 KB hebben.

        - **Bannerlogo.** Selecteer een .png- of .jpg-versie van uw logo die op de aanmeldingspagina wordt weergegeven nadat de gebruiker een gebruikersnaam heeft ingevoerd en op de portalpagina **Mijn apps.**
            
            De afbeelding mag niet groter zijn dan 60 pixels of breder dan 280 pixels. We raden u aan een transparante afbeelding te gebruiken, omdat de achtergrond mogelijk niet overeenkomt met de achtergrond van uw logo. We raden ook aan om geen opvulling rond de afbeelding toe te voegen of het kan uw logo klein laten lijken.

        - **Gebruikersnaam hint.** Typ de hinttekst die aan gebruikers wordt weergegeven als ze hun gebruikersnaam vergeten. Deze tekst moet Unicode zijn, zonder koppelingen of code, en mag niet meer dan 64 tekens bevatten. Als gasten zich aanmelden bij uw app, raden we u aan deze hint niet toe te voegen.

        - **Inlogpaginatekst.** Typ de tekst die onder aan de aanmeldingspagina wordt weergegeven. U deze tekst gebruiken om aanvullende informatie, zoals het telefoonnummer, door te geven aan uw helpdesk of een juridische verklaring. Deze tekst mag unicode zijn en mag niet meer dan 256 tekens bevatten. We raden ook aan om geen koppelingen of HTML-tags op te leggen.

    - **Geavanceerde instellingen**
            
        ![De pagina met bedrijfsbranding configureren, met geavanceerde instellingen voltooid](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **Achtergrondkleur van de aanmeldingspagina.** Geef de hexadecimale kleur op (bijvoorbeeld wit is #FFFFFF) die wordt weergegeven in plaats van uw achtergrondafbeelding in verbindingssituaties met een lage bandbreedte. We raden u aan de primaire kleur van uw bannerlogo of uw organisatiekleur te gebruiken.

        - **Afbeelding van het vierkant logo.** Selecteer een .png-afbeelding (voorkeur) of .jpg-afbeelding van het logo van uw organisatie die aan gebruikers wordt weergegeven tijdens het installatieproces voor nieuwe Windows 10 Enterprise-apparaten. Deze afbeelding wordt alleen gebruikt voor Windows-verificatie en wordt alleen weergegeven op tenants die [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) gebruiken voor implementatie of voor pagina's met wachtwoordinvoer in andere Windows 10-ervaringen. In sommige gevallen kan het ook worden weergegeven in het toestemmingsdialoogvenster.
        
            De afbeelding mag niet groter zijn dan 240x240 pixels en moet een bestandsgrootte van minder dan 10 KB hebben. We raden u aan een transparante afbeelding te gebruiken, omdat de achtergrond mogelijk niet overeenkomt met de achtergrond van uw logo. We raden ook aan om geen opvulling rond de afbeelding toe te voegen of het kan uw logo klein laten lijken.
    
        - **Vierkant logo afbeelding, donker thema.** Hetzelfde als de vierkante logo afbeelding hierboven. Deze logoafbeelding neemt de plaats in van de afbeelding met vierkante logo's wanneer deze wordt gebruikt met een donkere achtergrond, zoals bij Windows 10 Azure AD-schermen tijdens de out-of-box-ervaring (OOBE).  Als uw logo er goed uitziet op witte, donkerblauwe en zwarte achtergronden, hoeft u deze afbeelding niet toe te voegen. 
        
        - **Toon optie om ingelogd te blijven.** U ervoor kiezen uw gebruikers te laten zijn aangemeld bij Azure AD totdat u zich expliciet afmeldt. Als u **Nee**kiest, is deze optie verborgen en moeten gebruikers zich elke keer aanmelden wanneer de browser wordt gesloten en heropend.
        
            >[!Note]
            >Of sommige functies van SharePoint Online en Office 2010 beschikbaar zijn, hangt ervan af of gebruikers ervoor kunnen kiezen aangemeld te blijven. Als u deze instelling op **Nee** instelt, krijgen uw gebruikers mogelijk extra en onverwachte prompts te zien om zich aan te melden.
   

3. Nadat u klaar bent met het toevoegen van uw merknaam, selecteert u **Opslaan**.

    Als met dit proces uw eerste aangepaste merkconfiguratie wordt gemaakt, wordt dit de standaardinstelling voor uw tenant. Als u extra configuraties hebt, u uw standaardconfiguratie kiezen.
    
    >[!Important]
    >Als u meer configuraties voor huisstijl aan uw tenant wilt toevoegen, moet u **Nieuwe taal** kiezen op de pagina **Contoso - Bedrijfsbranding.** Hiermee wordt de pagina **Bedrijfsbranding configureren geopend,** waar u dezelfde stappen volgen als hierboven.

## <a name="update-your-custom-branding"></a>Uw aangepaste branding bijwerken
Nadat u uw aangepaste branding hebt gemaakt, u teruggaan en alles wijzigen wat u wilt.

### <a name="to-edit-your-custom-branding"></a>Uw aangepaste branding bewerken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer vervolgens **Bedrijfsbranding**en selecteer **Vervolgens Configureren**.

    ![Contoso - Bedrijfsmerkpagina, met standaardconfiguratie weergegeven](media/customize-branding/company-branding-default-config.png)

3. Voeg op de pagina **Bedrijfsbranding configureren** de informatie toe, verwijder of wijzig deze op basis van de beschrijvingen in het gedeelte [Uw Azure AD-aanmeldingspagina](#customize-your-azure-ad-sign-in-page) van dit artikel aanpassen.

4. Selecteer **Opslaan**.

   Het kan een uur duren voordat de wijzigingen die u aan de huisstijl van de aanmeldingspagina hebt aangebracht, worden weergegeven.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Taalspecifieke huisstijl toevoegen aan uw directory
U de taal van uw oorspronkelijke configuratie niet wijzigen vanuit uw standaardtaal. Als u echter een configuratie in een andere taal nodig hebt, u een nieuwe configuratie maken.

### <a name="to-add-a-language-specific-branding-configuration"></a>Een taalspecifieke merkconfiguratie toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**en selecteer vervolgens **Bedrijfsbranding**en selecteer **Vervolgens Nieuwe taal**.

    ![Contoso - Pagina Bedrijfsbranding, met nieuwe taaloptie gemarkeerd](media/customize-branding/company-branding-new-language.png)

3. Selecteer op de pagina **Bedrijfsbranding configureren** uw taal (bijvoorbeeld Frans) en voeg vervolgens uw vertaalde gegevens toe op basis van de beschrijvingen in het gedeelte [Aanmeldingspagina](#customize-your-azure-ad-sign-in-page) van Azure AD van dit artikel aanpassen.

4. Selecteer **Opslaan**.

    De **Contoso – Bedrijfsmerkpagina** updates om uw nieuwe Franse configuratie weer te geven.

    ![Contoso - Bedrijfsmerkpagina, met standaardconfiguratie weergegeven](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Uw aangepaste branding toevoegen aan pagina's
Voeg uw aangepaste branding toe aan pagina's door `?whr=yourdomainname`het einde van de URL met de tekst te wijzigen. Deze wijziging werkt op verschillende pagina's, waaronder de multi-factor authenticatie (MFA) setup pagina, de Self-service Password Reset (SSPR) setup pagina, en de aanmelding pagina.

**Voorbeelden:**

**Oorspronkelijke URL:**https://aka.ms/MFASetup<br>
**Aangepaste URL:**`https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**Oorspronkelijke URL:**https://aka.ms/SSPR<br>
**Aangepaste URL:**`https://passwordreset.microsoftonline.com/?whr=contoso.com`

 
