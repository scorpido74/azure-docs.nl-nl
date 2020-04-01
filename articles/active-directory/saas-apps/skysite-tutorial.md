---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SKYSITE | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SKYSITE.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 04c6a47a-1730-4acf-bc5c-a04daccff9b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93ef7f4201e9613cc6fa4391bc28d257272fa1c7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026127"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SKYSITE

In deze zelfstudie leert u hoe u SKYSITE integreert met Azure Active Directory (Azure AD). Wanneer u SKYSITE integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot SKYSITE.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SKYSITE met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SkySITE single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SKYSITE ondersteunt **IDP** geïnitieerde SSO

* SKYSITE ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-skysite-from-the-gallery"></a>SKYSITE toevoegen vanuit de galerie

Als u de integratie van SKYSITE in Azure AD wilt configureren, moet u SKYSITE vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SKYSITE in** het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **SKYSITE** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>Azure AD-aanmelding voor SKYSITE configureren en testen

Azure AD SSO configureren en testen met SKYSITE met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SKYSITE.

Als u Azure AD SSO wilt configureren en testen met SKYSITE, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[SkySITE SSO configureren](#configure-skysite-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[SkySITE-testgebruiker maken](#create-skysite-test-user)** - om een tegenhanger van B.Simon in SKYSITE te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Klik in de [Azure-portal](https://portal.azure.com/)op de integratiepagina van **skysite-toepassingen** op **het tabblad Eigenschappen** en voer de volgende stap uit: 

    ![Eigenschappen voor eenmalige aanmelding](./media/skysite-tutorial/config05.png)

    * Kopieer de **URL van gebruikerstoegang** en u moet deze plakken in de **sectie SkySITE SSO configureren,** die later in de zelfstudie wordt uitgelegd.

1. Navigeer op de **integratiepagina van skysite-toepassingen** naar **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. SkySITE-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, SKYSITE applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. Voer in de sectie **Gebruikerskenmerken & claims** in het dialoogvenster **Groepsclaims (Preview)** de volgende stappen uit:

    a. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

    ![installatiekopie](./media/skysite-tutorial/config01.png)

    ![installatiekopie](./media/skysite-tutorial/config02.png)

    b. Selecteer **Alle groepen** in de radiolijst.

    c. Selecteer **Bronkenmerk** van **groeps-id**.

    d. Klik op **Opslaan**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **SKYSITE instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SKYSITE.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **SKYSITE**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="configure-skysite-sso"></a>SKYSITE SSO configureren

1. Open een nieuw webbrowservenster en meld u aan bij uw SKYSITE-bedrijfssite als beheerder en voer de volgende stappen uit:

4. Klik op **Instellingen** rechtsboven op de pagina en navigeer vervolgens naar **Accountinstelling**.

    ![Configuratie](./media/skysite-tutorial/config03.png)

5. Ga naar het tabblad Enkel teken op het tabblad **SSO** en voer de volgende stappen uit:

    ![Configuratie](./media/skysite-tutorial/config04.png)

    a. Plak in het **tekstvak URL-tekst van de identiteitsprovider** de waarde van de URL van **gebruikerstoegang**, die u hebt gekopieerd van het tabblad **Eigenschappen** in azure-portal.

    b. Klik **op Certificaat uploaden**om het basisgecodeerde certificaat te uploaden dat u hebt gedownload van de Azure-portal.

    c. Klik op **Opslaan**.

### <a name="create-skysite-test-user"></a>SkySITE-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in SKYSITE. SKYSITE ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in SKYSITE, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de skysite-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de SKYSITE waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SkySITE uitproberen met Azure AD](https://aad.portal.azure.com/)

