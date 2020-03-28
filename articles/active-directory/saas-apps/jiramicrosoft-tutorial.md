---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met JIRA SAML SSO van Microsoft | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en JIRA SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd922d0e65da627f11e6aab3827cb848c3dd635
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75560483"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met JIRA SAML SSO door Microsoft

In deze zelfstudie leert u hoe jira SAML-sso van Microsoft worden geïntegreerd met Azure Active Directory (Azure AD). Wanneer u JIRA SAML SSO by Microsoft integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot JIRA SAML SSO door Microsoft.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij JIRA SAML SSO door Microsoft met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="description"></a>Beschrijving

Gebruik uw Microsoft Azure Active Directory-account met JIRA Server van Atlassian om eenmalige aanmelding mogelijk te maken. Op deze manier kunnen al uw organisatiegebruikers de Azure AD-referenties gebruiken om zich aan te melden bij de JIRA-toepassing. Deze invoegtoepassing gebruikt SAML 2.0 voor federatie.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met JIRA SAML SSO by Microsoft hebt u het volgende nodig:

- Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
- JIRA Core en Software 6.4 tot 8.5.1 of JIRA Service Desk 3.0 tot 4.6.0 moeten worden geïnstalleerd en geconfigureerd op windows 64-bits versie
- JIRA-server is ingeschakeld voor HTTPS
- In de volgende sectie kunt u zien wat de ondersteunde versies zijn van de JIRA-invoegtoepassing.
- De JIRA-server is bereikbaar op internet vanaf de aanmeldingspagina van Azure AD voor verificatie en moet token uit Azure AD kunnen ontvangen
- Beheerderreferenties zijn ingesteld in JIRA
- WebSudo is uitgeschakeld in JIRA
- Testgebruiker gemaakt in de JIRA-servertoepassing

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, raden wij u aan niet een productieomgeving van JIRA te gebruiken. Test de integratie eerst in een ontwikkel- of faseringsomgeving van de toepassing en gebruik dan pas de productieomgeving.

Om aan de slag te gaan, heb je de volgende items nodig:

* Gebruik niet de productieomgeving, tenzij dit echt nodig is.
* JIRA SAML SSO by Microsoft single sign-on (SSO) enabled subscription.

## <a name="supported-versions-of-jira"></a>Ondersteunde versies van JIRA

* JIRA-kern en software: 6,4 tot 8,5.1
* JIRA Service Desk 3.0.0 tot 4.6.0
* JIRA ondersteunt ook 5.2. Klik voor meer informatie op [Microsoft Azure Active Directory-eenmalige aanmelding voor JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Houd er rekening mee dat onze JIRA Plugin ook werkt op Ubuntu Versie 16.04 en Linux.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* JIRA SAML SSO by Microsoft ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>JIRA SAML SSO by Microsoft toevoegen vanuit de galerie

Voor het configureren van de integratie van JIRA SAML SSO by Microsoft in Azure AD, moet u JIRA SAML SSO by Microsoft uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **JIRA SAML SSO by Microsoft in** het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **JIRA SAML SSO by Microsoft** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>Azure AD-singlesign-aan configureren en testen voor JIRA SAML SSO door Microsoft

Azure AD SSO configureren en testen met JIRA SAML SSO door Microsoft met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in JIRA SAML SSO van Microsoft.

Voer de volgende bouwstenen uit om Azure AD SSO te configureren en te testen met JIRA SAML SSO van Microsoft:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer JIRA SAML SSO by Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak JIRA SAML SSO van Microsoft-testgebruiker](#create-jira-saml-sso-by-microsoft-test-user)** - om een tegenhanger van B.Simon in JIRA SAML SSO van Microsoft te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **JIRA SAML SSO by** Microsoft-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<domain:port>/plugins/servlet/saml/auth`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<domain:port>/`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. 'Port' is optioneel als het een benoemde URL betreft. Deze waarden krijgt u tijdens de configuratie van de JIRA-invoegtoepassing, wat later in de zelfstudie wordt uitgelegd.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot JIRA SAML SSO door Microsoft.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **JIRA SAML SSO by Microsoft** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>JIRA SAML SSO configureren door Microsoft SSO

1. Meld u in een ander browservenster aan bij uw JIRA-exemplaar als beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Eenmalige aanmelding configureren](./media/jiramicrosoft-tutorial/addon1.png)

