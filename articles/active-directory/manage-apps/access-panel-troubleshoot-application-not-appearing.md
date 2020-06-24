---
title: Een toegewezen toepassing wordt niet weer gegeven in het toegangs venster | Microsoft Docs
description: Problemen oplossen met een toepassing die niet wordt weer gegeven in het toegangs venster
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2018
ms.author: kenwith
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69f5196484b841e8f0de72ce52ae48e00963f6f5
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760992"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Een toegewezen toepassing wordt niet weer gegeven in het toegangs venster

Het toegangs venster is een webgebaseerde Portal, waarmee een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) Cloud toepassingen kan weer geven en starten waartoe de Azure AD-beheerder hen toegang heeft verleend. Deze toepassingen worden geconfigureerd namens de gebruiker in de Azure AD-Portal. De toepassing moet op de juiste wijze zijn geconfigureerd en toegewezen aan de gebruiker of een groep waarvan de gebruiker lid is om de toepassing te zien in het toegangs venster.

Het type apps dat een gebruiker kan zien, kan worden weer gegeven in de volgende categorieën:

-   Office 365-toepassingen

-   Toepassingen van micro soft en derden die zijn geconfigureerd met SSO op basis van Federatie

-   SSO-toepassingen op basis van wacht woorden

-   Toepassingen met bestaande SSO-oplossingen

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Als een toepassing is toegevoegd aan een gebruiker, meldt u zich na een paar minuten opnieuw aan en weer aan bij het toegangs venster van de gebruiker om te zien of de toepassing is toegevoegd.

-   Als een licentie alleen is verwijderd van een gebruiker of groep, kan de gebruiker enige tijd in beslag nemen, afhankelijk van de grootte en complexiteit van de groep voor wijzigingen die moeten worden aangebracht. Sta extra tijd toe voordat u zich aanmeldt bij het toegangs venster.

## <a name="problems-related-to-application-configuration"></a>Problemen met betrekking tot toepassings configuratie

Een toepassing wordt mogelijk niet weer gegeven in het toegangs venster van een gebruiker omdat deze niet correct is geconfigureerd. Hieronder vindt u enkele manieren waarop u problemen kunt oplossen met betrekking tot toepassings configuratie:

