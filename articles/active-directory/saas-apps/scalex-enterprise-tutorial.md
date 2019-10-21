---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met scaleX Enter prise | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en scaleX Enter prise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e54994d02dd1abbca1602952fbad058b3ad993d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72594264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met scaleX Enter prise

In deze zelf studie leert u hoe u scaleX Enter prise integreert met Azure Active Directory (Azure AD). Wanneer u scaleX Enter prise integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot scaleX Enter prise.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij scaleX Enter prise met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement scaleX Enter prise single sign-on (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* ScaleX Enter prise ondersteunt SSO **met SP en IDP**

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Een scaleX-onderneming toevoegen vanuit de galerie

Als u de integratie van scaleX Enter prise wilt configureren in azure AD, moet u scaleX Enter prise toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Enter prise scaleX** in het zoekvak.
1. Selecteer **scaleX Enter prise** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Eenmalige aanmelding van Azure AD voor scaleX Enter prise configureren en testen

Azure AD SSO met scaleX Enter prise configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in scaleX Enter prise.

Als u Azure AD SSO wilt configureren en testen met scaleX Enter prise, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ScaleX Enter PRISE SSO configureren](#configure-scalex-enterprise-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Enter prise test-gebruiker van scaleX](#create-scalex-enterprise-test-user)** om een equivalent van B. Simon te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **scaleX Enter prise** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://platform.rescale.com/saml2/<company id>/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van scaleX Enter prise client](https://info.rescale.com/contact_sales) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw scaleX Enter prise-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. De volgende scherm afbeelding ziet u de lijst met standaard kenmerken, waarbij het **EmailAddress** wordt toegewezen aan **gebruiker. mail**. ScaleX Enter prise Application verwacht is dat **EmailAddress** moet worden toegewezen aan **User. userPrincipalName**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![image](common/edit-attribute.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste in de sectie **scale-up instellen op ondernemings niveau** .

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot scaleX Enter prise.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **scaleX Enter prise**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-scalex-enterprise-sso"></a>ScaleX Enter prise SSO configureren

1. Als u de configuratie binnen scaleX Enter prise wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **scaleX instellen** om u te leiden naar de Enter prise-toepassing scaleX. Geef de beheerders referenties op om u aan te melden bij scaleX Enter prise. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u de Schaalx Enter prise hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij de site van uw scaleX Enter prise-bedrijf als beheerder en voert u de volgende stappen uit:

1. Klik op het menu in de rechter bovenhoek en selecteer **Contoso-beheer**.

    > [!NOTE]
    > Contoso is slechts een voor beeld. Dit moet de werkelijke bedrijfs naam zijn.

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selecteer **integraties** in het bovenste menu en selecteer **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Vul het formulier als volgt in:

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selecteer **een wille keurige gebruiker maken die kan verifiëren met SSO**

    b. **Service provider SAML**: plak de waarde ***urn: Oasis: names: TC: SAML: 2.0: NameID-indeling: persistent***

    c. **Naam van het veld e-mail adres van de ID-provider in ACS-antwoord**: plak de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Id van ID-provider EntityDescriptor entiteit:** Plak de id-waarde van **Azure AD** die is gekopieerd van de Azure Portal.

    e. **URL van ID-provider SingleSignOnService:** Plak de **aanmeldings-URL** vanuit het Azure Portal.

    f. **Openbaar x509-certificaat van de identiteits provider:** Open het x509-certificaat dat is gedownload van Azure in Klad blok en plak de inhoud in dit vak. Zorg ervoor dat het midden van de certificaat inhoud geen regel einden bevat.

    g. Schakel de volgende selectie vakjes in: **ingeschakeld, versleutelen NameID en ondertekenen AuthnRequests.**

    h. Klik op **SSO-instellingen bijwerken** om de instellingen op te slaan.

### <a name="create-scalex-enterprise-test-user"></a>Een test gebruiker met Schaalx Enter prise maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij scaleX Enter prise, moeten ze worden ingericht in op scaleX Enter prise. In het geval van scaleX Enter prise is inrichting een automatische taak en zijn er geen hand matige stappen vereist. Elke gebruiker die kan worden geverifieerd met SSO-referenties, wordt automatisch ingericht op de ScaleX-zijde.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel scaleX Enter prise in het toegangs venster klikt, moet u automatisch worden aangemeld bij de ScaleX-onderneming waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer scaleX Enter prise met Azure AD](https://aad.portal.azure.com/)