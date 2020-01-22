---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Sales Force | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d0793c863f4f682c967c7a5ae61c5a0b78cdb4d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292532"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Sales Force

In deze zelf studie leert u hoe u Sales Force kunt integreren met Azure Active Directory (Azure AD). Wanneer u Sales Force integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Sales Force.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Sales Force met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding voor het Sales Force-abonnement (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Salesforce biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

* Sales Force ondersteunt [ **automatische** gebruikers inrichting en](salesforce-provisioning-tutorial.md) het ongedaan maken van de inrichting (aanbevolen)

* Salesforce biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

* Een Sales Force-app kan nu worden geconfigureerd met Azure AD om SSO in te scha kelen. In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.
* Zodra u de Sales Force configureert, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Salesforce toevoegen vanuit de galerie

Voor het configureren van de integratie van Salesforce in Azure AD moet u Salesforce vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **Sales Force** in het zoekvak.
1. Selecteer **Sales Force** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Sales Force

Azure AD SSO met Sales Force configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sales Force.

Als u Azure AD SSO met Sales Force wilt configureren en testen, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO van Sales Force](#configure-salesforce-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    * Een **[Sales Force-test gebruiker maken](#create-salesforce-test-user)** : u hebt een equivalent van B. Simon in Sales Force dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **Sales Force** -toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **Aanmeldings-URL** een URL met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Typ in het tekstvak **Id** een waarde met de volgende indeling:

    Bedrijfsaccount: `https://<subdomain>.my.salesforce.com`

    Ontwikkelaarsaccount: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en -id. Neem contact op met het [Salesforce-klantondersteuningsteam](https://help.salesforce.com/support) om deze waarden te verkrijgen.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Sales Force instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Sales Force.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Salesforce** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-salesforce-sso"></a>Sales Force-SSO configureren

1. Als u de configuratie binnen Sales Force wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **Sales Force instellen** , wordt u naar de toepassing voor eenmalige aanmelding in Sales Force geleid. Geef de beheerders referenties op om u aan te melden bij eenmalige aanmelding voor Sales Force. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-13 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u Sales Force hand matig wilt installeren, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Sales Force-bedrijfs site als beheerder en voert u de volgende stappen uit:

1. Klik op **Instellen** onder het **instellingenpictogram** in de rechterbovenhoek van de pagina.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/configure1.png)

1. Schuif in het navigatiedeelvenster omlaag naar **INSTELLINGEN** en klik op **Identiteit** om het bijbehorende gedeelte uit te vouwen. Klik vervolgens op **Instellingen voor eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-admin-sso.png)

1. Op de pagina **Instellingen voor eenmalige aanmelding** klikt u op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Als u eenmalige aanmelding niet kunt inschakelen voor uw Salesforce-account, moet u mogelijk contact opnemen met het [Salesforce-klantondersteuningsteam](https://help.salesforce.com/support).

1. Selecteer **SAML ingeschakeld** en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-enable-saml.png)

1. Als u uw SAML-instellingen voor eenmalige aanmelding wilt configureren, klikt u op **Nieuw op basis van het bestand met metagegevens**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Klik op **Bestand kiezen** om het XML-bestand met metagegevens te uploaden dat u hebt gedownload uit Azure Portal. Klik dan op **Maken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/xmlchoose.png)

1. Op de pagina **SAML-instellingen voor eenmalige aanmelding** worden de velden automatisch ingevuld. Klik op Opslaan.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/salesforcexml.png)

1. Klik in het navigatiedeelvenster links in Salesforce op **Bedrijfsinstellingen** om het bijbehorende gedeelte uit te vouwen. Klik dan op **Mijn domein**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-my-domain.png)

1. Schuif omlaag naar het gedeelte **Verificatieconfiguratie** en klik op de knop **Bewerken**.

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. In het gedeelte **Verificatieconfiguratie** schakelt u **AzureSSO** in als **verificatieservice** voor de SAML-configuratie voor eenmalige aanmelding. Klik dan op **Opslaan** .

    ![Eenmalige aanmelding configureren](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Als er meer dan één verificatieservice wordt geselecteerd, wordt gebruikers gevraagd met welke verificatieservice ze zich graag willen aanmelden om gebruik te maken van eenmalige aanmelding in uw Salesforce-omgeving. Als u niet wilt dat dit gebeurt, moet u **andere verificatieservices uitgeschakeld laten**.

### <a name="create-salesforce-test-user"></a>Een Salesforce-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Sales Force. Salesforce biedt ondersteuning voor just-in-time inrichten, wat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Salesforce, wordt er een nieuwe gemaakt wanneer u Salesforce opent. Salesforce ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Salesforce-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Salesforce waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-salesforce-mobile"></a>SSO voor Sales Force (Mobile) testen

1. Open Sales Force Mobile Application. Klik op de pagina aanmelden op **aangepast domein gebruiken**.

    ![Mobiele Sales Force-app](media/salesforce-tutorial/mobile-app1.png)

1. Voer in het tekstvak **aangepast domein** de geregistreerde aangepaste domein naam in en klik op **door gaan**.

    ![Mobiele Sales Force-app](media/salesforce-tutorial/mobile-app2.png)

1. Voer uw Azure AD-referenties in om u aan te melden bij de Sales Force-toepassing en klik op **volgende**.

    ![Mobiele Sales Force-app](media/salesforce-tutorial/mobile-app3.png)

1. Klik op de pagina **toegang toestaan** , zoals hieronder wordt weer gegeven, op **toestaan** om toegang te geven tot de Sales Force-toepassing.

    ![Mobiele Sales Force-app](media/salesforce-tutorial/mobile-app4.png)

1. Als het aanmelden is voltooid, wordt de start pagina van de toepassing weer gegeven.

    mobiele ![Sales Force-app](media/salesforce-tutorial/mobile-app5.png) ![Sales Force-app](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Inrichten van gebruikers configureren](salesforce-provisioning-tutorial.md)

- [Probeer Sales Force met Azure AD](https://aad.portal.azure.com)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Sales Force beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
