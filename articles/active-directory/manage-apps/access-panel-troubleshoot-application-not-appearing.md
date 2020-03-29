---
title: Een toegewezen aanvraag wordt niet weergegeven in het toegangspaneel | Microsoft Documenten
description: Problemen oplossen waarom een toepassing niet wordt weergegeven in het access-paneel
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
ms.topic: article
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67272758"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Een toegewezen toepassing wordt niet weergegeven in het toegangspaneel

Het Access-paneel is een webportal waarmee een gebruiker met een werk- of schoolaccount in Azure Active Directory (Azure AD) cloudtoepassingen kan weergeven en starten waartoe de Azure AD-beheerder hem toegang heeft verleend. Deze toepassingen zijn geconfigureerd namens de gebruiker in de Azure AD-portal. De toepassing moet correct zijn geconfigureerd en toegewezen aan de gebruiker of een groep waarvan de gebruiker lid is om de toepassing in het toegangspaneel te zien.

Het type apps dat een gebruiker kan zien, valt in de volgende categorieën:

-   Office 365-toepassingen

-   Microsoft en toepassingen van derden geconfigureerd met federatiegebaseerde SSO

-   Op wachtwoorden gebaseerde SSO-toepassingen

-   Toepassingen met bestaande SSO-oplossingen

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Als een toepassing net aan een gebruiker is toegevoegd, probeert u zich na een paar minuten opnieuw aan en uit te loggen in het toegangspaneel van de gebruiker om te zien of de toepassing is toegevoegd.

-   Als een licentie net is verwijderd uit een gebruiker of groep, kan het lang duren voordat de gebruiker hierlid van is, afhankelijk van de grootte en complexiteit van de groep om wijzigingen aan te brengen. Geef extra tijd voor het aanmelden bij het toegangspaneel.

## <a name="problems-related-to-application-configuration"></a>Problemen in verband met toepassingsconfiguratie

Een toepassing wordt mogelijk niet weergegeven in het toegangspaneel van een gebruiker omdat deze niet goed is geconfigureerd. Hieronder vindt u enkele manieren waarop u problemen met de configuratie van toepassingen oplossen:

