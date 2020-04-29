---
title: 'Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met confluence SAML SSO door micro soft | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Confluence SAML SSO by Microsoft configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ea52474f3b352fabf19050f20012438e9d0c19
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76120585"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met confluence SAML SSO door micro soft

In deze zelf studie leert u hoe u confluence SAML SSO door micro soft integreert met Azure Active Directory (Azure AD). Wanneer u confluence SAML SSO door micro soft integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot confluence SAML SSO door micro soft.
* Stel uw gebruikers in staat om automatisch te worden aangemeld voor confluence SAML SSO door micro soft met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="description"></a>Beschrijving:

Gebruik uw Microsoft Azure Active Directory-account met Confluence Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op die manier kunnen alle gebruikers van uw organisatie de Azure AD-referenties gebruiken om zich aan te melden bij de confluence-toepassing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

## <a name="prerequisites"></a>Vereisten

Om Azure AD-integratie te configureren met Confluence SAML SSO by Microsoft hebt u de volgende items nodig:

- Een Azure AD-abonnement
- De toepassing Confluence Server geïnstalleerd op een server met 64-bits Windows (on-premises of in de IaaS-infrastructuur in de cloud)
- Confluence Server is ingeschakeld voor HTTPS
- In de volgende sectie kunt u zien wat de ondersteunde versies zijn van de Confluence-invoegtoepassing
- Confluence Server is bereikbaar op internet vanaf de aanmeldingspagina van Azure AD voor verificatie en moet token uit Azure AD kunnen ontvangen
- Beheerderreferenties zijn ingesteld in Confluence
- WebSudo is uitgeschakeld in Confluence
- Testgebruiker gemaakt in Confluence Server

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam niet een productieomgeving van Confluence te gebruiken. Test de integratie eerst in een ontwikkel- of faseringsomgeving van de toepassing en gebruik dan pas de productieomgeving.

U hebt de volgende items nodig om aan de slag te gaan:

* Gebruik niet de productieomgeving, tenzij dit echt nodig is.
* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Confluence SAML SSO door micro soft SSO (single sign-on) ingeschakeld abonnement.

## <a name="supported-versions-of-confluence"></a>Ondersteunde versies van Confluence

Vanaf dit moment worden de volgende versies van Confluence ondersteund:

- Confluence: 5,0 tot 5,10
- Confluence: 6.0.1 naar 6.15.9
- Confluence: 7.0.1 naar 7.1.0

> [!NOTE]
> Houd er rekening mee dat onze confluence-invoeg toepassing ook werkt op Ubuntu versie 16,04

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Confluence SAML SSO by Microsoft ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Confluence SAML SSO by Microsoft toevoegen vanuit de galerie

Om de integratie van Confluence SAML SSO by Microsoft te configureren in Azure AD, moet u Confluence SAML SSO by Microsoft vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **CONFLUENCE SAML SSO door micro soft** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **CONFLUENCE SAML SSO by micro soft** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Eenmalige aanmelding van Azure AD configureren en testen voor confluence SAML SSO door micro soft

Azure AD SSO met confluence SAML SSO van micro soft configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in confluence SAML SSO door micro soft.

Als u Azure AD SSO wilt configureren en testen met confluence SAML SSO van micro soft, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFLUENCE SAML SSO configureren met micro soft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak CONFLUENCE SAML SSO door micro soft test User](#create-confluence-saml-sso-by-microsoft-test-user)** : als u een equivalent van B. Simon in CONFLUENCE SAML SSO wilt door micro soft dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **confluence SAML SSO per micro soft** Application Integration de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van Confluence, wat later in de zelfstudie wordt uitgelegd.

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan confluence SAML SSO door micro soft.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Confluence SAML SSO by Microsoft** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Confluence SAML SSO configureren met micro soft SSO

1. Meld u in een ander browser venster aan bij uw confluence-exemplaar als beheerder.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/addon1.png)

1. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56503). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/addon12.png)

