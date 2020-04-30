---
title: 'Zelf studie: integratie Azure Active Directory met PageDNA | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68227465"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Zelf studie: integratie Azure Active Directory met PageDNA

In deze zelf studie leert u hoe u PageDNA integreert met Azure Active Directory (Azure AD).

Het integreren van PageDNA met Azure AD biedt de volgende voor delen:

* In azure AD kunt u bepalen wie toegang heeft tot PageDNA.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij PageDNA (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met PageDNA wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een PageDNA-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie kunt u eenmalige aanmelding voor Azure AD configureren en testen in een test omgeving en PageDNA integreren met Azure AD.

PageDNA biedt ondersteuning voor de volgende functies:

* Door SP geïnitieerde eenmalige aanmelding (SSO).

* Just-in-time-gebruikers inrichting.

## <a name="add-pagedna-from-the-azure-marketplace"></a>PageDNA toevoegen vanuit Azure Marketplace

Als u de integratie van PageDNA in azure AD wilt configureren, moet u PageDNA van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ nieuwe toepassing** boven aan het deel venster.

    ![De optie nieuwe toepassing](common/add-new-app.png)

1. Typ **PageDNA**in het zoekvak. Selecteer in de zoek resultaten **PageDNA**en selecteer **toevoegen** om de toepassing toe te voegen.

    ![PageDNA in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met PageDNA op basis van een test gebruiker met de naam **Julia Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PageDNA.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met PageDNA, moet u de volgende bouw stenen volt ooien:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[PageDNA eenmalige aanmelding configureren](#configure-pagedna-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
1. **[Maak een PageDNA-test gebruiker](#create-a-pagedna-test-user)** zodat er een gebruiker is met de naam Julia Simon in PageDNA die is gekoppeld aan de Azure AD-gebruiker met de naam Julia Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met PageDNA:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **PageDNA** Application Integration de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in het deel venster **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor PageDNA domein en Url's](common/sp-identifier.png)

    1. Voer in het vak **AANMELDINGS URL** een URL in met behulp van een van de volgende patronen:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Voer in het vak **id (Entiteits-ID)** een URL in met behulp van een van de volgende patronen:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteunings team van PageDNA](mailto:success@pagedna.com)om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het deel venster **basis-SAML-configuratie** in de Azure Portal.

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om **certificaat (RAW)** te downloaden uit de opgegeven opties en op uw computer op te slaan.

    ![De optie voor het downloaden van certificaten (RAW)](common/certificateraw.png)

1. Kopieer de URL of Url's die u nodig hebt in de sectie **PageDNA instellen** :

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Eenmalige aanmelding voor PageDNA configureren

Als u eenmalige aanmelding wilt configureren aan de PageDNA zijde, verzendt u het gedownloade certificaat (RAW) en de juiste gekopieerde Url's van de Azure Portal naar het [ondersteunings team van PageDNA](mailto:success@pagedna.com). Het PageDNA-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer **Azure Active Directory**   > **gebruikers** > in het linkerdeel venster van de Azure Portal**alle gebruikers**.

    ![De opties gebruikers en alle gebruikers](common/users.png)

1. Selecteer boven aan het scherm **+ nieuwe gebruiker**.

    ![Optie nieuwe gebruiker](common/new-user.png)

1. Voer de volgende stappen uit in het deel venster van de **gebruiker** :

    ![Het deel venster gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **BrittaSimon\@\<yourcompanydomain> in.\< extensie>**. Bijvoorbeeld **BrittaSimon\@contoso.com**.

    1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u de gebruiker Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door de gebruikers toegang te verlenen tot PageDNA.

1. Selecteer in de Azure Portal**alle toepassingen** > in **bedrijfs toepassingen** > **PageDNA**.

    ![Deel venster ondernemings toepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **PageDNA**.

    ![PageDNA in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeel venster onder **beheren**de optie **gebruikers en groepen**.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met **gebruikers** en kies vervolgens **selecteren** onder aan het deel venster.

1. Als u een waarde voor een rol in de SAML-bevestiging verwacht, selecteert u in het deel venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het deel venster op **selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-pagedna-test-user"></a>Een PageDNA-test gebruiker maken

Een gebruiker met de naam Julia Simon is nu gemaakt in PageDNA. U hoeft niets te doen om deze gebruiker te maken. PageDNA biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Als een gebruiker met de naam Julia Simon nog niet bestaat in PageDNA, wordt er een nieuwe gemaakt na verificatie.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u **PageDNA** in de portal mijn apps selecteert, moet u automatisch worden aangemeld bij het PageDNA-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelf studies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