-   [Fed-aanmelding configureren voor een Azure AD-galerietoepassing](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Fed-single sign-on configureren voor een niet-galerietoepassing](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Een enkele aanmeldingstoepassing voor een Azure AD-galerietoepassing configureren voor een malige aanmeldingstoepassing voor een Azure AD-galerietoepassing](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Een enkele aanmeldingstoepassing voor een niet-galerietoepassing configureren voor een aanvraag voor een wachtwoord](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Fed-aanmelding configureren voor een Azure AD-galerietoepassing

Alle toepassingen in de Azure AD-galerie die zijn ingeschakeld met de mogelijkheid voor één aanmelding voor onderneming, hebben een stapsgewijze zelfstudie beschikbaar. U hebt toegang tot de [lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor een gedetailleerde stapsgewijze richtlijnen.

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het gaat doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De metagegevenswaarden van de toepassing configureren in Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecteer Gebruikers-id en voeg gebruikerskenmerken toe die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-metagegevens en -certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   [Azure AD-metagegevenswaarden configureren in de toepassing (Aanmelding op URL, Uitgever, URL van afmelden en certificaat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de onderstaande stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  De [Azure-portal openen](https://portal.azure.com) en u aanmelden als **globale beheerder** of **medebeheerder**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6.  Typ in het tekstvak **Een naam invoeren** in de sectie Toevoegen vanuit de **galeriede** de naam van de toepassing.

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, u de naam wijzigen in het tekstvak **Naam.**

9.  Klik **op Knop Toevoegen** om de toepassing toe te voegen.

Na een korte periode u het configuratievenster van de toepassing zien.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Eenmalige aanmelding configureren voor een toepassing uit de Azure AD-galerie

Voer de onderstaande stappen uit om een malige aanmelding voor een toepassing te configureren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer **OP SAML gebaseerde aanmelding** in de vervolgkeuzelijst **Modus.**

9. Voer de vereiste waarden in **domein en URL's in.** U moet deze waarden krijgen van de leverancier van de toepassing.

   1. Als u de toepassing wilt configureren als sso die door SP is geïnitieerd, is de URL van Sign on een vereiste waarde. Voor sommige toepassingen is de id ook een vereiste waarde.

   2. Als u de toepassing wilt configureren als SSO die door IdP is geïnitieerd, is de URL van het antwoord een vereiste waarde. Voor sommige toepassingen is de id ook een vereiste waarde.

10. **Optioneel:** klik op **Geavanceerde URL-instellingen weergeven** als u de niet-vereiste waarden wilt zien.

11. Selecteer in de **gebruikerskenmerken**de unieke id voor uw gebruikers in de vervolgkeuzelijst **Gebruikers-id.**

12. **Optioneel:** klik op **Alle andere gebruikerskenmerken weergeven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. klik **op Kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolgkeuzelijst.

    2. klik **op Opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

13. klik ** &lt;op&gt; Toepassingsnaam configureren** om toegang te krijgen tot documentatie over het configureren van eenmalige aanmelding in de toepassing. U hebt ook de metagegevens-URL's en het certificaat die nodig zijn om SSO met de toepassing in te stellen.

14. klik **op Opslaan** om de configuratie op te slaan.

15. Gebruikers toewijzen aan de toepassing.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecteer Gebruikers-id en voeg gebruikerskenmerken toe die naar de toepassing moeten worden verzonden

Voer de onderstaande stappen uit om de gebruikers-id te selecteren of gebruikerskenmerken toe te voegen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u hier wilt weergeven niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u hebt geconfigureerd voor eenmalige aanmelding.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer onder de sectie **Gebruikerskenmerken** de unieke id voor uw gebruikers in de vervolgkeuzelijst **Gebruikers-id.** De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE] 
   >Azure AD selecteert de indeling voor het kenmerk NameID (User Identifier) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in de SAML AuthRequest. Ga voor meer informatie naar het artikel [Single Sign-On SAML protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) onder de sectie NameIDPolicy.
   >
   >

9. Als u gebruikerskenmerken wilt toevoegen, klikt u op **Alle andere gebruikerskenmerken weergeven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. klik **op Kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolgkeuzelijst.

   2. klik **op Opslaan.** U ziet het nieuwe kenmerk in de tabel.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>De metagegevens of -certificaat van Azure AD downloaden

Voer de onderstaande stappen uit om de metagegevens of het certificaat van de toepassing te downloaden van Azure AD:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u hebt geconfigureerd voor eenmalige aanmelding.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Ga naar de sectie **SAML-ondertekeningscertificaat** en klik op **Kolomwaarde downloaden.** Afhankelijk van wat de toepassing vereist voor het configureren van één aanmelding, ziet u de optie om de XML met ametjes of het certificaat te downloaden.

   Azure AD geeft geen URL om de metagegevens op te halen. De metagegevens kunnen alleen worden opgehaald als een XML-bestand.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Fed-single sign-on configureren voor een niet-galerietoepassing

Als u een niet-galerietoepassing wilt configureren, moet u Azure AD premium hebben en ondersteunt de toepassing SAML 2.0. Ga naar Azure AD-prijzen voor meer informatie over Azure [AD-versies.](https://azure.microsoft.com/pricing/details/active-directory/)

-   [De metagegevenswaarden van de toepassing configureren in Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selecteer Gebruikers-id en voeg gebruikerskenmerken toe die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-metagegevens en -certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   [Azure AD-metagegevenswaarden configureren in de toepassing (Aanmelding op URL, Uitgever, URL van afmelden en certificaat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>De metagegevenswaarden van de toepassing configureren in Azure AD (Sign on URL, Identifier, Reply URL)

Voer de onderstaande stappen uit om een enkele aanmelding te configureren voor een toepassing die zich niet in de Azure AD-galerie bevindt:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6. klik **op Niet-galerietoepassing** in de sectie **Uw eigen app toevoegen.**

7. Voer de naam van de toepassing in het tekstvak **Naam** in.

8. Klik **op Knop Toevoegen** om de toepassing toe te voegen.

9. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

10. Selecteer **OP SAML gebaseerde aanmelding** in de vervolgkeuzelijst **Modus.**

11. Voer de vereiste waarden in **domein en URL's in.** U moet deze waarden krijgen van de leverancier van de toepassing.

    1. Als u de toepassing wilt configureren als SSO die door IdP is geïnitieerd, voert u de URL Van het antwoord en de id in.

    2.  **Optioneel:** Als u de toepassing wilt configureren als sso die door SP is geïnitieerd, is de URL van Sign on een vereiste waarde.

12. Selecteer in de **gebruikerskenmerken**de unieke id voor uw gebruikers in de vervolgkeuzelijst **Gebruikers-id.**

13. **Optioneel:** klik op **Alle andere gebruikerskenmerken weergeven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. klik **op Kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolgkeuzelijst.

    2. Klik **op Opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

14. klik ** &lt;op&gt; Toepassingsnaam configureren** om toegang te krijgen tot documentatie over het configureren van eenmalige aanmelding in de toepassing. U hebt ook Azure AD-URL's en certificaten die nodig zijn voor de toepassing.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecteer Gebruikers-id en voeg gebruikerskenmerken toe die naar de toepassing moeten worden verzonden

Voer de onderstaande stappen uit om de gebruikers-id te selecteren of gebruikerskenmerken toe te voegen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u hebt geconfigureerd voor eenmalige aanmelding.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer onder de sectie **Gebruikerskenmerken** de unieke id voor uw gebruikers in de vervolgkeuzelijst **Gebruikers-id.** De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE] 
   >Azure AD selecteert de indeling voor het kenmerk NameID (User Identifier) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in de SAML AuthRequest. Ga voor meer informatie naar het artikel [Single Sign-On SAML protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) onder de sectie NameIDPolicy.
   >
   >

9. Als u gebruikerskenmerken wilt toevoegen, klikt u op **Alle andere gebruikerskenmerken weergeven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. klik **op Kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolgkeuzelijst.

   2. Klik **op Opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>De metagegevens of -certificaat van Azure AD downloaden

Voer de onderstaande stappen uit om de metagegevens of het certificaat van de toepassing te downloaden van Azure AD:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u hebt geconfigureerd voor eenmalige aanmelding.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Ga naar de sectie **SAML-ondertekeningscertificaat** en klik op **Kolomwaarde downloaden.** Afhankelijk van wat de toepassing vereist voor het configureren van één aanmelding, ziet u de optie om de XML met ametjes of het certificaat te downloaden.

Azure AD geeft geen URL om de metagegevens op te halen. De metagegevens kunnen alleen worden opgehaald als een XML-bestand.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Eenmalige aanmelding voor een Azure AD-galeriestoepassing configureren

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het gaat doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De toepassing voor eenmalige aanmelding voor wachtwoorden configureren](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de onderstaande stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  De [Azure-portal openen](https://portal.azure.com) en u aanmelden als **globale beheerder** of **medebeheerder**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6.  Typ in het tekstvak **Een naam invoeren** in de sectie Toevoegen vanuit de **galeriede** de naam van de toepassing.

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, u de naam wijzigen in het tekstvak **Naam.**

9.  Klik **op Knop Toevoegen** om de toepassing toe te voegen.

Na een korte periode u het configuratievenster van de toepassing zien.

#### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing voor eenmalige aanmelding voor wachtwoorden configureren

Voer de onderstaande stappen uit om een malige aanmelding voor een toepassing te configureren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer de modus **Aanmelding op basis van wachtwoord.**

9. [Gebruikers toewijzen aan de toepassing](#how-to-assign-a-user-to-an-application-directly).

10. Bovendien u namens de gebruiker ook referenties opgeven door de rijen van de gebruikers te selecteren en op **Referenties bijwerken** te klikken en namens de gebruikers de gebruikersnaam en het wachtwoord in te voeren. Anders worden gebruikers gevraagd om de referenties zelf in te voeren bij de lancering.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding voor een niet-galerietoepassing configureren voor een aanvraag voor een niet-galerie

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het gaat doen:

-   [Een niet-galerietoepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing voor eenmalige aanmelding voor wachtwoorden configureren](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Een niet-galerietoepassing toevoegen

Voer de onderstaande stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure-portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **medebeheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6.  klik **op Niet-galerietoepassing.**

7.  Voer de naam van uw toepassing in het tekstvak **Naam** in. Selecteer **Toevoegen.**

Na een korte periode u het configuratievenster van de toepassing zien.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>De toepassing voor eenmalige aanmelding voor wachtwoorden configureren

Voer de onderstaande stappen uit om een malige aanmelding voor een toepassing te configureren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    1.  Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6.  Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7.  Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8.  Selecteer de modus **Aanmelding op basis van wachtwoord.**

9.  Voer de **aanmeldings-URL in**. Dit is de URL waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de URL.

10. [Gebruikers toewijzen aan de toepassing](#how-to-assign-a-user-to-an-application-directly).

11. Bovendien u namens de gebruiker ook referenties opgeven door de rijen van de gebruikers te selecteren en op **Referenties bijwerken** te klikken en namens de gebruikers de gebruikersnaam en het wachtwoord in te voeren. Anders worden gebruikers gevraagd om de referenties zelf in te voeren bij de lancering.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemen met betrekking tot het toewijzen van toepassingen aan gebruikers

Een gebruiker ziet mogelijk geen toepassing in het access-paneel omdat deze niet aan de toepassing is toegewezen. Hieronder vindt u enkele manieren om te controleren:

-   [Controleren of een gebruiker aan de toepassing is toegewezen](#check-if-a-user-is-assigned-to-the-application)

-   [Een gebruiker rechtstreeks aan een toepassing toewijzen](#how-to-assign-a-user-to-an-application-directly)

-   [Controleren of een gebruiker is toegewezen aan een licentie met betrekking tot de toepassing](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Een licentie toewijzen aan een gebruiker](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Controleren of een gebruiker aan de toepassing is toegewezen

Voer de onderstaande stappen uit om te controleren of een gebruiker aan de toepassing is toegewezen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

6. **Zoek naar** de naam van de betreffende toepassing.

7. klik op **Gebruikers en groepen**.

8. Controleer of uw gebruiker aan de toepassing is toegewezen.

   * Als u de stappen in 'Hoe een gebruiker rechtstreeks aan een toepassing toewijst' niet te volgen.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Een gebruiker rechtstreeks aan een toepassing toewijzen

Als u een of meer gebruikers rechtstreeks aan een toepassing wilt toewijzen, voert u de onderstaande stappen uit:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen uit de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

8. Klik **op** de knop Toevoegen boven aan de lijst **Gebruikers en groepen** om het deelvenster Toewijzing **toevoegen** te openen.

9. klik op de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ de **volledige naam** of het volledige **e-mailadres** van de gebruiker die u wilt toewijzen aan het zoekvak Zoeken op naam of **e-mailadres.**

11. Plaats de **plaats van** de gebruiker in de lijst om een **selectievakje**te onthullen. Klik op het selectievakje naast de profielfoto of het logo van de gebruiker om uw gebruiker toe te voegen aan de **lijst Geselecteerde.**

12. **Optioneel:** Als u meer dan één gebruiker wilt **toevoegen,** typt u een andere **volledige naam** of **e-mailadres** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze gebruiker toe te voegen aan de **lijst Geselecteerde.**

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **Selecteren** om deze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde gebruikers.

Na een korte periode kunnen de geselecteerde gebruikers deze toepassingen starten in het Access Panel.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Controleren of een gebruiker een licentie heeft met betrekking tot de toepassing

Voer de onderstaande stappen uit om de toegewezen licenties van een gebruiker te controleren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

   * Als de gebruiker is toegewezen aan een Office-licentie, kunnen First Party Office-toepassingen worden weergegeven in het Toegangspaneel van de gebruiker.

### <a name="how-to-assign-a-user-a-license"></a>Een gebruiker een licentie toewijzen 

Als u een licentie aan een gebruiker wilt toewijzen, voert u de onderstaande stappen uit:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  klik op de knop **Toewijzen.**

9.  Selecteer **een of meer producten** uit de lijst met beschikbare producten.

10. **Optioneel** klikt u op het item **toewijzingsopties** om producten gedetailleerd toe te wijzen. Klik **op Ok** wanneer dit is voltooid.

11. Klik **op** de knop Toewijzen om deze licenties aan deze gebruiker toe te wijzen.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemen met betrekking tot het toewijzen van toepassingen aan groepen

Een gebruiker kan een toepassing in het Access-paneel zien omdat hij deel uitmaakt van een groep aan de toepassing toegewezen. Hieronder vindt u enkele manieren om te controleren:

-   [Groepslidmaatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [Een toepassing rechtstreeks aan een groep toewijzen](#how-to-assign-an-application-to-a-group-directly)

-   [Controleren of een gebruiker deel uitmaakt van een groep die is toegewezen aan een licentie](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Een licentie toewijzen aan een groep](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Groepslidmaatschappen van een gebruiker controleren

Volg de onderstaande stappen om het lidmaatschap van een groep te controleren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. klik op **Groepen**.

8. Controleer of uw gebruiker deel uitmaakt van een groep die aan de toepassing is toegewezen.

   * Als u de gebruiker uit de groep wilt verwijderen, **klikt u op de rij** van de groep en selecteert u verwijderen.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Een toepassing rechtstreeks aan een groep toewijzen

Als u een of meer groepen rechtstreeks aan een toepassing wilt toewijzen, voert u de onderstaande stappen uit:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen uit de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

8. Klik **op** de knop Toevoegen boven aan de lijst **Gebruikers en groepen** om het deelvenster Toewijzing **toevoegen** te openen.

9. klik op de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ de **volledige groepsnaam** van de groep die u wilt toewijzen aan het zoekvak **Zoeken op naam of e-mailadres.**

11. Plaats de **plaats in** de groep in de lijst om een **selectievakje**te onthullen. Klik op het selectievakje naast de profielfoto of het logo van de groep om uw gebruiker toe te voegen aan de **lijst Geselecteerd.**

12. **Optioneel:** Als u meer dan één groep wilt **toevoegen,** typt u een andere **volledige groepsnaam** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze groep toe te voegen aan de lijst **Geselecteerd.**

13. Wanneer u klaar bent met het selecteren van groepen, klikt u op de knop **Selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de groepen die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde groepen.

Na een korte periode kunnen de geselecteerde gebruikers deze toepassingen starten in het Access Panel.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Controleren of een gebruiker deel uitmaakt van een groep die is toegewezen aan een licentie

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. klik op **Groepen**.

8. klik op de rij van een specifieke groep.

9. Klik op **Licenties** om te zien welke licenties de groep eraan heeft toegewezen.

   * Als de groep is toegewezen aan een Office-licentie, kan dit bepaalde First Party Office-toepassingen in staat stellen om te worden weergegeven in het Toegangspaneel van de gebruiker.

### <a name="how-to-assign-a-license-to-a-group"></a>Een licentie toewijzen aan een groep

Als u een licentie aan een groep wilt toewijzen, voert u de onderstaande stappen uit:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle groepen**.

6.  **Zoek naar** de groep waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  Klik op **Licenties** om te zien welke licenties de groep momenteel heeft toegewezen.

8.  klik op de knop **Toewijzen.**

9.  Selecteer **een of meer producten** uit de lijst met beschikbare producten.

10. **Optioneel** klikt u op het item **toewijzingsopties** om producten gedetailleerd toe te wijzen. Klik **op Ok** wanneer dit is voltooid.

11. Klik **op** de knop Toewijzen om deze licenties aan deze groep toe te wijzen. Dit kan lang duren, afhankelijk van de grootte en complexiteit van de groep.

>[!NOTE]
>Om dit sneller te doen, u overwegen om een licentie rechtstreeks aan de gebruiker toe te wijsen. 
>
>

## <a name="next-steps"></a>Volgende stappen
[Nieuwe gebruikers toevoegen aan Azure Active Directory (Engelstalig artikel)](./../fundamentals/add-users-azure-active-directory.md)

