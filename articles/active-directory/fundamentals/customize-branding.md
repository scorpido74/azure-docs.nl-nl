---
title: Huisstijl van uw organisatie aanmelden pagina - Azure Active Directory toevoegen | Microsoft Docs
description: Instructies over het toevoegen van de huisstijl van uw organisatie op de aanmeldingspagina van Azure Active Directory.
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
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7caf6eff9f5bd497ab6c3a500f3940549b198576
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959041"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>Huisstijl van uw organisatie Azure Active Directory-aanmelden pagina toevoegen
Het logo en de aangepaste kleurenschema's van uw organisatie gebruiken om een consistent uiterlijk-en-werking op uw Azure Active Directory (Azure AD) aanmeldingspagina's. Uw aanmeldingspagina's worden weergegeven wanneer gebruikers zich aanmelden bij uw organisatie op basis van een web-apps, zoals Office 365, dat gebruikmaakt van Azure AD als id-provider.

>[!Note]
>Aangepaste huisstijl toe te voegen, moet u Azure Active Directory Premium 1, 2 Premium of Basic-versies gebruiken, of een Office 365-licentie hebben. Zie voor meer informatie over licentieverlening en versies [zich registreren voor Azure AD Premium](active-directory-get-started-premium.md).<br><br>De Azure AD-edities Premium en Basic zijn beschikbaar voor klanten in China via het wereldwijde exemplaar van Azure Active Directory. De Azure AD Premium en Basic-edities worden momenteel niet ondersteund in de Azure-service die wordt beheerd door 21Vianet in China. Neem contact met ons op via het [Azure Active Directory Forum](https://feedback.azure.com/forums/169401-azure-active-directory/) voor meer informatie.

## <a name="customize-your-azure-ad-sign-in-page"></a>Uw Azure AD-aanmeldingspagina aanpassen
U kunt uw Azure AD-aanmelden pagina's aanpassen, die worden weergegeven wanneer gebruikers zich aanmelden bij apps van de tenant-specifieke van uw organisatie, zoals [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com), of als een domeinvariabele, zoals wordtdoorgegeven[ *https://passwordreset.microsoftonline.com/?whr=contoso.com* ](https://passwordreset.microsoftonline.com/?whr=contoso.com).

Uw aangepaste huis stijl wordt niet onmiddellijk weer gegeven wanneer uw gebruikers naar sites gaan,\.zoals www-Office.com. In plaats daarvan wordt de gebruiker heeft om aan te melden voordat uw aangepaste huisstijl weergegeven. Nadat de gebruiker zich heeft aangemeld, kan het 15 minuten of langer duren voordat de huis stijl wordt weer gegeven. 

> [!NOTE]
> Alle huisstijl elementen zijn optioneel. Als u een logo in banner met geen afbeelding opgeeft, wordt de aanmeldingspagina uw logo met een standaardinstallatiekopie van achtergrond van de doelsite (bijvoorbeeld Office 365) weergegeven.<br><br>Bovendien branding-aanmeldingspagina niet meegenomen naar persoonlijke Microsoft-accounts. Als uw gebruikers- of zakelijke gasten zich hebt aangemeld met een persoonlijk Microsoft-account, wordt niet de aanmeldingspagina doorgevoerd in de huisstijl van uw organisatie.

### <a name="to-customize-your-branding"></a>Uw huisstijl aanpassen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, en selecteer vervolgens **huisstijl van bedrijf**, en selecteer vervolgens **configureren**.

    ![Contoso - huisstijl pagina bedrijf, configureren-optie is gemarkeerd](media/customize-branding/company-branding-configure-button.png)

3. Op de **huisstijl configureren** pagina, geeft u een of meer van de volgende informatie.

    >[!Important]
    >Alle aangepaste installatiekopieën die u op deze pagina toevoegt afbeeldingsgrootte (in pixels) hebben, en mogelijk de bestandsgrootte (KB), beperkingen. Vanwege deze beperkingen, heeft moet de meeste waarschijnlijk u een foto-editor gebruiken om de installatiekopieën op maat te maken.

    - **Algemene instellingen**

        ![Een bedrijfshuisstijl pagina met algemene instellingen die zijn voltooid](media/customize-branding/configure-company-branding-general-settings.png)

        - **Taal.** De taal is automatisch ingesteld als standaard en kan niet worden gewijzigd.
        
        - **Achtergrondafbeelding van aanmeldingspagina.** Selecteer een PNG- of JPG-afbeeldingsbestand wordt weergegeven als achtergrond voor de aanmeldingspagina's. 
        
            De installatiekopie mag niet groter zijn dan 1920 x 1080 pixels groot en een bestandsgrootte van minder dan 300 KB moet hebben.

        - **Standaardvaandel logo.** Selecteer een PNG- of jpg-versie van het logo op de aanmeldingspagina wordt weergegeven nadat de gebruiker een gebruikersnaam typt en op de **mijn Apps** portal-pagina.
            
            De afbeelding mag niet groter zijn dan 60 pixels of breder zijn dan 280 pixels. U wordt aangeraden een transparante afbeelding gebruikt, omdat de achtergrond mogelijk niet overeen met de achtergrond van het logo. Het beste ook niet toe te voegen opvulling rond de afbeelding of het ervoor kan zorgen dat uw logo kleine zoeken.

        - **Hint voor gebruikersnaam.** Typ de tekst van de hint die voor gebruikers wordt weergegeven als ze hun gebruikersnaam bent vergeten. Deze tekst moet Unicode, zonder koppelingen of code, en mag maximaal 64 tekens bevatten. Als gasten zich aanmelden bij uw app, het is raadzaam deze hint niet toevoegen.

        - **Tekst van aanmeldingspagina.** Typ de tekst die wordt weergegeven aan de onderkant van de aanmeldingspagina. U kunt deze tekst gebruiken om te communiceren als u meer informatie, zoals het telefoonnummer aan uw helpdesk of een juridische verklaring. Deze tekst moet Unicode en niet langer zijn dan 256 tekens. We raden ook niet met inbegrip van koppelingen of HTML-codes.

    - **Geavanceerde instellingen**
            
        ![Een bedrijfshuisstijl pagina met geavanceerde instellingen voltooid](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **De achtergrondkleur van de aanmeldingspagina.** De hexadecimale kleur opgeven (bijvoorbeeld wit is #FFFFFF) die wordt weergegeven in plaats van uw achtergrondafbeelding in situaties met een lage bandbreedte verbinding. We raden u aan met behulp van de primaire kleur van uw logo in banner of de kleur van uw organisatie.

        - **Afbeelding met vierkant logo.** Selecteer een PNG (bij voorkeur) of JPG-afbeelding van het logo van uw organisatie worden weergegeven aan gebruikers tijdens de installatieprocedure voor de nieuwe Windows 10 Enterprise-apparaten. Deze installatiekopie wordt alleen gebruikt voor Windows-verificatie en wordt alleen weergegeven op tenants die gebruikmaken van [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) voor implementatie of voor wachtwoordinvoer pagina's in andere Windows-10-ervaringen. In sommige gevallen kan het ook worden weer gegeven in het dialoog venster voor toestemming.
        
            De installatiekopie mag niet groter zijn dan 240 x 240 pixels groot en een bestandsgrootte van minder dan 10 KB als ze beschikken. U wordt aangeraden een transparante afbeelding gebruikt, omdat de achtergrond mogelijk niet overeen met de achtergrond van het logo. Het beste ook niet toe te voegen opvulling rond de afbeelding of het ervoor kan zorgen dat uw logo kleine zoeken.
    
        - **Afbeelding met vierkant logo, donkere thema.** Hetzelfde als de bovenstaande afbeelding met vierkant logo. Deze logoafbeelding gebruikt in plaats van de afbeelding met vierkant logo bij gebruik met een donkere achtergrond, zoals met Windows 10 Azure AD verbonden schermen tijdens de out-of-box experience (OOBE).  Als uw logo mooi op wit, donker blauw en zwarte achtergronden, moet u niet om toe te voegen deze installatiekopie. 
        
        - **Optie om aangemeld te blijven weergeven.** U kunt ervoor kiezen om uw gebruikers blijven aangemeld bij Azure AD pas expliciet afmelden. Als u ervoor kiest **Nee**, deze optie verborgen is en gebruikers moeten zich in elke keer dat de browser wordt gesloten en heropend.
        
            >[!Note]
            >Of sommige functies van SharePoint Online en Office 2010 beschikbaar zijn, hangt ervan af of gebruikers ervoor kunnen kiezen aangemeld te blijven. Als u deze instelling op **Nee** instelt, krijgen uw gebruikers mogelijk extra en onverwachte prompts te zien om zich aan te melden.
   

3. Nadat u klaar bent met uw huisstijl toe te voegen, selecteert u **opslaan**.

    Als dit proces uw eerste aangepaste huisstijl configuratie maakt, wordt de standaardwaarde voor uw tenant. Als u aanvullende configuraties hebt, kunt u zult kunnen de standaardconfiguratie te kiezen.
    
    >[!Important]
    >Toevoegen van meer zakelijke branding-configuraties die u kunt uw tenant, moet u **nieuwe taal** op de **Contoso - huisstijl** pagina. Hiermee opent u de **huisstijl configureren** pagina, waar u dezelfde als hierboven stappen kunt.

## <a name="update-your-custom-branding"></a>Uw aangepaste huisstijl bijwerken
Nadat u hebt gemaakt om uw aangepaste huisstijl, kunt u teruggaan en alles wat die u wilt wijzigen.

### <a name="to-edit-your-custom-branding"></a>Uw aangepaste huisstijl bewerken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, en selecteer vervolgens **huisstijl van bedrijf**, en selecteer vervolgens **configureren**.

    ![Contoso - huisstijl pagina, met standaardconfiguratie wordt weergegeven](media/customize-branding/company-branding-default-config.png)

3. Op de **huisstijl configureren** pagina, toevoegen, verwijderen of wijzigen van de gegevens, op basis van de beschrijvingen in de [aanpassen van uw Azure AD-aanmeldingspagina](#customize-your-azure-ad-sign-in-page) sectie van dit artikel.

4. Selecteer **Opslaan**.

   Het kan een uur duren voordat de wijzigingen die u aan de huisstijl van de aanmeldingspagina hebt aangebracht, worden weergegeven.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Taalspecifieke huisstijl toevoegen aan uw directory
U kunt de oorspronkelijke configuratie taal niet wijzigen van de standaardtaal. Als u een configuratie in een andere taal nodig hebt, kunt u echter een nieuwe configuratie maken.

### <a name="to-add-a-language-specific-branding-configuration"></a>Een taalspecifieke huisstijl configuratie toevoegen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, en selecteer vervolgens **huisstijl van bedrijf**, en selecteer vervolgens **nieuwe taal**.

    ![Contoso - huisstijl pagina, met de nieuwe taaloptie gemarkeerd](media/customize-branding/company-branding-new-language.png)

3. Op de **huisstijl configureren** pagina, selecteer uw taal (bijvoorbeeld Frans) en voegt u de vertaalde gegevens, op basis van de beschrijvingen in de [aanpassen van uw Azure AD-aanmeldingspagina](#customize-your-azure-ad-sign-in-page) sectie van dit artikel.

4. Selecteer **Opslaan**.

    De **Contoso-huisstijl** pagina updates om uw nieuwe Franse configuratie weer te geven.

    ![Contoso - huisstijl pagina, met standaardconfiguratie wordt weergegeven](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>Uw aangepaste huisstijl naar pagina's toevoegen
Toevoegen van uw aangepaste huisstijl naar pagina's door het wijzigen van het einde van de URL met de tekst, `?whr=yourdomainname`. Deze wijziging werkt op verschillende pagina's, met inbegrip van de pagina van de installatie van multi-factor Authentication (MFA), de installatiepagina selfservice wachtwoord opnieuw instellen (SSPR) en het teken op de pagina.

**Voorbeelden:**

**Oorspronkelijke URL:** https://aka.ms/MFASetup<br>
**Aangepaste URL:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**Oorspronkelijke URL:** https://aka.ms/SSPR<br>
**Aangepaste URL:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
