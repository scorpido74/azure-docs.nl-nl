---
title: Federatieve eenmalige aanmelding configureren voor een niet-galerie-app
description: Federatieve eenmalige aanmelding configureren voor een aangepaste niet-galerie toepassing die u wilt integreren met Azure AD
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74274598"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Federatieve eenmalige aanmelding configureren voor een toepassing buiten de galerie

Als u eenmalige aanmelding wilt configureren voor een niet-galerie toepassing *zonder code te schrijven*, moet u een abonnement of Azure AD Premium hebben en moet de toepassing SAML 2,0 ondersteunen. Ga voor meer informatie over Azure AD-versies naar [Azure ad-prijzen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Overzicht van de stappen die vereist zijn
Hieronder vindt u een overzicht van de stappen die vereist zijn voor het configureren van federatieve eenmalige aanmelding met SAML 2,0 voor een niet-galerie (bijvoorbeeld aangepaste) toepassing.

-   De meta gegevens waarden van de toepassing configureren in azure AD (aanmelden op URL, id, antwoord-URL)

-   [Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Azure AD-meta gegevens en-certificaat ophalen](#download-the-azure-ad-metadata-or-certificate)

-   Meta gegevens van Azure AD in de toepassing configureren (aanmeldings-URL, verlener, afmeldings-URL en certificaat)

-   Gebruikers toewijzen aan een toepassing

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Eenmalige aanmelding configureren voor niet-galerie toepassingen

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing die zich niet in de Azure AD-galerie bevindt:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6. Klik in de sectie **uw eigen app toevoegen** op **niet-galerie toepassing**

7. Voer de naam van de toepassing in het tekstvak **naam** in.

8. Klik op de knop **toevoegen** om de toepassing toe te voegen.

9. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

10. Selecteer op **SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.

11. Voer de vereiste waarden in het **domein en de url's in.** U moet deze waarden ophalen van de leverancier van de toepassing.

    1. Als u de toepassing wilt configureren als door IdP geïnitieerde SSO, voert u de antwoord-URL en de id in.

    2. **Optioneel:** De aanmeldings-URL is een vereiste waarde om de toepassing te configureren als door SP geïnitieerde SSO.

12. Selecteer in de **gebruikers kenmerken**de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** .

13. **Optioneel:** Klik op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:

    1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

    2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

14. Klik **op &lt;toepassings naam&gt; configureren** voor toegang tot documentatie over het configureren van eenmalige aanmelding in de toepassing. Daarnaast hebt u Azure AD-Url's en een certificaat vereist voor de toepassing.

15. [Gebruikers toewijzen aan de toepassing.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden

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
   >Azure AD Selecteer de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga naar het [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) via de sectie NameIDPolicy voor meer informatie.
   >
   >

9. Om gebruikers kenmerken toe te voegen, klikt u op **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Klik op **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   2. Klik op **opslaan.** U kunt het nieuwe kenmerk in de tabel zien.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>De meta gegevens of het certificaat van Azure AD downloaden

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

Azure AD biedt ook een URL om de meta gegevens op te halen. Volg dit patroon om de meta gegevens-URL op te halen die `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`specifiek is voor de toepassing:.

## <a name="assign-users-to-the-application"></a>Gebruikers toewijzen aan een toepassing

Volg de onderstaande stappen om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

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

Na korte tijd kunnen de gebruikers die u hebt geselecteerd deze toepassingen starten met behulp van de methoden die worden beschreven in de sectie Beschrijving van oplossing.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>De SAML-claims aanpassen die worden verzonden naar een toepassing

Zie [claim toewijzing in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
