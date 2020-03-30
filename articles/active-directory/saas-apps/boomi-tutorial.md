---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Boomi | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Boomi configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77086452"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Boomi

In deze zelfstudie leert u hoe Boomi worden geïntegreerd met Azure Active Directory (Azure AD). Wanneer u Boomi integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Boomi.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Boomi met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Boomi single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Boomi biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding
* Zodra u de Boomi configureert, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Boomi toevoegen vanuit de galerie

Om de integratie van Boomi te configureren in Azure AD, moet u Boomi vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Boomi** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Boomi** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Azure AD-aanmelding voor Boomi configureren en testen

Azure AD SSO configureren en testen met Boomi met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Boomi.

Als u Azure AD SSO met Boomi wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Boomi SSO](#configure-boomi-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Boomi-testgebruiker](#create-boomi-test-user)** - om een tegenhanger van B.Simon in Boomi te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Boomi-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u **het metagegevensbestand van serviceproviderprovider** hebt en wilt configureren in de **idp-modus:**

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het metagegevensbestand is geüpload, worden de **URL-waarden Id** en **Antwoord** automatisch ingevuld in de sectie BasisSAML-configuratie.

    ![installatiekopie](common/idp-intiated.png)

    > [!Note]
    > U krijgt het **metadatabestand van** de serviceprovider van de sectie **Boomi SSO configureren,** dat later in de zelfstudie wordt uitgelegd. Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. Boomi-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Boomi applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name |  Bronkenmerk|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Boomi instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Boomi.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Boomi** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-boomi-sso"></a>Boomi SSO configureren

1. Meld u in een ander browservenster aan bij uw Boomi-bedrijfssite als beheerder.

1. Ga naar **Company Name** en daarna naar **Set up**.

1. Klik op het tabblad **SSO Options** en voer de onderstaande stappen uit.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Schakel het selectievakje **Enable SAML Single Sign-On** in.

    b. Klik op **Import** om het gedownloade certificaat van Azure AD te uploaden naar **Identity Provider Certificate**.

    c. Plak in het tekstvak **Identity Provider Login URL** de waarde van **Aanmeldings-URL** uit het configuratievenster van de toepassing in Azure AD.

    d. Selecteer **de** **federatie-id-id in FEDERATION_ID knop kenmerkelement.**

    e. Kopieer de **URL van AtomSphere MetaData,** ga naar de **MetaData-URL** via de browser van uw keuze en sla de uitvoer op in een bestand. Upload de **MetaData-URL** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    f. Klik op de knop **Save**.

### <a name="create-boomi-test-user"></a>Een testgebruiker maken voor Boomi

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Boomi, moeten ze worden ingericht in Boomi. In het geval van Boomi is dat een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u aan bij uw Boomi-bedrijfssite als beheerder.

1. Ga na het aanmelden naar **User Management** en **Users**.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_001.png "Gebruikers")

1. Klik **+** op het pictogram en het dialoogvenster **Gebruikersrollen toevoegen/onderhouden** wordt geopend.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_002.png "Gebruikers")

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_003.png "Gebruikers")

    a. Typ in het tekstvak **User e-mail address** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker zoals B.

    c. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker zoals Simon.

    d. Typ een waarde voor de gebruiker in het vak **Federation ID**. Elke gebruiker moet een federatie-id hebben die de gebruiker uniek identificeert binnen het account.

    e. Wijs de rol **Standard User** toe aan de gebruiker. Wijs de rol Administrator niet toe, omdat ze daardoor normale sfeertoegang en toegang tot één aanmelding zouden krijgen.

    f. Klik op **OK**.

    > [!NOTE]
    > De gebruiker ontvangt geen welkomstbericht-e-mail met een wachtwoord dat kan worden gebruikt om in te loggen op het AtomSphere-account omdat zijn wachtwoord wordt beheerd via de identiteitsprovider. U kunt andere hulpprogramma's of API's van Boomi gebruiken voor het inrichten van AAD-gebruikersaccounts.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Boomi klikt, wordt u automatisch aangemeld bij de instantie van Boomi waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Probeer Boomi met Azure AD](https://aad.portal.azure.com/)
