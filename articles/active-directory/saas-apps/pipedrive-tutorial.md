---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Pipedrive | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Pipedrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: b3b2032d8cefe881e59fe339786877c4f03c9305
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553781"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Pipedrive

In deze zelfstudie leert u hoe u Pipedrive integreert met Azure AD (Azure Active Directory). Wanneer u Pipedrive integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Pipedrive.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Pipedrive.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Pipedrive-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Pipedrive biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* Zodra u eenmalige aanmelding voor Pipedrive hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-pipedrive-from-the-gallery"></a>Pipedrive toevoegen uit de galerie

Als u de integratie van Pipedrive in Azure AD wilt configureren, moet u Pipedrive vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **Pipedrive**.
1. Selecteer **Pipedrive** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Pipedrive

Configureer en test eenmalige aanmelding van Azure AD met Pipedrive met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Pipedrive.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Pipedrive te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Pipedrive configureren](#configure-pipedrive-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    * **[Een testgebruiker voor Pipedrive maken](#create-pipedrive-test-user)** :als u in Pipedrive een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in [Azure Portal](https://portal.azure.com/) op de integratiepagina voor **Pipedrive** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [clientondersteuningsteam van Pipedrive](mailto:support@pipedrive.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de Pipedrive-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Pipedrive-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **Certificaat (base64)** en selecteer **Downloaden** om het certificaat te downloaden. Sla het certificaat vervolgens op de computer op en kopieer de **URL van de App Federation-metagegevens** en sla deze ook op.

    ![De link om het certificaat te downloaden](./media/pipedrive-tutorial/certificate-data.png)

1. Kopieer in de sectie **Pipedrive instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Pipedrive.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Pipedrive** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-pipedrive-sso"></a>Eenmalige aanmelding voor Pipedrive configureren

1. Meld u in een ander browservenster als beheerder aan bij de website van Pipedrive.

1. Klik op **Gebruikersprofiel** en selecteer **Instellingen**.

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/configure1.png)

1. Blader omlaag naar beveiligingscentrum en selecteer **Eenmalige aanmelding**.

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/configure2.png)

1. In het gedeelte **SAML-configuratie voor Pipedrive** voert u de volgende stappen uit:

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/configure3.png)

    a. Plak in het tekstvak **Verlener** de waarde van **App-URL voor federatieve metagegevens** die u uit Azure Portal hebt gekopieerd.

    b. Plak in het tekstvak **Eenmalige aanmelding (SSO)-URL** de waarde van de **aanmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **Eenmalige afmelding (SLO)-URL** de waarde van de **afmeldings-URL** die u uit Azure Portal hebt gekopieerd.

    d. Open in het tekstvak **x.509-certificaat** het gedownloade bestand **Certificaat (Base64)** van Azure Portal in Kladblok en kopieer de inhoud ervan en plak deze in het tekstvak **x.509-certificaat** en sla de wijzigingen op.

### <a name="create-pipedrive-test-user"></a>Een testgebruiker voor Pipedrive maken

1. Meld u in een ander browservenster als beheerder aan bij de website van Pipedrive.

1. Blader omlaag naar bedrijf en selecteer **gebruikers beheren**.

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/user1.png)

1. Klik op **Gebruikers toevoegen**.
    
    ![Pipedrive-configuratie](./media/pipedrive-tutorial/user2.png)

1. Voer in de sectie **Gebruikers beheren** de volgende stappen uit:

    ![Pipedrive-configuratie](./media/pipedrive-tutorial/user3.png)

    a. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, bijvoorbeeld `B.Simon@contoso.com`.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in.

    d. Klik op **Gebruikers bevestigen en uitnodigen**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Pipedrive in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Pipedrive waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Pipedrive met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)