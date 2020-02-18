---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Jamf Pro | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splunk Enterprise en Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d83dbe756e8e6acdb58861ac359801bc13a63c4
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373189"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Zelf studie: SSO-integratie Azure Active Directory met Jamf Pro

In deze zelf studie leert u hoe u Jamf Pro integreert met Azure Active Directory (Azure AD). Wanneer u Jamf Pro integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot Jamf Pro.
* Meld uw gebruikers automatisch aan Jamf Pro met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Jamf Pro-abonnement dat eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. 

* Jamf Pro ondersteunt door **SP geïnitieerde** en door **IDP geïnitieerde** SSO.
* Zodra u Jamf Pro hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Jamf Pro toevoegen vanuit de galerie

Voor het configureren van de integratie van Jamf Pro in Azure AD, moet u Jamf Pro uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of uw persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het linkerdeel venster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Voer in de sectie **toevoegen vanuit de galerie** *Jamf Pro* in het zoekvak in.
1. Selecteer **Jamf Pro** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Eenmalige aanmelding configureren en testen in azure AD voor Jamf Pro

Azure AD SSO met Jamf Pro configureren en testen met behulp van een test gebruiker met de naam B. Simon. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jamf Pro.

In deze sectie kunt u Azure AD SSO configureren en testen met Jamf Pro.

