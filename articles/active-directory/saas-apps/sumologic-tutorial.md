---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SumoLogic | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6d941c6efe42993b6bad7c556582831d179250
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75659743"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SumoLogic

In deze zelf studie leert u hoe u SumoLogic integreert met Azure Active Directory (Azure AD). Wanneer u SumoLogic integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SumoLogic.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SumoLogic met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SumoLogic-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SumoLogic ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-sumologic-from-the-gallery"></a>SumoLogic toevoegen uit de galerie

Als u de integratie van SumoLogic in azure AD wilt configureren, moet u SumoLogic uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SumoLogic** in het zoekvak.
1. Selecteer **SumoLogic** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SumoLogic

Azure AD SSO met SumoLogic configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SumoLogic.

Als u Azure AD SSO wilt configureren en testen met SumoLogic, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SUMOLOGIC SSO configureren](#configure-sumologic-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een SumoLogic-test gebruiker](#create-sumologic-test-user)** -om een equivalent van B. Simon in SumoLogic te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **SumoLogic** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    | |
    |--|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | |
    |--|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van SumoLogic-clients](https://www.sumologic.com/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De SumoLogic-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht SumoLogic toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    |  Naam | Bronkenmerk |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Rollen | user.assignedroles |

    > [!NOTE]
    > Klik [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) voor informatie over het configureren van een **rol** in azure AD.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **SumoLogic instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SumoLogic.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SumoLogic**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-sumologic-sso"></a>SumoLogic SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw SumoLogic-bedrijfs site.

1. Ga naar **beveiliging \> beheren**.

    ![Beheren](./media/sumologic-tutorial/ic778556.png "Beheren")

1. Klik op **SAML**.

    ![Globale beveiligings instellingen](./media/sumologic-tutorial/ic778557.png "Globale beveiligings instellingen")

1. Selecteer in de **een configuratie selecteren of een nieuwe lijst maken de** optie **Azure AD**en klik vervolgens op **configureren**.

    ![SAML 2,0 configureren](./media/sumologic-tutorial/ic778558.png "SAML 2,0 configureren")

1. Voer de volgende stappen uit in het dialoog venster **SAML 2,0 configureren** :

    ![SAML 2,0 configureren](./media/sumologic-tutorial/ic778559.png "SAML 2,0 configureren")

    a. In het tekstvak **configuratie naam** typt u **Azure AD**.

    b. Selecteer de **foutopsporingsmodus**.

    c. Plak in het tekstvak **Uitgever** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    d. Plak de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd in het tekstvak **Authe aanvraag-URL** .

    e. Open uw met base 64 versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het hele certificaat in het tekstvak **X. 509-certificaat** .

    f. Selecteer als **e-mail kenmerk** **gebruik van SAML subject**.  

    g. Selecteer door **SP geïnitieerde aanmeldings configuratie**.

    h. Typ in het tekstvak **aanmeldings pad** **Azure** en klik op **Opslaan**.

### <a name="create-sumologic-test-user"></a>SumoLogic-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij SumoLogic, moeten ze worden ingericht op SumoLogic. In het geval van SumoLogic is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **SumoLogic** -Tenant.

1. Ga naar **Beheren \> Gebruikers**.

    ![Gebruikers](./media/sumologic-tutorial/ic778561.png "Gebruikers")

1. Klik op **Add**.

    ![Gebruikers](./media/sumologic-tutorial/ic778562.png "Gebruikers")

1. Voer de volgende stappen uit in het dialoog venster **nieuwe gebruiker** :

    ![Nieuwe gebruiker](./media/sumologic-tutorial/ic778563.png "Nieuwe gebruiker")

    a. Typ de gerelateerde gegevens van het Azure AD-account dat u wilt inrichten in de tekst vakken **voor naam**, **Achternaam**en **e-mail adres** .
  
    b. Selecteer een rol.
  
    c. Selecteer **actief**als **status**.
  
    d. Klik op **Opslaan**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van SumoLogic-gebruikers accounts of Api's die worden geleverd door SumoLogic, gebruiken om Azure AD-gebruikers accounts in te richten.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SumoLogic in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SumoLogic waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SumoLogic met Azure AD](https://aad.portal.azure.com/)