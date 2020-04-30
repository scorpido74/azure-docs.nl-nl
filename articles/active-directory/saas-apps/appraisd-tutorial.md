---
title: 'Zelf studie: integratie Azure Active Directory met beoordeeld | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67561202"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Zelf studie: integratie beoordeeld met Azure Active Directory

In deze zelf studie leert u hoe u kunt integreren met Azure Active Directory (Azure AD). Wanneer u de beoordeling met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de beoordeling.
* Stel in dat uw gebruikers automatisch worden aangemeld voor beoordeling met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Beoordeeld ondersteunt **SP-en IDP** -SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd toevoegen vanuit de galerie

Om de integratie van Appraisd in Microsoft Azure Active Directory te configureren, moet u Appraisd vanuit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **beoordeeld** in het zoekvak.
1. Selecteer **beoordeeld** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met beoordeeld met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de beoordeling.

Als u Azure AD SSO wilt configureren en testen met een beoordeling, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer beoordeeld](#configure-appraisd)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
5. **[Beoordeelde test gebruiker maken](#create-appraisd-test-user)** voor een tegen hanger van B. Simon beoordeeld in het verband met de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **beoordeelde** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop Opslaan en de volgende stappen uit te voeren:

    a. Klik op **Extra URL's instellen**.

    b. In het tekstvak **Relaystatus** typt u een URL: `<TENANTCODE>`

    c. Als u de toepassing in de door **SP** geïnitieerde modus wilt configureren, typt u in het tekstvak **Aanmeldings-URL** een URL met het volgende patroon: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > U krijgt de werkelijke waarde van de aanmeldings-URL en de relaystatus op de pagina voor de configuratie van eenmalige aanmelding met Appraisd, wat later in de zelfstudie wordt uitgelegd.

1. De beoordeelde toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Voor een beoordeelde toepassing wordt verwacht dat **nameidentifier** wordt toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de juiste URL ('s) op basis van uw vereiste op de sectie **beoordeeld** instellen.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Beoordeeld configureren

1. Als u de configuratie wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op het **installatie programma** dat u hebt beoordeeld, wordt u naar de geoordeelde toepassing geleid. Geef de beheerders referenties op om u aan te melden bij beoordeeld. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw beoordeelde bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik in de rechter bovenhoek van de pagina op **instellingen** pictogram en navigeer vervolgens naar **configuratie**.

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Klik aan de linkerkant van het menu op **SAML single sign-on**.

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Voer op de **configuratiepagina voor eenmalige aanmelding op basis van SAML 2.0** de volgende stappen uit:

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopieer de **standaard waarde voor relay State** en plak deze in het tekstvak **Relay-status** in de basis- **SAML-configuratie** op Azure Portal.

    b. Kopieer de waarde van de door de **service geïnitieerde aanmeldings-URL** en plak deze in het tekstvak **URL voor aanmelden** in de basis- **SAML-configuratie** op Azure Portal.

7. Schuif omlaag op de dezelfde pagina en voer de volgende stappen uit onder **Gebruikers identificeren**:

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Plak in het tekstvak **Identity Provider Single Sign-On URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Microsoft Azure-portal en klik op **Save**.

    b. Plak in het tekstvak URL-Uitgever van de **identiteits provider** de waarde van de **Azure ad-id**, die u hebt gekopieerd uit de Azure Portal en klik op **Opslaan**.

    c. Open in Klad blok het met base 64 gecodeerde certificaat dat u hebt gedownload van de Azure Portal, kopieer de inhoud en plak deze in het vak **X. 509-certificaat** en klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B. Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de beoordeling.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Appraisd** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-appraisd-test-user"></a>Appraisd testgebruiker maken

Als u wilt dat Microsoft Azure Active Directory-gebruikers zich kunnen aanmelden bij Appraisd, moeten ze worden ingericht in Appraisd. In het geval van Appraisd is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u bij Appraisd als een beveiligingsbeheerder aan.

2. Klik in de rechter bovenhoek van de pagina op **instellingen** pictogram en ga vervolgens naar **Administration Center**.

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Klik in de werk balk boven aan de pagina op **personen**en vervolgens op navigeren om **een nieuwe gebruiker toe te voegen**.

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Voer de volgende stappen uit in het dialoogvenster **Add a new user**:

    ![installatiekopie](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Voer in het tekstvak **voor naam** de voor naam van de gebruiker in, zoals **Julia**.

    b. Voer in het tekstvak **laatste naam** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `B. Simon@contoso.com`.

    d. Klik op **gebruiker toevoegen**.

### <a name="test-sso"></a>SSO testen

Wanneer u de geoordeelde tegel in het toegangs venster selecteert, wordt u automatisch aangemeld bij het beoordeeld waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
