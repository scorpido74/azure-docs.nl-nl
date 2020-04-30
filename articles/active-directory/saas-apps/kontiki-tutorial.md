---
title: 'Zelf studie: integratie Azure Active Directory met Kontiki | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kontiki.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67098492"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Zelf studie: integratie Azure Active Directory met Kontiki

In deze zelf studie leert u hoe u Kontiki integreert met Azure Active Directory (Azure AD).

Het integreren van Kontiki met Azure AD biedt u de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot Kontiki.
* Gebruikers kunnen automatisch worden aangemeld bij Kontiki met hun Azure AD-accounts (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [eenmalige aanmelding bij toepassingen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Kontiki wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Kontiki-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie kunt u eenmalige aanmelding voor Azure AD configureren en testen in een test omgeving en Kontiki integreren met Azure AD.

Kontiki biedt ondersteuning voor de volgende functies:

* **Met SP geïnitieerde eenmalige aanmelding**
* **Just In Time inrichten van gebruikers**

## <a name="add-kontiki-in-the-azure-portal"></a>Kontiki toevoegen in de Azure Portal

Als u Kontiki wilt integreren met Azure AD, moet u Kontiki toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer in het linkermenu **Azure Active Directory**.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Selecteer**alle toepassingen**in **bedrijfs toepassingen** > .

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **nieuwe toepassing**om een toepassing toe te voegen.

    ![De optie nieuwe toepassing](common/add-new-app.png)

1. Typ **Kontiki**in het zoekvak. Selecteer in de zoek resultaten **Kontiki**en selecteer vervolgens **toevoegen**.

    ![Kontiki in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Kontiki op basis van een test gebruiker met de naam **Julia Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kontiki.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Kontiki, moet u de volgende bouw stenen volt ooien:

| Taak | Beschrijving |
| --- | --- |
| **[Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on)** | Hiermee kunnen uw gebruikers deze functie gebruiken. |
| **[Eenmalige aanmelding voor Kontiki configureren](#configure-kontiki-single-sign-on)** | Hiermee configureert u de instellingen voor eenmalige aanmelding in de toepassing. |
| **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** | Test de eenmalige aanmelding van Azure AD voor een gebruiker met de naam Julia Simon. |
| **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** | Hiermee kan Julia Simon de eenmalige aanmelding van Azure AD gebruiken. |
| **[Een Kontiki-test gebruiker maken](#create-a-kontiki-test-user)** | Maakt een equivalent van Julia Simon in Kontiki dat is gekoppeld aan de Azure AD-representatie van de gebruiker. |
| **[Eenmalige aanmelding testen](#test-single-sign-on)** | Verifieert of de configuratie werkt. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie configureert u eenmalige aanmelding voor Azure AD met Kontiki in de Azure Portal.

1. Selecteer in de [Azure Portal](https://portal.azure.com/)in het deel venster **Kontiki** Application Integration de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML** of **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deel venster **basis configuratie van SAML** in het tekstvak **URL voor aanmelden** een URL in die het volgende patroon heeft:`https://<companyname>.mc.eval.kontiki.com`

    ![Informatie over eenmalige aanmelding voor Kontiki domein en Url's](common/sp-signonurl.png)

    > [!NOTE]
    > Neem contact op met het [ondersteunings team van Kontiki-clients](https://customersupport.kontiki.com/enterprise/contactsupport.html) om de juiste waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **down load** naast **federatieve meta gegevens-XML**. Selecteer een download optie op basis van uw vereisten. Sla het certificaat op uw computer op.

    ![De download optie voor het XML-certificaat voor federatieve meta gegevens](common/metadataxml.png)

1. Kopieer in de sectie **Kontiki instellen** de volgende url's op basis van uw vereisten:

    * Aanmeldings-URL
    * Azure AD-id
    * Afmeldings-URL

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Eenmalige aanmelding voor Kontiki configureren

Als u eenmalige aanmelding wilt configureren aan de Kontiki zijde, verzendt u het gedownloade XML-bestand met federatieve meta gegevens en de relevante Url's die u hebt gekopieerd van de Azure Portal naar het [ondersteunings team van Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Het Kontiki-ondersteunings team gebruikt de gegevens die u hebt verzonden om ervoor te zorgen dat de eenmalige SAML-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in de Azure Portal.

    ![De opties gebruikers en alle gebruikers](common/users.png)

1. Selecteer **Nieuwe gebruiker**.

    ![De optie nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **brittasimon\@\<uw-Company-domein> in.\< extensie>**. Bijvoorbeeld **brittasimon\@contoso.com**.

    1. Schakel het selectie vakje **wacht woord weer geven** in. Noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    1. Selecteer **Maken**.

    ![Het deel venster gebruiker](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u Julia Simon toegang tot Kontiki zodat ze de eenmalige aanmelding van Azure kan gebruiken.

1. Selecteer in de Azure Portal**alle toepassingen** > in **bedrijfs toepassingen** > **Kontiki**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **Kontiki**.

    ![Kontiki in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **Gebruikers en groepen** in het menu.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers. Kies **Selecteren**.

1. Als u een rolnaam verwacht in de SAML-bewering, selecteert u in het deel venster **rol selecteren** de relevante rol voor de gebruiker in de lijst. Kies **Selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-kontiki-test-user"></a>Een Kontiki-test gebruiker maken

Er is geen actie-item voor u om gebruikers inrichten te configureren in Kontiki. Wanneer een toegewezen gebruiker zich probeert aan te melden bij Kontiki met behulp van de portal mijn apps, controleert Kontiki of de gebruiker bestaat. Als er geen gebruikers account wordt gevonden, wordt het gebruikers account automatisch door Kontiki gemaakt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u eenmalige aanmelding hebt ingesteld en u in de portal mijn apps **Kontiki** selecteert, wordt u automatisch aangemeld bij Kontiki. Zie [apps openen en gebruiken in de portal mijn apps](../user-help/my-apps-portal-end-user-access.md)voor meer informatie over de portal mijn apps.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
