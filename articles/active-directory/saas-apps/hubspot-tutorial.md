---
title: 'Zelf studie: integratie Azure Active Directory met HubSpot | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68944454"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Zelf studie: integratie Azure Active Directory met HubSpot

In deze zelf studie leert u hoe u HubSpot integreert met Azure Active Directory (Azure AD).

Het integreren van HubSpot met Azure AD biedt u de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot HubSpot.
* Gebruikers kunnen automatisch worden aangemeld bij HubSpot met hun Azure AD-accounts (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [eenmalige aanmelding bij toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met HubSpot wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een HubSpot-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie kunt u eenmalige aanmelding voor Azure AD configureren en testen in een test omgeving en HubSpot integreren met Azure AD.

HubSpot biedt ondersteuning voor de volgende functies:

* **Met SP geïnitieerde eenmalige aanmelding**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="add-hubspot-in-the-azure-portal"></a>HubSpot toevoegen in de Azure Portal

Als u HubSpot wilt integreren met Azure AD, moet u HubSpot toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer in het linkermenu **Azure Active Directory**.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Selecteer**alle toepassingen**in **bedrijfs toepassingen** > .

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **nieuwe toepassing**om een toepassing toe te voegen.

    ![De optie nieuwe toepassing](common/add-new-app.png)

1. Typ **HubSpot**in het zoekvak. Selecteer in de zoek resultaten **HubSpot**en selecteer vervolgens **toevoegen**.

    ![HubSpot in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met HubSpot op basis van een test gebruiker met de naam **Julia Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in HubSpot.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met HubSpot, moet u de volgende bouw stenen volt ooien:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding voor HubSpot configureren](#configure-hubspot-single-sign-on)** | Hiermee configureert u de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Julia Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Hiermee kan Julia Simon de eenmalige aanmelding van Azure AD gebruiken. |
| **[Een HubSpot-test gebruiker maken](#create-a-hubspot-test-user)** | Maakt een equivalent van Julia Simon in HubSpot dat is gekoppeld aan de Azure AD-representatie van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Verifieert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie configureert u eenmalige aanmelding voor Azure AD met HubSpot in de Azure Portal.

1. Selecteer in de [Azure Portal](https://portal.azure.com/)in het deel venster **HubSpot** Application Integration de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML** of **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in het deel venster **basis-SAML-configuratie** om de *modus IDP*te configureren:

    1. Voer in het vak **id** een URL in die het volgende patroon bevat: https:\//API.HubSpot.com/login-API/v1/SAML/login?portalId =\<klant-\>id.

    1. Voer in het vak **antwoord-URL** een URL in die het volgende patroon heeft: https\/:/API.HubSpot.com/login-API/v1/SAML/ACS?portalId\<= klant\>-id.

    ![Informatie over eenmalige aanmelding voor HubSpot domein en Url's](common/idp-intiated.png)

    > [!NOTE]
    > Als u de Url's wilt Format teren, kunt u ook verwijzen naar de patronen die worden weer gegeven in het deel venster **basis-SAML-configuratie** in de Azure Portal.

1. De toepassing in de door *SP geïnitieerde* modus configureren:

    1. Selecteer **extra Url's instellen**.

    1. Voer in het vak **URL voor aanmelden** **\/https:/app.HubSpot.com/login**in.

    ![De optie extra Url's instellen](common/metadata-upload-additional-signon.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **down load** naast **certificaat (base64)**. Selecteer een download optie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De download optie voor het certificaat (base64)](common/certificatebase64.png)

1. Kopieer in de sectie **HubSpot instellen** de volgende url's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Eenmalige aanmelding voor HubSpot configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw HubSpot-beheerders account.

1. Selecteer het pictogram **instellingen** in de rechter bovenhoek van de pagina.

    ![Het pictogram instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **standaard instellingen**voor het account.

    ![De optie standaard waarden voor accounts in HubSpot](./media/hubspot-tutorial/config2.png)

1. Schuif omlaag naar de sectie **beveiliging** en selecteer vervolgens **instellen**.

    ![De optie instellen in HubSpot](./media/hubspot-tutorial/config3.png)

1. Voer de volgende stappen uit in de sectie **eenmalige aanmelding instellen** :

    1. Selecteer in het vak **URl van doel groep (entiteits-id van service provider)** **kopiëren** om de waarde te kopiëren. Plak in het Azure Portal in het deel venster **basis-SAML-configuratie** de waarde in het vak **id** .

    1. Selecteer in het vak **aanmeldings-URl, ACS, ontvanger of omleiding** de optie **kopiëren** om de waarde te kopiëren. Plak in het Azure Portal in het deel venster **basis-SAML-configuratie** de waarde in het vak antwoord- **URL** .

    1. Plak in HubSpot in het vak ID- **provider of de URL** van de uitgever de waarde voor de **Azure ad-id** die u in de Azure Portal hebt gekopieerd.

    1. Plak in HubSpot, in het vak **URL-provider voor eenmalige aanmelding** , de waarde voor de **aanmeldings-URL** die u hebt gekopieerd in de Azure Portal.

    1. Open in Windows Klad blok het door u gedownloade certificaat bestand (base64). Selecteer en kopieer de inhoud van het bestand. Plak vervolgens in HubSpot in het vak **X. 509-certificaat** .

    1. Selecteer **Verifiëren**.

        ![De sectie eenmalige aanmelding instellen in HubSpot](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in de Azure Portal.

    ![De opties gebruikers en alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **brittasimon\@\<uw-Company-domein> in.\< extensie\>**. Bijvoorbeeld **brittasimon\@contoso.com**.

    1. Schakel het selectie vakje **wacht woord weer geven** in. Noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    1. Selecteer **Maken**.

    ![Het deel venster gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u Julia Simon toegang tot HubSpot zodat ze de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in de Azure Portal**alle toepassingen** > in **bedrijfs toepassingen** > **HubSpot**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **HubSpot**.

    ![HubSpot in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolnaam verwacht in de SAML-bewering, selecteert u in het deel venster **rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-hubspot-test-user"></a>Een HubSpot-test gebruiker maken

Om Azure AD in te scha kelen voor een gebruiker om zich aan te melden bij HubSpot, moet de gebruiker zijn ingericht in HubSpot. In HubSpot is inrichten een hand matige taak.

Een gebruikers account inrichten in HubSpot:

1. Meld u als beheerder aan bij de HubSpot-bedrijfs site.

1. Selecteer het pictogram **instellingen** in de rechter bovenhoek van de pagina.

    ![Het pictogram instellingen in HubSpot](./media/hubspot-tutorial/config1.png)

1. Selecteer **gebruikers & teams**.

    ![De optie gebruikers & teams in HubSpot](./media/hubspot-tutorial/user1.png)

1. Selecteer **gebruiker maken**.

    ![De optie gebruiker maken in HubSpot](./media/hubspot-tutorial/user2.png)

1. Voer in het vak **e-mail addess (ES) toevoegen** het e-mail adres van de gebruiker in\@de notatie brittasimon contoso.com in en selecteer **volgende**.

    ![Het vak e-mail adres (sen) toevoegen in de sectie gebruikers maken in HubSpot](./media/hubspot-tutorial/user3.png)

1. Selecteer in de sectie **gebruikers maken** elk tabblad. Stel op elk tabblad de relevante opties en machtigingen voor de gebruiker in. Selecteer vervolgens **volgende**.

    ![Tabbladen in de sectie gebruikers maken in HubSpot](./media/hubspot-tutorial/user4.png)

1. Selecteer **verzenden**om de uitnodiging naar de gebruiker te verzenden.

    ![De verzend optie in HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > De gebruiker wordt geactiveerd nadat de gebruiker de uitnodiging heeft geaccepteerd.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u eenmalige aanmelding hebt ingesteld en u in de portal mijn apps **HubSpot** selecteert, wordt u automatisch aangemeld bij HubSpot. Zie [apps openen en gebruiken in de portal mijn apps](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over de portal mijn apps.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
