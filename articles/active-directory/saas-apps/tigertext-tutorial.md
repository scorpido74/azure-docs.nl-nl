---
title: 'Zelfstudie: Integratie van Azure Active Directory met TigerText Secure Messenger | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en TigerText Secure Messenger.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9dff60767e923bad1322b689acd98e69eb9c2ac6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516926"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Zelfstudie: Integratie van Azure Active Directory met TigerText Secure Messenger

In deze zelfstudie leert u hoe u TigerText Secure Messenger kunt integreren met Azure Active Directory (Azure AD).

Integratie van TigerText Secure Messenger met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot TigerText Secure Messenger.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TigerText Secure Messenger (eenmalige aanmelding).
* U kunt uw accounts vanaf één locatie beheren, de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding van Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met TigerText Secure Messenger te configureren:

* Een Azure AD-abonnement Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een abonnement op TigerText Secure Messenger waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen en TigerText Secure Messenger integreren met Azure AD.

TigerText Secure Messenger ondersteunt door SP geïnitieerde eenmalige aanmelding (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>TigerText Secure Messenger toevoegen vanuit Azure Marketplace

Voor het configureren van de integratie van TigerText Secure Messenger met Azure AD moet u TigerText Secure Messenger vanuit Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie voor Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het deelvenster.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **TigerText Secure Messenger** in. Selecteer **TigerText Secure Messenger** in de zoekresultaten en selecteer vervolgens **Toevoegen** om de toepassing toe te voegen.

    ![TigerText Secure Messenger in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met TigerText Secure Messenger configureren en testen op basis van een testgebruiker met de naam **Britta Simon**. Eenmalige aanmelding werkt alleen als er een koppeling tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in TigerText Secure Messenger tot stand is gebracht.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met TigerText Secure Messenger te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on)** zodat uw gebruikers deze functie kunnen gebruiken.
1. **[Eenmalige aanmelding configureren voor TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** voor het testen van eenmalige aanmelding bij Azure AD met Britta Simon.
1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat Britta Simon gebruik kan maken van eenmalige aanmelding bij Azure AD.
1. **[Een testgebruiker voor TigerText Secure Messenger maken](#create-a-tigertext-secure-messenger-test-user)** , zodat een gebruiker met de naam Britta Simon in TigerText Secure Messenger is gekoppeld aan de Azure AD-gebruiker met de naam Britta Simon.
1. **[Eenmalige aanmelding testen](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met TigerText Secure Messenger te configureren:

1. In [Azure Portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **TigerText Secure Messenger**-toepassing de optie **Eenmalige aanmelding**.

    ![De optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. In het deelvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** de optie **Bewerken** (potloodpictogram) om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deelvenster **Standaard SAML-configuratie** de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij TigerText Secure Messenger](common/sp-identifier.png)

    1. Voer in het vak **Aanmeldings-URL** een URL in:

       `https://home.tigertext.com`

    1. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > De waarde voor **Id (Entiteits-id)** is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteuningsteam van TigerText Secure Messenger](mailto:prosupport@tigertext.com) om de waarde op te vragen. U kunt ook verwijzen naar de patronen die worden weergegeven in het deelvenster **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAM** in de sectie **SAML-handtekeningcertificaat** de optie **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden van de beschikbare opties en op te slaan op uw computer.

    ![De optie voor het downloaden van het XML-bestand met federatieve metagegevens](common/metadataxml.png)

1. Kopieer de URL('s) die u nodig hebt in het gedeelte **TigerText Secure Messenger instellen**:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Eenmalige aanmelding voor TigerText Secure Messenger configureren

Als u eenmalige aanmelding aan de zijde van TigerText Secure Messenger wilt configureren, moet u het gedownloade XML-bestand met federatieve metagegevens en de correcte uit Microsoft Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam van TigerText Secure Messenger](mailto:prosupport@tigertext.com). Het team van TigerText Secure Messenger zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal aan de linkerkant **Azure Active Directory**   > **Gebruikers** > **Alle gebruikers**.

    ![De opties Gebruikers en Alle gebruikers](common/users.png)

1. Selecteer bovenaan het scherm **+ Nieuwe gebruiker**.

    ![De optie Nieuwe gebruiker](common/new-user.png)

1. Voer in het deelvenster **Gebruiker** de volgende stappen uit:

    ![Het deelvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam** **Britta Simon**in.
  
    1. Voer in het vak **Naam** **Britta Simon\@\<yourcompanydomain>.\<extension>** in. Bijvoorbeeld **BrittaSimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot TigerText Secure Messenger.

1. Selecteer in Azure Portal **Bedrijfstoepassingen** > **Alle toepassingen** > **TigerText Secure Messenger**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **TigerText Secure Messenger** in de lijst met toepassingen.

    ![TigerText Secure Messenger in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **Beheren** de optie **Gebruikers en groepen**.

    ![De optie Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het deelvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** **Britta Simon** in de lijst **Gebruikers** en kies vervolgens **Selecteren** onder aan het deelvenster.

1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onder aan het deelvenster.

1. Selecteer **Toewijzen** in het deelvenster **Toewijzing toevoegen**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Een testgebruiker voor TigerText Secure Messenger maken

In dit gedeelte gaat u een gebruiker met de naam Britta Simon maken in TigerText Secure Messenger. Voeg in samenwerking met het [ondersteuningsteam van TigerText Secure Messenger](mailto:prosupport@tigertext.com) Britta Simon als gebruiker toe aan TigerText Secure Messenger. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding met Azure AD met behulp van de portal Mijn apps.

Wanneer u **TigerText Secure Messenger** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het TigerText Secure Messenger-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Inleiding tot de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
