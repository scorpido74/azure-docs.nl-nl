---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Freshworks | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Freshworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72376411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Freshworks

In deze zelfstudie leert u hoe u Freshworks integreert met Azure Active Directory (Azure AD). Wanneer u Freshworks integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Freshworks.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Freshworks met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Freshworks single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Freshworks ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-freshworks-from-the-gallery"></a>Freshworks toevoegen vanuit de galerie

Als u de integratie van Freshworks in Azure AD wilt configureren, moet u Freshworks uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Freshworks** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Freshworks** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Azure AD-aanmelding voor Freshworks configureren en testen

Azure AD SSO configureren en testen met Freshworks met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Freshworks.

Als u Azure AD SSO wilt configureren en testen met Freshworks, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Freshworks SSO configureren](#configure-freshworks-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak de testgebruiker](#create-freshworks-test-user)** van Freshworks - om een tegenhanger van B.Simon in Freshworks te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Freshworks-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.freshworks.com/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Freshworks Client](mailto:support@freshworks.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Als u de **ondertekeningsopties** wilt wijzigen volgens uw vereiste, klikt u op **De** knop Bewerken om het dialoogvenster **SAML-ondertekeningscertificaat** te openen.

     ![installatiekopie](common/edit-certificate.png)

     ![Freshworks-configuratie](./media/freshworks-tutorial/response.png)

    a. Selecteer **SAML-antwoord ondertekenen** als **ondertekeningsoptie**.

    b. Klik op **Opslaan**.

1. Kopieer in de sectie **Freshworks instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Freshworks.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Freshworks**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-freshworks-sso"></a>Freshworks SSO configureren

1. Open een nieuw webbrowservenster en meld u aan bij uw Freshworks-bedrijfssite als beheerder en voer de volgende stappen uit:

2. Klik links van het menu op **het** pictogram Beveiliging en controleer vervolgens de optie **Eén aanmelding** en selecteer **SAML SSO** onder **Verificatiemethoden**.

    ![Freshworks-configuratie](./media/freshworks-tutorial/configure01.png)

3. Voer in de sectie **Eén aanmelding** de volgende stappen uit:

    ![Freshworks-configuratie](./media/freshworks-tutorial/configure02.png)

    a. Klik **op Kopiëren** om de **entiteits-id van de serviceprovider (SP)** voor uw instantie te kopiëren en deze in het tekstvak Id **(Entiteit-id)** te plakken in de sectie **BasisSAML-configuratie** op azure-portal.

    b. Plak in de **entiteits-id die door het idp-tekstvak wordt geleverd,** de waarde **van de Azure AD-id,** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **SAML SSO URL** de **url-waarde aanmelding,** die u hebt gekopieerd uit de Azure-portal.

    d. Open het certificaat Base64 gecodeerd in kladblok, kopieer de inhoud ervan en plak het in het tekstvak **Beveiligingscertificaat.**

    e. Klik op **Opslaan**.

### <a name="create-freshworks-test-user"></a>Freshworks-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Freshworks. Werk samen met [het Ondersteuningsteam van Freshworks Client](mailto:support@freshworks.com) om de gebruikers toe te voegen aan het Freshworks-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Freshworks in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Freshworks waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Freshworks uitproberen met Azure AD](https://aad.portal.azure.com/)

