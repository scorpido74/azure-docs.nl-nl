---
title: 'Zelfstudie: Integratie van Azure Active Directory met HubSpot | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 51e27526b10bcdd74d6e2bffb8bf620d7b022aac
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551440"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Zelfstudie: Azure Active Directory-integratie met HubSpot

In deze zelfstudie leert u hoe u HubSpot kunt integreren met Azure Active Directory (Azure AD).

Integratie van HubSpot met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang krijgt tot HubSpot.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-accounts worden aangemeld bij HubSpot (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie voor meer informatie over de integratie van SaaS-apps (software als een service) met Azure AD [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met HubSpot hebt u het volgende nodig:

* Een Azure AD-abonnement Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een abonnement op HubSpot waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen en HubSpot integreren met Azure AD.

HubSpot biedt ondersteuning voor de volgende functies:

* **Door SP geïnitieerde eenmalige aanmelding**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="add-hubspot-in-the-azure-portal"></a>HubSpot toevoegen in Azure Portal

Om HubSpot met Azure AD te integreren, moet u HubSpot toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer de knop **Azure Active Directory** in het linkermenu.

    ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Typ **HubSpot** in het zoekvak. Selecteer **HubSpot** in de zoekresultaten en selecteer vervolgens **Toevoegen**.

    ![HubSpot in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met HubSpot op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in HubSpot tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met HubSpot, moet u de volgende stappen uitvoeren:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding met HubSpot configureren](#configure-hubspot-single-sign-on)** | Configureert de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Stelt Britta Simon in staat om gebruik te maken van eenmalige aanmelding via Azure AD. |
| **[Een HubSpot-testgebruiker maken](#create-a-hubspot-test-user)** | Maakt een tegenhanger van Britta Simon in HubSpot die is gekoppeld aan de Azure AD-representatie van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In dit gedeelte configureert u eenmalige aanmelding van Azure AD met HubSpot in Azure Portal.

1. Selecteer in [Azure Portal](https://portal.azure.com/) **Eenmalige aanmelding** op de integratiepagina van de toepassing **HubSpot**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Een methode voor eenmalige aanmelding selecteren** de modus **SAML** of **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (het potloodpictogram) om het deelvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit om de *door IDP geïnitieerde modus* te configureren:

    1. Voer in het vak **Id** een URL in met het volgende patroon: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. Voer in het vak **Antwoord-URL** een URL in met het volgende patroon: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Om de URL’s in te delen, kunt u ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in Azure Portal.

1. Als u de toepassing wilt configureren in de *door SP geïnitieerde* modus:

    1. Selecteer **Extra URL's instellen**.

    1. Voer in het vak **Aanmeldings-URL** het volgende in: **https:\//app.hubspot.com/login**.

    ![De optie Extra URL’s instellen](common/metadata-upload-additional-signon.png)

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** naast **Certificaat (Base64)** . Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De optie om het certificaat (Base64) te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **HubSpot instellen** de juiste URL's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Eenmalige aanmelding bij HubSpot configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw HubSpot-beheerdersaccount.

1. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **Standaardinstellingen van account**.

    ![De optie Standaardinstellingen van account in HubSpot](./media/hubspot-tutorial/config2.png)

1. Schuif omlaag naar de sectie **Beveiliging** en selecteer vervolgens **Instellen**.

    ![De optie Instellen in HubSpot](./media/hubspot-tutorial/config3.png)

1. Voer de volgende stappen uit in het gedeelte **Eenmalige aanmelding instellen**:

    1. Selecteer **Kopiëren** in het vak **URl van doelgroep (entiteits-id van serviceprovider)** om de waarde te kopiëren. Ga in Azure Portal naar het deelvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Id**.

    1. Selecteer in het vak **URl voor aanmelding, ACS, ontvanger of doorsturen** **Kopiëren** om de waarde te kopiëren. Ga in Azure Portal naar het deelvenster **Standaard SAML-configuratie** en plak de waarde in het vak **Antwoord-URL**.

    1. Plak in het vak **ID van identiteitsprovider of URL van verlener** in HubSpot de waarde voor **Azure AD-id** die u in Azure Portal hebt gekopieerd.

    1. Plak in HubSpot in het vak **URL van id-provider voor eenmalige aanmelding** de **aanmeldings-URL** die u hebt gekopieerd in Azure Portal.

    1. Open in Windows Kladblok het certificaatbestand (Base64) dat u hebt gedownload. Selecteer en kopieer de inhoud van het bestand. Plak deze vervolgens in HubSpot in het vak **X.509-certificaat**.

    1. Selecteer **Verifiëren**.

        ![De sectie Eenmalige aanmelding instellen in HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.

    ![De opties Gebruikers en Alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam** **Britta Simon**in.
  
    1. Voer in het vak **Gebruikersnaam** **brittasimon\@\<your-company-domain> in.\<extension\>** . Bijvoorbeeld **brittasimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

    ![Het deelvenster Gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u Britta Simon toegang tot HubSpot zodat ze de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in Azure Portal **Bedrijfstoepassingen** > **Alle toepassingen** > **HubSpot**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **HubSpot** in de lijst met toepassingen.

    ![HubSpot in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** de optie **Britta Simon** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolwaarde in de SAML-assertie verwacht, selecteert u in het deelvenster **Rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-hubspot-test-user"></a>Een HubSpot-testgebruiker maken

De gebruiker moet zijn ingericht in HubSpot om Azure AD in te schakelen zodat een gebruiker zich kan aanmelden bij HubSpot. Het inrichten is in HubSpot een handmatige taak.

Ga als volgt te werk om een gebruikersaccount in te richten in HubSpot:

1. Meld u aan bij uw HubSpot-bedrijfssite als beheerder.

1. Selecteer het pictogram **Instellingen** in de rechterbovenhoek van de pagina.

    ![Het pictogram Instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **Gebruikers en teams**.

    ![De optie Gebruikers en teams in HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecteer **Create user**.

    ![De optie Gebruiker maken in HubSpot](./media/hubspot-tutorial/user2.png)

1. Voer in het vak **E-mailadres(sen) toevoegen** het e-mailadres van de gebruiker in met de indeling brittasimon\@contoso.com en selecteer vervolgens **Volgende**.

    ![Het vak E-mailadres(sen) toevoegen in de sectie Gebruikers maken in HubSpot](./media/hubspot-tutorial/user3.png)

1. Selecteer elk tabblad in de sectie **Gebruikers maken**. Stel op elk tabblad de relevante opties en machtigingen voor de gebruiker in. Selecteer vervolgens **Volgende**.

    ![Tabbladen in de sectie Gebruikers maken in HubSpot](./media/hubspot-tutorial/user4.png)

1. Selecteer **Verzenden** om de uitnodiging naar de gebruiker te verzenden.

    ![De optie Verzenden in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > De gebruiker wordt geactiveerd nadat deze de uitnodiging heeft geaccepteerd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Als u eenmalige aanmelding hebt ingesteld en **HubSpot** selecteert in de Mijn apps-portal, wordt u automatisch aangemeld bij HubSpot. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Lees voor meer informatie de volgende artikelen:

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
