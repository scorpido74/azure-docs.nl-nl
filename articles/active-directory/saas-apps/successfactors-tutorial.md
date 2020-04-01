---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SuccessFactors | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en SuccessFactors configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76292976"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SuccessFactors

In deze zelfstudie leert u hoe u SuccessFactors integreren met Azure Active Directory (Azure AD). Wanneer u SuccessFactors integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot SuccesFactors.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SuccessFactors met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Met SuccesFactors single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SuccessFactors ondersteunt **SP** geïnitieerd e-initiatief SSO.
* Zodra u de SuccessFactors configureert, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors toevoegen vanuit de galerie

Om de integratie van SuccessFactors te configureren in Azure AD, moet u SuccessFactors vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SuccessFactors** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **Succesfactoren** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Azure AD SSO configureren en testen op succesfactoren

Azure AD SSO configureren en testen met SuccessFactors met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SuccessFactors.

Als u Azure AD SSO wilt configureren en testen met SuccessFactors, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Configureer SuccessFactors SSO](#configure-successfactors-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak de testgebruiker van SuccessFactors](#create-successfactors-test-user)** - om een tegenhanger van B.Simon in SuccessFactors te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SuccessFactors-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: 

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) om deze waarden op te vragen.

4. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Succesfactoren instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SuccessFactors.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **SuccessFactors** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-successfactors-sso"></a>Succesfactoren SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij de **beheerportal van SuccessFactors**.

2. Ga naar **Application Security** en **Single Sign On Features**.

3. Typ een waarde in het veld **Reset Token** en klik op **Save Token** om eenmalige aanmelding via SAML in te schakelen.

    ![Eenmalige aanmelding configureren in de app][11]

    > [!NOTE]
    > Deze waarde wordt gebruikt als een schakeloptie. Als er een waarde wordt opgeslagen, is eenmalige aanmelding via SAML ingeschakeld. Als er een lege waarde wordt opgeslagen, is eenmalige aanmelding via SAML uitgeschakeld.

4. Ga naar het onderstaande scherm en voer de volgende acties uit:

    ![Eenmalige aanmelding configureren in de app][12]
  
    a. Schakel het keuzerondje **SAML v2 SSO** in.
  
    b. Geef een waarde op voor **SAML Asserting Party Name**(bijvoorbeeld de SAML-verlener plus de bedrijfsnaam).

    c. Plak in het tekstvak **SAM Issuer** de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    d. Selecteer **Assertion** bij **Require Mandatory Signature**.

    e. Selecteer **Enabled** bij **Enable SAML Flag**.

    f. Selecteer **No** bij **Login Request Signature(SF Generated/SP/RP)**.

    g. Selecteer **Browser/Post Profile** bij **SAML Profile**.

    h. Selecteer **No** bij **Enforce Certificate Valid Period**.

    i. Kopieer de inhoud van het certificaatbestand dat u hebt gedownload uit de Azure-portal en plak deze in het tekstvak **SAML Verifying Certificate**.

    > [!NOTE] 
    > De certificaatinhoud moet begin- en eindcodes bevatten voor het certificaat.

5. Ga naar het gedeelte SAML V2 en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren in de app][13]

    a. Selecteer **Yes** bij **Support SP-initiated Global Logout**.

    b. Plak in het tekstvak **Global Logout Service URL (LogoutRequest destination)** de waarde voor **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Selecteer **No** bij **Require sp must encrypt all NameID elements**.

    d. Selecteer **unspecified** bij **NameID Format**.

    e. Selecteer **Yes** bij **Enable sp initiated login (AuthnRequest)**.

    f. Plak in het tekstvak **single sign on redirect service location** de waarde voor **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

6. Voer deze stappen uit als u de gebruikersnamen voor aanmelding hoofdlettergevoelig wilt maken.

    ![Eenmalige aanmelding configureren][29]

    a. Ga naar **Company Settings**(onderaan het scherm).

    b. Schakel het selectievakje **Enable Non-Case-Sensitive Username** in.

    c. Klik op **Opslaan**.

    > [!NOTE]
    > Als u deze optie probeert in te schakelen, controleert het systeem of er dan een dubbele SAML-aanmeldingsnaam wordt gemaakt. Dit is bijvoorbeeld het geval als de klant de gebruikersnamen Gebruiker1 en gebruiker1 heeft. Er is sprake van dubbele vermeldingen als hoofdlettergevoeligheid wordt uitgeschakeld. Er verschijnt dan een foutbericht en de functie wordt niet ingeschakeld. De klant moet een van de gebruikersnamen wijzigen, zodat deze verschillend zijn gespeld.

### <a name="create-successfactors-test-user"></a>Een testgebruiker maken voor SuccessFactors

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij SuccessFactors, moeten ze zijn ingericht in SuccessFactors. In het geval van SuccessFactors is dat een handmatige taak.

Om gebruikers toe te voegen in SuccessFactors, moet u contact opnemen met het [ondersteuningsteam van SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel SuccessFactors klikt, wordt u automatisch aangemeld bij de instantie van SuccessFactors waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Succesfactors met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe successfactors te beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
