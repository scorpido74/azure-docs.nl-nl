---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met monday.com | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en monday.com configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 2128559d8074281c1019d99e1316da34c7ec6797
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met monday.com

In deze zelfstudie leert u hoe u monday.com kunt integreren met Azure Active Directory (Azure AD). Wanneer u monday.com integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie er toegang heeft tot monday.com.
* Uw gebruikers zich automatisch laten aanmelden met hun Azure AD-account bij monday.com.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op monday.com waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* monday.com ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* monday.com biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-mondaycom-from-the-gallery"></a>Monday.com toevoegen vanuit de galerie

Voor het configureren van de integratie van monday.com met Azure AD moet u monday.com vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **monday.com** in het zoekvak.
1. Selecteer **monday.com** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mondaycom"></a>Eenmalige aanmelding van Azure AD configureren en testen voor monday.com

Configureer en test eenmalige aanmelding van Azure AD met monday.com met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in monday.com.

Voltooi de volgende stappen om eenmalige aanmelding via Azure AD bij monday.com te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding bij monday.com configureren](#configure-mondaycom-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Een monday.com-testgebruiker maken](#create-mondaycom-test-user)** : als u een equivalent van B.Simon in monday.com wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **monday.com** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u een **Service Provider-metagegevensbestand** hebt en in de door **IDP** geïnitieerde modus wilt configureren:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **antwoord-URL** automatisch ingevuld in de sectie Standaard SAML-configuratie.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Als de waarden voor **Id** en **Antwoord-URL** niet automatisch worden ingevuld, vult u de waarden zelf in. De waarde voor **Id** en **Antwoord-URL** is hetzelfde en de waarde heeft het volgende patroon: `https://<your-domain>.monday.com/saml/saml_callback`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![image](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteuningsteam van monday.com](https://monday.com/contact-us/) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. In monday.com worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Bovendien verwacht monday.com nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze worden hieronder weergegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze herzien volgens uw vereisten.

    | Naam | Bronkenmerk |
    |--|--|
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **monday.com instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot monday.com.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **monday.com** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-mondaycom-sso"></a>Eenmalige aanmelding configureren voor monday.com

1. Als u de configuratie in monday.com wilt automatiseren, moet u de **browserextensie My Apps Secure Sign-in** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

1. Als u op **monday.com instellen** klikt nadat u de extensie hebt toegevoegd aan de browser, wordt u doorgestuurd naar monday.com. Geef hier de beheerdersreferenties op om u aan te melden bij monday.com. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 6 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

1. Als u monday.com handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de bedrijfssite van monday.com. Voer hierna de volgende stappen uit:

1. Ga in de rechterbovenhoek van de pagina naar **Profile** en klik op **Admin**.

    ![Configuratie van monday.com](./media/mondaycom-tutorial/configuration01.png)

1. Selecteer **Security** en klik op **Open** naast SAML.

    ![Configuratie van monday.com](./media/mondaycom-tutorial/configuration02.png)

1. Vul hieronder de gegevens van uw IDP in.

    ![Configuratie van monday.com](./media/mondaycom-tutorial/configuration03.png)

    > [!NOTE]
    > Raadpleeg [dit artikel](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) voor meer informatie.

### <a name="create-mondaycom-test-user"></a>Testgebruiker maken voor monday.com

In deze sectie maakt u een gebruiker met de naam B.Simon in monday.com. monday.com ondersteunt Just-In-Time-inrichting. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in monday.com bestaat, wordt er een nieuwe gemaakt wanneer u monday.com probeert te openen.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel monday.com klikt, wordt u als het goed is automatisch aangemeld bij de instantie van monday.com waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [monday.com uitproberen met Azure AD](https://aad.portal.azure.com/)
