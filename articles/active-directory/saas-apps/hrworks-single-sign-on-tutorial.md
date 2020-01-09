---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met eenmalige aanmelding van HRworks | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en HRworks eenmalige aanmelding.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met eenmalige aanmelding van HRworks

In deze zelf studie leert u hoe u eenmalige aanmelding integreert met Azure Active Directory (Azure AD). Wanneer u eenmalige aanmelding met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot HRworks single sign-on.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om eenmalige aanmelding te HRworks met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* HRworks eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Eenmalige aanmelding voor HRworks ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Eenmalige aanmelding voor HRworks toevoegen vanuit de galerie

Als u de integratie van HRworks eenmalige aanmelding wilt configureren in azure AD, moet u HRworks eenmalige aanmelding toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **HRworks eenmalige aanmelding** in het zoekvak.
1. Selecteer **eenmalige aanmelding HRworks** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Eenmalige aanmelding van Azure AD configureren en testen voor HRworks eenmalige aanmelding

Azure AD SSO configureren en testen met eenmalige aanmelding van HRworks met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in HRworks eenmalige aanmelding.

Als u Azure AD SSO wilt configureren en testen met HRworks eenmalige aanmelding, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[HRworks](#configure-hrworks-single-sign-on-sso)** eenmalige aanmelding configureren: voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een HRworks-test gebruiker voor eenmalige aanmelding](#create-hrworks-single-sign-on-test-user)** . deze heeft een equivalent van B. Simon in HRworks eenmalige aanmelding die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **HRworks single sign-on** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van de client voor eenmalige aanmelding](mailto:nadja.sommerfeld@hrworks.de) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **eenmalige aanmelding voor HRworks instellen** kopieert u de juiste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot eenmalige aanmelding van HRworks.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **eenmalige aanmelding HRworks**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-hrworks-single-sign-on-sso"></a>Eenmalige eenmalige aanmelding voor HRworks configureren

1. Als u de configuratie wilt automatiseren in eenmalige aanmelding van HRworks, moet u de **extensie mijn apps beveiligde aanmelding** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **HRworks eenmalige aanmelding instellen** wordt u doorgestuurd naar de toepassing voor eenmalige aanmelding van HRworks. Geef de beheerders referenties op om u aan te melden bij HRworks eenmalige aanmelding. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-4 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u eenmalige aanmelding HRworks hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw bedrijfs site voor HRworks eenmalige aanmelding als beheerder en voert u de volgende stappen uit:

1. Klik op **beheerder** > **basis principes** > **beveiliging** > **eenmalige aanmelding** aan de linkerkant van de menu balk en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Schakel het selectie vakje **eenmalige aanmelding gebruiken** in.

    b. Selecteer **XML-meta** gegevens als **invoer methode voor meta gegevens**.

    c. Selecteer **afzonderlijke NameID-id** als **waarde voor NameID**.

    d. Open in Klad blok de XML voor meta gegevens die u hebt gedownload van de Azure Portal, kopieer de inhoud en plak deze in het tekstvak **meta gegevens** .

    e. Klik op **Opslaan**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Test gebruiker voor eenmalige aanmelding voor HRworks maken

Als u Azure AD-gebruikers wilt inschakelen, meldt u zich aan bij HRworks eenmalige aanmelding, moeten ze worden ingericht in HRworks eenmalige aanmelding. In HRworks-eenmalige aanmelding is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan om eenmalige aanmelding te HRworks als beheerder.

1. Klik op **beheerder** > **personen** > **personen** > **nieuwe persoon** aan de linkerkant van de menu balk.

     ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Klik in het pop-upvenster op **volgende**.

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. Vul in het pop-upvenster **nieuwe persoon met land voor juridische voor waarden** de relevante gegevens in, zoals de **voor naam**, **Achternaam** en klik op **maken**.

    ![Eenmalige aanmelding configureren](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel HRworks single sign-on in het toegangs venster klikt, moet u automatisch worden aangemeld bij de eenmalige aanmelding van HRworks waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer HRworks eenmalige aanmelding met Azure AD](https://aad.portal.azure.com/)