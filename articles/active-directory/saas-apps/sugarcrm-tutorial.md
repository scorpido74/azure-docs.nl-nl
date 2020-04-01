---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Sugar CRM | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Sugar CRM

In deze zelfstudie leert u hoe u Sugar CRM integreren met Azure Active Directory (Azure AD). Wanneer u Sugar CRM integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Sugar CRM.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Sugar CRM met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Met Sugar CRM single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Sugar CRM ondersteunt **SP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-sugar-crm-from-the-gallery"></a>Sugar CRM toevoegen vanuit de galerie

Als u de integratie van Sugar CRM in Azure AD wilt configureren, moet u Sugar CRM uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Sugar CRM** in de sectie Toevoegen in **de galerie** in het zoekvak.
1. Selecteer **Sugar CRM** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Azure AD-aanmelding voor Sugar CRM configureren en testen

Azure AD SSO configureren en testen met Sugar CRM met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sugar CRM.

Als u Azure AD SSO wilt configureren en testen met Sugar CRM, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Sugar CRM SSO configureren](#configure-sugar-crm-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. Maak de gebruiker van **[Sugar CRM-tests](#create-sugar-crm-test-user)** - om een tegenhanger van B.Simon in Sugar CRM te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Sugar CRM-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: 

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met [het ondersteuningsteam van Sugar CRM Client](https://support.sugarcrm.com/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Sugar CRM instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Sugar CRM.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Sugar CRM**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sugar-crm-sso"></a>Sugar CRM SSO configureren

1. Meld u in een ander browservenster aan bij uw sugar CRM-bedrijfssite als beheerder.

1. Ga naar **Beheerder**.

    ![Beheerder](./media/sugarcrm-tutorial/ic795888.png "Beheerder")

1. Klik **in** de sectie Beheer op **Wachtwoordbeheer**.

    ![Beheer](./media/sugarcrm-tutorial/ic795889.png "Beheer")

1. Selecteer **SAML-verificatie inschakelen**.

    ![Beheer](./media/sugarcrm-tutorial/ic795890.png "Beheer")

1. Voer in het gedeelte **SAML-verificatie** de volgende stappen uit:

    ![SAML-verificatie](./media/sugarcrm-tutorial/ic795891.png "SAML-verificatie")  

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.
  
    b. Plak in het tekstvak van de URL van **SLO** de waarde van **de URL van Afmelden**, die u hebt gekopieerd van azure-portal.
  
    c. Open uw basis-64 gecodeerd certificaat in kladblok, kopieer de inhoud ervan in uw klembord en plak het volledige certificaat in het tekstvak **van het Certificaat x.509.**
  
    d. Klik op **Opslaan**.

### <a name="create-sugar-crm-test-user"></a>De gebruiker van de Sugar CRM-test maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Sugar CRM, moeten ze worden ingericht voor Sugar CRM. In het geval van Sugar CRM is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **Sugar CRM-bedrijfssite** als beheerder.

1. Ga naar **Beheerder**.

    ![Beheerder](./media/sugarcrm-tutorial/ic795888.png "Beheerder")

1. Klik **in** de sectie Beheer op **Gebruikersbeheer**.

    ![Beheer](./media/sugarcrm-tutorial/ic795893.png "Beheer")

1. Ga naar **Gebruikers \> Maken nieuwe gebruiker**.

    ![Nieuwe gebruiker maken](./media/sugarcrm-tutorial/ic795894.png "Nieuwe gebruiker maken")

1. Voer op het tabblad **Gebruikersprofiel** de volgende stappen uit:

    ![Nieuwe gebruiker](./media/sugarcrm-tutorial/ic795895.png "Nieuwe gebruiker")

    * Typ de **gebruikersnaam,** **achternaam**en **e-mailadres** van een geldige Azure Active Directory-gebruiker in de gerelateerde tekstvakken.
  
1. Selecteer **Actief**als **status**.

1. Voer op het tabblad Wachtwoord de volgende stappen uit:

    ![Nieuwe gebruiker](./media/sugarcrm-tutorial/ic795896.png "Nieuwe gebruiker")

    a. Typ het wachtwoord in het bijbehorende tekstvak.

    b. Klik op **Opslaan**.

> [!NOTE]
> U alle andere tools voor het maken van audio-CRM-gebruikersaccounts of API's van Sugar CRM gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Sugar CRM in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Sugar CRM waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Sugar CRM met Azure AD](https://aad.portal.azure.com/)

