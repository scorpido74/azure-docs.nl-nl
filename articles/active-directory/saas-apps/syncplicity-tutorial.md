---
title: 'Zelfstudie: Integratie van Azure Active Directory met Syncplicity | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: da532adfa2d4ab97edb44de9ae49c646ccdff381
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544890"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Zelfstudie: Syncplicity integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Syncplicity integreert met Azure Active Directory (Azure AD). Wanneer u Syncplicity integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie er toegang heeft tot Syncplicity.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij Syncplicity.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een gratis proefversie van één maand ontvangen.
* Een abonnement op Syncplicity waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. Syncplicity ondersteunt door **SP** geïnitieerde eenmalige aanmelding.

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity toevoegen vanuit de galerie

Voor het configureren van de integratie van Syncplicity met Azure AD moet u Syncplicity vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Syncplicity**.
1. Selecteer **Syncplicity** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met Syncplicity met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Syncplicity.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Syncplicity te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Syncplicity configureren](#configure-syncplicity-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Testgebruiker maken in Syncplicity](#create-syncplicity-test-user)** : om een tegenhanger voor B.Simon te maken in Syncplicity die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **Syncplicity**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Op de pagina **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.syncplicity.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [Klantondersteuningsteam van Syncplicity](https://www.syncplicity.com/contact-us) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Syncplicity instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Eenmalige aanmelding voor Syncplicity configureren

1. Meld u aan bij uw **Syncplicity**-tenant.

1. Klik in het menu bovenaan op **beheerder**, selecteer **instellingen** en klik vervolgens op **Aangepast domein en eenmalige aanmelding**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Voer op de pagina met het dialoogvenster **Eenmalige aanmelding** de volgende stappen uit:

    ![Eenmalige aanmelding \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Typ in het tekstvak **Aangepast domein** de naam van uw domein.
  
    b. Selecteer **Ingeschakeld** bij **Status voor eenmalige aanmelding**.

    c. Plak in het tekstvak **Entiteits-id** de waarde van **Id (Entiteits-id)** , die u hebt gebruikt in de **Standaard SAML-configuratie** in de Azure-portal.

    d. Plak in het tekstvak **Aanmeldingspagina-URL** de **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **Afmeldingspagina-URL** de **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    f. Klik in **Certificaat van identiteitsprovider** op **Bestand kiezen** en upload vervolgens het certificaat dat u hebt gedownload uit de Azure-portal.

    g. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Syncplicity.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Syncplicity** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-syncplicity-test-user"></a>Syncplicity-testgebruiker maken

Voordat Azure AD-gebruikers zich kunnen aanmelden, moeten ze worden ingericht voor de Syncplicity-toepassing. In deze sectie wordt beschreven hoe u Azure AD-gebruikersaccounts maakt in Syncplicity.

**Voer de volgende stappen uit om een gebruikersaccount voor Syncplicity in te richten:**

1. Meld u aan bij uw **Syncplicity**-tenant (bijvoorbeeld: `https://company.Syncplicity.com`).

1. Klik op **beheerder**, selecteer **gebruikersaccounts** en klik vervolgens op **EEN GEBRUIKER TOEVOEGEN**.

    ![Gebruikers beheren](./media/syncplicity-tutorial/ic769764.png "Gebruikers beheren")

1. Typ de **e-mailadressen** van een Azure AD-account dat u wilt inrichten, selecteer **Gebruiker** bij **Rol** en klik vervolgens op **VOLGENDE**.

    ![Accountgegevens](./media/syncplicity-tutorial/ic769765.png "Accountgegevens")

    > [!NOTE]
    > De houder van het Azure AD-account krijgt een e-mailbericht met een koppeling om het account te bevestigen en te activeren.

1. Selecteer een groep in uw bedrijf waar uw nieuwe gebruiker een lid van moet worden, en klik vervolgens op **VOLGENDE**.

    ![Groepslidmaatschap](./media/syncplicity-tutorial/ic769772.png "Groepslidmaatschap")

    > [!NOTE]
    > Als er geen groepen worden vermeld, klikt u op **VOLGENDE**.

1. Selecteer de mappen die u onder Syncplicity’s controle wilt plaatsen op de computer van de gebruiker, en klik vervolgens op **VOLGENDE**.

    ![Syncplicity-mappen](./media/syncplicity-tutorial/ic769773.png "Syncplicity-mappen")

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het maken van Syncplicity-gebruikersaccounts of API's van Syncplicity gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u in het toegangsvenster de tegel Syncplicity selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van Syncplicity waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)