---
title: 'Zelfstudie: Azure Active Directory-integratie met Displayr | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Displayr.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103991"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Zelfstudie: Displayr integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Displayr integreert met Azure Active Directory (Azure AD). Wanneer u Displayr integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Displayr.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Displayr met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Displayr single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Displayr ondersteunt **SP** geïnitieerde SSO.

## <a name="adding-displayr-from-the-gallery"></a>Displayr toevoegen vanuit de galerie

Als u de integratie van Displayr in Azure AD wilt configureren, moet u Displayr vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Displayr** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **Displayr** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Displayr met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Displayr.

Als u Azure AD SSO wilt configureren en testen met Displayr, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Displayr](#configure-displayr)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak Displayr-testgebruiker](#create-displayr-test-user)** om een tegenhanger van Britta Simon in Displayr te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Displayr-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Vooreenmalig aanmelden instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stap uit:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<YOURDOMAIN>.displayr.com`

    b. Typ in het tekstvak **Id (Entity ID)** een URL met het volgende patroon:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Displayr Client](mailto:support@displayr.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie Standaard SAML-configuratie in de Azure-portal.

1. Zoek op de pagina **Single Sign-On with SAML** instellen met SAML in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Displayr-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Klik op het pictogram **Bewerken** om het dialoogvenster gebruikerskenmerken te openen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Displayr applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie. Voer in de sectie **Gebruikerskenmerken & claims** in het dialoogvenster **Groepsclaims (Preview)** de volgende stappen uit:

    a. Klik op de **pen** naast **Groepen die zijn geretourneerd in claim**.

    ![installatiekopie](./media/displayr-tutorial/config04.png)

    ![installatiekopie](./media/displayr-tutorial/config05.png)

    b. Selecteer **Alle groepen** in de radiolijst.

    c. Selecteer **Bronkenmerk** van **groeps-id**.

    d. Schakel **De naam van de groepsclaim aanpassen**in .

    e. Controleer **Groepen uitzenden als rolclaims**.

    f. Klik op **Opslaan**.

1. Kopieer in de sectie **Displayr instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Displayr configureren

1. Als u de configuratie in Displayr wilt automatiseren, moet u de **beveiligingsextensie Mijn apps secure sign-in** installeren door op **De extensie installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Displayr** en verwijst u naar de Displayr-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Displayr. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Displayr handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan bij uw Displayr-bedrijfssite en voert u de volgende stappen uit:

4. Klik op **Instellingen** en navigeer naar **Account**.

    ![Configuratie](./media/displayr-tutorial/config01.png)

5. Ga in het bovenste menu naar **Instellingen** en blader naar beneden om op **Enkelteken configureren (SAML) te**klikken.

    ![Configuratie](./media/displayr-tutorial/config02.png)

6. Voer op de pagina **Enkel aanmelding (SAML)** de volgende stappen uit:

    ![Configuratie](./media/displayr-tutorial/config03.png)

    a. Schakel het selectievakje **Eenmalig aanmelden inschakelen (SAML) in.**

    b. Kopieer de werkelijke **id-waarde** uit de sectie **BasisSAML-configuratie** van Azure AD en plak deze in het tekstvak **Van de uitgever.**

    c. Plak in het tekstvak **Inlog-URL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.

    d. Plak in het tekstvak **Van de URL van afmelden** de waarde van de URL van **Afmelden**, die u hebt gekopieerd van azure-portal.

    e. Open het certificaat (Base64) in Kladblok, kopieer de inhoud ervan en plak het in het **tekstvak Certificaat.**

    f. **Groepstoewijzingen** zijn optioneel.

    g. Klik op **Opslaan**.  

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Displayr.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Displayr**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-displayr-test-user"></a>Displayr-testgebruiker maken

Als u Azure AD-gebruikers wilt inschakelen, moet u zich aanmelden bij Displayr en moeten deze worden ingericht in Displayr. In Displayr is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Displayr als beheerder.

2. Klik op **Instellingen** en navigeer naar **Account**.

    ![Configuratie van displayr](./media/displayr-tutorial/config01.png)

3. Ga vanuit het bovenste menu naar **Instellingen** en blader naar beneden, totdat de sectie **Gebruikers** vervolgens op **Nieuwe gebruiker**klikt.

    ![Configuratie van displayr](./media/displayr-tutorial/config07.png)

4. Voer op de pagina **Nieuwe gebruiker** de volgende stappen uit:

    ![Configuratie van displayr](./media/displayr-tutorial/config06.png)

    a. Voer in **het tekstvak Naam** de naam van de gebruiker in, zoals **Brittasimon.**

    b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `Brittasimon@contoso.com`.

    c. Selecteer uw juiste **groepslidmaatschap**.

    d. Klik op **Opslaan**.

### <a name="test-sso"></a>Test SSO

Wanneer u de displayr-tegel in het toegangspaneel selecteert, moet u automatisch worden aangemeld bij de Displayr waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
