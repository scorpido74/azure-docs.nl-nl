---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Hightail | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Hightail.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.openlocfilehash: 3b3f279b28bf78114556623865a9f132174c6e41
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hightail"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Hightail

In deze zelfstudie leert u hoe u Hightail integreert met Azure Active Directory (Azure AD). Wanneer u Hightail integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot Hightail.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Hightail.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement met eenmalige aanmelding ingeschakeld voor Hightail.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Hightail biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* Hightail biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-hightail-from-the-gallery"></a>Hightail toevoegen vanuit de galerie

Om de integratie van Hightail in Azure AD te configureren, moet u Hightail vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **Hightail** in het zoekvak.
1. Selecteer **Hightail** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hightail"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Hightail

Configureer en test eenmalige aanmelding van Azure AD met Hightail met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Hightail.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met Hightail te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Hightail configureren](#configure-hightail-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een Hightail-testgebruiker maken](#create-hightail-test-user)** : als u een equivalent van B.Simon in Hightail wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Hightail** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **Antwoord-URL**: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://www.hightail.com/loginSSO`

1. De Hightail-toepassing verwacht dat de SAML-asserties een specifieke indeling hebben. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien worden in de Hightail-toepassing nog enkele kenmerken verwacht die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk|
    | -------- |-------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | UserIdentity | user.mail |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op de computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **Hightail instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Voordat u eenmalige aanmelding voor de Hightail-app configureert, moet u uw e-maildomein laten goedkeuren door het Hightail-team, zodat alle gebruikers die dit domein gebruiken, de functionaliteit voor eenmalige aanmelding kunnen gebruiken.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Hightail.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Hightail** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-hightail-sso"></a>Eenmalige aanmelding voor Hightail configureren

1. Als u de configuratie in Hightail wilt automatiseren, moet u de **browserextensie voor veilig aanmelden bij mijn apps** installeren door te klikken op **De extensie installeren**.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **Hightail instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de Hightail-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Hightail. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u Hightail handmatig wilt installeren, opent u in een ander browservenster het beheerdersportal van **Hightail**.

1. Klik op het pictogram **Gebruiker** in de rechterbovenhoek van de pagina. 

    ![Eenmalige aanmelding configureren](./media/hightail-tutorial/configure1.png)

1. Klik op het tabblad **Beheerdersconsole weergeven**.

    ![Eenmalige aanmelding configureren](./media/hightail-tutorial/configure2.png)

1. Klik in het menu aan de bovenkant op het tabblad **SAML** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/hightail-tutorial/configure3.png)

    a. Plak in het tekstvak **Aanmeldings-URL** de waarde van de uit de Azure Portal gekopieerde **Aanmeldings-URL**.

    b. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **SAML-certificaat**.

    c. Klik op **KOPIËREN** om de SAML consumer-URL voor uw exemplaar te kopiëren en plak deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** in de Azure-portal.

    d. Klik op **Configuraties opslaan**.

### <a name="create-hightail-test-user"></a>Hightail-testgebruiker maken

In dit gedeelte maakt u in Hightail een gebruiker met de naam Britta Simon. Hightail biedt ondersteuning voor Just-In-Time-inrichting van gebruikers, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Hightail bestaat, wordt er een nieuwe gemaakt nadat deze is geverifieerd.

> [!NOTE]
> Als u handmatig een gebruiker moet maken, moet u contact opnemen met het [ondersteuningsteam van Hightail](mailto:support@hightail.com).

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Hightail klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Hightail in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Hightail met Azure AD](https://aad.portal.azure.com/)