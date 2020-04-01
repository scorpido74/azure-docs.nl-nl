---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met LinkedIn Elevate | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en LinkedIn Elevate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03efae5f9dec904f141a6776766850aa1f328892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74892121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met LinkedIn Elevate

In deze zelfstudie leert u hoe u LinkedIn Elevate integreren met Azure Active Directory (Azure AD). Wanneer u LinkedIn Elevate integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot LinkedIn Elevate.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij LinkedIn Elevate met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* LinkedIn Elevate single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.



* LinkedIn Elevate ondersteunt **SP en IDP** geïnitieerdse SSO
* LinkedIn Elevate ondersteunt **Just In Time-gebruikersinrichting**
* LinkedIn Elevate ondersteunt [ **geautomatiseerde** gebruikersinrichting](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn Elevate toevoegen vanuit de galerie

Als u de integratie van LinkedIn Elevate in Azure AD wilt configureren, moet u LinkedIn Elevate vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **LinkedIn Omhoog** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **LinkedIn Elevate** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Azure AD-aanmelding configureren en testen voor LinkedIn Elevate

Azure AD SSO configureren en testen met LinkedIn Elevate met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in LinkedIn Elevate.

Als u Azure AD SSO wilt configureren en testen met LinkedIn Elevate, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer LinkedIn Elevate SSO](#configure-linkedin-elevate-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[LinkedIn Elevate-testgebruiker maken](#create-linkedin-elevate-test-user)** - om een tegenhanger van B.Simon in LinkedIn Elevate te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **LinkedIn** Elevate-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Voer in het tekstvak **Id** de waarde **entiteits-id** in, u kopieert de waarde van de entiteits-id van de Linkedin-portal die later in deze zelfstudie wordt uitgelegd.

    b. Voer in het tekstvak **Url van antwoord** de **url-waarde assertion consumer access (ACS)** in, u kopieert de URL-waarde (Assertion Consumer Access) van de Linkedin-portal die later in deze zelfstudie wordt uitgelegd.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. LinkedIn Elevate-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. LinkedIn Elevate-toepassing verwacht dat naam-id wordt toegewezen met **user.mail,** dus u moet de toewijzing van het kenmerk bewerken door te klikken op pictogram Bewerken en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, LinkedIn Elevate applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name | Bronkenmerk|
    | -------| -------------|
    | department | user.department |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **LinkedIn Elevate instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot LinkedIn Elevate.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **LinkedIn Elevate**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-linkedin-elevate-sso"></a>LinkedIn Elevate SSO configureren

1. Meld je in een ander browservenster aan bij je LinkedIn Elevate-tenant als beheerder.

1. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook **Elevate - Elevate AAD-test** in de vervolgkeuzelijst.

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klik op **OR Klik hier om afzonderlijke velden uit het formulier te laden en te kopiëren** en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopieer **entiteits-id** en plak deze in het tekstvak **Identifier** in de **basisSAML-configuratie** in de Azure-portal.

    b. **Acs-url (Assertion Consumer Access) kopiëren** en plakken in het tekstvak **Url van antwoord** in de **basisSAML-configuratie** in de Azure-portal.

1. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload van de Azure-portal door te klikken op de optie XML-bestand uploaden.

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Klik op **Aan** om SSO in te schakelen. De SSO-status verandert **van Niet verbonden** naar **Verbonden**

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn Elevate-testgebruiker maken

LinkedIn Elevate Application ondersteunt Just in time user provisioning en na authenticatie worden gebruikers automatisch in de applicatie gemaakt. Op de pagina beheerdersinstellingen op de LinkedIn Elevate-portal draai de schakelaar **Automatisch licenties toewijzen** aan actieve Just-in-time provisioning en dit zal ook een licentie toewijzen aan de gebruiker. LinkedIn Elevate ondersteunt ook automatische gebruikersinrichting, vindt u [hier](linkedinelevate-provisioning-tutorial.md) meer informatie over het configureren van automatische gebruikersinrichting.

   ![Een Azure AD-testgebruiker maken](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LinkedIn Elevate in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de LinkedIn Elevate waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer LinkedIn Elevate met Azure AD](https://aad.portal.azure.com/)

