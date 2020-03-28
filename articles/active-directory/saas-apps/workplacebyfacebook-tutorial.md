---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Workplace by Facebook | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Workplace by Facebook configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de84f2aee5f59d14ab70cb1687968643c4cdb31e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136377"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Workplace by Facebook

In deze zelfstudie leert u hoe u Workplace by Facebook integreert met Azure Active Directory (Azure AD). Wanneer u Workplace by Facebook integreert met Azure AD, u het nieuwe:

* Beheer in Azure AD die toegang heeft tot Workplace by Facebook.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Workplace door Facebook met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Met Eenmalige aanmelding (Workplace by Facebook) is een abonnement ingeschakeld.

> [!NOTE]
> Facebook heeft twee producten, Workplace Standard (gratis) en Workplace Premium (betaald). Elke tenant van Workplace Premium kan gratis en zonder licenties SCIM- en SSO-integratie configureren. SSO en SCIM zijn niet beschikbaar in exemplaren van Workplace Standard.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Workplace by Facebook ondersteunt door **SP** geïnitieerde SSO
* Workplace by Facebook ondersteunt **just-in-time inrichten**
* Workplace by Facebook ondersteunt **[automatisch inrichten van gebruikers](workplacebyfacebook-provisioning-tutorial.md)**
* Workplace by Facebook Mobile-toepassing kan nu worden geconfigureerd met Azure AD voor het inschakelen van SSO. In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.
* Zodra je Workplace by Facebook hebt geconfigureerd, kun je sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van je organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Workplace by Facebook toevoegen vanuit de galerie

Om de integratie van Workplace by Facebook in Azure AD te configureren, moet u Workplace by Facebook vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Workplace by Facebook** in de sectie Toevoegen in de **galerijsectie** in het zoekvak.
1. Selecteer **Workplace by Facebook** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Azure AD SSO voor Workplace door Facebook configureren en testen

Azure AD SSO configureren en testen met Workplace by Facebook met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Workplace by Facebook.

