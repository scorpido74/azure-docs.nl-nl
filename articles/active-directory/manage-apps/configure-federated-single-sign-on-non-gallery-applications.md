---
title: Fed-eenmalige aanmelding configureren voor een niet-galerie-app
description: Hoe u federatieve enkele aanmelding configureert voor een aangepaste niet-galerietoepassing die u wilt integreren met Azure AD
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274598"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Fed-single sign-on configureren voor een niet-galerietoepassing

Als u eenmalige aanmelding wilt configureren voor een niet-galerietoepassing zonder code te *schrijven,* moet u een abonnement of Azure AD Premium hebben en moet de toepassing SAML 2.0 ondersteunen. Ga naar Azure AD-prijzen voor meer informatie over Azure [AD-versies.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="overview-of-steps-required"></a>Overzicht van vereiste stappen
Hieronder vindt u een overzicht op hoog niveau van de stappen die nodig zijn om federatieve eenmalige aanmelding met SAML 2.0 te configureren voor een niet-galerie (bijvoorbeeld aangepaste) toepassing.

-   De metagegevenswaarden van de toepassing configureren in Azure AD (Sign on URL, Identifier, Reply URL)

-   [Selecteer Gebruikers-id en voeg gebruikerskenmerken toe die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-metagegevens en -certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   Azure AD-metagegevenswaarden configureren in de toepassing (Aanmelding op URL, Uitgever, URL van afmelden en certificaat)

-   Gebruikers toewijzen aan een toepassing

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Eenmalige aanmelding configureren voor niet-galerietoepassingen

Voer de onderstaande stappen uit om een enkele aanmelding te configureren voor een toepassing die zich niet in de Azure AD-galerie bevindt:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6. klik **op Niet-galerietoepassing** in de sectie **Uw eigen app toevoegen**

7. Voer de naam van de toepassing in het tekstvak **Naam** in.

8. Klik **op Knop Toevoegen** om de toepassing toe te voegen.

9. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

10. Selecteer **OP SAML gebaseerde aanmelding** in de vervolgkeuzelijst **Modus.**

11. Voer de vereiste waarden in **domein en URL's in.** U moet deze waarden krijgen van de leverancier van de toepassing.

    1. Als u de toepassing wilt configureren als SSO die door IdP is geïnitieerd, voert u de URL Van het antwoord en de id in.

    2. **Optioneel:** Als u de toepassing wilt configureren als sso die door SP is geïnitieerd, is de URL van Sign-on een vereiste waarde.

12. Selecteer in de **gebruikerskenmerken**de unieke id voor uw gebruikers in de vervolgkeuzelijst **Gebruikers-id.**

13. **Optioneel:** klik op **Alle andere gebruikerskenmerken weergeven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. klik **op Kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolgkeuzelijst.

    2. Klik **op Opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

14. klik ** &lt;op&gt; Toepassingsnaam configureren** om toegang te krijgen tot documentatie over het configureren van eenmalige aanmelding in de toepassing. U hebt ook Azure AD-URL's en -certificaten die vereist zijn voor de toepassing.

15. [Gebruikers toewijzen aan de toepassing.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selecteer Gebruikers-id en voeg gebruikerskenmerken toe die naar de toepassing moeten worden verzonden

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

## <a name="download-the-azure-ad-metadata-or-certificate"></a>De metagegevens of -certificaat van Azure AD downloaden

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

Azure AD biedt ook een URL om de metagegevens op te halen. Volg dit patroon om de url met `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`metagegevens specifiek voor de toepassing te krijgen: .

## <a name="assign-users-to-the-application"></a>Gebruikers toewijzen aan een toepassing

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

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die zijn beschreven in de sectie oplossingsbeschrijving.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>De SAML-claims aanpassen die naar een toepassing zijn verzonden

Zie Claimtoewijzing in Azure Active Directory voor meer informatie voor meer informatie voor het aanpassen van de SAML-attribuutclaims die naar uw toepassing zijn [verzonden.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
