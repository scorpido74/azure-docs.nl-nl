---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met People | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en People.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 93b418a2bc4e1b03370b1bb86bd7da95255db0b8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552387"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met People

In deze zelfstudie leert u hoe u People integreert met Azure AD (Azure Active Directory). Wanneer u People integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie er toegang heeft tot People.
* Inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij People.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een People-abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* People ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* De mobiele People-toepassing kan nu worden geconfigureerd met Azure AD voor het inschakelen van eenmalige aanmelding. In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

>[!NOTE]
>De id van deze toepassing is een vaste tekenreekswaarde zodat maar één exemplaar in één tenant kan worden geconfigureerd.

## <a name="adding-people-from-the-gallery"></a>People toevoegen uit de galerie

Voor het configureren van de integratie van People met Azure Active Directory moet u People uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak **People**.
1. Selecteer **People** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>Eenmalige aanmelding van Azure AD configureren en testen voor People

Configureer en test eenmalige aanmelding van Azure AD met People met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in People.

Voltooi de volgende stappen om eenmalige aanmelding bij People met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Eenmalige aanmelding bij People configureren](#configure-people-sso)** : om de instellingen voor eenmalige aanmelding aan de toepassingszijde te configureren.
    1. **[Een People-testgebruiker maken](#create-people-test-user)** : als u een equivalent van B.Simon in People wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **People** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard-SAML-configuratie** voert u de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<company name>.peoplehr.net`

    b. Typ een URL in het vak **Id**: `https://www.peoplehr.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [Klantondersteuningsteam van People](mailto:customerservices@peoplehr.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **People instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot People.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **People** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-people-sso"></a>Eenmalige aanmelding van People configureren

1. Als u de configuratie in People wilt automatiseren, moet u de **browserextensie Mijn apps voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![De extensie Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **People instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar de People-toepassing. Geef hier de beheerdersreferenties op om u aan te melden bij People. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u People handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de People-bedrijfssite. Voer hierna de volgende stappen uit:
   
4. Klik in het menu aan de linkerkant op **Instellingen**.

    ![Eenmalige aanmelding configureren](./media/people-tutorial/tutorial_people_001.png)

5. Klik op **Company** (Bedrijf).

    ![Eenmalige aanmelding configureren](./media/people-tutorial/tutorial_people_002.png)

6. Klik in het **Bestand met metagegevens voor eenmalige aanmelding van SAML** op **Bladeren** om het gedownloade bestand met metagegevens te uploaden.

    ![Eenmalige aanmelding configureren](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>People-testgebruiker maken

In deze sectie maakt u in People een gebruiker met de naam B. Simon. Werk samen met het [ondersteuningsteam van People](mailto:customerservices@peoplehr.com) om de gebruikers toe te voegen in het People-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel People in het toegangsvenster klikt, wordt u automatisch aangemeld bij de People-instantie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-people-mobile"></a>Eenmalige aanmelding testen voor People (mobiel)

1. Open de mobiele People-toepassing. Voer op de aanmeldingspagina de **e-mail-id** in en klik vervolgens op **Eenmalige aanmelding**.

    ![Het aanmelden](./media/people-tutorial/test01.png)

2. Voer het **UserID van de organisatie** in en klik op **Volgende**.

    ![Het e-mailadres](./media/people-tutorial/test02.png)

3. Als het aanmelden is gelukt, wordt de startpagina van de toepassing zoals hieronder weergegeven:

    ![Eenmalig](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer People met Azure AD](https://aad.portal.azure.com)
