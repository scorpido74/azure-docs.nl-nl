---
title: 'Zelf studie: integratie Azure Active Directory met Freedcamp | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67101916"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Zelf studie: Freedcamp integreren met Azure Active Directory

In deze zelf studie leert u hoe u Freedcamp integreert met Azure Active Directory (Azure AD). Wanneer u Freedcamp integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Freedcamp.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Freedcamp met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Freedcamp-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. Freedcamp ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Freedcamp toevoegen uit de galerie

Als u de integratie van Freedcamp in azure AD wilt configureren, moet u Freedcamp uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Freedcamp** in het zoekvak.
1. Selecteer **Freedcamp** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met Freedcamp met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Freedcamp.

Als u Azure AD SSO wilt configureren en testen met Freedcamp, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Freedcamp](#configure-freedcamp)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
5. **[Maak Freedcamp-test gebruiker](#create-freedcamp-test-user)** een tegen hanger van Julia Simon in Freedcamp dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Freedcamp** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    1. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Gebruikers kunnen ook de URL-waarden invoeren met betrekking tot hun eigen klant domein en ze mogen niet noodzakelijkerwijs het patroon `freedcamp.com`zijn, maar ze kunnen een specifieke waarde van een klant domein invoeren, specifiek voor hun toepassings exemplaar. U kunt ook contact opnemen met het [ondersteunings team van Freedcamp-clients](mailto:devops@freedcamp.com) voor meer informatie over URL-patronen.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Freedcamp instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Freedcamp configureren

1. Als u de configuratie wilt automatiseren in Freedcamp, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Freedcamp** gaat u naar de Freedcamp-toepassing. Geef de beheerders referenties op om u aan te melden bij Freedcamp. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Freedcamp hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Freedcamp-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik in de rechter bovenhoek van de pagina op **profiel** en navigeer vervolgens naar **Mijn account**.

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config01.png)

5. Klik aan de linkerkant van de menu balk op **SSO** en voer op de pagina **uw SSO-verbindingen** de volgende stappen uit:

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config02.png)

    a. Typ in het tekstvak **titel** de titel.

    b. Plak in het tekstvak **Entiteits-ID** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak **URL voor aanmelding** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    d. Open het met base64 gecodeerde certificaat in Klad blok, kopieer de inhoud en plak het in het tekstvak **certificaat** .

    e. Klik op **verzenden**.

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

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Freedcamp.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Freedcamp**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-freedcamp-test-user"></a>Freedcamp-test gebruiker maken

Als u Azure AD-gebruikers wilt inschakelen, meldt u zich aan bij Freedcamp, moeten ze worden ingericht in Freedcamp. In Freedcamp is inrichten een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u in een ander browser venster aan bij Freedcamp als een beveiligings beheerder.

2. Klik in de bovenste ToRight hoek van de pagina op **profiel** en navigeer vervolgens naar **systeem beheren**.

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config03.png)

3. Op de rechter kant van de pagina systeem beheren voert u de volgende stappen uit:

    ![Freedcamp-configuratie](./media/freedcamp-tutorial/config04.png)

    a. Klik op **gebruikers toevoegen of uitnodigen**.

    b. Voer in het tekstvak **e-mail** het e-mail adres van de `Brittasimon@contoso.com`gebruiker in.

    c. Klik op **gebruiker toevoegen**.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel Freedcamp in het toegangs venster selecteert, wordt u automatisch aangemeld bij de Freedcamp waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)