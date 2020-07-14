---
title: 'Zelfstudie: Azure Active Directory-integratie met ExpenseIn | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en ExpenseIn.
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
ms.openlocfilehash: bf7e3659aa3044a4453574a2cd171303d00abc19
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85606678"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Zelfstudie: ExpenseIn integreren met Azure Active Directory

In deze zelfstudie leert u hoe u ExpenseIn kunt integreren met Azure Active Directory (Azure AD). Wanneer u ExpenseIn integreert met Azure AD, kunt u het volgende doen:

* In Azure AD bepalen wie toegang heeft tot ExpenseIn.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij ExpenseIn.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement waarvoor eenmalige aanmelding (SSO) van ExpenseIn is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen. ExpenseIn ondersteunt door **SP en IDD** geïnitieerde eenmalige aanmelding.

## <a name="adding-expensein-from-the-gallery"></a>ExpenseIn toevoegen vanuit de galerie

Als u de integratie van ExpenseIn met Azure AD wilt configureren, moet u ExpenseIn vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** **ExpenseIn** in het zoekvak.
1. Selecteer **ExpenseIn** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test eenmalige aanmelding van Azure AD met ExpenseIn met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ExpenseIn.

Voltooi de volgende stappen om eenmalige aanmelding bij ExpenseIn met Azure AD te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer ExpenseIn](#configure-expensein)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding van Azure AD te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Een testgebruiker voor ExpenseIn maken](#create-expensein-test-user)** : als u een tegenhanger van B.Simon in ExpenseIn wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **ExpenseIn** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Antwoord-URL** een van de volgende URL's:

    ```https
    https://app.expensein.com/samlcallback
    https://mobileapi.expensein.com/identity/samlcallback
    ```

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL: `https://app.expensein.com/saml`

1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u in de sectie **SAML-handtekeningcertificaat** op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren. Klik vervolgens op **Downloaden** om het **Certificaat (Base64)** te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. In de sectie **ExpenseIn instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>ExpenseIn configureren

1. Als u de configuratie in ExpenseIn wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbreiding van Mijn apps](common/install-myappssecure-extension.png)

2. Als u op **ExpenseIn instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de ExpenseIn-toepassing. Geef hier de Administrator-referenties op om u aan te melden bij ExpenseIn. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3 t/m 5 geautomatiseerd.

    ![Instelling configureren](common/setup-sso.png)

3. Als u ExpenseIn handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij de ExpenseIn-bedrijfssite. Voer daarna de volgende stappen uit:

4. Klik op **Beheer** bovenaan de pagina, navigeer naar **Eenmalige aanmelding** en klik op **Provider toevoegen**.

     ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config01.png)

5. Voer de volgende stappen uit in het pop-upvenster **Nieuwe identiteitsprovider**:

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config02.png)

    a. Typ in het tekstvak **Providernaam** de naam, zoals ex:Azure.

    b. Selecteer **Ja** voor **Door provider geïnitieerde aanmelding toestaan**.

    c. Plak in het tekstvak **Doel-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    d. Plak in het tekstvak **Verlener** de waarde van **Azure AD-id**, die u hebt gekopieerd uit Azure Portal.

    e. Open het certificaat (Base64) in Notepad, kopieer de inhoud ervan en plak deze in het tekstvak **Certificaat**.

    f. Klik op **Create**.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot ExpenseIn.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **ExpenseIn** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-expensein-test-user"></a>Testgebruiker voor ExpenseIn maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij ExpenseIn, moeten ze worden ingericht in ExpenseIn. In het geval van ExpenseIn is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij ExpenseIn.

2. Klik op **Beheer** bovenaan de pagina, navigeer naar **Gebruikers** en klik op **Nieuwe gebruiker**.

     ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config03.png)

3. Voer in het pop-upvenster **Details** de volgende stappen uit:

    ![ExpenseIn-configuratie](./media/expenseIn-tutorial/config04.png)

    a. Typ in het tekstvak **First Name** de voornaam van de gebruiker, zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Voer in het tekstvak **E-mail** het e-mailadres van de gebruiker in, zoals `B.Simon@contoso.com`.

    d. Klik op **Create**.

### <a name="test-sso"></a>Eenmalige aanmelding testen

Wanneer u de tegel ExpenseIn selecteert in het toegangsvenster, zou u automatisch moeten worden aangemeld bij de instantie van ExpenseIn waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