Voer de volgende bouwstenen in om Azure AD SSO met Workplace by Facebook te configureren en te testen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
2. **[Configureer Workplace by Facebook SSO](#configure-workplace-by-facebook-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Workplace by Facebook-testgebruiker](#create-workplace-by-facebook-test-user)** - om een tegenhanger van B.Simon in Workplace door Facebook te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
3. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Workplace by Facebook-toepassingsintegratie** de sectie **Beheren** en selecteer Enkele **aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<instancename>.facebook.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://www.facebook.com/company/<instanceID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Zie de verificatiepagina van het Dashboard werkplekbedrijven voor de juiste waarden voor uw Workplace-community, dit wordt later in de zelfstudie uitgelegd.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Workplace by Facebook** instellen de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon azure single sign-on gebruiken door toegang te verlenen tot Workplace by Facebook.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer Workplace by **Facebook**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-workplace-by-facebook-sso"></a>Werkplek configureren op Facebook SSO

1. Als u de configuratie binnen Workplace by Facebook wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat je de extensie aan de browser hebt toegevoegd, klik je op **Workplace instellen door Facebook** en stuur je naar de Workplace by Facebook-applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Workplace by Facebook. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

1. Als je Workplace by Facebook handmatig wilt instellen, open je een nieuw browservenster en meld je je aan bij je Workplace by Facebook-bedrijfssite als beheerder en voer je de volgende stappen uit:

    > [!NOTE]
    > Als onderdeel van het SAML-verificatieproces kan Workplace queryreeksen van maximaal 2,5 kB gebruiken om parameters door te geven aan Azure AD.

1. Navigeer in het linkernavigatiedeelvenster naar het tabblad > **Beveiligingsverificatie.** **Security**

    ![Deelvenster Beheer](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Controleer de optie **Enkelondertekenen(SSO).**
    
    b. Klik op **+Nieuwe SSO-provider toevoegen**.
    > [!NOTE]
    > Controleer ook het selectievakje Wachtwoord aanmeldvak. Beheerders hebben deze optie mogelijk nodig om in te loggen terwijl ze de certificaatrollover doen om te voorkomen dat ze buitengesloten worden.

1. Selecteer op het tabblad **Verificatie** de optie **Eenmalige aanmelding (SSO)** en voer de volgende stappen uit:

    ![Tabblad Verificatie](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Voer in de **naam van de SSO-provider**de sso-instantienaam in zoals Azureadsso.

    b. Plak in het tekstvak **SAM-URL** de waarde van **Aanmeldings-URL**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in **het tekstvak URL van SAML Issuer** de waarde van **azure AD-id**, die u hebt gekopieerd vanuit azure-portal.

    d. Open in Kladblok het **met Base 64 gecodeerde certificaat** dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **SAML-certificaat**.

    e. Kopieer de **URL van de doelgroep** voor uw instantie en plak deze in het tekstvak Id **(Entity ID)** in de sectie **BasisSAML-configuratie** op azure-portal.

    f. Kopieer de **URL van geadresseerden** voor uw instantie en plak deze in **het tekstvak Ondertekenen op URL** in de sectie **BasisSAML-configuratie** op Azure-portal.

    g. Kopieer de **URL van DE ACS -URL (Assertion Consumer Service)** voor uw instantie en plak deze in het tekstvak Van De URL van **Antwoord** in de sectie **BasisSAML-configuratie** op Azure-portal.

    h. Blader naar de onderkant van het gedeelte en klik op de knop **SSO testen**. Er verschijnt een pop-upvenster met de aanmeldingspagina van Azure AD. Voer uw referenties op de gebruikelijke manier in om te verifiëren.

    **Probleemoplossing:** Zorg ervoor dat het e-mailadres dat wordt geretourneerd uit Azure AD hetzelfde is als het Workplace-account waarmee u bent aangemeld.

    i. Zodra de test is voltooid, bladert u naar de onderkant van de pagina en klikt u op de knop **Opslaan**.

    j. Alle gebruikers van Workplace zien nu voortaan de Azure AD-aanmeldingspagina voor verificatie.

1. **SAML-afmeldingsomleiding (optioneel)** -

    U kunt eventueel een URL voor SAML afmelding configureren, die kan worden gebruikt om te verwijzen naar de afmeldingspagina van Azure AD. Als deze instelling is ingeschakeld en geconfigureerd, wordt de gebruiker niet meer omgeleid naar de afmeldingspagina van Workplace. In plaats daarvan wordt de gebruiker omgeleid naar de URL die is opgegeven in het tekstvak Omleiden SAML-afmelding.

### <a name="configuring-reauthentication-frequency"></a>Reauthentication-frequentie configureren

U kunt instellen dat Workplace elke dag, drie dagen, week, twee weken, maanden of nooit vraagt om een SAML-controle.

> [!NOTE]
> De minimumwaarde voor de SAML-controle voor mobiele toepassingen is ingesteld op één week.

U ook een SAML-reset forceren voor alle gebruikers met behulp van de knop: SAML-verificatie vereisen voor alle gebruikers nu.

### <a name="create-workplace-by-facebook-test-user"></a>Testgebruiker voor Workplace by Facebook maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in Workplace door Facebook. Workplace by Facebook ondersteunt just-in-time inrichten. Deze functie is standaard ingeschakeld.

U hoeft geen handelingen uit te voeren in dit gedeelte. Als er geen gebruiker bestaat in Workplace by Facebook, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot Workplace by Facebook.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met [het ondersteuningsteam van Workplace by Facebook](https://workplace.fb.com/faq/).

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Workplace by Facebook klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Test SSO voor Workplace door Facebook (mobiel)

1. Open Workplace by Facebook Mobile applicatie. Klik op de aanmeldingspagina op **Log in**.

    ![De aanmelding](./media/workplacebyfacebook-tutorial/test05.png)

2. Voer uw zakelijke e-mail in en klik op **DOORGAAN.**

    ![De e-mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Klik **slechts één keer**.

    ![De eens](./media/workplacebyfacebook-tutorial/test04.png)

4. Klik op **Toestaan**.

    ![De Toestaan](./media/workplacebyfacebook-tutorial/test03.png)

5. Ten slotte na een succesvolle aanmelding, zal de homepage van de toepassing worden weergegeven.    

    ![De startpagina](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Gebruikersinrichting configureren](workplacebyfacebook-provisioning-tutorial.md)

- [Workplace by Facebook uitproberen met Azure AD](https://aad.portal.azure.com)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
