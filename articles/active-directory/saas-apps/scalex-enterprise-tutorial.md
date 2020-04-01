---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ScaleX Enterprise | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ScaleX Enterprise.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ScaleX Enterprise

In deze zelfstudie leert u hoe u ScaleX Enterprise integreren met Azure Active Directory (Azure AD). Wanneer u ScaleX Enterprise integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot ScaleX Enterprise.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ScaleX Enterprise met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* ScaleX Enterprise single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* ScaleX Enterprise ondersteunt **SP en IDP** gestart met SSO

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ScaleX Enterprise toevoegen vanuit de galerie

Als u de integratie van ScaleX Enterprise in Azure AD wilt configureren, moet u ScaleX Enterprise vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ScaleX Enterprise** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **ScaleX Enterprise** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Azure AD-aanmelding voor ScaleX Enterprise configureren en testen

Azure AD SSO configureren en testen met ScaleX Enterprise met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ScaleX Enterprise.

Als u Azure AD SSO wilt configureren en testen met ScaleX Enterprise, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer ScaleX Enterprise SSO](#configure-scalex-enterprise-sso)** - om de instellingen voor één aanmelding aan toepassingszijde te configureren.
    * **[Maak ScaleX Enterprise-testgebruiker](#create-scalex-enterprise-test-user)** - om een tegenhanger van B.Simon in ScaleX Enterprise te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ScaleX** Enterprise-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://platform.rescale.com/saml2/<company id>/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het ondersteuningsteam van ScaleX Enterprise Client](https://info.rescale.com/contact_sales) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Uw ScaleX Enterprise-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven, waarbij **het e-mailadres** is toegewezen aan **user.mail**. ScaleX Enterprise-toepassing verwacht dat **het e-mailadres** wordt toegewezen met **user.userprincipalname,** dus u moet de toewijzing van het kenmerk bewerken door op **pictogram Bewerken** te klikken en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **ScaleX Enterprise instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ScaleX Enterprise.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **ScaleX Enterprise**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-scalex-enterprise-sso"></a>ScaleX Enterprise SSO configureren

1. Als u de configuratie binnen ScaleX Enterprise wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure installeren** door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **ScaleX Enterprise instellen** en verwijst u naar de ScaleX Enterprise-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij ScaleX Enterprise. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u ScaleX Enterprise handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich aan bij uw ScaleX Enterprise-bedrijfssite als beheerder en voert u de volgende stappen uit:

1. Klik op het menu rechtsboven en selecteer **Contoso-beheer**.

    > [!NOTE]
    > Contoso is slechts een voorbeeld. Dit zou uw werkelijke bedrijfsnaam moeten zijn.

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Selecteer **Integraties** in het bovenste menu en selecteer **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Vul het formulier als volgt in:

    ![Eenmalige aanmelding configureren](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Selecteer **Elke gebruiker maken die zich kan verifiëren met SSO**

    b. **Serviceprovider saml**: Plak de waarde ***urn:oase:namen:tc:SAML:2.0:nameid-notatie:persistent***

    c. **E-mailveld Naam van identiteitsprovider in ACS-antwoord:** De waarde plakken`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Entiteits-id van de identiteitsprovider:** Plak de azure **AD-id-waarde** die is gekopieerd van de Azure-portal.

    e. **URL van singlesignonservice van identiteitsprovider:** Plak de **aanmeldings-URL** van de Azure-portal.

    f. **Openbaar X509-certificaat voor identiteitsprovider:** Open het X509-certificaat dat is gedownload van het Azure in kladblok en plak de inhoud in dit vak. Zorg ervoor dat er geen regeleinden in het midden van de inhoud van het certificaat zijn.

    g. Schakel de volgende selectievakjes **in: Ingeschakeld, Naam-id versleutelen en AuthnRequests ondertekenen.**

    h. Klik **op SSO-instellingen bijwerken** om de instellingen op te slaan.

### <a name="create-scalex-enterprise-test-user"></a>ScaleX Enterprise-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij ScaleX Enterprise, moeten ze zijn ingericht bij ScaleX Enterprise. In het geval van ScaleX Enterprise is inrichten een automatische taak en zijn er geen handmatige stappen vereist. Elke gebruiker die met succes kan verifiëren met SSO-referenties, wordt automatisch ingericht aan de ScaleX-kant.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ScaleX Enterprise in het Access-paneel klikt, moet u automatisch worden aangemeld bij de ScaleX Enterprise waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer ScaleX Enterprise met Azure AD](https://aad.portal.azure.com/)