---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Foodee | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Foodee.
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
ms.openlocfilehash: aaa645e7399b4c53aec8b042573882c6a5da4104
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92451925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-foodee"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Foodee

In deze zelfstudie leert u hoe u Foodee integreert met Azure AD (Azure Active Directory). Wanneer u Foodee integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot Foodee.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Foodee.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](../manage-apps/what-is-single-sign-on.md) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Abonnement op Foodee waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Foodee ondersteunt met **SP en IDP** geïnitieerde eenmalige aanmelding
* Foodee ondersteunt het **Just-In-Time** inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-foodee-from-the-gallery"></a>Foodee toevoegen vanuit de galerie

Voor het configureren van de integratie van Foodee in Azure AD moet u Foodee vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory** .
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen** .
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **Foodee** in het zoekvak.
1. Selecteer **Foodee** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-foodee"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Foodee

Configureer en test eenmalige aanmelding van Azure AD met Foodee met behulp van een testgebruiker met de naam **B. Simon** . Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Foodee.

Voltooi de volgende stappen om eenmalige aanmelding voor Foodee met Azure AD te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij Foodee configureren](#configure-foodee-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een Foodee-testgebruiker maken](#create-foodee-test-user)** : als u een tegenhanger van B. Simon in Foodee wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/), op de integratiepagina van de toepassing **Foodee** , de sectie **Beheren** en selecteer **Eenmalige aanmelding** .
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding** .
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://concierge.food.ee/sso/saml/<INSTANCENAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem voor deze waarden contact op met het [klantenondersteuningsteam van Foodee](mailto:dev@food.ee). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **Foodee instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory** , selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers** .
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord** .
   1. Klik op **Create** .

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B. Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Foodee.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen** .
1. Selecteer **Foodee** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen** .

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen** .

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen** .

### <a name="configure-foodee-sso"></a>Eenmalige aanmelding voor Foodee configureren

1. Als u de configuratie in Foodee wilt automatiseren, moet u de **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Foodee instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Foodee-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij Foodee. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 en 4 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u Foodee handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de Foodee-bedrijfssite. Voer hierna de volgende stappen uit:

4. Klik op het **profiellogo** in de rechterbovenhoek van de pagina, ga naar **Single Sign On** en voer de volgende stappen uit:

   ![Configuratie van Foodee](./media/foodee-tutorial/config01.png)

   1. Typ de naam in het tekstvak **IDP NAME** , bijvoorbeeld ex:Azure.
   1. Open het XML-bestand met federatieve metagegevens in Kladblok, kopieer de inhoud en plak deze in het tekstvak **IDP METADATA XML** .
   1. Klik op **Opslaan** .

### <a name="create-foodee-test-user"></a>Testgebruiker voor Foodee maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Foodee. Foodee ondersteunt Just-In-Time-inrichting; deze optie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Foodee bestaat, wordt er een nieuwe gemaakt wanneer u Foodee opent.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Foodee-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Foodee waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](./tutorial-list.md) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)

- [Foodee met Azure AD uitproberen](https://aad.portal.azure.com/)