---
title: 'Zelfstudie: Azure Active Directory-integratie met Sectigo Certificate Manager | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sectigo Certificate Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: d68e5335fff0341d8808e581061519977e1bb517
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543275"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Zelfstudie: Azure Active Directory-integratie met Sectigo Certificate Manager

In deze zelfstudie leert u hoe u Sectigo Certificate Manager integreert met Azure Active Directory (Azure AD).

Integratie van Sectigo Certificate Manager met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie er toegang krijgt tot Sectigo Certificate Manager.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Sectigo Certificate Manager (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps (software als een service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van de Azure AD-integratie met Sectigo Certificate Manager hebt u het volgende nodig:

* Een Azure AD-abonnement Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Sectigo Certificate Manager-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u in een testomgeving eenmalige aanmelding van Azure AD en integreert Sectigo Certificate Manager met Azure AD.

Sectigo Certificate Manager ondersteunt de volgende functies:

* **Door SP geïnitieerde eenmalige aanmelding**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Sectigo Certificate Manager toevoegen in Azure Portal

Om Sectigo Certificate Manager te integreren met Azure AD moet u Sectigo Certificate Manager toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer de knop **Azure Active Directory** in het linkermenu.

    ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Nieuwe toepassing** om een toepassing toe te voegen.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **Sectigo Certificate Manager** in. Selecteer in de zoekresultaten **Sectigo Certificate Manager** en selecteer **Toevoegen**.

    ![Sectigo Certificate Manager in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met Sectigo Certificate Manager op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een gekoppelde relatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sectigo Certificate Manager tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Sectigo Certificate Manager, moet u de volgende stappen uitvoeren:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding van Sectigo Certificate Manager configureren](#configure-sectigo-certificate-manager-single-sign-on)** | Configureert de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Britta Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Stelt Britta Simon in om gebruik te maken van eenmalige aanmelding via Azure AD. |
| **[Een testgebruiker maken voor Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Maakt een tegenhanger van Britta Simon in Sectigo Certificate Manager die is gekoppeld aan de Azure AD-representatie van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Controleert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In dit gedeelte configureert u eenmalige aanmelding van Azure AD met Sectigo Certificate Manager in Azure Portal.

1. Selecteer in [Azure Portal](https://portal.azure.com/) in het integratiedeelvenster voor de toepassing **Sectigo Certificate Manager** de optie **Eenmalige aanmelding**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Een methode voor eenmalige aanmelding selecteren** de modus **SAML** of **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (het potloodpictogram) om het deelvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit om de *door IDP geïnitieerde modus* te configureren:

    1. Voer in het vak **Id** een van deze URL's in:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. Voer in het vak **Antwoord-URL** een van deze URL's in:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Selecteer **Extra URL's instellen**.

    1. Voer in het vak **Relay-status** een van deze URL's in:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Gegevens van domein en URL's voor eenmalige aanmelding van Sectigo Certificaat Manager](common/idp-relay.png)

1.  Voer de volgende stappen uit om de toepassing te configureren in *door SP geïnitieerde modus*:

    * Voer in het vak **Aanmeldings-URL** een van deze URL's in:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Gegevens van domein en URL's voor eenmalige aanmelding van Sectigo Certificaat Manager](common/both-signonurl.png)

1. Selecteer in het deelvenster **Eenmalige aanmelding instellen met SAML** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** naast **Certificaat (Base64)** . Selecteer een downloadoptie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De optie om het certificaat (Base64) te downloaden](common/certificatebase64.png)

1. Kopieer de volgende URL's op basis van uw vereisten in het gedeelte **Sectigo Certificate Manager instellen**:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Eenmalige aanmelding van Sectigo Certificate Manager configureren

Verzend het gedownloade certificaatbestand (Base64) en de relevante URL's die u uit Azure Portal hebt gekopieerd naar het ondersteuningsteam van [Sectigo Certificate Manager](https://sectigo.com/support) als u eenmalige aanmelding wilt configureren aan de zijde van Sectigo Certificate Manager. Het ondersteuningsteam van Sectigo Certificate Manager gebruikt de informatie die u verzendt om ervoor te zorgen dat de SAML-verbinding voor eenmalige aanmelding aan beide zijden correct is ingesteld.

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

In deze sectie verleent u Britta Simon toegang tot Sectigo Certificate Manager zodat ze de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in Azure Portal **Bedrijfstoepassingen** > **Alle toepassingen** > **Sectigo Certificate Manager**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** de optie **Britta Simon** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolwaarde in de SAML-assertie verwacht, selecteert u in het deelvenster **Rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Een testgebruiker maken voor Sectigo Certificate Manager

In deze sectie maakt u een gebruiker met de naam Britta Simon in Sectigo Certificate Manager. Werk met het ondersteuningsteam van [Sectigo Certificate Manager](https://sectigo.com/support) samen om de gebruiker toe te voegen op het Sectigo Certificate Manager-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Als u eenmalige aanmelding hebt ingesteld en **Sectigo Certificate Manager** selecteert in de portal Mijn apps, wordt u automatisch aangemeld bij Sectigo Certificate Manager. Zie [Apps openen en gebruiken in de portal Mijn apps](../user-help/my-apps-portal-end-user-access.md) voor meer informatie over de portal Mijn apps.

## <a name="next-steps"></a>Volgende stappen

Lees voor meer informatie de volgende artikelen:

- [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


