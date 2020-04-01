---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Harness | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Harness.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21409eb056743d92db42e0787af24f8cec07db1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026956"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Harness

In deze zelfstudie leert u hoe u Harnas integreren met Azure Active Directory (Azure AD). Wanneer u Harness integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Harnas.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Harness met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Gebruik eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Harness ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-harness-from-the-gallery"></a>Harnas toevoegen vanuit de galerie

Als u de integratie van Harness in Azure AD wilt configureren, moet u Harnas uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Harnas** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **Harnas** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Azure AD-aanmelding voor Harnas configureren en testen

Azure AD SSO configureren en testen met Harnas met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Harnas.

Als u Azure AD SSO met Harnas wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Harnas SSO](#configure-harness-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak de testgebruiker van Harnas](#create-harness-test-user)** - om een tegenhanger van B.Simon in Harnas te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina De integratie **van** toepassingen benutten de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.harness.io/`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. U krijgt de werkelijke URL van het antwoord van de sectie **Harnas sso configureren,** die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Harnas instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Harness.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Harnas**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-harness-sso"></a>Harnas SSO configureren

1. Als u de configuratie binnen Harness wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Harness** en leidt u naar de Toepassing Harnas. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Harnas. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Harnas handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan op uw Harness-bedrijfssite en voert u de volgende stappen uit:

4. Klik rechtsboven op de pagina op > **Verificatieinstellingen**voor continue > **beveiliging.** **Continuous Security**

    ![Harnasconfiguratie](./media/harness-tutorial/configure01.png)

5. Klik in de sectie **SSO Providers** op **+ SSO Providers** > **SAML toevoegen.**

    ![Harnasconfiguratie](./media/harness-tutorial/configure03.png)

6. Voer in de pop-up **van DE SAML Provider** de volgende stappen uit:

    ![Harnasconfiguratie](./media/harness-tutorial/configure02.png)

    a. Kopieer de **in uw SSO-provider, schakel saml-gebaseerde aanmelding in, voer vervolgens de volgende URL-instantie in** en plak deze in tekstvak Antwoord-URL in de sectie **BasisSAML-configuratie** op Azure-portal.

    b. Typ de weergavenaam in het tekstvak **Weergavenaam.**

    c. Klik **op Bestand kiezen** om het XML-bestand Met aalmetten van federatiemetagegevens te uploaden, dat u hebt gedownload van Azure AD.

    d. Klik **op VERZENDEN**.

### <a name="create-harness-test-user"></a>Gebruiker van de Harnastest maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Harness, moeten ze zijn ingericht in Harnas. In Harnas is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Harnas als beheerder.

1. Klik rechtsboven op de pagina op**Gebruikers** **van Continuous Security** > **Access Management.** > 

    ![Harnasconfiguratie](./media/harness-tutorial/configure04.png)

1. Klik aan de rechterkant van de pagina op **+ Gebruiker toevoegen**.

    ![Harnasconfiguratie](./media/harness-tutorial/configure05.png)

1. Voer in de pop-up **Gebruiker toevoegen** de volgende stappen uit:

    ![Harnasconfiguratie](./media/harness-tutorial/configure06.png)

    a. Voer in het tekstvak **E-mailadres(en)** de e-mail van de gebruiker in, zoals `B.simon@contoso.com`.

    b. Selecteer uw **gebruikersgroepen**.

    c. Klik **op Verzenden**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Harnas in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het harnas waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Harnas uitproberen met Azure AD](https://aad.portal.azure.com/)

