---
title: 'Zelf studie: integratie Azure Active Directory met ExpenseIn | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102845"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Zelf studie: ExpenseIn integreren met Azure Active Directory

In deze zelf studie leert u hoe u ExpenseIn integreert met Azure Active Directory (Azure AD). Wanneer u ExpenseIn integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot ExpenseIn.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij ExpenseIn met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* ExpenseIn-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. ExpenseIn ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn toevoegen uit de galerie

Als u de integratie van ExpenseIn in azure AD wilt configureren, moet u ExpenseIn uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **ExpenseIn** in het zoekvak.
1. Selecteer **ExpenseIn** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met ExpenseIn configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ExpenseIn.

Als u Azure AD SSO wilt configureren en testen met ExpenseIn, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer ExpenseIn](#configure-expensein)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
5. **[Maak een ExpenseIn-test gebruiker](#create-expensein-test-user)** voor het maken van een equivalent van B. Simon in ExpenseIn dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **ExpenseIn** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u de volgende stap uit in de sectie **basis configuratie van SAML** :

    Typ een van de URL in het tekstvak **antwoord-URL** :

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://app.expensein.com/saml`

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , op de knop kopiëren om de URL voor de **federatieve meta gegevens** van de app te kopiëren en klik op **downloaden** om het **certificaat (base64)** te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Op de sectie **ExpenseIn instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>ExpenseIn configureren

1. Als u de configuratie wilt automatiseren in ExpenseIn, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup ExpenseIn** gaat u naar de ExpenseIn-toepassing. Geef de beheerders referenties op om u aan te melden bij ExpenseIn. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u ExpenseIn hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw ExpenseIn-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik boven aan de pagina op **beheerder** en navigeer naar **eenmalige aanmelding** en klik op **provider toevoegen**.

     ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config01.png)

5. Voer de volgende stappen uit in het pop-upvenster **nieuwe ID-provider** :

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config02.png)

    a. Typ in het tekstvak **naam van provider** de naam zoals bijvoorbeeld: Azure.

    b. Selecteer **Ja** als **Intitated-aanmelding voor provider toestaan**.

    c. Plak in het tekstvak **doel-URL** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    d. Plak in het tekstvak **Uitgever** de waarde van de **Azure ad-id**, die u hebt gekopieerd uit Azure Portal.

    e. Open het certificaat (base64) in Klad blok, kopieer de inhoud en plak het in het tekstvak **certificaat** .

    f. Klik op **maken**.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ExpenseIn.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **ExpenseIn**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-expensein-test-user"></a>ExpenseIn-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij ExpenseIn, moeten ze worden ingericht in ExpenseIn. In ExpenseIn is inrichten een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij ExpenseIn als beheerder.

2. Klik boven aan de pagina op **beheerder** , navigeer naar **gebruikers** en klik op **nieuwe gebruiker**.

     ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config03.png)

3. Voer de volgende stappen uit in het pop-upvenster **Details** :

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config04.png)

    a. Voer in het tekstvak **voor naam** de voor naam van de gebruiker in zoals **B**.

    b. Voer in het tekstvak **laatste naam** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Klik op **maken**.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel ExpenseIn in het toegangs venster selecteert, wordt u automatisch aangemeld bij de ExpenseIn waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
