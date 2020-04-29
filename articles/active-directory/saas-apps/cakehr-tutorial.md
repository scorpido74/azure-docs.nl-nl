---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met CakeHR | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en CakeHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c9bbdb1-ac47-4fb8-a1cc-1e647b0323a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0860411c95e48a16d75df4aeeedf3405a5b1835
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72595025"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met CakeHR

In deze zelf studie leert u hoe u CakeHR integreert met Azure Active Directory (Azure AD). Wanneer u CakeHR integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot CakeHR.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij CakeHR met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* CakeHR-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* CakeHR ondersteunt door **SP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-cakehr-from-the-gallery"></a>CakeHR toevoegen uit de galerie

Als u de integratie van CakeHR in azure AD wilt configureren, moet u CakeHR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **CakeHR** in het zoekvak.
1. Selecteer **CakeHR** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor CakeHR

Azure AD SSO met CakeHR configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CakeHR.

Als u Azure AD SSO wilt configureren en testen met CakeHR, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CAKEHR SSO configureren](#configure-cakehr-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een CakeHR-test gebruiker](#create-cakehr-test-user)** -om een equivalent van B. Simon in CakeHR te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **CakeHR** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<yourcakedomain>.cake.hr/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [ondersteunings team van CakeHR-clients](mailto:info@cake.hr) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer in de sectie **SAML-handtekening certificaat** de waarde van de **vinger afdruk** en sla deze op in uw Klad blok.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Op de sectie **CakeHR instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan CakeHR.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **CakeHR**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-cakehr-sso"></a>CakeHR SSO configureren

1. Als u de configuratie wilt automatiseren in CakeHR, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **CakeHR instellen** gaat u naar de CakeHR-toepassing. Geef de beheerders referenties op om u aan te melden bij CakeHR. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u CakeHR hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw CakeHR-bedrijfs site als beheerder en voert u de volgende stappen uit:

1. Klik in de rechter bovenhoek van de pagina op **profiel** en navigeer vervolgens naar **instellingen**.

    ![CakeHR-configuratie](./media/cakehr-tutorial/config01.png)

1. Klik aan de linkerkant van de menu balk op **integraties** > **SAML SSO** en voer de volgende stappen uit:

    ![CakeHR-configuratie](./media/cakehr-tutorial/config02.png)

    a. Typ `cake.hr`in het tekstvak **Entiteits-ID** .

    b. Plak in het tekstvak **URL voor verificatie** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **sleutel vingerafdruk (SHA1-indeling)** de **vingerafdruk** waarde die u van Azure Portal hebt gekopieerd.

    d. Schakel het selectie vakje **eenmalige aanmelding inschakelen** in.

    e. Klik op **Opslaan**.

### <a name="create-cakehr-test-user"></a>CakeHR-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij CakeHR, moeten ze worden ingericht in CakeHR. In CakeHR is inrichten een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij CakeHR als een beveiligings beheerder.

2. Klik aan de linkerkant van de menu balk op **bedrijf** > **toevoegen**.

    ![CakeHR-configuratie](./media/cakehr-tutorial/config03.png)

3. Voer de volgende stappen uit in het pop-upvenster **nieuwe werk nemer toevoegen** :

     ![CakeHR-configuratie](./media/cakehr-tutorial/config04.png)

    a. Voer in het tekstvak **volledige naam** de naam van de gebruiker in zoals B. Simon.

    b. Voer in het tekstvak **e-mail** adres het e-mail `B.Simon@contoso.com`adres van de gebruiker in.

    c. Klik op **account maken**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel CakeHR in het toegangs venster klikt, moet u automatisch worden aangemeld bij de CakeHR waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer CakeHR met Azure AD](https://aad.portal.azure.com/)
