---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met suiker CRM | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en suiker CRM.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74231966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met suiker CRM

In deze zelf studie leert u hoe u een suiker-CRM integreert met Azure Active Directory (Azure AD). Wanneer u een suiker CRM integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de suiker CRM.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij suiker CRM met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement voor eenmalige aanmelding (SSO) van suiker CRM.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Suiker CRM ondersteunt door **SP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-sugar-crm-from-the-gallery"></a>Suiker CRM toevoegen vanuit de galerie

Als u de integratie van suiker CRM wilt configureren in azure AD, moet u in de galerie suiker CRM toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** , **suiker CRM** in het zoekvak.
1. Selecteer in het paneel resultaten van **suiker CRM** en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Eenmalige aanmelding van Azure AD voor suiker-CRM configureren en testen

Azure AD SSO met suiker CRM configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in suiker CRM.

Als u Azure AD SSO wilt configureren en testen met suiker CRM, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Suiker CRM-SSO configureren](#configure-sugar-crm-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een sector van een **[suiker CRM-test gebruiker maken](#create-sugar-crm-test-user)** , die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **suiker-CRM** -toepassing en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

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
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [ondersteunings team van suiker CRM-client](https://support.sugarcrm.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **suiker-CRM instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot suiker CRM.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **suiker CRM**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-sugar-crm-sso"></a>Suiker CRM-SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw sector van uw suiker CRM-bedrijfs site.

1. Ga naar de **beheerder**.

    ![Beheerder](./media/sugarcrm-tutorial/ic795888.png "Beheerder")

1. Klik in de sectie **beheer** op **wachtwoord beheer**.

    ![Beheer](./media/sugarcrm-tutorial/ic795889.png "Beheer")

1. Selecteer **SAML-verificatie inschakelen**.

    ![Beheer](./media/sugarcrm-tutorial/ic795890.png "Beheer")

1. Voer in het gedeelte **SAML-verificatie** de volgende stappen uit:

    ![SAML-verificatie](./media/sugarcrm-tutorial/ic795891.png "SAML-verificatie")  

    a. Plak in het tekstvak **aanmeldings-URL** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.
  
    b. Plak in het tekstvak **URL van Slo** de waarde van de **afmeldings-URL**die u van Azure Portal hebt gekopieerd.
  
    c. Open uw met base 64 versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het hele certificaat in het tekstvak **X. 509-certificaat** .
  
    d. Klik op **Opslaan**.

### <a name="create-sugar-crm-test-user"></a>Een suiker CRM-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij een suiker-CRM, moeten ze worden ingericht voor de suiker CRM. In het geval van suiker CRM is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij uw sector van uw **suiker CRM** -bedrijfs site.

1. Ga naar de **beheerder**.

    ![Beheerder](./media/sugarcrm-tutorial/ic795888.png "Beheerder")

1. Klik in de sectie **beheer** op **gebruikers beheer**.

    ![Beheer](./media/sugarcrm-tutorial/ic795893.png "Beheer")

1. Ga naar **gebruikers \> nieuwe gebruiker maken**.

    ![Nieuwe gebruiker maken](./media/sugarcrm-tutorial/ic795894.png "Nieuwe gebruiker maken")

1. Voer op het tabblad **gebruikers profiel** de volgende stappen uit:

    ![Nieuwe gebruiker](./media/sugarcrm-tutorial/ic795895.png "Nieuwe gebruiker")

    * Typ de **gebruikers naam**, **Achternaam**en het **e-mail adres** van een geldige Azure Active Directory gebruiker in de bijbehorende tekst vakken.
  
1. Selecteer **actief**als **status**.

1. Voer op het tabblad wacht woord de volgende stappen uit:

    ![Nieuwe gebruiker](./media/sugarcrm-tutorial/ic795896.png "Nieuwe gebruiker")

    a. Typ het wacht woord in het bijbehorende tekstvak.

    b. Klik op **Opslaan**.

> [!NOTE]
> U kunt andere suiker CRM-hulpprogram ma's voor het maken van gebruikers accounts of Api's die worden geleverd door de sector suiker CRM gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel suiker-CRM in het toegangs venster klikt, moet u automatisch worden aangemeld bij de suiker CRM waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Suiker CRM uitproberen met Azure AD](https://aad.portal.azure.com/)