1. [CONFIGUREER SSO in azure AD](#configure-sso-in-azure-ad) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-test gebruiker](#create-an-azure-ad-test-user) om Azure AD SSO te testen met het account B. Simon.
    1. [Wijs de test gebruiker van Azure AD toe](#assign-the-azure-ad-test-user) , zodat B. Simon SSO kan gebruiken in azure AD.
1. [Eenmalige aanmelding configureren in Jamf Pro](#configure-sso-in-jamf-pro) om de SSO-instellingen aan de kant van de toepassing te configureren.
    1. [Maak een Jamf Pro-test gebruiker](#create-a-jamf-pro-test-user) die een soort is van B. Simon in Jamf Pro dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test de SSO-configuratie](#test-the-sso-configuration) om te controleren of de configuratie werkt.

## <a name="configure-sso-in-azure-ad"></a>Eenmalige aanmelding configureren in azure AD

In deze sectie schakelt u Azure AD SSO in de Azure Portal in.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Jamf Pro** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Op de pagina **eenmalige aanmelding met SAML instellen** selecteert u het pictogram voor de pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Bewerk de pagina basis configuratie van SAML.](common/edit-urls.png)

1. Als u de toepassing in de **gestarte** modus in IDP wilt configureren, voert u de waarden voor de volgende velden in in de sectie **basis configuratie van SAML** :

    a. Voer in het tekstvak **id** een URL in die gebruikmaakt van de volgende formule: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. Voer in het tekstvak **antwoord-URL** een URL in die gebruikmaakt van de volgende formule: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Selecteer **extra Url's instellen**. Als u de toepassing in de door **SP gestarte** modus wilt configureren, voert u in het tekstvak **URL voor aanmelding** een URL in die gebruikmaakt van de volgende formule: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id, de antwoord-URL en de aanmeldings-URL. U krijgt de daad werkelijke id-waarde uit de sectie **eenmalige aanmelding** in Jamf Pro Portal, die verderop in de zelf studie wordt uitgelegd. U kunt de waarde van het werkelijke subdomein ophalen uit de id-waarde en die subdomeingegevens gebruiken als uw aanmeldings-URL en antwoord-URL. U kunt ook verwijzen naar de formules die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure Portal.

1. Op de pagina **eenmalige aanmelding met SAML instellen** gaat u naar de sectie **SAML-handtekening certificaat** , selecteert u de knop **kopiëren** om de URL voor de **federatieve meta gegevens**van de app te kopiëren en vervolgens op uw computer op te slaan.

    ![De download koppeling voor het SAML-handtekening certificaat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.
   1. Voer in het veld **gebruikers naam** [naam] @ [companydomain] in. [extensie]. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u B. Simon toegang tot Jamf Pro.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Jamf Pro**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![Selecteer de knop gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met gebruikers en selecteer vervolgens de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker. Selecteer vervolgens de knop **selecteren** onder aan het scherm.
1. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

## <a name="configure-sso-in-jamf-pro"></a>Eenmalige aanmelding in Jamf Pro configureren

1. Als u de configuratie in Jamf Pro wilt automatiseren, installeert u de **uitbrei ding mijn apps Secure Sign-in browser** door **de extensie installeren**te selecteren.

    ![Pagina mijn apps beveiligde aanmelding browser uitbreiding](common/install-myappssecure-extension.png)

2. Nadat u de uitbrei ding aan de browser hebt toegevoegd, selecteert u **Jamf Pro instellen**. Wanneer de Jamf Pro-toepassing wordt geopend, geeft u de beheerders referenties op om u aan te melden. De browser uitbreiding configureert automatisch de toepassing en automatiseert stap 3 tot en met 7.

    ![Configuratie pagina voor installatie in Jamf Pro](common/setup-sso.png)

3. Om Jamf Pro hand matig in te stellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Jamf Pro-bedrijfs site als beheerder. Voer vervolgens de volgende stappen uit.

4. Selecteer het **pictogram instellingen** in de rechter bovenhoek van de pagina.

    ![Selecteer het pictogram instellingen in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Selecteer **eenmalige aanmelding**.

    ![Eenmalige aanmelding selecteren in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Voer de volgende stappen uit op de pagina **eenmalige aanmelding** .

    ![De pagina voor eenmalige aanmelding in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Selecteer **Bewerken**.

    b. Schakel het selectie vakje **verificatie via eenmalige aanmelding inschakelen** in.

  c. Selecteer **Azure** als een optie in de vervolg keuzelijst **ID-provider** .

  d. Kopieer de waarde van de **entiteit-id** en plak deze in het veld **id (Entiteits-ID)** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

> [!NOTE]
> Gebruik de waarde in het veld `<SUBDOMAIN>` om de aanmeldings-URL en de antwoord-URL te volt ooien in het gedeelte **basis configuratie van SAML** in de Azure Portal.

  e. Selecteer **meta gegevens-URL** in de vervolg keuzelijst **ID-provider meta gegevens bron** . In het veld dat wordt weer gegeven, plakt u de URL-waarde van de **app Federation-meta gegevens** die u hebt gekopieerd uit de Azure Portal.

  f. Beschrijving Bewerk de waarde voor het verval van het token of selecteer verval datum van SAML-token uitschakelen.

7. Schuif op dezelfde pagina omlaag naar de sectie **gebruikers toewijzing** . Voer vervolgens de volgende stappen uit.

    ![De sectie gebruikers toewijzing van de pagina eenmalige aanmelding in Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Selecteer de optie **NameID** voor de **gebruikers toewijzing**van de ID-provider. Deze optie is standaard ingesteld op **NameID**, maar u kunt een aangepast kenmerk definiëren.

    b. Selecteer **e-mail** voor **Jamf Pro-gebruikers toewijzing**. Jamf Pro wijst SAML-kenmerken toe die door de IdP eerst worden verzonden door gebruikers en vervolgens op groepen. Wanneer een gebruiker toegang probeert te krijgen tot Jamf Pro, haalt Jamf Pro informatie over de gebruiker van de identiteits provider en komt deze overeen met alle Jamf Pro-gebruikers accounts. Als het binnenkomende gebruikers account niet wordt gevonden, probeert Jamf Pro dit te laten overeenkomen met de groeps naam.

    c. Plak de waarde `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` in het veld **kenmerk naam van ID-provider** .

    d. Schuif op dezelfde pagina omlaag naar de sectie **beveiliging** en selecteer **gebruikers toestaan om verificatie met eenmalige aanmelding over te slaan**. Als gevolg hiervan worden gebruikers niet omgeleid naar de aanmeldings pagina van de identiteits provider voor verificatie en kunnen ze zich rechtstreeks aanmelden bij Jamf Pro. Wanneer een gebruiker toegang probeert te krijgen tot Jamf Pro via de id-provider, vindt IdP-geïnitieerde SSO-verificatie en -autorisatie plaats.

    e. Selecteer **Opslaan**.

### <a name="create-a-jamf-pro-test-user"></a>Een Jamf Pro-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Jamf Pro, moeten ze worden ingericht in Jamf Pro. Inrichten in Jamf Pro is een hand matige taak.

Voer de volgende stappen uit om een gebruikers account in te richten:

1. Meld u aan bij uw Jamf Pro-bedrijfs site als beheerder.

2. Selecteer het pictogram **instellingen** in de rechter bovenhoek van de pagina.

    ![Het pictogram instellingen in Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Selecteer **Jamf Pro-gebruikers Accounts & groepen**.

    ![Het pictogram Jamf Pro gebruikers accounts & groups in Jamf Pro-instellingen](./media/jamfprosamlconnector-tutorial/user1.png)

4. Selecteer **Nieuw**.

    ![Pagina Jamf Pro-gebruikers accounts &-groepen systeem instellingen](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecteer **Standaard account maken**.

    ![De optie standaard account maken op de pagina Jamf Pro-gebruikers accounts & groepen](./media/jamfprosamlconnector-tutorial/user3.png)

6. Voer de volgende stappen uit in het dialoog venster **Nieuw account** :

    ![Nieuwe opties voor het instellen van accounts in Jamf Pro-systeem instellingen](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Voer in het veld **gebruikers naam** `Britta Simon`, de volledige naam van de test gebruiker.

    b. Selecteer de opties voor **toegangs niveau**, **set met bevoegdheden**en **toegangs status** die in overeenstemming zijn met uw organisatie.

    c. Voer in het veld **volledige naam** `Britta Simon`in.

    d. Voer in het veld **e-mail adres** het e-mail adres van het account van de Juliae Simon in.

    e. Voer in het veld **wacht woord** het wacht woord van de gebruiker in.

    f. Voer in het veld **wacht woord bevestigen** het wacht woord van de gebruiker opnieuw in.

    g. Selecteer **Opslaan**.

## <a name="test-the-sso-configuration"></a>De SSO-configuratie testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel Jamf Pro selecteert in het toegangs venster, moet u automatisch worden aangemeld bij het Jamf Pro-account waarvoor u SSO hebt geconfigureerd. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Zelf studies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Probeer Jamf Pro met Azure AD](https://aad.portal.azure.com/)