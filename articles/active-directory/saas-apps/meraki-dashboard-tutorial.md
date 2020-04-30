---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het Meraki-dash board | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory-en Meraki-dash board.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 09766236-5de0-43fd-8950-5316390ce646
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d5fd75a737a0a866b6e5c26c417458ee95845fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82084121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het Meraki-dash board

In deze zelf studie leert u hoe u Meraki dash board integreert met Azure Active Directory (Azure AD). Wanneer u Meraki-dash board integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het Meraki-dash board.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Meraki dash board met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Meraki-abonnement voor eenmalige aanmelding (SSO) van het dash board.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Meraki-dash board ondersteunt door **IDP** GEÏNITIEERDe SSO
* Nadat u het Meraki-dash board hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Meraki-dash board toevoegen uit de galerie

Als u de integratie van Meraki-dash board wilt configureren in azure AD, moet u Meraki-dash board toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Meraki dash board** in het zoekvak.
1. Selecteer **Meraki-dash board** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-meraki-dashboard"></a>Eenmalige aanmelding voor Azure AD voor Meraki-dash board configureren en testen

Configureer en test Azure AD SSO met Meraki-dash board met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Meraki dash board.

Als u Azure AD SSO wilt configureren en testen met Meraki-dash board, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Meraki-dash board-SSO configureren](#configure-meraki-dashboard-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Meraki-dashboard test gebruiker](#create-meraki-dashboard-test-user)** : als u een equivalent van B. Simon in Meraki-dash board wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Meraki dash board** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:
     
    Typ in het tekstvak **antwoord-URL** een URL met het volgende patroon:`https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. Werk deze waarde bij met de daad werkelijke antwoord-URL-waarde, die verderop in de zelf studie wordt uitgelegd.

1. Klik op de knop **Opslaan**.

1. Meraki-dashboard toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht de Meraki-dashboard toepassing nog maar weinig kenmerken die kunnen worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Zie [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)voor meer informatie over het configureren van rollen in azure AD.

1. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

1. Kopieer de waarde van de **vinger afdruk** in de sectie **SAML-handtekening certificaat** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

1. Kopieer op de sectie **Meraki-dash board instellen** de waarde voor de afmeldings-URL en sla deze op uw computer op.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het Meraki-dash board.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen het **Meraki-dash board**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-meraki-dashboard-sso"></a>Meraki-dash board-SSO configureren

1. Meld u in een ander webbrowser venster aan bij Meraki dash board als beheerder.

1. Navigeer naar **organisatie-** -> **instellingen**.

    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/configure1.png)

1. Wijzig onder verificatie **SAML SSO** in **SAML SSO ingeschakeld**.

    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/configure2.png)

1. Klik op **een SAML-IDP toevoegen**.

    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/configure3.png)

1. Plak de waarde van de **vinger afdruk** , die u hebt gekopieerd van de Azure Portal naar het tekstvak **X. 590-certificaat SHA1-vinger afdruk** . Klik vervolgens op **Opslaan**. Na het opslaan wordt de URL van de consument weer gegeven. Kopieer de waarde van de Consumer-URL en plak deze in het tekstvak **antwoord-URL** in het **gedeelte basis-SAML-configuratie** in de Azure Portal.

    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/configure4.png)

### <a name="create-meraki-dashboard-test-user"></a>Test gebruiker van Meraki-dash board maken

1. Meld u in een ander webbrowser venster aan bij Meraki dash board als beheerder.

1. Navigeer naar **organisatie** -> **beheerders**.

    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/user1.png)

1. Klik in de sectie SAML-beheerders rollen op de knop **SAML-rol toevoegen** .

    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/user2.png)

1. Voer de rol **meraki_full_admin**in, Markeer de toegang van de **organisatie** als **vol** en klik op **rol maken**. Herhaal dit proces voor **meraki_readonly_admin**. Dit is het moment dat de **organisatie toegang heeft tot** het vak **alleen-lezen** .
 
    ![Meraki-dashboard configuratie](./media/meraki-dashboard-tutorial/user3.png)

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Meraki-dash board in het toegangs venster klikt, moet u automatisch worden aangemeld bij het Meraki-dash board waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Meraki-dash board uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Het Meraki-dash board beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

