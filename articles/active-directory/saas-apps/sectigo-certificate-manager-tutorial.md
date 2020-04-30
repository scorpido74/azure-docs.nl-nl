---
title: 'Zelf studie: integratie Azure Active Directory met Sectigo Certificate Manager | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sectigo Certificate Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67588239"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Zelf studie: integratie Azure Active Directory met Sectigo Certificate Manager

In deze zelf studie leert u hoe u Sectigo Certificate Manager integreert met Azure Active Directory (Azure AD).

Het integreren van Sectigo-certificaat beheer met Azure AD biedt u de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot Sectigo Certificate Manager.
* Gebruikers kunnen automatisch worden aangemeld bij Sectigo Certificate Manager met hun Azure AD-accounts (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [eenmalige aanmelding bij toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Sectigo Certificate Manager wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Sectigo Certificate Manager-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD in een test omgeving en integreert u Sectigo Certificate Manager met Azure AD.

Sectigo Certificate Manager ondersteunt de volgende functies:

* **Met SP geïnitieerde eenmalige aanmelding**
* **Door IDP geïnitieerde eenmalige aanmelding**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Sectigo Certificate Manager toevoegen in de Azure Portal

Als u Sectigo certificaat beheer wilt integreren met Azure AD, moet u Sectigo Certificate Manager toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer in het linkermenu **Azure Active Directory**.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Selecteer**alle toepassingen**in **bedrijfs toepassingen** > .

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **nieuwe toepassing**om een toepassing toe te voegen.

    ![De optie nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **Sectigo Certificate Manager**in. Selecteer in de zoek resultaten **Sectigo Certificate Manager**en selecteer vervolgens **toevoegen**.

    ![Sectigo certificaat beheerder in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Sectigo Certificate Manager op basis van een test gebruiker met de naam **Julia Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sectigo Certificate Manager.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Sectigo Certificate Manager, moet u de volgende bouw stenen volt ooien:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding van Sectigo Certificate Manager configureren](#configure-sectigo-certificate-manager-single-sign-on)** | Hiermee configureert u de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Julia Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Hiermee kan Julia Simon de eenmalige aanmelding van Azure AD gebruiken. |
| **[Een test gebruiker voor een Sectigo-certificaat beheerder maken](#create-a-sectigo-certificate-manager-test-user)** | Maakt een equivalent van Julia Simon in Sectigo Certificate Manager dat is gekoppeld aan de Azure AD-weer gave van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Verifieert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie configureert u eenmalige aanmelding voor Azure AD met Sectigo Certificate manager in de Azure Portal.

1. Selecteer in de [Azure Portal](https://portal.azure.com/)in het deel venster **certificaat beheer** toepassing voor Sectigo de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML** of **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in het deel venster **basis-SAML-configuratie** om de *modus IDP*te configureren:

    1. Voer in het vak **id** een van de volgende url's in:
       * https:\//CERT-Manager.com/shibboleth
       * https:\//hard.cert-Manager.com/shibboleth

    1. Voer een van de volgende Url's in het vak **antwoord-URL** in:
        * https:\//CERT-Manager.com/shibboleth.SSO/SAML2/post
        * https:\//hard.cert-Manager.com/shibboleth.SSO/SAML2/post

    1. Selecteer **extra Url's instellen**.

    1. Voer een van de volgende Url's in het vak **Relay-status** in:
       * https:\//CERT-Manager.com/Customer/SSLSupport/IDP
       * https:\//hard.cert-Manager.com/Customer/SSLSupport/IDP

    ![Gegevens van het Sectigo-domein en Url's voor eenmalige aanmelding](common/idp-relay.png)

1.  Voer de volgende stappen uit om de toepassing te configureren in de *modus door SP gestart*:

    * Voer een van de volgende Url's in het vak **URL voor aanmelden** in:
      * https:\//CERT-Manager.com/shibboleth.SSO/login
      * https:\//hard.cert-Manager.com/shibboleth.SSO/login

      ![Gegevens van het Sectigo-domein en Url's voor eenmalige aanmelding](common/both-signonurl.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **down load** naast **certificaat (base64)**. Selecteer een download optie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De download optie voor het certificaat (base64)](common/certificatebase64.png)

1. Kopieer in de sectie **Sectigo Certificate Manager instellen** de volgende url's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Eenmalige aanmelding van Sectigo Certificate Manager configureren

Als u eenmalige aanmelding wilt configureren op de Sectigo certificaat beheerder, verzendt u het gedownloade certificaat bestand (base64) en de relevante Url's die u hebt gekopieerd van de Azure Portal naar het [ondersteunings team van het Sectigo Certificate Manager](https://sectigo.com/support). Het ondersteunings team van Sectigo Certificate Manager gebruikt de informatie die u ze stuurt om ervoor te zorgen dat de eenmalige SAML-verbinding op beide zijden correct is ingesteld.

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

In deze sectie verleent u Julia Simon toegang tot Sectigo Certificate Manager zodat ze de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in de Azure Portal **bedrijfs toepassingen** > **alle toepassingen** > **Sectigo Certificate Manager**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **Sectigo Certificate Manager**.

    ![Sectigo certificaat beheer in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolnaam verwacht in de SAML-bewering, selecteert u in het deel venster **rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Een test gebruiker voor een Sectigo-certificaat beheerder maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Sectigo Certificate Manager. Werk met het [ondersteunings team van Sectigo Certificate Manager](https://sectigo.com/support) om de gebruiker toe te voegen in het platform voor het Sectigo-certificaat beheer. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u eenmalige aanmelding hebt ingesteld en u **Sectigo Certificate Manager** selecteert in de portal mijn apps, wordt u automatisch aangemeld bij Sectigo Certificate Manager. Zie [apps openen en gebruiken in de portal mijn apps](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over de portal mijn apps.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


