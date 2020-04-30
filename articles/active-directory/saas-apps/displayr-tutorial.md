---
title: 'Zelf studie: integratie met de afspeelset Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de deplayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67103991"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Zelf studie: de uitsluiter integreren met Azure Active Directory

In deze zelf studie leert u hoe u de deplayr kunt integreren met Azure Active Directory (Azure AD). Wanneer u de deplayr integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de deplayr.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij de deplayr met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. De deplayr ondersteunt door **SP** geïnitieerde SSO.

## <a name="adding-displayr-from-the-gallery"></a>De deplayer toevoegen vanuit de galerie

Als u de integratie van de deplayr wilt configureren in azure AD, moet u de weer gave van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **deplayr** in het zoekvak.
1. Selecteer de optie **deplayr** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met de deplayr configureren en testen met behulp van een test gebruiker met de naam **Julia Simon**. Voor eenmalige aanmelding moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de afspeel omgeving.

Als u Azure AD SSO wilt configureren en testen met de functie voor afspelen, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Hiermee configureert u de unplayr](#configure-displayr)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
5. **[Maak de test gebruiker](#create-displayr-test-user)** voor het deactiveren van een exemplaar van Julia Simon die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina voor de integratie van **de toepassing de** sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stap uit:

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<YOURDOMAIN>.displayr.com`

    b. Typ in het tekstvak **id (Entiteits-ID)** een URL met het volgende patroon:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team voor client ondersteuning](mailto:support@displayr.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie Standaard SAML-configuratie in de Azure-portal.

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar het gedeelte SAML **-** **handtekening certificaat** en selecteer vervolgens **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. De toepassing voor het deplayprogramma verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Behalve hierboven verwachtte de toepassing voor het deplayprogramma nog enkele kenmerken die in de SAML-respons worden door gegeven. Voer de volgende stappen uit in de sectie **gebruikers kenmerken & claims** van het dialoog venster **groeps claims (preview)** :

    a. Klik op de **pen** naast **groepen die zijn geretourneerd in claim**.

    ![installatiekopie](./media/displayr-tutorial/config04.png)

    ![installatiekopie](./media/displayr-tutorial/config05.png)

    b. Selecteer **alle groepen** in de lijst met keuze rondjes.

    c. Selecteer **bron kenmerk** van **groep-ID**.

    d. Controleer **de naam van de groepclaim aanpassen**.

    e. Controleer **Verzend groepen als rol claims**.

    f. Klik op **Opslaan**.

1. Kopieer de juiste URL ('s) op basis van uw vereiste op de sectie **installatie-uitstel** .

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>De deplayr configureren

1. Als u de configuratie wilt automatiseren in de deplayr, moet u de **extensie mijn apps beveiligde aanmeldings browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op het **installatie programma van Setup** om u naar de toepassing voor het deplayprogramma te leiden. Geef de beheerders referenties op om u aan te melden bij de deplayr. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u de functie voor het uitvoeren van de installatie hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij de bedrijfs site van uw bedrijf als beheerder en voert u de volgende stappen uit:

4. Klik op **instellingen** en navigeer vervolgens naar **account**.

    ![Configuratie](./media/displayr-tutorial/config01.png)

5. Ga naar **instellingen** in het bovenste menu en schuif naar beneden op de pagina om op **eenmalige aanmelding configureren (SAML)** te klikken.

    ![Configuratie](./media/displayr-tutorial/config02.png)

6. Voer de volgende stappen uit op de pagina voor **eenmalige aanmelding (SAML)** :

    ![Configuratie](./media/displayr-tutorial/config03.png)

    a. Schakel het selectie vakje **eenmalige aanmelding inschakelen (SAML)** in.

    b. Kopieer de waarde werkelijk **identifier** uit de sectie **basis configuratie van SAML** van Azure AD en plak deze in het tekstvak voor de **Uitgever** .

    c. Plak in het tekstvak **URL voor aanmelding** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    d. Plak in het tekstvak **Afmeldings-URL** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    e. Open het certificaat (base64) in Klad blok, kopieer de inhoud en plak het in het tekstvak **certificaat** .

    f. **Groeps toewijzingen** zijn optioneel.

    g. Klik op **Opslaan**.  

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de deplayr.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **deplayr**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-displayr-test-user"></a>Test gebruiker voor deplayer maken

Als u Azure AD-gebruikers wilt inschakelen, meldt u zich aan bij de afspeel functie en moet deze worden ingericht in de afspeel functie. In de deplayr is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij de deplayr.

2. Klik op **instellingen** en navigeer vervolgens naar **account**.

    ![Configuratie van de deplayr](./media/displayr-tutorial/config01.png)

3. Schakel over naar de **instellingen** in het bovenste menu en schuif omlaag in de pagina totdat de sectie **gebruikers** op **nieuwe gebruiker**klikken.

    ![Configuratie van de deplayr](./media/displayr-tutorial/config07.png)

4. Voer de volgende stappen uit op de pagina **nieuwe gebruiker** :

    ![Configuratie van de deplayr](./media/displayr-tutorial/config06.png)

    a. Voer in het tekstvak **naam** de naam van de gebruiker in, zoals **Brittasimon**.

    b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `Brittasimon@contoso.com`.

    c. Selecteer het juiste **groepslid maatschap**.

    d. Klik op **Opslaan**.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel deplayr selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de replayr waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
