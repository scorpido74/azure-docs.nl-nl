---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met OfficeSpace-software | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory-en OfficeSpace-software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80712c7f59845287006c1699524573c6094498b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75561708"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met OfficeSpace-software

In deze zelf studie leert u hoe u OfficeSpace-software integreert met Azure Active Directory (Azure AD). Wanneer u OfficeSpace-software integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot OfficeSpace-software.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor OfficeSpace-software met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* OfficeSpace-abonnement met eenmalige aanmelding (SSO) van de software.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* OfficeSpace-software ondersteunt door **SP** GEÏNITIEERDe SSO


* OfficeSpace-software ondersteunt **just-in-time** -gebruikers inrichting


## <a name="adding-officespace-software-from-the-gallery"></a>OfficeSpace-software toevoegen vanuit de galerie

Als u de integratie van OfficeSpace-software in azure AD wilt configureren, moet u OfficeSpace-software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **OfficeSpace-software** in het zoekvak.
1. Selecteer **OfficeSpace software** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor OfficeSpace-software

Configureer en test Azure AD SSO met OfficeSpace-software met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in OfficeSpace-software.

Als u Azure AD SSO wilt configureren en testen met OfficeSpace-software, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[OfficeSpace software SSO configureren](#configure-officespace-software-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een OfficeSpace-gebruikers test gebruiker](#create-officespace-software-test-user)** -om een equivalent van B. Simon in OfficeSpace-software te hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina integratie van **OfficeSpace-software** toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team](mailto:support@officespacesoftware.com) van de OfficeSpace-software om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De OfficeSpace-software toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. OfficeSpace-software toepassing verwacht dat **nameidentifier** moet worden toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Daarnaast verwacht OfficeSpace software toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer de waarde van de **vinger afdruk** in de sectie **SAML-handtekening certificaat** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer op de sectie **OfficeSpace-software instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan OfficeSpace-software.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **OfficeSpace software**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="configure-officespace-software-sso"></a>SSO van OfficeSpace-software configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw OfficeSpace-software-Tenant.

2. Ga naar **instellingen** en klik op **connectors**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Klik op **SAML-verificatie**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Voer in het gedeelte **SAML-verificatie** de volgende stappen uit:

    ![Eenmalige aanmelding aan app-zijde configureren](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Plak in het tekstvak de URL van de **Afmeldings provider** de waarde van de **afmeldings-URL** die u van Azure Portal hebt gekopieerd.

    b. Plak in het tekstvak **client IDP doel-URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Plak de waarde van de **vinger afdruk** die u hebt gekopieerd van Azure Portal naar het TEKSTVAK **client IDP certificaat vinger afdruk** . 

    d. Klik op **instellingen opslaan**.

### <a name="create-officespace-software-test-user"></a>OfficeSpace software test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in OfficeSpace-software. OfficeSpace-software ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al aanwezig is in de OfficeSpace-software, wordt er na verificatie een nieuwe gemaakt.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, moet u contact opnemen met het [ondersteunings team van OfficeSpace-software](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel OfficeSpace software in het toegangs venster klikt, moet u automatisch worden aangemeld bij de OfficeSpace-software waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer OfficeSpace-software met Azure AD](https://aad.portal.azure.com/)