1. Voer de volgende stappen uit om het confluence reverse proxy-scenario of load balancer scenario uit te voeren:

    > [!NOTE]
    > U moet de server eerst configureren met de onderstaande instructies en vervolgens de invoegtoepassing installeren.

    a. Voeg het onderstaande kenmerk toe in de **connector**-poort in het bestand **server.xml** van de JIRA-servertoepassing.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Wijzig **Base URL** in **System Settings** overeenkomstig de proxy/load balancer.

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Zodra de invoegtoepassing is geïnstalleerd, wordt deze weergegeven bij **User Installed** in de sectie **Manage add-ons**. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/addon15.png)

1. Voer de volgende stappen uit op de configuratiepagina:

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Zorg ervoor dat er maar één certificaat is toegewezen aan de app, zodat er geen fout optreedt bij het omzetten van de metagegevens. Als er meerdere certificaten zijn, krijgt de beheerder een foutmelding bij het omzetten van de metagegevens.

    1. Plak in het tekstvak **Metadata URL** de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op de knop **Resolve**. De URL met de IdP-metagegevens wordt gelezen en de overeenkomende velden worden ingevuld.

    1. Kopieer de waarden voor **Identifier, Reply URL, Sign on URL** en plak deze in respectievelijk de vakken **Id, Antwoord-URL en Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    1. Typ bij **Login Button Name** de naam van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.
    
    1. Typ in de **knop Aanmelden** de beschrijving van de knop die de gebruiker wil zien op het aanmeldings scherm.

    1. Selecteer bij **SAML User ID Location** het keuzerondje **User ID is in the NameIdentifier element of the Subject statement** of **User ID is in an Attribute element**.  Deze ID moet de confluence-gebruikers-ID zijn. Als de gebruikers-ID niet overeenkomt, kunnen gebruikers zich niet aanmelden met het systeem. 

       > [!Note]
       > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    1. Als u **gebruikers-id in een kenmerk element** optie selecteert, typt u in het tekstvak **kenmerk naam** de naam van het kenmerk waarin de gebruikers-id wordt verwacht. 

    1. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    1. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    1. Schakel **eenmalige aanmelding inschakelen** in als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij confluence. 

    1. Schakel het selectie vakje inschakelen van **Azure-aanmelding** in als u zich alleen wilt aanmelden via Azure AD-referenties.

       > [!Note]
       > Als u het standaard aanmeldings formulier wilt inschakelen voor beheerders aanmelding op de aanmeldings pagina wanneer de geforceerde Azure-aanmelding is ingeschakeld, voegt u de query parameter toe aan de browser-URL.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Klik op **Save** om de instellingen op te slaan.

       > [!NOTE]
       > Ga voor meer informatie over installatie en probleem oplossing naar de [Beheerders handleiding voor MS CONFLUENCE SSO connector](../ms-confluence-jira-plugin-adminguide.md). Er is ook een [Veelgestelde vragen](../ms-confluence-jira-plugin-faq.md) over uw hulp.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij confluence on-premises server, moeten ze worden ingericht in confluence SAML SSO van micro soft. In het geval van Confluence SAML SSO by Microsoft is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw confluence on-premises server als beheerder.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/confluencemicrosoft-tutorial/user1.png)

1. Klik onder gebruikers op tabblad **gebruikers toevoegen** . Voer de volgende stappen uit op de dialoog pagina **een gebruiker toevoegen** :

    ![Werknemer toevoegen](./media/confluencemicrosoft-tutorial/user2.png)

    a. Typ in het tekstvak **username** het e-mail adres van de gebruiker zoals B. Simon.

    b. Typ in het tekstvak **volledige naam** de volledige naam van de gebruiker zoals B. Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    d. Typ in het tekstvak **wacht woord** het wacht woord voor B. Simon.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.

    f. Klik op de knop **Add**.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel Confluence SAML SSO by Microsoft klikt, moet u automatisch worden aangemeld bij de instantie van Confluence SAML SSO by Microsoft waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer confluence SAML SSO van micro soft met Azure AD](https://aad.portal.azure.com/)
