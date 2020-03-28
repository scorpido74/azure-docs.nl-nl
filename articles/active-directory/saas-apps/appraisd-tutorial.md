---
title: 'Zelfstudie: Azure Active Directory-integratie met Appraisd | Microsoft Documenten'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67561202"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Zelfstudie: Appraisd integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Appraisd integreren met Azure Active Directory (Azure AD). Wanneer u Appraisd integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Appraisd.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Appraisd met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Appraisd single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Appraisd ondersteunt **SP en IDP** geïnitieerd SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd toevoegen vanuit de galerie

Om de integratie van Appraisd in Microsoft Azure Active Directory te configureren, moet u Appraisd vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Appraisd** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Appraisd** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Appraisd met behulp van een testgebruiker genaamd **B. Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Appraisd.

Als u Azure AD SSO met Appraisd wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Appraisd](#configure-appraisd)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-eenmaligaanmelding met B. Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak appraisd-testgebruiker](#create-appraisd-test-user)** om een tegenhanger van B. Simon in Appraisd te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Appraisd-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop Opslaan te klikken en de volgende stappen uit te voeren:

    a. Klik op **Extra URL's instellen**.

    b. In het tekstvak **Relaystatus** typt u een URL: `<TENANTCODE>`

    c. Als u de toepassing in de door **SP** geïnitieerde modus wilt configureren, typt u in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > U krijgt de werkelijke waarde van de aanmeldings-URL en de relaystatus op de pagina voor de configuratie van eenmalige aanmelding met Appraisd, wat later in de zelfstudie wordt uitgelegd.

1. Appraisd-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Appraisd-toepassing verwacht **dat naam-id** wordt toegewezen met **user.mail,** dus u moet de toewijzing van het kenmerk bewerken door op **pictogram Bewerken** te klikken en de toewijzing van het kenmerk wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Appraisd** instellen de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Appraisd configureren

1. Als u de configuratie binnen Appraisd wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Setup Appraisd** zal u doorverwijzen naar de Appraisd applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Appraisd. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Appraisd handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan op uw Appraisd-bedrijfssite en voert u de volgende stappen uit:

4. Klik rechtsboven op de pagina op het pictogram **Instellingen** en navigeer vervolgens naar **Configuratie.**

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Klik aan de linkerkant van het menu op **SAML single sign-on**.

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Voer op de **configuratiepagina voor eenmalige aanmelding op basis van SAML 2.0** de volgende stappen uit:

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopieer de waarde **standaardrelaystatus** en plak deze in het tekstvak **Relaystatus** in **BasisSAML-configuratie** op Azure-portal.

    b. Kopieer de **door service geïnitieerde inlog-URL-waarde** en plak deze in het **tekstvak VOOR aanmeldings-URL** in **BasisSAML-configuratie** op Azure-portal.

7. Schuif omlaag op de dezelfde pagina en voer de volgende stappen uit onder **Gebruikers identificeren**:

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Plak in het tekstvak **Identity Provider Single Sign-On URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Microsoft Azure-portal en klik op **Save**.

    b. Plak in het tekstvak van de URL van de uitgever van **de identiteitsprovider** de waarde van **azure AD-id**, die u hebt gekopieerd vanuit de Azure-portal en klik op **Opslaan**.

    c. Open in Kladblok het basis-64-gecodeerde certificaat dat u hebt gedownload van de Azure-portal, kopieer de inhoud ervan en plak het vervolgens in het vak **X.509-certificaat** en klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B. Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B. Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B. Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Appraisd.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Appraisd** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen **B. Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-appraisd-test-user"></a>Appraisd testgebruiker maken

Als u wilt dat Microsoft Azure Active Directory-gebruikers zich kunnen aanmelden bij Appraisd, moeten ze worden ingericht in Appraisd. In het geval van Appraisd is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u bij Appraisd als een beveiligingsbeheerder aan.

2. Klik rechtsboven op de pagina op het pictogram **Instellingen** en navigeer vervolgens naar **het beheercentrum**.

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Klik op de werkbalk boven aan de pagina op **Personen**en navigeer vervolgens naar **Een nieuwe gebruiker toevoegen.**

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Voer de volgende stappen uit in het dialoogvenster **Add a new user**:

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta.**

    b. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `B. Simon@contoso.com`.

    d. Klik **op Gebruiker toevoegen**.

### <a name="test-sso"></a>Test SSO

Wanneer u de appraisd-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Appraisd waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