-   [Federatieve eenmalige aanmelding configureren voor een toepassing in de Azure AD-galerie](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Federatieve eenmalige aanmelding configureren voor een toepassing buiten de galerie](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Een eenmalige aanmelding voor een wacht woord configureren voor een toepassing in de Azure AD-galerie](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Een eenmalige aanmelding voor een wacht woord voor een niet-galerie toepassing configureren](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Federatieve eenmalige aanmelding configureren voor een toepassing in de Azure AD-galerie

Voor alle toepassingen in de Azure AD-galerie die is ingeschakeld met de Enter prise-functionaliteit voor eenmalige aanmelding, is een stapsgewijze zelf studie beschikbaar. U kunt toegang krijgen tot de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor een gedetailleerde stapsgewijze uitleg.

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-meta gegevens en-certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   [Meta gegevens van Azure AD in de toepassing configureren (aanmeldings-URL, verlener, afmeldings-URL en certificaat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Typ de naam van de toepassing in het tekstvak **Geef een naam** op in de sectie **toevoegen vanuit de galerie** .

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam ervan wijzigen in het tekstvak **naam** .

9.  Klik op de knop **toevoegen** om de toepassing toe te voegen.

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Eenmalige aanmelding configureren voor een toepassing vanuit de Azure AD-galerie

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer **op SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.

9. Voer de vereiste waarden in het **domein en de url's in.** U moet deze waarden ophalen van de leverancier van de toepassing.

   1. De aanmeldings-URL is een vereiste waarde om de toepassing te configureren als door SP geïnitieerde SSO. Voor sommige toepassingen is de id ook een vereiste waarde.

   2. Als u de toepassing wilt configureren als door IdP geïnitieerde SSO, de antwoord-URL is een vereiste waarde. Voor sommige toepassingen is de id ook een vereiste waarde.

10. **Optioneel:** Klik op **Geavanceerde URL-instellingen weer geven** als u de niet-vereiste waarden wilt zien.

11. Selecteer in de **gebruikers kenmerken**de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** .

12. **Optioneel:** Klik op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

    2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

13. Klik **op &lt; toepassings naam &gt; configureren** voor toegang tot documentatie over het configureren van eenmalige aanmelding in de toepassing. Daarnaast hebt u de Url's voor meta gegevens en het certificaat dat vereist is voor het instellen van SSO met de toepassing.

14. Klik op **Opslaan** om de configuratie op te slaan.

15. Gebruikers toewijzen aan de toepassing.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden

Volg de onderstaande stappen om de gebruikers-id te selecteren of gebruikers kenmerken toe te voegen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u hier wilt weer geven niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer in de sectie **gebruikers kenmerken** de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** . De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE] 
   >Azure AD selecteert de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga naar het [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) via de sectie NameIDPolicy voor meer informatie.
   >
   >

9. Om gebruikers kenmerken toe te voegen, klikt u op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   2. Klik op **opslaan.** Het nieuwe kenmerk wordt weer geven in de tabel.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>De meta gegevens of het certificaat van Azure AD downloaden

Volg de onderstaande stappen om de meta gegevens van de toepassing of het certificaat te downloaden van Azure AD:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Ga naar de sectie **SAML-handtekening certificaat** en klik vervolgens op kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

   Azure AD biedt geen URL om de meta gegevens op te halen. De meta gegevens kunnen alleen worden opgehaald als een XML-bestand.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Federatieve eenmalige aanmelding configureren voor een toepassing buiten de galerie

Als u een niet-galerie toepassing wilt configureren, moet u Azure AD Premium hebben en de toepassing biedt ondersteuning voor SAML 2,0. Ga voor meer informatie over Azure AD-versies naar [Azure ad-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

-   [De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-meta gegevens en-certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   [Meta gegevens van Azure AD in de toepassing configureren (aanmeldings-URL, verlener, afmeldings-URL en certificaat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing die zich niet in de Azure AD-galerie bevindt:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6. Klik op **niet-galerie toepassing** in het gedeelte **uw eigen app toevoegen** .

7. Voer de naam van de toepassing in het tekstvak **naam** in.

8. Klik op de knop **toevoegen** om de toepassing toe te voegen.

9. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

10. Selecteer op **SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.

11. Voer de vereiste waarden in het **domein en de url's in.** U moet deze waarden ophalen van de leverancier van de toepassing.

    1. Als u de toepassing wilt configureren als door IdP geïnitieerde SSO, voert u de antwoord-URL en de id in.

    2.  **Optioneel:** De aanmeldings-URL is een vereiste waarde om de toepassing te configureren als door SP geïnitieerde SSO.

12. Selecteer in de **gebruikers kenmerken**de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** .

13. **Optioneel:** Klik op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

    2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

14. Klik **op &lt; toepassings naam &gt; configureren** voor toegang tot documentatie over het configureren van eenmalige aanmelding in de toepassing. Daarnaast hebt u Azure AD-Url's en-certificaten die vereist zijn voor de toepassing.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden

Volg de onderstaande stappen om de gebruikers-id te selecteren of gebruikers kenmerken toe te voegen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer in de sectie **gebruikers kenmerken** de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** . De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE] 
   >Azure AD selecteert de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga naar het [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) via de sectie NameIDPolicy voor meer informatie.
   >
   >

9. Om gebruikers kenmerken toe te voegen, klikt u op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>De meta gegevens of het certificaat van Azure AD downloaden

Volg de onderstaande stappen om de meta gegevens van de toepassing of het certificaat te downloaden van Azure AD:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Ga naar de sectie **SAML-handtekening certificaat** en klik vervolgens op kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

Azure AD biedt geen URL om de meta gegevens op te halen. De meta gegevens kunnen alleen worden opgehaald als een XML-bestand.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Typ de naam van de toepassing in het tekstvak **Geef een naam** op in de sectie **toevoegen vanuit de galerie** .

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam ervan wijzigen in het tekstvak **naam** .

9.  Klik op de knop **toevoegen** om de toepassing toe te voegen.

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

#### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer de **aanmeldings modus op basis van wacht woord.**

9. [Gebruikers toewijzen aan de toepassing](#how-to-assign-a-user-to-an-application-directly).

10. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing buiten de galerie

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   [Een niet-galerie toepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Een niet-galerie toepassing toevoegen

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Klik op **toepassing voor niet-galerie.**

7.  Voer de naam van uw toepassing in het tekstvak **naam** in. Selecteer **toevoegen.**

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    1.  Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6.  Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7.  Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8.  Selecteer de **aanmeldings modus op basis van wacht woord.**

9.  Voer de **aanmeldings-URL**in. Dit is de URL waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldings velden zichtbaar zijn op de URL.

10. [Gebruikers toewijzen aan de toepassing](#how-to-assign-a-user-to-an-application-directly).

11. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemen met betrekking tot het toewijzen van toepassingen aan gebruikers

Een gebruiker ziet mogelijk geen toepassing in het toegangs venster, omdat deze niet aan de toepassing is toegewezen. Hieronder vindt u enkele manieren om te controleren:

-   [Controleren of een gebruiker aan de toepassing is toegewezen](#check-if-a-user-is-assigned-to-the-application)

-   [Direct toewijzen van een gebruiker aan een toepassing](#how-to-assign-a-user-to-an-application-directly)

-   [Controleren of een gebruiker is toegewezen aan een licentie die betrekking heeft op de toepassing](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Een licentie toewijzen aan een gebruiker](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Controleren of een gebruiker aan de toepassing is toegewezen

Volg de onderstaande stappen om te controleren of een gebruiker aan de toepassing is toegewezen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

6. **Zoek** de naam van de toepassing in kwestie.

7. Klik op **gebruikers en groepen**.

8. Controleer of de gebruiker is toegewezen aan de toepassing.

   * Als dat niet het geval is, volgt u de stappen in ' een gebruiker rechtstreeks aan een toepassing toewijzen '.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Direct toewijzen van een gebruiker aan een toepassing

Volg de onderstaande stappen om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder**.

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer in de lijst de toepassing waaraan u een gebruiker wilt toewijzen.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige naam** of het **e-mail adres** van de gebruiker die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast de foto of het logo van het profiel van de gebruiker om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen in het toegangs venster starten.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Controleren of een gebruiker een licentie heeft die betrekking heeft op de toepassing

Volg de onderstaande stappen om de toegewezen licenties van een gebruiker te controleren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7. Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

   * Als de gebruiker is toegewezen aan een Office-licentie, kunnen de Office-toepassingen van de eerste partij worden weer gegeven in het toegangs venster van de gebruiker.

### <a name="how-to-assign-a-user-a-license"></a>Een licentie toewijzen aan een gebruiker 

Volg de onderstaande stappen om een licentie aan een gebruiker toe te wijzen:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  Klik op de knop **toewijzen** .

9.  Selecteer **een of meer producten** uit de lijst met beschik bare producten.

10. **Optioneel** klikt u op het item **toewijzings opties** om producten nauw keurig toe te wijzen. Klik op **OK** wanneer dit is voltooid.

11. Klik op de knop **toewijzen** om deze licenties aan deze gebruiker toe te wijzen.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemen met betrekking tot het toewijzen van toepassingen aan groepen

Een gebruiker ziet mogelijk een toepassing in het toegangs venster, omdat deze deel uitmaakt van een groep waaraan de toepassing is toegewezen. Hieronder vindt u enkele manieren om te controleren:

-   [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [Een toepassing rechtstreeks toewijzen aan een groep](#how-to-assign-an-application-to-a-group-directly)

-   [Controleren of een gebruiker deel uitmaakt van een groep die is toegewezen aan een licentie](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Een licentie toewijzen aan een groep](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>De groepslid maatschappen van een gebruiker controleren

Volg de onderstaande stappen om het lidmaatschap van een groep te controleren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7. Klik op **groepen**.

8. Controleer of uw gebruiker deel uitmaakt van een groep die is toegewezen aan de toepassing.

   * Als u de gebruiker uit de groep wilt verwijderen, **klikt u op de rij** van de groep en selecteert u verwijderen.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Een toepassing rechtstreeks toewijzen aan een groep

Volg de onderstaande stappen om een of meer groepen rechtstreeks toe te wijzen aan een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder**.

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer in de lijst de toepassing waaraan u een gebruiker wilt toewijzen.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige groeps naam** van de groep die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **groep** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast het profiel foto of logo van de groep om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één groep wilt toevoegen**, typt u een andere **volledige groeps naam** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze groep aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van groepen, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de groepen die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing toe te wijzen aan de geselecteerde groepen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen in het toegangs venster starten.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Controleren of een gebruiker deel uitmaakt van een groep die is toegewezen aan een licentie

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7. Klik op **groepen**.

8. Klik op de rij van een specifieke groep.

9. Klik op **licenties** om te zien welke licenties de groep hieraan heeft toegewezen.

   * Als de groep is toegewezen aan een Office-licentie, kunnen bepaalde Office-toepassingen van de eerste partij worden weer gegeven in het toegangs venster van de gebruiker.

### <a name="how-to-assign-a-license-to-a-group"></a>Een licentie toewijzen aan een groep

Volg de onderstaande stappen om een licentie toe te wijzen aan een groep:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle groepen**.

6.  **Zoek** de groep waarin u bent geïnteresseerd en **Klik op de rij** die u wilt selecteren.

7.  Klik op **licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8.  Klik op de knop **toewijzen** .

9.  Selecteer **een of meer producten** uit de lijst met beschik bare producten.

10. **Optioneel** klikt u op het item **toewijzings opties** om producten nauw keurig toe te wijzen. Klik op **OK** wanneer dit is voltooid.

11. Klik op de knop **toewijzen** om deze licenties aan deze groep toe te wijzen. Dit kan enige tijd duren, afhankelijk van de grootte en complexiteit van de groep.

>[!NOTE]
>Als u dit sneller wilt doen, kunt u overwegen om een tijdelijke licentie rechtstreeks aan de gebruiker toe te wijzen. 
>
>

## <a name="next-steps"></a>Volgende stappen
[Nieuwe gebruikers toevoegen aan Azure Active Directory (Engelstalig artikel)](./../fundamentals/add-users-azure-active-directory.md)

