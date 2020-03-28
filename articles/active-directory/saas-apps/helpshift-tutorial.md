---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Helpshift | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Helpshift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 114de95d-e9a7-4f87-b14d-54b91a63ce49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f7e932a3b71e802c5b814f6dfb59922148c2519
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75533872"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Helpshift

In deze zelfstudie leert u hoe u Helpshift integreren met Azure Active Directory (Azure AD). Wanneer u Helpshift integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Helpshift.
* Stel uw gebruikers in om automatisch te worden aangemeld bij Helpshift met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Met Helpshift single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Helpshift ondersteunt **SP en IDP** geïnitieerde SSO

## <a name="adding-helpshift-from-the-gallery"></a>Helpshift toevoegen vanuit de galerie

Als u de integratie van Helpshift in Azure AD wilt configureren, moet u Helpshift vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Helpshift** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Helpshift** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Azure AD-aanmelding voor Helpshift configureren en testen

Azure AD SSO configureren en testen met Helpshift met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Helpshift.

Als u Azure AD SSO wilt configureren en testen met Helpshift, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Helpshift SSO configureren](#configure-helpshift-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Helpshift-testgebruiker maken](#create-helpshift-test-user)** - om een tegenhanger van B.Simon in Helpshift te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Helpshift-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<YourDOMAIN>.helpshift.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Klik op **Extra URL's instellen** en voer de volgende stappen uit als u de toepassing in de met **SP** geïnitieerde modus wilt configureren:

    d. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de URL van het antwoord, de URL van Sign-on en de relaystatus. Neem contact op met [helpshift client support team](mailto:support@helpshift.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Helpshift instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Helpshift.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Helpshift**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-helpshift-sso"></a>Helpshift SSO configureren

1. Meld u in een andere webbrowser aan bij uw Helpshift-toepassing als beheerder.

1. Open het **Helpshift-dashboard** en klik op **het pictogram Instellingen**.

    ![De Helpshift-configuratie](./media/helpshift-tutorial/configuration01.png)

1. Klik op het tabblad **Integraties** en voer de volgende stappen uit:

    ![De Helpshift-configuratie](./media/helpshift-tutorial/configuration02.png)

    a. Schakel de **Single Sign-On(SAML – SSO)** in.

    b. Selecteer **Identity Provider(IDP)** als **Azure Active Directory**.

    c. Plak in het tekstvak **SAML 2.0-eindpunt URL** de **url-waarde aanmelding,** die u hebt gekopieerd uit de Azure-portal.

    d. Open het bestand **'Certificaat(Basis64)** in kladblok, kopieer de inhoud van het bestand (zonder gebruik te maken van de regels 'BEGIN CERTIFICAAT—––'- en '—-END CERTIFICATE—––') en plak het in het tekstvak **van het Certificaat x.509.**

    e. Plak in het tekstvak **van de url van de uitgever** de waarde van de Azure **AD-id,** die u hebt gekopieerd van de Azure-portal.

    f. Klik op **WIJZIGINGEN TOEPASSEN**.

### <a name="create-helpshift-test-user"></a>Helpshift-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Helpshift. Werk samen met [helpshift-clientondersteuningsteam](mailto:support@helpshift.com) om de gebruikers toe te voegen aan het Helpshift-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Tegel Helpshift in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Helpshift waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Helpshift uitproberen met Azure AD](https://aad.portal.azure.com/)