---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met NS1 SSO voor Azure | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en NS1 SSO voor Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met NS1 SSO voor Azure

In deze zelfstudie leert u hoe u NS1 SSO voor Azure integreert met Azure Active Directory (Azure AD). Wanneer u NS1 SSO voor Azure integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot NS1 SSO voor Azure.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij NS1 SSO voor Azure met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* NS1 SSO voor Azure single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* NS1 SSO voor Azure ondersteunt SP en IDP heeft SSO geïnitieerd.
* Nadat u NS1 SSO voor Azure hebt geconfigureerd, u sessiebeheer afdwingen. Dit beschermt exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime. Sessiebeheer strekt zich uit van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>NS1 SSO voor Azure toevoegen vanuit de galerie

Als u de integratie van NS1 SSO voor Azure in Azure AD wilt configureren, moet u NS1 SSO voor Azure vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **NS1 SSO voor Azure** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **NS1 SSO voor Azure** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Azure AD-eenmaligaanmelding voor NS1 SSO voor Azure configureren en testen

Azure AD SSO configureren en testen met NS1 SSO voor Azure met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een gekoppelde relatie aanmaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in NS1 SSO voor Azure.

Hier volgen de algemene stappen om Azure AD SSO te configureren en te testen met NS1 SSO voor Azure:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.

    a. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.

    b. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer NS1 SSO voor Azure SSO](#configure-ns1-sso-for-azure-sso)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.

    a. **[Maak een NS1 SSO voor Azure-testgebruiker](#create-an-ns1-sso-for-azure-test-user)** om een tegenhanger van B.Simon in NS1 SSO voor Azure te hebben. Deze tegenhanger is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **NS1 SSO voor** Azure-toepassingsintegratie de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ in het tekstvak **Id** de volgende URL:`https://api.nsone.net/saml/metadata`

    b. Typ in het tekstvak **URL beantwoorden** een URL die het volgende patroon gebruikt:`https://api.nsone.net/saml/sso/<ssoid>`

1. Selecteer **Extra URL's instellen**en voer de volgende stap uit als u de toepassing wilt configureren in de gestarte **SP-modus:**

    Typ in het tekstvak **AANmeldings-URL** de volgende URL:`https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > De URL-waarde van het antwoord is niet echt. Werk de waarde van de antwoord-URL bij met de werkelijke antwoord-URL. Neem contact op met het [NS1 SSO for Azure Client Support-team](mailto:techops@nsone.net) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De NS1 SSO voor Azure-toepassing verwacht de SAML-beweringen in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U de waarden van deze kenmerken beheren vanuit de sectie **Gebruikerskenmerken & Claims** op de pagina met toepassingsintegratie. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram om het dialoogvenster **Gebruikerskenmerken te** openen.

    ![Schermafbeelding van gebruikerskenmerken & sectie Claims, met potloodpictogram gemarkeerd](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selecteer de kenmerknaam die u wilt bewerken.

    ![Schermafbeelding van de sectie Gebruikerskenmerken & claims, waarbij de naam van het kenmerk is gemarkeerd](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecteer **Transformatie**.

    ![Schermafbeelding van de sectie Claim beheren, waarbij Transformatie is gemarkeerd](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Voer in de sectie **Transformatie beheren** de volgende stappen uit:

    ![Schermafbeelding van de sectie Transformatie beheren, met verschillende velden gemarkeerd](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecteer **ExactMailPrefix()** als **Transformatie**.

    1. Selecteer **user.userprincipalname** als **parameter 1**.

    1. Selecteer **Toevoegen**.

    1. Selecteer **Opslaan**.

1. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de knop Kopiëren. Hiermee wordt de url van de **appfederatie-metagegevens kopieën** en opgeslagen op uw computer.

    ![Schermafbeelding van het SAML-ondertekeningscertificaat, waarbij de knop kopiëren is gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:

   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het veld **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot NS1 SSO voor Azure.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **NS1 SSO voor Azure**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de gebruikerslijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO configureren voor Azure SSO

Als u eenmalige aanmelding wilt configureren aan de KANT van de NS1 SSO voor Azure, moet u de URL van de app-federatie metagegevens naar het [NS1 SSO voor Azure-ondersteuningsteam](mailto:techops@nsone.net)verzenden. Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Een NS1 SSO maken voor Azure-testgebruiker

In deze sectie maakt u een gebruiker genaamd B.Simon in NS1 SSO voor Azure. Werk samen met het NS1 SSO voor Azure-ondersteuningsteam om de gebruikers toe te voegen aan het NS1 SSO voor Azure-platform. U geen enkele aanmelding gebruiken totdat u gebruikers maakt en activeert.

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw azure AD-configuratie voor eenmalige aanmelding met access panel.

Wanneer u de NS1 SSO voor Azure-tegel selecteert in het Access Panel, moet u automatisch worden aangemeld bij de NS1 SSO voor Azure waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangspaneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie .

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer NS1 SSO voor Azure met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)