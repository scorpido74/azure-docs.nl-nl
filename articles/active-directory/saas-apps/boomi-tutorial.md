---
title: 'Zelfstudie: Eenmalige aanmelding via Azure Active Directory integreren met Boomi | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Boomi configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: 8f6c815bf6b8be6d280dcdc60401f7a91e171e66
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542738"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Zelfstudie: Integratie van eenmalige aanmelding via Azure Active Directory met Boomi

In deze zelfstudie leert u hoe u Boomi kunt integreren met Azure Active Directory (Azure AD). Wanneer u Boomi integreert met Azure AD, kunt u het volgende:

* In Azure AD beheren wie toegang tot Boomi heeft.
* Uw gebruikers zich met hun Azure AD-account automatisch laten aanmelden bij Boomi.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op Boomi waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Boomi biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding
* Zodra u Boomi hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Boomi toevoegen vanuit de galerie

Om de integratie van Boomi te configureren in Azure AD, moet u Boomi vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Boomi**.
1. Selecteer **Boomi** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Eenmalige aanmelding via Azure AD configureren en testen voor Boomi

Configureer en test eenmalige aanmelding via Azure AD bij Boomi met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Boomi.

Voltooi de volgende stappen om eenmalige aanmelding via Azure AD bij Boomi te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Boomi configureren](#configure-boomi-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Een testgebruiker voor Boomi maken](#create-boomi-test-user)** : als u een tegenhanger van B.Simon in Boomi wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Boomi** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u een **metagegevensbestand van de serviceprovider** hebt en in de door **IDP** geïnitieerde modus wilt configureren:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    ![image](common/idp-intiated.png)

    > [!Note]
    > U ontvangt het **metagegevensbestand van de serviceprovider** in de sectie **Eenmalige aanmelding bij Boomi configureren**, die verderop in de zelfstudie wordt beschreven. Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, kunt u de waarden zelf invullen afhankelijk van uw behoeften.

1. In de Boomi-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Boomi-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Boomi instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding via Azure te gebruiken door toegang te verlenen tot Boomi.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Boomi** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-boomi-sso"></a>Eenmalige aanmelding bij Boomi configureren

1. Meld u in een ander browservenster als beheerder aan bij uw Boomi-bedrijfssite.

1. Ga naar **Company Name** en daarna naar **Set up**.

1. Klik op het tabblad **SSO Options** en voer de onderstaande stappen uit.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Schakel het selectievakje **Enable SAML Single Sign-On** in.

    b. Klik op **Import** om het gedownloade certificaat van Azure AD te uploaden naar **Identity Provider Certificate**.

    c. Plak in het tekstvak **Identity Provider Login URL** de waarde van **Aanmeldings-URL** uit het configuratievenster van de toepassing in Azure AD.

    d. Schakel bij **Federation Id Location** het keuzerondje **Federation Id is in FEDERATION_ID Attribute element** in.

    e. Kopieer de **Metagegevens-URL van AtomSphere**, ga naar de **Metagegevens-URL** via een browser naar keuze en sla de uitvoer op in een bestand. Upload de **Metagegevens-URL** in het gedeelte **SAML-basisconfiguratie** in de Azure Portal.

    f. Klik op de knop **Save**.

### <a name="create-boomi-test-user"></a>Een testgebruiker maken voor Boomi

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Boomi, moeten ze worden ingericht voor Boomi. In het geval van Boomi is dat een handmatige taak.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u als beheerder aan bij de bedrijfssite van Boomi.

1. Ga na het aanmelden naar **User Management** en **Users**.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_001.png "Gebruikers")

1. Klik op het pictogram **+** om het dialoogvenster **Add / Maintain User Roles** te openen.

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_002.png "Gebruikers")

    ![Gebruikers](./media/boomi-tutorial/tutorial_boomi_003.png "Gebruikers")

    a. Typ in het tekstvak **User e-mail address** het e-mailadres van de gebruiker, bijvoorbeeld B.Simon@contoso.com.

    b. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, zoals B.

    c. Typ in het tekstvak **Last name** de achternaam van de gebruiker, zoals Simon.

    d. Typ een waarde voor de gebruiker in het vak **Federation ID**. Elke gebruiker moet een federatie-id hebben die de gebruiker uniek identificeert binnen het account.

    e. Wijs de rol **Standard User** toe aan de gebruiker. Wijs niet de rol Administrator toe, omdat de gebruiker dan niet alleen beschikt over gewone AtomSphere-toegang, maar ook over toegang via eenmalige aanmelding.

    f. Klik op **OK**.

    > [!NOTE]
    > De gebruiker ontvangt geen welkomstbericht met een wachtwoord dat kan worden gebruikt voor aanmelding bij het AtomSphere-account omdat het wachtwoord van de gebruiker wordt beheerd via de id-provider. U kunt andere hulpprogramma's of API's van Boomi gebruiken voor het inrichten van AAD-gebruikersaccounts.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Boomi klikt, wordt u automatisch aangemeld bij de instantie van Boomi waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Boomi proberen met Azure AD](https://aad.portal.azure.com/)
