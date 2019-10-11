---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Velpic SAML | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241566"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Velpic SAML

In deze zelf studie leert u hoe u Velpic SAML integreert met Azure Active Directory (Azure AD). Wanneer u Velpic SAML integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Velpic SAML.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld voor Velpic SAML met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Velpic-abonnement voor eenmalige SAML-aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Velpic SAML ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Velpic SAML toevoegen vanuit de galerie

Als u de integratie van Velpic SAML wilt configureren in azure AD, moet u Velpic SAML toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Velpic SAML** in het zoekvak.
1. Selecteer **VELPIC SAML** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Velpic SAML

Azure AD SSO met Velpic SAML configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Velpic SAML.

Als u Azure AD SSO wilt configureren en testen met Velpic SAML, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[VELPIC SAML SSO configureren](#configure-velpic-saml-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een VELPIC SAML test User](#create-velpic-saml-test-user)** -als u wilt dat er een equivalent van B. Simon in Velpic SAML is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Velpic SAML** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<sub-domain>.velpicsaml.net`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Houd er rekening mee dat de aanmeldings-URL wordt verschaft door het Velpic SAML-team en de id-waarde is beschikbaar wanneer u de SSO-invoeg toepassing op Velpic-SAML-zijde configureert. U moet deze waarde kopiëren van de Velpic SAML Application-pagina en deze hier plakken.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **VELPIC SAML instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Velpic SAML.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **VELPIC SAML**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-velpic-saml-sso"></a>Velpic SAML SSO configureren

1. Als u de configuratie in Velpic SAML wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **VELPIC SAML instellen** gaat u naar de Velpic SAML-toepassing. Geef de beheerders referenties op om u aan te melden bij Velpic SAML. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-8 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Velpic SAML hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Velpic SAML-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik op het tabblad **beheren** en ga naar **integratie** sectie waar u op de knop **plugins** moet klikken om een nieuwe invoeg toepassing te maken voor aanmelden.

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_1.png)

5. Klik op de knop **Plugin' toevoegen** .
    
    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_2.png)

6. Klik op de tegel **SAML** op de pagina invoeg toepassing toevoegen.
    
    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_3.png)

7. Voer de naam van de nieuwe SAML-invoeg toepassing in en klik op de knop **toevoegen** .

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_4.png)

8. Voer de volgende gegevens in:

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_5.png)

    a. Typ in het tekstvak **naam** de naam van de SAML-invoeg toepassing.

    b. Plak in het tekstvak **URL van uitgever** de **Azure ad-id** die u hebt gekopieerd uit het venster **aanmelden configureren** van de Azure Portal.

    c. Upload het XML-bestand met meta gegevens dat u van Azure Portal hebt gedownload in de configuratie van de provider van de **META** gegevens.

    d. U kunt er ook voor kiezen om SAML just-in-time inrichten in te scha kelen door het selectie vakje **' automatisch nieuwe gebruikers maken '** in te scha kelen. Als een gebruiker niet bestaat in Velpic en deze vlag niet is ingeschakeld, mislukt de aanmelding vanuit Azure. Als de markering is ingeschakeld, wordt de gebruiker automatisch ingericht in Velpic op het moment van de aanmelding. 

    e. Kopieer de **URL voor eenmalige aanmelding** uit het tekstvak en plak deze in de Azure Portal.
    
    f. Klik op **Opslaan**.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML-test gebruiker maken

Deze stap is doorgaans niet vereist omdat de toepassing just-in-time-gebruikers inrichting ondersteunt. Als de automatische gebruikers inrichting niet is ingeschakeld, kan hand matig maken van de gebruiker worden uitgevoerd, zoals hieronder wordt beschreven.

Meld u aan bij uw Velpic SAML-bedrijfs site als beheerder en voer de volgende stappen uit:
    
1. Klik op het tabblad beheren en ga naar de sectie gebruikers en klik vervolgens op de knop Nieuw om gebruikers toe te voegen.

    ![gebruiker toevoegen](./media/velpicsaml-tutorial/velpic_7.png)

2. Voer de volgende stappen uit op de dialoog pagina **' nieuwe gebruiker maken '** .

    ![Gebruiker](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. In het tekstvak **voor de voor naam** typt u de voor naam B.

    b. Typ in het tekstvak **Achternaam** de achternaam van Simon.

    c. Typ in het tekstvak **gebruikers naam** de gebruikers naam van B. Simon.

    d. Typ in het tekstvak **e-mail** het e-mail adres van B.Simon@contoso.com-account.

    e. De rest van de informatie is optioneel. u kunt deze indien nodig invullen.
    
    f. Klik op **OPSLAAN**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

1. Wanneer u op de tegel Velpic SAML in het toegangs venster klikt, moet u de aanmeldings pagina van de Velpic SAML-toepassing ophalen. De knop **Aanmelden met Azure AD** wordt weer geven op de aanmeldings pagina.

    ![Invoegtoepassing](./media/velpicsaml-tutorial/velpic_6.png)

1. Klik op de knop **Aanmelden met Azure AD** om u aan te melden bij Velpic met uw Azure ad-account.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Velpic SAML met Azure AD](https://aad.portal.azure.com/)

