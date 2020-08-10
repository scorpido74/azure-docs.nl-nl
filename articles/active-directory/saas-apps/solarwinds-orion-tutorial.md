---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SolarWinds Orion | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SolarWinds Orion.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 34d50638-6783-4def-93fc-be0d11fbc320
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86eae9d44607d2e997625d91924483c86766f01b
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449400"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met SolarWinds Orion

In deze zelfstudie leert u hoe u SolarWinds Orion integreert met Azure Active Directory (Azure AD). Wanneer u SolarWinds Orion integreert met Azure AD, kunt u:

* Bepalen in Azure AD wie toegang heeft tot SolarWinds Orion.
* Ervoor zorgen dat uw gebruikers automatisch met hun Azure AD-account worden aangemeld bij SolarWinds Orion.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een SolarWinds Orion-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SolarWinds Orion ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* Zodra u SolarWinds Orion hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-solarwinds-orion-from-the-gallery"></a>SolarWinds Orion toevoegen uit de galerie

Om de integratie van SolarWinds Orion te configureren in Azure AD, moet u SolarWinds Orion vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **SolarWinds Orion**.
1. Selecteer **SolarWinds Orion** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Eenmalige aanmelding van Azure AD voor SolarWinds Orion configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met SolarWinds Orion met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SolarWinds Orion.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met SolarWinds Orion te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met SolarWinds Orion configureren](#configure-solarwinds-orion-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een SolarWinds Orion-testgebruiker maken](#create-solarwinds-orion-test-user)** : als u een equivalent van B.Simon in SolarWinds Orion wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de integratiepagina van de toepassing **SolarWinds Orion**, ga naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van SolarWinds Orion](mailto:technicalsupport@solarwinds.com) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In de SolarWinds Orion-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht de SolarWinds Orion-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email |user.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **SolarWinds Orion instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SolarWinds Orion.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **SolarWinds Orion** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-solarwinds-orion-sso"></a>Eenmalige aanmelding met SolarWinds Orion configureren

1. Meld u aan bij SolarWinds Orion en ga naar **Settings** -> **All Settings**.

    ![ SolarWinds Orion configureren ](./media/solarwinds-orion-tutorial/settings.png)

1. Selecteer **SAML Configuration** in de sectie **USER ACCOUNTS**.

    ![ SolarWinds Orion configureren ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Klik op **ADD IDENTITY PROVIDER**.

    ![ SolarWinds Orion configureren ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Voer de volgende stappen uit op de pagina **Add Identity Provider**:

    ![ SolarWinds Orion configureren ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Ga naar het tabblad **Configure**.

    b. Geef in het tekstvak **Identity Provider Name** een geldige naam voor de id-provider op, zoals `My SSO service`.

    c. Plak in het tekstvak **SSO Target URL** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d.  Plak in het tekstvak **Issuer URL** de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    e. Open in de Azure-portal het gedownloade **Base64-certificaat** in Kladblok en plak de inhoud in het tekstvak **X.509-certificaat**.

    f. Klik op **Opslaan**.

### <a name="create-solarwinds-orion-test-user"></a>Een SolarWinds Orion-testgebruiker maken

1. Meld u aan bij de website van SolarWinds Orion en ga naar **Settings** -> **All Settings**.

    ![ SolarWinds Orion-testgebruiker maken ](./media/solarwinds-orion-tutorial/settings.png)

1. Selecteer **Manage Accounts** in de sectie **USER ACCOUNTS**.

    ![ SolarWinds Orion-testgebruiker maken ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Klik op het tabblad **INDIVIDUAL ACCOUNTS** op **ADD NEW ACCOUNT**.

    ![ SolarWinds Orion-testgebruiker maken ](./media/solarwinds-orion-tutorial/create-user.png)

1. Selecteer het type account dat u nodig hebt om afzonderlijke SAML-gebruikers of SAML-gebruikersgroepen te maken.

    ![ SolarWinds Orion-testgebruiker maken ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Voer in het tekstvak **NAME ID** de gebruikers- of groepsnaam in. Deze moet exact overeenkomen met de gebruikers- of groepsnaam in Azure AD.

1.  Klik op **Next** (Volgende) en verzend de pagina.

    ![ SolarWinds Orion-testgebruiker maken ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel SolarWinds Orion klikt, wordt u automatisch aangemeld bij de instantie van SolarWinds Orion waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SolarWinds Orion met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SolarWinds Orion beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

