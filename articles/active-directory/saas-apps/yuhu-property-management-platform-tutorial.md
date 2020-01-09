---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Yuhu-eigenschappen beheer platform | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Yuhu-eigenschappen beheer platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5f7846fe-49f8-48b4-abda-a3719cb18c15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f15dfa36c24c7a84e254b110e9cceae54c8786
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533242"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yuhu-property-management-platform"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het beheer platform van Yuhu-eigenschappen

In deze zelf studie leert u hoe u Yuhu-eigenschappen beheer platform kunt integreren met Azure Active Directory (Azure AD). Wanneer u Yuhu-eigenschappen beheer platform integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het Yuhu-beheer platform voor eigenschappen.
* Stel in dat uw gebruikers automatisch worden aangemeld bij Yuhu met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Yuhu-kenmerk beheer platform voor eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Yuhu-eigenschappen beheer platform ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-yuhu-property-management-platform-from-the-gallery"></a>Het Yuhu-platform voor het beheren van eigenschappen toevoegen vanuit de galerie

Als u de integratie van Yuhu-eigenschappen beheer platform wilt configureren in azure AD, moet u het beheer platform voor Yuhu-eigenschappen toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Yuhu Property Management platform** in het zoekvak.
1. Selecteer **Yuhu-eigenschappen beheer platform** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-yuhu-property-management-platform"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Yuhu-eigenschappen beheer platform

Azure AD SSO configureren en testen met Yuhu-eigenschappen beheer platform met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Yuhu-beheer platform voor eigenschappen.

Als u Azure AD SSO wilt configureren en testen met Yuhu-eigenschappen beheer platform, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO van Yuhu-eigenschappen beheer platform](#configure-yuhu-property-management-platform-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    * **[Maak een Yuhu-platform gebruiker](#create-yuhu-property-management-platform-test-user)** voor het beheer van de test, zodat er een soort is van B. Simon in het beheer platform van de Yuhu-eigenschap die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Yuhu-eigenschappen beheer platform** toepassing de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.yuhu.io/companies`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `yuhu-<ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteunings team van Yuhu Property Management platform](mailto:hello@yuhu.io) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De Yuhu-eigenschap beheer platform toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op de bovenstaande Yuhu-toepassings platform toepassing verwacht nog enkele kenmerken te worden door gegeven aan het SAML-antwoord dat hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | voornaam | user.givenname |
    | achternaam | user.surname ||
    | e-mail | user.mail |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **certificaat (RAW)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Kopieer op de sectie **Yuhu-eigenschappen beheer platform** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot het Yuhu-beheer platform voor eigenschappen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Yuhu platform**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-yuhu-property-management-platform-sso"></a>Yuhu-eigenschappen beheer platform-SSO configureren

Voor het configureren van eenmalige aanmelding op **Yuhu eigenschaps beheer platform** , moet u het gedownloade **certificaat (RAW)** en de juiste gekopieerde url's verzenden van Azure Portal naar het [ondersteunings team van Yuhu-eigenschappen beheer platform](mailto:hello@yuhu.io). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-yuhu-property-management-platform-test-user"></a>Een test gebruiker voor Yuhu-beheer platform maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Yuhu-eigenschappen beheer platform. Werk met het [Yuhu-ondersteunings team voor het beheer platform](mailto:hello@yuhu.io) om de gebruikers toe te voegen in het Yuhu-eigenschappen beheer platform platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Yuhu-eigenschappen beheer platform in het toegangs venster klikt, moet u automatisch worden aangemeld bij het Yuhu-eigenschappen beheer platform waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Yuhu platform voor eigenschappen beheer met Azure AD](https://aad.portal.azure.com/)