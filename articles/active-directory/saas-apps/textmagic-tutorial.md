---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met TextMagic | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7b900a0e2203fffb5240acd4ab5201fc9d52d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72533033"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met TextMagic

In deze zelf studie leert u hoe u TextMagic integreert met Azure Active Directory (Azure AD). Wanneer u TextMagic integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot TextMagic.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij TextMagic met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* TextMagic-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* TextMagic ondersteunt door **IDP** GEÏNITIEERDe SSO

* TextMagic ondersteunt **just-in-time** -gebruikers inrichting

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-textmagic-from-the-gallery"></a>TextMagic toevoegen uit de galerie

Als u de integratie van TextMagic in azure AD wilt configureren, moet u TextMagic uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **TextMagic** in het zoekvak.
1. Selecteer **TextMagic** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor TextMagic

Azure AD SSO met TextMagic configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TextMagic.

Als u Azure AD SSO wilt configureren en testen met TextMagic, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[TEXTMAGIC SSO configureren](#configure-textmagic-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een TextMagic-test gebruiker](#create-textmagic-test-user)** -om een equivalent van B. Simon in TextMagic te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **TextMagic** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Id** typt u een URL: `https://my.textmagic.com/saml/metadata`

5. De TextMagic-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. TextMagic-toepassing verwacht dat **nameidentifier** moeten worden toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Daarnaast verwacht TextMagic toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Naam |   Bronkenmerk| Naamruimte  |
    | --------------- | --------------- | --------------- |
    | bedrijf | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | voornaam               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | achternaam            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefoon               | User.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **TextMagic instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan TextMagic.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **TextMagic**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="configure-textmagic-sso"></a>TextMagic SSO configureren

1. Als u de configuratie wilt automatiseren in TextMagic, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup TextMagic** gaat u naar de TextMagic-toepassing. Geef de beheerders referenties op om u aan te melden bij TextMagic. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u TextMagic hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw TextMagic-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Selecteer **account instellingen** onder de gebruikers naam.

    ![TextMagic-configuratie](./media/textmagic-tutorial/config1.png)

5. Klik op het tabblad **eenmalige aanmelding (SSO)** en vul de volgende velden in:  

    ![TextMagic-configuratie](./media/textmagic-tutorial/config2.png)

    a. Plak in id **-provider entiteit-id:** tekstvak de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    b. Plak de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd in de URL van de **ID-provider SSO:** TextBox.

    c. Plak in de **identiteits provider SLO-URL:** tekstvak de waarde van de **afmeldings-URL**die u van Azure Portal hebt gekopieerd.

    d. Open uw met **base 64 versleutelde certificaat** in Klad blok dat u hebt gedownload van Azure Portal, kopieer de inhoud ervan naar het klem bord en plak het in het **open bare x509-certificaat:** tekstvak.

    e. Klik op **Opslaan**.

### <a name="create-textmagic-test-user"></a>TextMagic-test gebruiker maken

De toepassing biedt ondersteuning **voor Just-in-time-gebruikers** en nadat verificatie gebruikers automatisch in de toepassing worden gemaakt. U moet de gegevens eenmaal bij de eerste aanmelding invullen om het Subaccount in het systeem te activeren.
Er is geen actie-item voor u in deze sectie.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TextMagic in het toegangs venster klikt, moet u automatisch worden aangemeld bij de TextMagic waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer TextMagic met Azure AD](https://aad.portal.azure.com/)

