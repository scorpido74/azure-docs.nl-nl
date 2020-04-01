---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met EBSCO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f37085744b9a0e7785ef3a411d53e4df5d15e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595012"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met EBSCO

In deze zelfstudie leert u hoe u EBSCO integreert met Azure Active Directory (Azure AD). Wanneer u EBSCO integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot EBSCO.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij EBSCO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* EBSCO single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* EBSCO ondersteunt **SP en IDP** geïnitieerd sso
* EBSCO ondersteunt **Just In Time** gebruikersinrichting

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-ebsco-from-the-gallery"></a>EBSCO toevoegen vanuit de galerie

Als u de integratie van EBSCO in Azure AD wilt configureren, moet u EBSCO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **EBSCO** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **EBSCO** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Azure AD-aanmelding voor EBSCO configureren en testen

Azure AD SSO configureren en testen met EBSCO met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in EBSCO.

Als u Azure AD SSO wilt configureren en testen met EBSCO, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Ebsco SSO configureren](#configure-ebsco-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Ebsco-testgebruiker maken](#create-ebsco-test-user)** - om een tegenhanger van B.Simon in EBSCO te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **EBSCO-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    In het tekstvak **Id** typt u een URL: `pingsso.ebscohost.com`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [ebsco-clientondersteuningsteam](mailto:support@ebsco.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    o **Unieke elementen:**  

    o **Custid** = Voer unieke EBSCO-klant-ID in 

    o **Profiel** = Klanten kunnen de link afstemmen op het doorsturen van gebruikers naar een specifiek profiel (afhankelijk van wat ze bij EBSCO kopen). Ze kunnen een specifieke profiel-ID invoeren. De belangrijkste iD's zijn eds (EBSCO Discovery Service) en ehost (EBSOCOhost databases). Instructies voor hetzelfde worden [hier](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)gegeven.

1. EBSCO-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

    > [!Note]
    > Het **naamkenmerk** is verplicht en wordt toegewezen met **de waarde Naam-id** in ebsco-toepassing. Dit wordt standaard toegevoegd, zodat u dit niet handmatig hoeft toe te voegen.

1. Naast bovenstaande, EBSCO applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **EBSCO instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot EBSCO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **EBSCO**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-ebsco-sso"></a>EBSCO SSO configureren

Als u eenmalige aanmelding aan **de EBSCO-kant** wilt configureren, moet u de gedownloade **XML met aalmetagegevens** van de Federatie en de juiste gekopieerde URL's van Azure-portal naar [ebsco-ondersteuningsteam](mailto:support@ebsco.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-ebsco-test-user"></a>EBSCO-testgebruiker maken

In het geval van EBSCO is de inprovisioning van de gebruiker automatisch.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

Azure AD geeft de vereiste gegevens door aan EBSCO-toepassing. EbSCO's gebruikersinrichting kan automatisch zijn of een eenmalig formulier vereisen. Het hangt ervan af of de client veel reeds bestaande EBSCOhost-accounts heeft met persoonlijke instellingen die zijn opgeslagen. Hetzelfde kan tijdens de implementatie worden besproken met het [EBSCO support team.](mailto:support@ebsco.com) Hoe dan ook, de client hoeft geen EBSCOhost-accounts aan te maken voordat ze worden getest.

   > [!Note]
   > U eBSCOhost gebruikersinrichting/personalisatie automatiseren. Neem contact op met [ebsco-ondersteuningsteam](mailto:support@ebsco.com) over just-in-time gebruikersinrichting.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

1. Wanneer u op de EBSCO-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij uw EBSCO-toepassing.
Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

1. Klik op de **inlogknop** in de rechterbovenhoek wanneer je je eenmaal aanmeldt bij de applicatie.

    ![De EBSCO-aanmelding in de lijst Toepassingen](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. U ontvangt een eenmalige prompt om de instelling/SAML-login te koppelen aan een **MyEBSCOhost-account nu aan uw instellingsaccount** of **maak een nieuw MyEBSCOhost-account aan en koppel deze aan uw instellingsaccount.** Het account wordt gebruikt voor personalisatie op de EBSCOhost applicatie. Selecteer de optie **Een nieuw account maken** en u ziet dat het formulier voor personalisatie vooraf is ingevuld met de waarden uit het saml-antwoord zoals weergegeven in de onderstaande schermafbeelding. Klik **op 'Doorgaan'** om deze selectie op te slaan.
    
     ![De EBSCO-gebruiker in de lijst Toepassingen](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Na het voltooien van de bovenstaande setup, duidelijk cookies / cache en weer inloggen. U hoeft zich niet opnieuw handmatig aan te melden en de personalisatie-instellingen worden onthouden.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [EbSCO proberen met Azure AD](https://aad.portal.azure.com/)