---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met SD-elementen | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SD Elements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a387659e2375444fd32cf731ab4bccc210b669a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081685"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SD Elements

In deze zelfstudie leert u hoe u SD-elementen integreren met Azure Active Directory (Azure AD). Wanneer u SD-elementen integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot SD-elementen.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SD Elements met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SD Elements single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SD Elements ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-sd-elements-from-the-gallery"></a>SD-elementen toevoegen uit de galerie

Als u de integratie van SD-elementen in Azure AD wilt configureren, moet u SD-elementen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SD-elementen** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **SD-elementen** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Azure AD-aanmelding voor SD-elementen configureren en testen

Azure AD SSO configureren en testen met SD-elementen met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SD Elements.

Als u Azure AD SSO wilt configureren en testen met SD-elementen, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[SD Elements SSO configureren](#configure-sd-elements-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Testgebruiker SD-elementen maken](#create-sd-elements-test-user)** - om een tegenhanger van B.Simon in SD-elementen te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SD** Elements-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de waarden in voor de volgende velden:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [het ondersteuningsteam van SD Elements](mailto:support@sdelements.com) Client om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De toepassing SD Elements verwacht dat de SAML-beweringen in een specifieke indeling zijn, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, SD Elements applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name |  Bronkenmerk|
    | --- | --- |
    | e-mail |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **SD-elementen instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SD Elements.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **SD-elementen**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sd-elements-sso"></a>SD-elementen SSO configureren

1. Als u eenmalige aanmelding wilt inschakelen, neemt u contact op met uw [ondersteuningsteam voor SD Elements](mailto:support@sdelements.com) en geeft u hen het gedownloade certificaatbestand.

1. Meld je in een ander browservenster aan bij de tenant van SD Elements als beheerder.

1. Klik in het menu bovenaan op **Systeem**en vervolgens **op Eén aanmelding**.

    ![Eenmalige aanmelding configureren](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Voer in het dialoogvenster **Instellingen voor aanmelding** smaken de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Selecteer **SAML**als **SSO-type**.

    b. Plak in het tekstvak **Identiteitsprovider-id** de waarde van **Azure AD-id**, die u hebt gekopieerd van Azure-portal.

    c. Plak in het tekstvak **Single Sign-On Service** van de identiteitsprovider de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    d. Klik op **Opslaan**.

### <a name="create-sd-elements-test-user"></a>Testgebruiker SD-elementen maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in SD Elements. In het geval van SD Elements is het maken van SD Elements-gebruikers een handmatige taak.

**Voer de volgende stappen uit om B.Simon in SD-elementen te maken:**

1. Meld u in een webbrowservenster aan op uw bedrijfssite van SD Elements als beheerder.

1. Klik in het menu bovenaan op **Gebruikersbeheer**en vervolgens **op Gebruikersgebruik**.

    ![Een testgebruiker voor SD-elementen maken](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klik op **Add New User**.

    ![Een testgebruiker voor SD-elementen maken](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Voer in het dialoogvenster **Nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![Een testgebruiker voor SD-elementen maken](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Voer in het **e-mailtekstvak** de **b.simon@contoso.com**e-mail van de gebruiker in, zoals .

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **B.**.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    d. Selecteer **Gebruiker** **als rol**.

    e. Klik op **Gebruiker maken**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SD-elementen in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SD-elementen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SD-elementen uitproberen met Azure AD](https://aad.portal.azure.com/)