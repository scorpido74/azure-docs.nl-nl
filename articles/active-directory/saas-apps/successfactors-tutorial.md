---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SuccessFactors | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en SuccessFactors configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 48524bec352d2fa9c169a1345e52ad4c789e59d3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552115"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met SuccessFactors

In deze zelfstudie leert u hoe u SuccessFactors kunt integreren met Azure Active Directory (Azure AD). Wanneer u SuccessFactors integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot SuccessFactors.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SuccessFactors.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op SuccessFactors waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SuccessFactors biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding.
* Zodra u SuccessFactors hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors toevoegen vanuit de galerie

Om de integratie van SuccessFactors te configureren in Azure AD, moet u SuccessFactors vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **SuccessFactors** in het zoekvak in de sectie **Toevoegen uit de galerie**.
1. Selecteer **SuccessFactors** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Eenmalige aanmelding van Azure AD voor SuccessFactors configureren en testen

Configureer en test eenmalige aanmelding van Azure AD voor SuccessFactors met behulp van een testgebruiker met de naam **B. Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SuccessFactors.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD voor SuccessFactors te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding voor SuccessFactors configureren](#configure-successfactors-sso)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    1. **[Een testgebruiker voor SuccessFactors maken](#create-successfactors-test-user)** : als u een tegenhanger van B. Simon in SuccessFactors wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/), op de integratiepagina van de toepassing **SuccessFactors**, de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [klantondersteuningsteam van SuccessFactors](https://www.sap.com/support.html) om deze waarden op te vragen.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **SuccessFactors instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
    1. Voer in het veld **Naam**`B.Simon` in.  
    1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
    1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SuccessFactors.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SuccessFactors** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-successfactors-sso"></a>Eenmalige aanmelding voor SuccessFactors configureren

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

    f. Selecteer **No** bij **Login Request Signature(SF Generated/SP/RP)** .

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

    e. Selecteer **Yes** bij **Enable sp initiated login (AuthnRequest)** .

    f. Plak in het tekstvak **single sign on redirect service location** de waarde voor **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

6. Voer deze stappen uit als u de gebruikersnamen voor aanmelding hoofdlettergevoelig wilt maken.

    ![Eenmalige aanmelding configureren][29]

    a. Ga naar **Company Settings**(onderaan het scherm).

    b. Schakel het selectievakje **Enable Non-Case-Sensitive Username** in.

    c. Klik op **Opslaan**.

    > [!NOTE]
    > Als u deze optie probeert in te schakelen, controleert het systeem of er dan een dubbele SAML-aanmeldingsnaam wordt gemaakt. Dit is bijvoorbeeld het geval als de klant de gebruikersnamen Gebruiker1 en gebruiker1 heeft. Er is sprake van dubbele vermeldingen als hoofdlettergevoeligheid wordt uitgeschakeld. Er verschijnt dan een foutbericht en de functie wordt niet ingeschakeld. De klant moet een van de gebruikersnamen wijzigen, zodat deze verschillend zijn gespeld.

### <a name="create-successfactors-test-user"></a>Een testgebruiker maken voor SuccessFactors

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij SuccessFactors, moeten ze worden ingericht bij SuccessFactors. In het geval van SuccessFactors is dat een handmatige taak.

Om gebruikers toe te voegen in SuccessFactors, moet u contact opnemen met het [ondersteuningsteam van SuccessFactors](https://www.sap.com/support.html).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel SuccessFactors klikt, wordt u automatisch aangemeld bij de instantie van SuccessFactors waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SuccessFactors met Azure AD uitproberen](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SuccessFactors beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
