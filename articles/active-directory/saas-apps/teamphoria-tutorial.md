---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Teamphoria | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2631b34f5658c9d4f76ca26d378bc63fe59ad156
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373256"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamphoria"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Teamphoria

In deze zelfstudie leert u hoe u Teamphoria integreren met Azure Active Directory (Azure AD). Wanneer u Teamphoria integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Teamphoria.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Teamphoria met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Teamphoria single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Teamphoria ondersteunt **SP** geïnitieerde SSO

## <a name="adding-teamphoria-from-the-gallery"></a>Teamphoria toevoegen vanuit de galerie

Als u de integratie van Teamphoria in Azure AD wilt configureren, moet u Teamphoria uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Teamphoria** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Teamphoria** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-teamphoria"></a>Azure AD-aanmelding voor Teamphoria configureren en testen

Azure AD SSO configureren en testen met Teamphoria met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Teamphoria.

Als u Azure AD SSO wilt configureren en testen met Teamphoria, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Teamphoria SSO](#configure-teamphoria-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak teamphoria-testgebruiker](#create-teamphoria-test-user)** - om een tegenhanger van B.Simon in Teamphoria te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Teamphoria-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [teamphoria client support team](https://www.teamphoria.com/) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Teamphoria instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Teamphoria.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Teamphoria**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-teamphoria-sso"></a>Teamphoria SSO configureren

1. Als u de configuratie binnen Teamphoria wilt automatiseren, moet u **de beveiligingsextensie Mijn apps Secure installeren** door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Teamphoria instellen** en stuurt u naar de Teamphoria-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Teamphoria. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Teamphoria handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw teamphoria-bedrijfssite en voert u de volgende stappen uit:

4. Ga naar de optie **BEHEER-INSTELLINGEN** op de linkerwerkbalk en klik onder het tabblad Configureren op EÉN AANMELDING om het **SSO-configuratievenster** te openen.

    ![Eenmalige aanmelding configureren](./media/teamphoria-tutorial/admin_sso_configure.png)

5. Klik op **NIEUWE IDENTITEITSPROVIDER TOEVOEGEN** in de rechterbovenhoek om het formulier te openen voor het toevoegen van de instellingen voor SSO.

    ![Eenmalige aanmelding configureren](./media/teamphoria-tutorial/add_new_identity_provider.png)

6. Voer de details in de onderstaande velden in.

    ![Eenmalige aanmelding configureren](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **WEERGAVENAAM:** Voer de weergavenaam van de plug-in in in op de beheerderspagina.

    b. **KNOPNAAM**: De naam van het tabblad dat wordt weergegeven op de aanmeldingspagina om in te loggen via SSO.

    c. **CERTIFICAAT:** Open het certificaat dat eerder is gedownload van de Azure-portal in kladblok, kopieer de inhoud van hetzelfde en plak het hier in het vak.

    d. **INGANGsPUNT**: Plak de eerder gekopieerde **inlog-URL** uit de Azure-portal.

    e. Schakel de optie **in op AAN** en klik op **OPSLAAN**.

### <a name="create-teamphoria-test-user"></a>Teamphoria-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Teamphoria, moeten ze worden ingericht in Teamphoria. In het geval van Teamphoria is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw teamphoria-bedrijfssite als beheerder.

1. Klik op **BEHEERDERSinstellingen** op de linkerwerkbalk en klik onder het tabblad **BEHEREN** op **GEBRUIKERS** om de beheerderspagina voor gebruikers te openen.

    ![Werknemer toevoegen](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klik op de optie **HANDMATIG UITNODIGEN.**

    ![Mensen uitnodigen](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Voer op deze pagina de volgende actie uit.

    ![Mensen uitnodigen](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Voer in het tekstvak **e-mailadres** het **e-mailadres** van de gebruiker in, zoals B.Simon.

    b. Voer in het tekstvak **VOORNAAM** de voornaam van de gebruiker in, zoals **B.**

    c. Voer in het tekstvak **ACHTERNAAM** de achternaam van de gebruiker in, zoals **Simon**.

    d. Klik **op UITNODIGEN 1 GEBRUIKER**. De gebruiker moet de uitnodiging accepteren om in het systeem te worden gemaakt.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Teamphoria in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Teamphoria waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Teamphoria met Azure AD](https://aad.portal.azure.com/)

