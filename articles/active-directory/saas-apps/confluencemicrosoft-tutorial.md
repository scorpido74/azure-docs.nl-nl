---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Confluence SAML SSO door Microsoft | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120585"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Confluence SAML SSO door Microsoft

In deze zelfstudie leert u hoe u Samenvloeiing SAML SSO by Microsoft integreert met Azure Active Directory (Azure AD). Wanneer u Confluence SAML SSO by Microsoft integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Confluence SAML SSO van Microsoft.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Confluence SAML SSO by Microsoft met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="description"></a>Beschrijving:

Gebruik uw Microsoft Azure Active Directory-account met Confluence Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op deze manier kunnen al uw organisatiegebruikers de Azure AD-referenties gebruiken om zich aan te melden bij de Toepassing Samenvloeiing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

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

Om aan de slag te gaan, heb je de volgende items nodig:

* Gebruik niet de productieomgeving, tenzij dit echt nodig is.
* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Samenvloeiing SAML SSO door Microsoft single sign-on (SSO) ingeschakeld abonnement.

## <a name="supported-versions-of-confluence"></a>Ondersteunde versies van Confluence

Vanaf dit moment worden de volgende versies van Confluence ondersteund:

- Samenloop: 5,0 tot 5,10
- Samenloop: 6.0.1 tot 6.15.9
- Samenloop: 7,0,1 tot 7,1.0

> [!NOTE]
> Houd er rekening mee dat onze Samenloopplugin ook werkt op Ubuntu Versie 16.04

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Confluence SAML SSO by Microsoft ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Confluence SAML SSO by Microsoft toevoegen vanuit de galerie

Om de integratie van Confluence SAML SSO by Microsoft te configureren in Azure AD, moet u Confluence SAML SSO by Microsoft vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ In de sectie **Toevoegen van de galerie** het type **Confluence SAML SSO by Microsoft** in het zoekvak.
1. Selecteer **Samenvloeiing SAML SSO by Microsoft** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Azure AD-singlesign-on configureren en testen voor Samenvloeiing SAML SSO van Microsoft

Azure AD SSO configureren en testen met Confluence SAML SSO door Microsoft met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Confluence SAML SSO van Microsoft.

Als u Azure AD SSO wilt configureren en testen met Confluence SAML SSO van Microsoft, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Confluence SAML SSO by Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Confluence SAML SSO door Microsoft-testgebruiker](#create-confluence-saml-sso-by-microsoft-test-user)** - om een tegenhanger van B.Simon in Confluence SAML SSO van Microsoft te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Confluence SAML SSO by** Microsoft-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding.**
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van Confluence, wat later in de zelfstudie wordt uitgelegd.

1. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op de knop Kopiëren om **de url van de appfederatie-metagegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Confluence SAML SSO van Microsoft.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Confluence SAML SSO by Microsoft** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Confluence SAML SSO configureren door Microsoft SSO

1. Meld u in een ander browservenster aan bij uw instantie Confluence als beheerder.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/addon1.png)

1. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56503). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Eenmalige aanmelding configureren](./media/confluencemicrosoft-tutorial/addon12.png)

1. Voer de volgende stappen uit voor het uitvoeren van het scenario voor omgekeerde proxy van Samenvloeiing of het scenario van de load balancer:

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
    
    1. Typ **in Aanmeldingsknopbeschrijving** de beschrijving van de knop die uw organisatie wil dat de gebruikers op het inlogscherm zien.

    1. Selecteer bij **SAML User ID Location** het keuzerondje **User ID is in the NameIdentifier element of the Subject statement** of **User ID is in an Attribute element**.  Deze ID moet de Confluence gebruikersnaam zijn. Als de gebruikersnaam niet is afgestemd, kunnen gebruikers zich niet aanmelden. 

       > [!Note]
       > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    1. Als u **gebruikersnaam selecteert, bevindt u zich in een** element attribuut, typt u in **tekstvak Kenmerknaam** de naam van het kenmerk waar gebruikersnaam wordt verwacht. 

    1. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    1. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    1. Schakel **Eenmalig afmelden in** als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij Confluence. 

    1. Schakel het selectievakje **Fora Azure Login** inschakelen in als u zich alleen via Azure AD-referenties wilt aanmelden.

       > [!Note]
       > Als u het standaard aanmeldingsformulier voor beheerders wilt inschakelen op de aanmeldingspagina wanneer de force azure-aanmelding is ingeschakeld, voegt u de queryparameter toe in de URL van de browser.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Klik op **Save** om de instellingen op te slaan.

       > [!NOTE]
       > Ga voor meer informatie over installatie en probleemoplossing naar [MS Confluence SSO Connector Admin Guide](../ms-confluence-jira-plugin-adminguide.md). Er is ook een [FAQ](../ms-confluence-jira-plugin-faq.md) voor uw hulp.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Confluence SAML SSO by Microsoft-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij de on-premises server van Confluence, moeten ze door Microsoft worden ingericht in Confluence SAML SSO. In het geval van Confluence SAML SSO by Microsoft is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw On-premises server van Confluence als beheerder.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/confluencemicrosoft-tutorial/user1.png)

1. Klik onder de sectie Gebruikers op Het tabblad **Gebruikers toevoegen.** Voer op de pagina **Een gebruiker toevoegen** de volgende stappen uit:

    ![Werknemer toevoegen](./media/confluencemicrosoft-tutorial/user2.png)

    a. Typ in het tekstvak **Gebruikersnaam** de e-mail van gebruiker zoals B.Simon.

    b. Typ in het tekstvak **Volledige naam** de volledige naam van de gebruiker zoals B.Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    d. Typ in het tekstvak **Wachtwoord** het wachtwoord voor B.Simon.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.

    f. Klik op de knop **Add**.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel Confluence SAML SSO by Microsoft klikt, moet u automatisch worden aangemeld bij de instantie van Confluence SAML SSO by Microsoft waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Samenloop SAML SSO van Microsoft met Azure AD](https://aad.portal.azure.com/)
