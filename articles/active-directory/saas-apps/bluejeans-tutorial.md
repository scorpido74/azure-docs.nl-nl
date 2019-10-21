---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met BlueJeans voor Azure AD | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en BlueJeans voor Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595053"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met BlueJeans voor Azure AD

In deze zelf studie leert u hoe u BlueJeans voor Azure AD integreert met Azure Active Directory (Azure AD). Wanneer u BlueJeans voor Azure AD integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot BlueJeans voor Azure AD.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij BlueJeans voor Azure AD met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* BlueJeans voor Azure AD-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* BlueJeans voor Azure AD ondersteunt door **SP** GEÏNITIEERDe SSO

* BlueJeans voor Azure AD ondersteunt [ **geautomatiseerde** gebruikers inrichting](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>BlueJeans voor Azure AD toevoegen vanuit de galerie

Als u de integratie van BlueJeans voor Azure AD wilt configureren in azure AD, moet u BlueJeans voor Azure AD toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **BLUEJEANS voor Azure AD** in het zoekvak.
1. Selecteer **BlueJeans voor Azure AD** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor BlueJeans voor Azure AD

Configureer en test Azure AD SSO met BlueJeans voor Azure AD met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in BlueJeans voor Azure AD.

Als u Azure AD SSO wilt configureren en testen met BlueJeans voor Azure AD, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[BlueJeans configureren voor Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak BlueJeans voor Azure AD-test gebruiker](#create-bluejeans-for-azure-ad-test-user)** : als u een equivalent van B. Simon in BlueJeans voor Azure ad wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **BLUEJEANS voor Azure AD** -toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [BlueJeans voor het ondersteunings team van Azure AD](https://support.bluejeans.com/contact) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **BlueJeans voor Azure AD instellen** kopieert u de juiste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan BlueJeans voor Azure AD.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **BlueJeans voor Azure AD**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>BlueJeans configureren voor Azure AD SSO

1. Meld u in een ander webbrowser venster aan bij uw **BlueJeans voor Azure AD** -bedrijfs site als beheerder.

2. Ga naar **ADMIN \> GROEPSINSTELLINGEN \> BEVEILIGING**.

    ![Beheerder](./media/bluejeans-tutorial/ic785868.png "Beheerder")

3. Voer in het gedeelte **BEVEILIGING** de volgende stappen uit:

    ![Eenmalige aanmelding voor SAML](./media/bluejeans-tutorial/ic785869.png "Eenmalige aanmelding voor SAML")

    a. Selecteer **SAML-eenmalige aanmelding**.

    b. Selecteer **Automatische inrichting inschakelen**.

4. Ga verder met de volgende stappen:

    ![Certificaatpad](./media/bluejeans-tutorial/ic785870.png "Certificaatpad")

    a. Klik op **Bestand kiezen** om het base-64 gecodeerde certificaat dat u hebt gedownload vanuit de Azure-portal te uploaden.

    b. Plak in het tekstvak **Aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Wachtwoord-URL wijzigen** de waarde van **Wachtwoord-URL wijzigen** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL**, die u hebt gekopieerd uit Azure Portal.

5. Ga verder met de volgende stappen:

    ![Wijzigingen opslaan](./media/bluejeans-tutorial/ic785874.png "Wijzigingen opslaan")

    a. In het tekstvak **gebruikers-id** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **E-mailadres**.

    c. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>BlueJeans maken voor Azure AD-test gebruiker

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in BlueJeans voor Azure AD. BlueJeans voor Azure AD biedt ondersteuning voor automatische gebruikers inrichting, dat standaard is ingeschakeld. U kunt [hier](bluejeans-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u aan bij uw **BlueJeans voor Azure AD** -bedrijfs site als beheerder.

2. Ga naar **ADMIN \> GEBRUIKERS BEHEREN \> GEBRUIKER TOEVOEGEN**.

    ![Beheerder](./media/bluejeans-tutorial/ic785877.png "Beheerder")

    > [!IMPORTANT]
    > Het tabblad **GEBRUIKER TOEVOEGEN** is alleen beschikbaar als op het tabblad **BEVEILIGING** **Automatische inrichting inschakelen** niet is ingeschakeld.

3. Voer in het gedeelte **GEBRUIKER TOEVOEGEN** de volgende stappen uit:

    ![Gebruiker toevoegen](./media/bluejeans-tutorial/ic785886.png "Gebruiker toevoegen")

    a. Voer in het tekstvak **voor naam** de voor naam van de gebruiker in zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **BlueJeans voor Azure AD-gebruikers naam** de gebruikers naam van de gebruiker in, zoals **Brittasimon**

    d. Voer in het tekstvak **Create a Password** uw wachtwoord in.

    e. Voer in het tekstvak **Company** de naam van uw bedrijf in.

    f. Voer in het tekstvak **e-mail adres** het e-mail bericht van de gebruiker in, zoals `b.simon\@contoso.com`.

    g. Voer in het tekstvak **een BlueJeans maken voor Azure AD Meeting I. D** uw Vergader-id in.

    h. Geef uw wachtwoordcode op in het tekstvak **Pick a Moderator Passcode**.

    i. Klik op **CONTINUE**.

    ![Gebruiker toevoegen](./media/bluejeans-tutorial/ic785887.png "Gebruiker toevoegen")

    J. Klik op **ADD USER**.

> [!NOTE]
> U kunt elk ander BlueJeans voor Azure AD-hulpprogram ma's voor het maken van gebruikers accounts of Api's die worden geleverd door BlueJeans voor Azure AD gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel BlueJeans voor Azure AD in het toegangs venster klikt, moet u automatisch worden aangemeld bij de BlueJeans voor Azure AD waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer BlueJeans voor Azure AD met Azure AD](https://aad.portal.azure.com/)

