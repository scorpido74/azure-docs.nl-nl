---
title: 'Zelfstudie: Azure Active Directory-integratie met ExpenseIn | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102845"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Zelfstudie: ExpenseIn integreren met Azure Active Directory

In deze zelfstudie leert u hoe u ExpenseIn integreert met Azure Active Directory (Azure AD). Wanneer u ExpenseIn integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot ExpenseIn.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ExpenseIn met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* KostenIn single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. ExpenseIn ondersteunt **SP en IDP** geïnitieerd SSO.

## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn toevoegen vanuit de galerie

Als u de integratie van ExpenseIn in Azure AD wilt configureren, moet u ExpenseIn vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ExpenseIn** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **ExpenseIn** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met ExpenseIn met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ExpenseIn.

Als u Azure AD SSO wilt configureren en testen met ExpenseIn, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[ExpenseIn configureren](#configure-expensein)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[ExpenseIn testgebruiker maken](#create-expensein-test-user)** om een tegenhanger van B.Simon in ExpenseIn te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ExpenseIn-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stap uit als u de toepassing in de **idp-modus** wilt configureren:

    Typ in het tekstvak **URL beantwoorden** een van de URL's:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.expensein.com/saml`

1. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op de knop Kopiëren om **de url van de appfederatie-metagegevens** te kopiëren en klik op **Downloaden** om het **certificaat (Base64)** te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Kopieer in de sectie **ExpenseIn instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Expensein configureren

1. Als u de configuratie binnen ExpenseIn wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Setup ExpenseIn** zal u doorverwijzen naar de ExpenseIn applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij ExpenseIn. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u ExpenseIn handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich aan bij uw ExpenseIn-bedrijfssite als beheerder en voert u de volgende stappen uit:

4. Klik boven aan de pagina op **Beheerder** en navigeer naar **Eén aanmelding** en klik op **Provider toevoegen**.

     ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config01.png)

5. Voer de volgende stappen uit in de pop-up Van de **nieuwe identiteitsprovider:**

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config02.png)

    a. Typ in het tekstvak **Providernaam** de naam als ex:Azure.

    b. Selecteer **Ja** als Provider toestaan **Aangemeld .**

    c. Plak in het tekstvak **Doelurl** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    d. Plak in het tekstvak **Uitgever** de waarde van **Azure AD-id**, die u hebt gekopieerd van Azure-portal.

    e. Open het certificaat (Base64) in kladblok, kopieer de inhoud ervan en plak het in het **tekstvak Certificaat.**

    f. Klik **op Maken**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ExpenseIn.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **ExpenseIn**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-expensein-test-user"></a>ExpenseIn-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij ExpenseIn, moeten ze worden ingericht in ExpenseIn. In ExpenseIn is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij ExpenseIn als beheerder.

2. Klik boven aan de pagina op **Beheerder** en navigeer vervolgens naar **Gebruikers** en klik op **Nieuwe gebruiker**.

     ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config03.png)

3. Voer in de pop-up **Details** de volgende stappen uit:

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config04.png)

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **B.**

    b. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Klik **op Maken**.

### <a name="test-sso"></a>Test SSO

Wanneer u de tegel ExpenseIn selecteert in het Toegangspaneel, moet u automatisch worden aangemeld bij de ExpenseIn waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
