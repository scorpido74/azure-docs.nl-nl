---
title: 'Zelfstudie: Azure Active Directory-integratie met Jitbit Helpdesk | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Jitbit Helpdesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 5d4900713cd8d96180bf74b300a738a8b676421e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547151"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Zelfstudie: Azure Active Directory-integratie met Jitbit Helpdesk

In deze zelfstudie leert u hoe u Jitbit Helpdesk kunt integreren met Azure Active Directory (Azure AD).
De integratie van Jitbit Helpdesk met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Jitbit Helpdesk.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Jitbit Helpdesk (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Jitbit Helpdesk hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Jitbit Helpdesk waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Jitbit Helpdesk ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Jitbit Helpdesk toevoegen vanuit de galerie

Om de integratie van Jitbit Helpdesk te configureren in Azure AD, moet u Jitbit Helpdesk vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Jitbit Helpdesk toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Jitbit Helpdesk** in het zoekvak, selecteer **Jitbit Helpdesk** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Jitbit Helpdesk in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Jitbit Helpdesk op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Jitbit Helpdesk tot stand is gebracht.

Om eenmalige aanmelding met Azure Active Directory bij Jitbit Helpdesk te testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Jitbit Helpdesk configureren](#configure-jitbit-helpdesk-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker voor Jitbit Helpdesk maken](#create-jitbit-helpdesk-test-user)** : als u een tegenhanger van Britta Simon in Jitbit Helpdesk wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Jitbit Helpdesk moet u de volgende stappen uitvoeren:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **Jitbit Helpdesk** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Jitbit Helpdesk](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [klantenondersteuningsteam van Jitbit Helpdesk](https://www.jitbit.com/support/) om deze waarde op te vragen.

    b. Typ een URL in het vak **Id (Entiteits-id)** , als volgt: `https://www.jitbit.com/web-helpdesk/`

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in het gedeelte **Jitbit Helpdesk instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Eenmalige aanmelding van Jitbit Helpdesk configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Jitbit Helpdesk-bedrijfssite.

1. Klik in de werkbalk bovenaan op **Beheer**.

    ![Beheer](./media/jitbit-helpdesk-tutorial/ic777681.png "Beheer")

1. Klik op **Algemene instellingen**.

    ![Gebruikers, bedrijven en machtigingen](./media/jitbit-helpdesk-tutorial/ic777680.png "Gebruikers, bedrijven en machtigingen")

1. Voer in het gedeelte **Verificatie-instellingen** configureren de volgende stappen uit:

    ![Verificatie-instellingen](./media/jitbit-helpdesk-tutorial/ic777683.png "Verificatie-instellingen")

    a. Selecteer **SAML 2.0-eenmalige aanmelding inschakelen** om u aan te melden met behulp van eenmalige aanmelding (SSO) met **OneLogin**.

    b. Plak in het tekstvak **Eindpunt-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Open in Kladblok het met **Base 64** gecodeerde certificaat, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **X.509-certificaat**

    d. Klik op **Save changes** (Wijzigingen opslaan).

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension** .  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding via Azure door haar toegang te geven tot Jitbit Helpdesk.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **Jitbit Helpdesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Jitbit Helpdesk** in de lijst met toepassingen.

    ![De Jitbit Helpdesk-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit Helpdesk-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Jitbit Helpdesk, moeten ze worden ingericht in Jitbit Helpdesk. In het geval van Jitbit Helpdesk is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Jitbit Helpdesk**-tenant.

1. Klik in het menu bovenaan op **Beheer**.

    ![Beheer](./media/jitbit-helpdesk-tutorial/ic777681.png "Beheer")

1. Klik op **Gebruikers, bedrijven en machtigingen**.

    ![Gebruikers, bedrijven en machtigingen](./media/jitbit-helpdesk-tutorial/ic777682.png "Gebruikers, bedrijven en machtigingen")

1. Klik op **Gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/jitbit-helpdesk-tutorial/ic777685.png "Gebruiker toevoegen")

1. Typ in het gedeelte Maken de gegevens van het Azure AD-account dat u als volgt wilt inrichten:

    ![Maken](./media/jitbit-helpdesk-tutorial/ic777686.png "Maken")

   a. Typ de gebruikersnaam van de gebruiker in het tekstvak **Gebruikersnaam**, bijvoorbeeld **Britta Simon**.

   b. Typ in het tekstvak **E-mail** het e-mailadres van de gebruiker, bijvoorbeeld **BrittaSimon@contoso.com** .

   c. Typ in het tekstvak **First Name** de voornaam van de gebruiker, bijvoorbeeld **Britta**.

   d. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

   e. Klik op **Create**.

> [!NOTE]
> U kunt de Jitbit Helpdesk-gebruikersaccounts ook inrichten met behulp van andere hulpprogramma's of API's van Jitbit Helpdesk voor het maken van Azure AD-gebruikersaccounts.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Jitbit Helpdesk in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Jitbit Helpdesk waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
