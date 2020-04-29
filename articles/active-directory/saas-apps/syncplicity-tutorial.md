---
title: 'Zelf studie: integratie Azure Active Directory met Syncplicity | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74233290"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Zelf studie: Syncplicity integreren met Azure Active Directory

In deze zelf studie leert u hoe u Syncplicity integreert met Azure Active Directory (Azure AD). Wanneer u Syncplicity integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Syncplicity.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Syncplicity met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proef versie van één maand ontvangen.
* Syncplicity-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Syncplicity ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity toevoegen uit de galerie

Als u de integratie van Syncplicity in azure AD wilt configureren, moet u Syncplicity uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Syncplicity** in het zoekvak.
1. Selecteer **Syncplicity** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO configureren en testen

Azure AD SSO met Syncplicity configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Syncplicity.

Als u Azure AD SSO wilt configureren en testen met Syncplicity, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[SYNCPLICITY SSO configureren](#configure-syncplicity-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een Syncplicity-test gebruiker](#create-syncplicity-test-user)** -om een equivalent van B. Simon in Syncplicity te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Syncplicity** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.syncplicity.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van Syncplicity-clients](https://www.syncplicity.com/contact-us) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Syncplicity instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Syncplicity SSO configureren

1. Meld u aan bij uw **Syncplicity** -Tenant.

1. Klik in het menu aan de bovenkant op **Administrator**, selecteer **instellingen**en klik vervolgens op **aangepast domein en eenmalige aanmelding**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Voer de volgende stappen uit op de pagina voor **eenmalige aanmelding (SSO)** :

    ![Eenmalige aanmelding \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Typ in het tekstvak **aangepast domein** de naam van uw domein.
  
    b. Selecteer **ingeschakeld** als de **status eenmalige aanmelding**.

    c. Plak in het tekstvak **Entiteits-ID** de id-waarde **(entiteit-id)** die u hebt gebruikt in de **basis configuratie van SAML** in de Azure Portal.

    d. Plak in het tekstvak **URL voor aanmeldings pagina** de AANMELDINGS- **URL** die u hebt gekopieerd uit Azure Portal.

    e. Plak in het tekstvak **URL van Afmeldings pagina** de **afmeldings-URL** die u van Azure Portal hebt gekopieerd.

    f. Klik in certificaat van de **identiteits provider**op **bestand kiezen**en upload het certificaat dat u hebt gedownload van de Azure Portal.

    g. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Syncplicity.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Syncplicity**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-syncplicity-test-user"></a>Syncplicity-test gebruiker maken

Azure AD-gebruikers kunnen zich alleen aanmelden als ze zijn ingericht voor Syncplicity-toepassing. In deze sectie wordt beschreven hoe u Azure AD-gebruikers accounts maakt in Syncplicity.

**Voer de volgende stappen uit om een gebruikers account in te richten op Syncplicity:**

1. Meld u aan bij uw **Syncplicity** -Tenant (bijvoorbeeld `https://company.Syncplicity.com`:).

1. Klik op **beheerder** en selecteer **gebruikers accounts** en klik vervolgens op **een gebruiker toevoegen**.

    ![Gebruikers beheren](./media/syncplicity-tutorial/ic769764.png "Gebruikers beheren")

1. Typ de **e-mail adressen** van een Azure ad-account dat u wilt inrichten, selecteer **gebruiker** als **rol**en klik vervolgens op **volgende**.

    ![Account gegevens](./media/syncplicity-tutorial/ic769765.png "Accountgegevens")

    > [!NOTE]
    > De houder van het Azure AD-account ontvangt een e-mail bericht met een koppeling om het account te bevestigen en te activeren.

1. Selecteer een groep in uw bedrijf waarvan uw nieuwe gebruiker lid moet worden, en klik vervolgens op **volgende**.

    ![Groepslid maatschap](./media/syncplicity-tutorial/ic769772.png "Groepslid maatschap")

    > [!NOTE]
    > Als er geen groepen worden weer gegeven, klikt u op **volgende**.

1. Selecteer de mappen die u wilt plaatsen onder het besturings element Syncplicity op de computer van de gebruiker en klik vervolgens op **volgende**.

    ![Syncplicity mappen](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappen")

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van Syncplicity-gebruikers accounts of Api's die worden geleverd door Syncplicity, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Syncplicity in het toegangs venster selecteert, wordt u automatisch aangemeld bij de Syncplicity waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)