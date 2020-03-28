---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Pipedrive | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Pipedrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3b97628d-9d0c-45ac-b8ef-7480cf7ec602
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 235169efdcd415df055885d325394c942a2a8d88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79485648"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Pipedrive

In deze zelfstudie leert u hoe u Pipedrive integreren met Azure Active Directory (Azure AD). Wanneer u Pipedrive integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Pipedrive.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Pipedrive met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Pipedrive single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Pipedrive ondersteunt **SP en IDP** gestart met SSO
* Zodra u Pipedrive SSO hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-pipedrive-from-the-gallery"></a>Pipedrive toevoegen vanuit de galerie

Als u de integratie van Pipedrive in Azure AD wilt configureren, moet u Pipedrive vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Pipedrive** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **Pipedrive** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>Azure AD-aanmelding voor Pipedrive configureren en testen

Azure AD SSO configureren en testen met Pipedrive met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Pipedrive.

Als u Azure AD SSO wilt configureren en testen met Pipedrive, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Pipedrive SSO configureren](#configure-pipedrive-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Pipedrive-testgebruiker maken](#create-pipedrive-test-user)** - om een tegenhanger van B.Simon in Pipedrive te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Pipedrive-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [het ondersteuningsteam van Pipedrive Client](mailto:support@pipedrive.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Pipedrive-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Pipedrive applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer en kopieer ook de url van de App **Federation Metadata** en sla het op uw computer op.

    ![De link om het certificaat te downloaden](./media/pipedrive-tutorial/certificate-data.png)

1. Kopieer in de sectie **Pipedrive instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Pipedrive.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Pipedrive**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-pipedrive-sso"></a>Pipedrive SSO configureren

1. Meld u in een ander browservenster aan bij pipedrive-website als beheerder.

1. Klik op **Gebruikersprofiel** en selecteer **Instellingen**.

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/configure1.png)

1. Schuif omlaag naar het beveiligingscentrum en selecteer **Eén aanmelding**.

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/configure2.png)

1. Voer in de **saml-configuratie voor pipedrive** de volgende stappen uit:

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/configure3.png)

    a. Plak in het tekstvak **Van de uitgever** de **url-waarde van de app-federatie-metagegevens,** die u hebt gekopieerd van de Azure-portal.

    b. Plak in het **tekstvak Url-url van Enkelteken (SSO)** de **waarde van de inlog-URL,** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak Van de **url van De Enlo** de **URL-waarde Van afmelden,** die u hebt gekopieerd uit de Azure-portal.

    d. Open in het tekstvak van het **x.509-certificaat** het gedownloade **certificaatbestand (Base64)** van Azure-portal in kladblok en kopieer de inhoud ervan en plak in het tekstvak van **het x.509-certificaat** en sla wijzigingen op.

### <a name="create-pipedrive-test-user"></a>Pipedrive-testgebruiker maken

1. Meld u in een ander browservenster aan bij pipedrive-website als beheerder.

1. Scroll omlaag naar het bedrijf en selecteer **gebruikers beheren.**

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/user1.png)

1. Klik op **Gebruikers toevoegen**.
    
    ![Pipedrive-configuratie](./media/pipedrive-tutorial/user2.png)

1. Voer **in** de sectie Gebruikers beheren de volgende stappen uit:

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/user3.png)

    a. Voer in het **tekstvak E-mail** het `B.Simon@contoso.com`e-mailadres van de gebruiker in, zoals .

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in.

    d. Klik **op Gebruikers bevestigen en uitnodigen**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de pipedrive-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Pipedrive waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Pipedrive uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)