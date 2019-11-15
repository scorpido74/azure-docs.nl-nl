---
title: Problemen bij het aanmelden bij een toepassing vanuit het toegangs venster | Microsoft Docs
description: Problemen oplossen met toegang tot een toepassing vanuit het Microsoft Azure AD toegangs venster op myapps.microsoft.com
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082149"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemen bij het aanmelden bij een toepassing vanuit het toegangs venster

Het toegangs venster is een portal op Internet waarmee een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) Cloud toepassingen kan weer geven en starten waartoe de Azure AD-beheerder hen toegang heeft verleend. 

Deze toepassingen worden geconfigureerd namens de gebruiker in de Azure AD-Portal. De toepassing moet op de juiste wijze zijn geconfigureerd en toegewezen aan de gebruiker of een groep waarvan de gebruiker lid is om de toepassing te zien in het toegangs venster.

Het type apps dat een gebruiker kan zien, kan worden weer gegeven in de volgende categorieën:

-   Office 365-toepassingen

-   Toepassingen van micro soft en derden die zijn geconfigureerd met SSO op basis van Federatie

-   SSO-toepassingen op basis van wacht woorden

-   Toepassingen met bestaande SSO-oplossingen

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Zorg ervoor dat u een **browser** gebruikt die voldoet aan de minimale vereisten voor het toegangs venster.

-   Zorg ervoor dat de browser van de gebruiker de URL van de toepassing heeft toegevoegd aan de **vertrouwde sites**.

-   Controleer of de toepassing correct is **geconfigureerd** .

-   Zorg ervoor dat het gebruikers account is **ingeschakeld** voor aanmeldingen.

-   Controleer of het account van de gebruiker **niet is vergrendeld.**

-   Zorg ervoor dat het wacht woord van de gebruiker **niet is verlopen of is verg eten.**

-   Zorg ervoor dat **multi-factor Authentication** de gebruikers toegang niet blokkeert.

-   Zorg ervoor dat het beleid voor **voorwaardelijke toegang** of het beleid voor **identiteits beveiliging** de gebruikers toegang niet blokkeert.

-   Zorg ervoor dat de **contact gegevens** van de verificatie van een gebruiker up-to-date zijn om multi-factor Authentication of beleid voor voorwaardelijke toegang te kunnen afdwingen.

-   Probeer ook de cookies van uw browser te wissen en opnieuw aan te melden.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Browser vereisten voor de vergadering voor het toegangs venster

Het toegangs venster vereist een browser die Java script ondersteunt en waarvoor CSS is ingeschakeld. Als u eenmalige aanmelding (SSO) op basis van wacht woorden in het toegangs venster wilt gebruiken, moet de uitbrei ding van het toegangs paneel worden geïnstalleerd in de browser van de gebruiker. Deze uitbrei ding wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor SSO op basis van een wacht woord.

Voor SSO op basis van een wacht woord kunnen de browsers van de eind gebruiker het volgende zijn:

-   Internet Explorer 8, 9, 10, 11, op Windows 7 of hoger

-   Micro soft Edge in Windows 10 jubileum Edition of hoger

-   Chrome--op Windows 7 of hoger en op MacOS X of hoger

-   Firefox 26,0 of hoger, op Windows XP SP2 of hoger, en op Mac OS X 10,6 of hoger

## <a name="how-to-install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Volg de onderstaande stappen om de browser uitbreiding van het toegangs venster te installeren:

