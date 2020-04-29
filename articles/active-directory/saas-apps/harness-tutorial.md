---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met harnas | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en harnas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21409eb056743d92db42e0787af24f8cec07db1b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72026956"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met harnas

In deze zelf studie leert u hoe u harnas integreert met Azure Active Directory (Azure AD). Wanneer u harnas integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot harnas.
* Stel uw gebruikers in staat om automatisch te worden aangemeld om te kunnen profiteren van hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Profiteer van een abonnement op eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Harnas ondersteunt SSO die door **SP en IDP** is gestart

## <a name="adding-harness-from-the-gallery"></a>Harnas toevoegen vanuit de galerie

Als u de integratie van harnas in azure AD wilt configureren, moet u een harnas van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **harnas** in het zoekvak.
1. Selecteer de optie **harnas** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Eenmalige aanmelding van Azure AD voor harnas configureren en testen

Azure AD SSO met harnas configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in harnas.

Als u Azure AD SSO wilt configureren en testen met harnas, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Harnas-SSO configureren](#configure-harness-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. Een **[harnas test gebruiker maken](#create-harness-test-user)** : u hebt een soort van B. Simon in harnas dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **harnas** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    Typ in het tekstvak **antwoord-URL** een URL met het volgende patroon:`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://app.harness.io/`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet de echte waarde. U krijgt de daad werkelijke antwoord-URL via de SSO-sectie voor **harnas configureren** , die verderop in de zelf studie wordt beschreven. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de sectie **instellen harnas** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de harnas.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **harnas**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-harness-sso"></a>Harnas-SSO configureren

1. Als u de configuratie binnen de harnas wilt automatiseren, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op de **installatie van harnas** wordt u naar de harnas-toepassing geleid. Geef de beheerders referenties op om u aan te melden bij harnas. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u een harnas hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw harnas bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik in de rechter bovenhoek van de pagina op **continue beveiliging** > **toegangs beheer** > **verificatie-instellingen**.

    ![Harnas configuratie](./media/harness-tutorial/configure01.png)

5. Klik in de sectie **SSO-providers** op **+ SSO-providers** > toevoegen**SAML**.

    ![Harnas configuratie](./media/harness-tutorial/configure03.png)

6. Voer de volgende stappen uit in het pop-upvenster van de **SAML-provider** :

    ![Harnas configuratie](./media/harness-tutorial/configure02.png)

    a. Kopieer de **in uw SSO-provider, Schakel op SAML gebaseerde aanmelding in en voer vervolgens het volgende URL-** exemplaar in en plak het in het tekstvak antwoord-URL in het gedeelte **basis-SAML-configuratie** op Azure Portal.

    b. Typ uw weergave naam in het tekstvak **weergave naam** .

    c. Klik op **bestand kiezen** om het XML-bestand met federatieve meta gegevens te uploaden, dat u hebt gedownload van Azure AD.

    d. Klik op **verzenden**.

### <a name="create-harness-test-user"></a>Harnas test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij een harnas, moeten ze worden ingericht in harnas. In harnas is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan om te profiteren van een beheerder.

1. Klik rechtsboven op de pagina op **continue beveiliging** > **Access Management** > -**gebruikers**.

    ![Harnas configuratie](./media/harness-tutorial/configure04.png)

1. Klik aan de rechter kant van de pagina op **+ gebruiker toevoegen**.

    ![Harnas configuratie](./media/harness-tutorial/configure05.png)

1. Voer in het pop-upvenster **gebruiker toevoegen** de volgende stappen uit:

    ![Harnas configuratie](./media/harness-tutorial/configure06.png)

    a. Voer in het tekstvak **e-mail adres (sen)** het e-mail `B.simon@contoso.com`bericht van de gebruiker in.

    b. Selecteer uw **gebruikers groepen**.

    c. Klik op **verzenden**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel harnas in het toegangs venster klikt, moet u automatisch worden aangemeld bij de harnas waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer te profiteren van Azure AD](https://aad.portal.azure.com/)

