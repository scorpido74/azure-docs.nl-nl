---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met PureCloud van Genesys | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PureCloud by Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 0d2953710c559deb094761410e6d46401e103d8b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553272"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met PureCloud van Genesys

In deze zelfstudie leert u hoe u PureCloud van Genesys kunt integreren met Azure Active Directory (Azure AD). Nadat u dit hebt gedaan, kunt u het volgende doen:

* Azure AD gebruiken om te bepalen welke gebruikers toegang hebben tot PureCloud van Genesys.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij PureCloud van Genesys.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u nog geen account hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen.
* Een abonnement op PureCloud van Genesys waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* PureCloud van Genesys biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding.

> [!NOTE]
> Omdat de id van deze toepassing een vaste tekenreekswaarde is, kan er maar één exemplaar in één tenant worden geconfigureerd.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud by Genesys toevoegen vanuit de galerie

Om de integratie van PureCloud van Genesys in Azure AD te configureren, moet u PureCloud van Genesys vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps. Voer de volgende stappen uit om dit te doen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **PureCloud van Genesys** in het zoekvak.
1. Selecteer **PureCloud van Genesys** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Eenmalige aanmelding van Azure AD configureren en testen voor PureCloud van Genesys

Configureer en test eenmalige aanmelding van Azure AD met PureCloud van Genesys met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PureCloud van Genesys.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met PureCloud van Genesys te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding van PureCloud van Genesys configureren](#configure-purecloud-by-genesys-sso)** : de instellingen voor eenmalige aanmelding aan de toepassingszijde configureren.
    1. **[Testgebruiker voor PureCloud van Genesys maken](#create-purecloud-by-genesys-test-user)** : als u een tegenhanger van B.Simon in PureCloud van Genesys wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal:

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **PureCloud van Genesys** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het penpictogram voor **Standaard SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Voer in het vak **Id** een URL in die overeenkomt met uw regio:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Voer in het vak **Antwoord-URL** een URL in die overeenkomt met uw regio:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Voer in het vak **Aanmeldings-URL** een URL in die overeenkomt met uw regio:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. In de PureCloud by Genesys-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. Op de volgende schermopname wordt de lijst met standaardkenmerken weergegeven:

    ![image](common/default-attributes.png)

1. Bovendien verwacht de PureCloud van Genesys-toepassing nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd, zoals weergegeven in de volgende tabel. Deze kenmerken worden ook vooraf ingevuld, maar u kunt deze indien nodig herzien.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in het gedeelte **PureCloud van Genesys instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal:

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het vak **Gebruikersnaam** de gebruikersnaam in de volgende indeling in: username@companydomain.extension. Bijvoorbeeld: `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie gaat u B.Simon toestemming geven voor gebruik van eenmalige aanmelding van Azure door haar toegang te geven tot PureCloud van Genesys.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **PureCloud by Genesys** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in de lijst met gebruikers in het dialoogvenster **Gebruikers en groepen** de naam **B.Simon** in de lijst Gebruikers en kies **Selecteren** onder aan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Kies vervolgens de knop **Selecteren** onder aan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen**.

## <a name="configure-purecloud-by-genesys-sso"></a>Eenmalige aanmelding van PureCloud van Genesys configureren

1. Meld u in een ander browservenster als beheerder aan bij PureCloud van Genesys.

1. Selecteer **Admin** in de rechterbovenhoek en ga vervolgens naar **Single Sign-on** onder **Integrations**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Ga naar het tabblad **ADFS/Azure AD(Premium)** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Klik op **Browse** om het met base-64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal te uploaden naar het **ADFS-certificaat**.

    b. Plak in het tekstvak **ADFS Issuer URI** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het vak **Target URI** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Om de waarde voor **Relying Party Identifier** te verkrijgen, gaat u naar de Azure-portal. Selecteer op de integratiepagina voor de **PureCloud van Genesys**-toepassing het tabblad **Eigenschappen** en kopieer de waarde van **Toepassings-id**. Plak deze in het vak **Relying Party Identifier**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Selecteer **Opslaan**.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud by Genesys-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich aanmelden bij PureCloud van Genesys, moeten ze worden ingericht in PureCloud van Genesys. In het geval van PureCloud by Genesys is dat een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Meld u als beheerder aan bij PureCloud van Genesys.

1. Selecteer bovenaan **Admin** en ga naar **People** onder **People & Permissions**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Selecteer op de pagina **People** de optie **Add Person**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Voer in het dialoogvenster **Add People to the Organization** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Voer in het vak **Full Name** de naam van een gebruiker in. Bijvoorbeeld: **B.simon**.

    b. Voer in het vak **Email** het e-mailadres van de gebruiker in. Bijvoorbeeld: **b.simon\@contoso.com**.

    c. Selecteer **Maken**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel **PureCloud van Genesys** klikt, wordt u automatisch aangemeld bij het account van PureCloud van Genesys waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [PureCloud van Genesys gebruiken met Azure AD](https://aad.portal.azure.com/)
