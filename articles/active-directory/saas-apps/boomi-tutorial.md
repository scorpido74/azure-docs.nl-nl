---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Boomi | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Boomi configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086452"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Boomi

In deze zelf studie leert u hoe u Boomi integreert met Azure Active Directory (Azure AD). Wanneer u Boomi integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Boomi.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Boomi met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Boomi-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Boomi biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding
* Zodra u de Boomi hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Boomi toevoegen vanuit de galerie

Om de integratie van Boomi te configureren in Azure AD, moet u Boomi vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Boomi** in het zoekvak.
1. Selecteer **Boomi** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Boomi

Azure AD SSO met Boomi configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Boomi.

Als u Azure AD SSO wilt configureren en testen met Boomi, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[BOOMI SSO configureren](#configure-boomi-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Boomi-test gebruiker](#create-boomi-test-user)** -om een equivalent van B. Simon in Boomi te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Boomi** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** als u het **META gegevensbestand van de service provider** hebt en wilt configureren in de gestarte modus **IDP** :

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het meta gegevensbestand is geüpload, worden de waarden voor de **id** en de **antwoord-URL** automatisch ingevuld in de basis configuratie sectie voor SAML.

    ![image](common/idp-intiated.png)

    > [!Note]
    > U krijgt het **META gegevensbestand van de service provider** via de **Boomi SSO-sectie configure** . dit wordt verderop in de zelf studie beschreven. Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. De Boomi-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Daarnaast verwacht Boomi toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Boomi instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Boomi.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Boomi** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-boomi-sso"></a>Boomi SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Boomi-bedrijfs site.

1. Ga naar **Company Name** en daarna naar **Set up**.

1. Klik op het tabblad **SSO Options** en voer de onderstaande stappen uit.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Schakel het selectievakje **Enable SAML Single Sign-On** in.

    b. Klik op **Import** om het gedownloade certificaat van Azure AD te uploaden naar **Identity Provider Certificate**.

    c. Plak in het tekstvak **Identity Provider Login URL** de waarde van **Aanmeldings-URL** uit het configuratievenster van de toepassing in Azure AD.

    d. Voor **federatieve id-locatie**selecteert u de **Federatie-id bevindt zich in FEDERATION_ID keuze rondje kenmerk element** .

    e. Kopieer de **URL van de AtomSphere-meta gegevens**, ga naar de **meta gegevens-URL** via de browser van uw keuze en sla de uitvoer op in een bestand. Upload de **meta gegevens-URL** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    f. Klik op de knop **Save**.

### <a name="create-boomi-test-user"></a>Een testgebruiker maken voor Boomi

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Boomi, moeten ze worden ingericht in Boomi. In het geval van Boomi is dat een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als beheerder aan bij de Boomi-bedrijfs site.

1. Ga na het aanmelden naar **User Management** en **Users**.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_001.png "Gebruikers")

1. Klik op het pictogram **+** om het dialoogvenster **Add / Maintain User Roles** te openen.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_002.png "Gebruikers")

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_003.png "Gebruikers")

    a. Typ in het tekstvak **User e-mail address** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. Typ in het tekstvak **voor de voor naam** de voor naam van de gebruiker zoals B.

    c. Typ in het tekstvak **Last name** de achternaam van de gebruiker, zoals Simon.

    d. Typ een waarde voor de gebruiker in het vak **Federation ID**. Elke gebruiker moet een federatie-id hebben die de gebruiker uniek identificeert binnen het account.

    e. Wijs de rol **Standard User** toe aan de gebruiker. Wijs de beheerdersrol niet toe, omdat deze de normale toegang tot de atmosfeer en eenmalige aanmelding zou betekenen.

    f. Klik op **OK**.

    > [!NOTE]
    > De gebruiker ontvangt geen welkomst meldings-e-mail met een wacht woord dat kan worden gebruikt om u aan te melden bij het AtomSphere-account, omdat het wacht woord wordt beheerd via de ID-provider. U kunt andere hulpprogramma's of API's van Boomi gebruiken voor het inrichten van AAD-gebruikersaccounts.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Boomi klikt, wordt u automatisch aangemeld bij de instantie van Boomi waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Probeer Boomi met Azure AD](https://aad.portal.azure.com/)
