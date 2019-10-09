---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SKYSITE | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SKYSITE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 04c6a47a-1730-4acf-bc5c-a04daccff9b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ef7f4201e9613cc6fa4391bc28d257272fa1c7
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026127"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SKYSITE

In deze zelf studie leert u hoe u SKYSITE integreert met Azure Active Directory (Azure AD). Wanneer u SKYSITE integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SKYSITE.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SKYSITE met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SKYSITE-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SKYSITE ondersteunt door **IDP** GEÏNITIEERDe SSO

* SKYSITE ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-skysite-from-the-gallery"></a>SKYSITE toevoegen uit de galerie

Als u de integratie van SKYSITE in azure AD wilt configureren, moet u SKYSITE uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SKYSITE** in het zoekvak.
1. Selecteer **SKYSITE** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SKYSITE

Azure AD SSO met SKYSITE configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SKYSITE.

Als u Azure AD SSO wilt configureren en testen met SKYSITE, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SKYSITE SSO configureren](#configure-skysite-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een SKYSITE-test gebruiker](#create-skysite-test-user)** -om een equivalent van B. Simon in SKYSITE te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Klik in het [Azure Portal](https://portal.azure.com/)op de pagina **SKYSITE** toepassings integratie op het **tabblad Eigenschappen** en voer de volgende stap uit: 

    ![Eigenschappen voor eenmalige aanmelding](./media/skysite-tutorial/config05.png)

    * Kopieer de **gebruikers toegangs-URL** en u moet deze plakken in de **sectie SKYSITE SSO configureren**. deze wordt verderop in de zelf studie beschreven.

1. Ga op de pagina Toepassings integratie van **SKYSITE** naar **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In het gedeelte **basis configuratie van SAML** is de toepassing vooraf geconfigureerd in de gestarte modus **IDP** en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

1. De SKYSITE-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op pictogram **bewerken** om het dialoog venster gebruikers kenmerken te openen.

    ![image](common/edit-attribute.png)

1. Daarnaast verwacht SKYSITE toepassing nog enkele kenmerken die in het SAML-antwoord weer worden door gegeven. Voer de volgende stappen uit in de sectie **gebruikers kenmerken & claims** van het dialoog venster **groeps claims (preview)** :

    a. Klik op de **pen** naast **groepen die zijn geretourneerd in claim**.

    ![image](./media/skysite-tutorial/config01.png)

    ![image](./media/skysite-tutorial/config02.png)

    b. Selecteer **alle groepen** in de lijst met keuze rondjes.

    c. Selecteer **bron kenmerk** van **groep-ID**.

    d. Klik op **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Op de sectie **SKYSITE instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SKYSITE.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SKYSITE**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="configure-skysite-sso"></a>SKYSITE SSO configureren

1. Open een nieuw webbrowser venster en meld u aan bij uw SKYSITE-bedrijfs site als beheerder en voer de volgende stappen uit:

4. Klik op **instellingen** in de rechter bovenhoek van de pagina en navigeer vervolgens naar **account instelling**.

    ![Configuratie](./media/skysite-tutorial/config03.png)

5. Ga naar het tabblad **eenmalige aanmelding (SSO)** en voer de volgende stappen uit:

    ![Configuratie](./media/skysite-tutorial/config04.png)

    a. Plak de waarde van de URL van de **gebruikers toegang**die u hebt gekopieerd van het tabblad eigenschappen in azure Portal in het tekstvak URL-aanmeldings **gegevens** van **ID-provider** .

    b. Klik op **certificaat uploaden**om het base64-gecodeerde certificaat te uploaden dat u hebt gedownload van de Azure Portal.

    c. Klik op **Opslaan**.

### <a name="create-skysite-test-user"></a>SKYSITE-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in SKYSITE. SKYSITE biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in SKYSITE, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel SKYSITE in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SKYSITE waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SKYSITE met Azure AD](https://aad.portal.azure.com/)

