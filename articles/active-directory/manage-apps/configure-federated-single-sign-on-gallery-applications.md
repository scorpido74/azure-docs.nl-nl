---
title: Federatieve eenmalige aanmelding configureren voor een toepassing in de Azure AD-galerie | Microsoft Docs
description: Federatieve eenmalige aanmelding configureren voor een bestaande Azure AD Gallery-toepassing en zelf studies gebruiken om snel aan de slag te gaan
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
ROBOTS: NOINDEX
ms.openlocfilehash: 8e50a495e1b0406e0c935ac31111dc6b5d0c0821
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207132"
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Federatieve eenmalige aanmelding configureren voor een toepassing in de Azure AD-galerie

Er is een stapsgewijze zelf studie beschikbaar voor alle toepassingen in de galerie Azure Active Directory (Azure AD) die ondersteuning biedt voor eenmalige aanmelding in de onderneming. U kunt toegang krijgen tot de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) voor gedetailleerde stapsgewijze instructies.

## <a name="overview-of-steps-required"></a>Overzicht van de stappen die vereist zijn
Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De meta gegevens waarden van de toepassing configureren in azure AD (aanmeldings-URL, id, antwoord-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [De meta gegevens en het certificaat van Azure AD ophalen](#download-the-azure-ad-metadata-or-certificate)

-   [Meta gegevens van Azure AD in de toepassing configureren (aanmeldings-URL, verlener, afmeldings-URL en certificaat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Gebruikers toewijzen aan de toepassing](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd menu aan de linkerkant te selecteren.

3.  Typ ' Azure Active Directory ' in het zoekvak en selecteer **Azure Active Directory**.

4.  Selecteer **bedrijfs toepassingen** in het navigatie menu aan de linkerkant van Azure AD.

5.  Selecteer **toevoegen** in de rechter bovenhoek van het deel venster **ondernemings toepassingen** .

6.  Typ de naam van de toepassing in het vak **Geef een naam** op in de sectie **toevoegen vanuit de galerie** .

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam wijzigen in het vak **naam** .

9.  Selecteer **toevoegen** om de toepassing toe te voegen.

Na een korte periode zou u het configuratie venster van de toepassing moeten kunnen zien.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Eenmalige aanmelding configureren voor een toepassing vanuit de Azure AD-galerie

Voer de volgende stappen uit om eenmalige aanmelding voor een toepassing te configureren:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd menu aan de linkerkant te selecteren.

3. Typ ' Azure Active Directory ' in het zoekvak en selecteer **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Nadat de toepassing is geladen, selecteert u de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer **op SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.

9. Voer de vereiste waarden in het **domein en de url's in.** U moet deze waarden ophalen van de leverancier van de toepassing.

   1. De aanmeldings-URL is een vereiste waarde om de toepassing te configureren als door SP geïnitieerde SSO. Voor sommige toepassingen is de id ook een vereiste waarde.

   2. Als u de toepassing wilt configureren als door IdP geïnitieerde SSO, de antwoord-URL is een vereiste waarde. Voor sommige toepassingen is de id ook een vereiste waarde.

10. **Optioneel**: Selecteer **Geavanceerde URL-instellingen weer geven** als u de niet-vereiste waarden wilt zien.

11. Selecteer in de **gebruikers kenmerken**de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** .

12. **Optioneel**: Selecteer **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

    Een kenmerk toevoegen:
   
    1. Selecteer **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

    1. Selecteer **Save.** U kunt het nieuwe kenmerk in de tabel zien.

13. Selecteer **toepassings &lt;naam&gt; configureren** voor toegang tot documentatie over het configureren van eenmalige aanmelding in de toepassing. Daarnaast hebt u de benodigde Url's en certificaten voor meta gegevens om SSO met de toepassing in te stellen.

14. Selecteer **Opslaan** om de configuratie op te slaan.

15. Gebruikers toewijzen aan de toepassing.

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Gebruikers-id selecteren en gebruikers kenmerken toevoegen die naar de toepassing moeten worden verzonden

Voer de volgende stappen uit om de gebruikers-id te selecteren of gebruikers kenmerken toe te voegen:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd menu aan de linkerkant te selecteren.

3. Typ ' Azure Active Directory ' in het zoekvak en selecteer **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing die u hebt geconfigureerd met eenmalige aanmelding.

7. Nadat de toepassing is geladen, selecteert u de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer in de sectie **gebruikers kenmerken** de unieke id voor uw gebruikers in de vervolg keuzelijst **gebruikers-id** . De geselecteerde optie moet overeenkomen met de verwachte waarde in de toepassing om de gebruiker te verifiëren.

   >[!NOTE] 
   >Azure AD selecteert de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Zie het [SAML-protocol voor eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) in de sectie NameIDPolicy voor meer informatie.
   >
   >

9. Om gebruikers kenmerken toe te voegen, selecteert u **alle andere gebruikers kenmerken weer geven en bewerken** om de kenmerken te bewerken die naar de toepassing in het SAML-token moeten worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:
  
   1. Selecteer **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   2. Selecteer **Opslaan**. U kunt het nieuwe kenmerk in de tabel zien.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>De meta gegevens of het certificaat van Azure AD downloaden

Voer de volgende stappen uit om de meta gegevens of het certificaat van de toepassing te downloaden uit Azure AD:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd menu aan de linkerkant te selecteren.

3. Typ ' Azure Active Directory ' in het zoekvak en selecteer **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   *  Als u de gewenste toepassing hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

6. Selecteer de toepassing die u hebt geconfigureerd met eenmalige aanmelding.

7. Nadat de toepassing is geladen, selecteert u de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Ga naar de sectie **SAML-handtekening certificaat** en Selecteer kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

Azure AD biedt ook een URL voor toegang tot de meta gegevens. Gebruik het volgende model om de meta gegevens-URL op te halen die specifiek is voor de toepassing:`https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`

## <a name="assign-users-to-the-application"></a>Gebruikers toewijzen aan een toepassing

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd menu aan de linkerkant te selecteren.

3. Typ ' Azure Active Directory ' in het zoekvak en selecteer **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing die u wilt toewijzen van een gebruiker in de lijst.

7. Nadat de toepassing is geladen, selecteert u **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Selecteer de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige naam** of het **e-mail adres** van de gebruiker die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Schakel het selectie vakje in naast de foto of het logo van het profiel van de gebruiker om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel**: Als u **meer dan één gebruiker wilt toevoegen**, typt u een **volledige naam** of **e-mail adres** in het vak **zoeken op naam of e-mail adres** en schakelt u het selectie vakje in om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, selecteert u de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel**: Selecteer de Selecteer **functie** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Selecteer de knop **toewijzen** om de toepassing toe te wijzen aan de geselecteerde gebruikers.

Na een korte periode, de gebruikers die u hebt geselecteerd mogelijk om deze toepassingen met behulp van de methoden die worden beschreven in de sectie oplossing beschrijving te starten.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>De SAML-claims aanpassen die worden verzonden naar een toepassing

Zie [claims toewijzen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="next-steps"></a>Volgende stappen
[Geef single sign-on bij uw apps met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)