3. Download de invoegtoepassing uit het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56506). Upload de invoegtoepassing van Microsoft handmatig via het menu **Upload add-on**. Het downloaden van invoegtoepassingen valt onder de [Microsoft-serviceovereenkomst](https://www.microsoft.com/servicesagreement/).

    ![Eenmalige aanmelding configureren](./media/jiramicrosoft-tutorial/addon12.png)

4. Voer de volgende stappen uit voor het uitvoeren van het omgekeerde-proxyscenario of load balancer-scenario van JIRA:

    > [!NOTE]
    > U moet de server eerst configureren met de onderstaande instructies en vervolgens de invoegtoepassing installeren.

    a. Voeg het onderstaande kenmerk toe in de **connector**-poort in het bestand **server.xml** van de JIRA-servertoepassing.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Eenmalige aanmelding configureren](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Wijzig **Base URL** in **System Settings** overeenkomstig de proxy/load balancer.

    ![Eenmalige aanmelding configureren](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Zodra de invoegtoepassing is geïnstalleerd, wordt deze weergegeven bij **User Installed** in de sectie **Manage add-ons**. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Eenmalige aanmelding configureren](./media/jiramicrosoft-tutorial/addon14.png)

6. Voer de volgende stappen uit op de configuratiepagina:

    ![Eenmalige aanmelding configureren](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Zorg ervoor dat er maar één certificaat is toegewezen aan de app, zodat er geen fout optreedt bij het omzetten van de metagegevens. Als er meerdere certificaten zijn, krijgt de beheerder een foutmelding bij het omzetten van de metagegevens.

    1. Plak in het tekstvak **Metadata URL** de waarde voor **App-URL voor federatieve metagegevens** die u hebt gekopieerd uit de Azure-portal en klik op de knop **Resolve**. De URL met de IdP-metagegevens wordt gelezen en de overeenkomende velden worden ingevuld.

    1. Kopieer de waarden voor **Identifier, Reply URL, Sign on URL** en plak deze in respectievelijk de vakken **Id, Antwoord-URL en Aanmeldings-URL** in de sectie **Domein- en URL-gegevens voor JIRA SAML SSO** in de Azure-portal.

    1. Typ bij **Login Button Name** de naam van de knop die gebruikers van uw organisatie moeten zien op het aanmeldingsscherm.
    
    1. Typ **in Aanmeldingsknopbeschrijving** de beschrijving van de knop die uw organisatie wil dat de gebruikers op het inlogscherm zien.

    1. Selecteer in **SAML-gebruikersnaamlocaties** een **gebruikersnaam in het element NameIdentifier van de onderwerpinstructie** of gebruikersnaam in een **kenmerkelement**.  Deze ID moet de JIRA-gebruikersnaam zijn. Als de gebruikersnaam niet is afgestemd, kunnen gebruikers zich niet aanmelden.

       > [!Note]
       > De gebruikers-id in de standaard SAML-configuratie is de NameIdentifier. U kunt dit wijzigen in een kenmerkoptie en de juiste kenmerknaam invoeren.

    1. Als u **gebruikersnaam selecteert, bevindt u zich in een** element attribuut, typt u in **tekstvak Kenmerknaam** de naam van het kenmerk waar gebruikersnaam wordt verwacht.

    1. Als u het federatieve domein (zoals ADFS, enzovoort) gebruikt met Azure AD, selecteert u de optie **Enable Home Realm Discovery** en geeft u een naam op voor **Domain Name**.

    1. Typ bij **Domain Name** de domeinnaam als het aanmelding op basis van ADFS betreft.

    1. Schakel **Eenmalig afmelden in** als u zich wilt afmelden bij Azure AD wanneer een gebruiker zich afmeldt bij JIRA.
    
    1. Schakel het selectievakje **Fora Azure Login** inschakelen in als u zich alleen via Azure AD-referenties wilt aanmelden.
    
       > [!Note]
       > Als u het standaard aanmeldingsformulier voor beheerders wilt inschakelen op de aanmeldingspagina wanneer force azure-aanmelding is ingeschakeld, voegt u de queryparameter toe in de URL van de browser.
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. Klik op **Save** om de instellingen op te slaan.

       > [!NOTE]
       > Ga voor meer informatie over installatie en probleemoplossing naar [de MS JIRA SSO Connector Admin Guide](../ms-confluence-jira-plugin-adminguide.md). Er is ook een [FAQ](../ms-confluence-jira-plugin-faq.md) voor uw hulp.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Een testgebruiker maken in JIRA SAML SSO by Microsoft

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij de on-premises server van JIRA, moeten ze door Microsoft zijn ingericht in JIRA SAML SSO. In het geval van JIRA SAML SSO by Microsoft is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw JIRA on-premises server als beheerder.

2. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/jiramicrosoft-tutorial/user1.png)

3. U wordt omgeleid naar de toegangspagina voor beheerders. Voer uw **wachtwoord** in en klik op de knop **Bevestigen**.

    ![Werknemer toevoegen](./media/jiramicrosoft-tutorial/user2.png)

4. Onder de tabbladsectie **Gebruikersbeheer** klikt u op **Gebruiker maken**.

    ![Werknemer toevoegen](./media/jiramicrosoft-tutorial/user3.png) 

5. Op de pagina **Nieuwe gebruiker maken** voert u de volgende stappen uit:

    ![Werknemer toevoegen](./media/jiramicrosoft-tutorial/user4.png) 

    a. Typ in het tekstvak **Email address** het e-mailadres van de gebruiker, bijvoorbeeld B.simon@contoso.com.

    b. Typ in het tekstvak **Volledige naam** de volledige naam van de gebruiker zoals B.Simon.

    c. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld B.simon@contoso.com.

    d. In het tekstvak **Wachtwoord** typt u het wachtwoord van de gebruiker.

    e. Klik **op Gebruiker maken**.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel JIRA SAML SSO by Microsoft klikt, zou u automatisch moeten worden aangemeld bij de instantie van JIRA SAML SSO by Microsoft waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer JIRA SAML SSO by Microsoft met Azure AD](https://aad.portal.azure.com/)