1.  Open het [toegangs venster](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als een **gebruiker** in uw Azure AD.

2.  Klik op een **wacht woord-SSO-toepassing** in het toegangs venster.

3.  Selecteer **nu installeren**bij de vraag of u de software wilt installeren.

4.  Op basis van uw browser wordt u omgeleid naar de download koppeling. **Voeg** de uitbrei ding toe aan uw browser.

5.  Als uw browser vraagt, selecteert u de extensie **inschakelen** of **toestaan** .

6.  Nadat de installatie is voltooid, start u de browser sessie **opnieuw** .

7.  Meld u aan bij het toegangs venster en controleer of u uw wacht woord-SSO-toepassingen kunt **starten** .

U kunt de uitbrei ding voor Chrome en micro soft Edge ook downloaden via de onderstaande rechtstreekse koppelingen:

-   [Uitbrei ding Chrome toegangs paneel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Uitbrei ding toegang tot micro soft Edge-paneel](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Federatieve eenmalige aanmelding configureren voor een toepassing in de Azure AD-galerie

Voor alle toepassingen in de Azure AD-galerie die is ingeschakeld met de Enter prise-functie voor eenmalige aanmelding, is een stapsgewijze zelf studie beschikbaar. U kunt toegang krijgen tot de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor een gedetailleerde stapsgewijze uitleg.

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   Een toepassing toevoegen vanuit de Azure AD-galerie

-   [De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-meta gegevens en-certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   [Meta gegevens van Azure AD in de toepassing configureren (aanmeldings-URL, verlener, afmeldings-URL en certificaat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Gebruikers toewijzen aan een toepassing

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Typ de naam van de toepassing in het tekstvak **Geef een naam** op in de sectie **toevoegen vanuit de galerie** .

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam ervan wijzigen in het tekstvak **naam** .

9.  Klik op de knop **toevoegen** om de toepassing toe te voegen.

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Eenmalige aanmelding configureren voor een toepassing vanuit de Azure AD-galerie

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer **op SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.

9. Voer de vereiste waarden in het **domein en de url's in.** U moet deze waarden ophalen van de leverancier van de toepassing.

   1. De aanmeldings-URL is een vereiste waarde om de toepassing te configureren als door SP geïnitieerde SSO. Voor sommige toepassingen is de id ook een vereiste waarde.

   2. De antwoord-URL is een vereiste waarde om de toepassing te configureren als een door IdP geïnitieerde SSO. Voor sommige toepassingen is de id ook een vereiste waarde.

10. **Optioneel:** Klik op **Geavanceerde URL-instellingen weer geven** als u de niet-vereiste waarden wilt zien.

11. Selecteer in de **gebruikers kenmerken**de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** .

12. **Optioneel:** Klik op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

    2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

13. Klik op **&lt;toepassings naam&gt;configureren** voor toegang tot documentatie over het configureren van eenmalige aanmelding in de toepassing. Daarnaast hebt u de Url's van de meta gegevens en het certificaat dat vereist is voor de installatie van SSO met de toepassing.

14. Klik op **Opslaan** om de configuratie op te slaan.

15. Gebruikers toewijzen aan de toepassing.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden

Volg de onderstaande stappen om de gebruikers-id te selecteren of gebruikers kenmerken toe te voegen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u hier wilt weer geven niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer in de sectie **gebruikers kenmerken** de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** . De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE]
   >Azure AD Selecteer de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga naar het [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) via de sectie NameIDPolicy voor meer informatie.
   >
   >

9. Om gebruikers kenmerken toe te voegen, klikt u op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>De meta gegevens of het certificaat van Azure AD downloaden

Volg de onderstaande stappen om de meta gegevens van de toepassing of het certificaat te downloaden van Azure AD:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Ga naar de sectie **SAML-handtekening certificaat** en klik vervolgens op kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

   Azure AD biedt geen URL om de meta gegevens op te halen. De meta gegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Federatieve eenmalige aanmelding configureren voor een toepassing buiten de galerie

Als u een niet-galerie toepassing wilt configureren, moet u Azure AD Premium hebben en de toepassing biedt ondersteuning voor SAML 2,0. Ga voor meer informatie over Azure AD-versies naar [Azure ad-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

-   De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)

-   [Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-meta gegevens en-certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   Meta gegevens van Azure AD in de toepassing configureren (aanmeldings-URL, verlener, afmeldings-URL en certificaat)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing die zich niet in de Azure AD-galerie bevindt:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6. Klik op **niet-galerie toepassing** in het gedeelte **uw eigen app toevoegen** .

7. Voer de naam van de toepassing in het tekstvak **naam** in.

8. Klik op de knop **toevoegen** om de toepassing toe te voegen.

9. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

10. Selecteer op **SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst

11. Voer de vereiste waarden in het **domein en de url's in.** U moet deze waarden ophalen van de leverancier van de toepassing.

    1. Als u de toepassing wilt configureren als door IdP geïnitieerde SSO, voert u de antwoord-URL en de id in.

    2. **Optioneel:** De aanmeldings-URL is een vereiste waarde om de toepassing te configureren als door SP geïnitieerde SSO.

12. Selecteer in de **gebruikers kenmerken**de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** .

13. **Optioneel:** Klik op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

    2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

14. Klik op **&lt;toepassings naam&gt;configureren** voor toegang tot documentatie over het configureren van eenmalige aanmelding in de toepassing. Daarnaast hebt u Azure AD-Url's en een certificaat vereist voor de toepassing.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden

Volg de onderstaande stappen om de gebruikers-id te selecteren of gebruikers kenmerken toe te voegen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer in de sectie **gebruikers kenmerken** de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** . De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE]
   >Azure AD Selecteer de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga naar het [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) via de sectie NameIDPolicy voor meer informatie.
   >
   >

9. Om gebruikers kenmerken toe te voegen, klikt u op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   2 Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>De meta gegevens of het certificaat van Azure AD downloaden

Volg de onderstaande stappen om de meta gegevens van de toepassing of het certificaat te downloaden van Azure AD:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Ga naar de sectie **SAML-handtekening certificaat** en klik vervolgens op kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

   Azure AD biedt geen URL om de meta gegevens op te halen. De meta gegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   Een toepassing toevoegen vanuit de Azure AD-galerie

-   De toepassing configureren voor eenmalige aanmelding met een wacht woord

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Typ de naam van de toepassing in het tekstvak **Geef een naam** op in de sectie **toevoegen vanuit de galerie** .

7.  Zoek en selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam ervan wijzigen in het tekstvak **naam** .

9.  Klik op de knop **toevoegen** om de toepassing toe te voegen.

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer de **aanmeldings modus op basis van wacht woord.**

9. Gebruikers toewijzen aan de toepassing.

10. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing buiten de galerie

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   [Een niet-galerie toepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Een niet-galerie toepassing toevoegen

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Klik op **toepassing voor niet-galerie.**

7.  Voer de naam van uw toepassing in het tekstvak **naam** in. Selecteer **toevoegen.**

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer de **aanmeldings modus op basis van wacht woord.**

9. Voer de **aanmeldings-URL**in. Dit is de URL waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldings velden zichtbaar zijn op de URL.

10. Gebruikers toewijzen aan de toepassing.

11. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Direct toewijzen van een gebruiker aan een toepassing

Als u wilt toewijzen een of meer gebruikers rechtstreeks aan een toepassing, de volgende stappen uit te voeren:

1. Open de [ **Azure-portal** ](https://portal.azure.com/) en meld u aan als een **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ in **' Azure Active Directory**' in het zoekvak van filter en selecteer de **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weergeven die hier niet ziet, gebruikt u de **Filter** besturingselement aan de bovenkant van de **lijst met alle toepassingen** en stel de **weergeven** optie naar **alle Toepassingen.**

6. Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Klik op de **toevoegen** knop boven de **gebruikers en groepen** lijst om de **toevoegen toewijzing** deelvenster.

9. Klik op de **gebruikers en groepen** selector vanaf de **toevoegen toewijzing** deelvenster.

10. Typ in het **volledige naam** of **e-mailadres** van de gebruiker die u geïnteresseerd bent in toewijzen in de **zoeken op naam of e-mailadres** zoekvak.

11. Beweeg de muisaanwijzer over de **gebruiker** in de lijst om weer te geven een **selectievakje**. Klik op het selectievakje naast de profielfoto van de gebruiker of het logo om toe te voegen van de gebruiker naar de **geselecteerde** lijst.

12. **Optioneel:** als u wilt **toevoegen van meer dan één gebruiker**, type in een andere **volledige naam** of **e-mailadres** in de **zoeken op naam of e-mailadres** zoekvak en klik op het selectievakje voor deze gebruiker toevoegen aan de **geselecteerde** lijst.

13. Wanneer u klaar bent met gebruikers te selecteren, klikt u op de **Selecteer** knop aan de lijst met gebruikers en groepen die moeten worden toegewezen aan de toepassing wilt toevoegen.

14. **Optioneel:** klikt u op de **rol selecteren** selector in de **toevoegen toewijzing** deelvenster te selecteren van een rol toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de **toewijzen** knop om de toepassing aan de geselecteerde gebruikers te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen in het toegangs venster starten.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor probleem oplossing niet het probleem oplossen

Open een ondersteunings ticket met de volgende informatie, indien beschikbaar:

-   ID correlatie fout

-   UPN (e-mail adres van gebruiker)

-   TenantID

-   Browser type

-   Tijd zone en tijd/tijds duur tijdens fout

-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[Geef single sign-on bij uw apps met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

